---
layout: post
title: Fluentbit optimization. Buffering
date: 2024-05-14 18:00:00.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- No category
tags: []
meta:
  _edit_last: '1'
  hefo_before: '0'
  hefo_after: '0'
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/fluentbit-buffering/"
excerpt: "This post provides some technical ideas on how to improve Fluentbit performance by tuning the buffering configuration"
---

![The flow of the logs](/assets/2024/05/fluentbit-logs-flowing.png)

# Introduction

In any application, there is a need to monitor various signals to ensure the stability and reliability of the application. Logs are one of the sources for such signals. Although they are not usually used for monitoring, they are crucial for understanding when something goes wrong.

Fluent Bit is an open-source log processor and forwarder, part of the Fluentd ecosystem, designed to collect, parse, and route logs from various sources to multiple destinations efficiently. In this post, I will explore a very important concept that Fluent Bit offers: buffering. When incorrectly configured, buffering can lead to all sorts of issues, including causing logs to become stuck, to be dropped, or not to be ingested at all.

# Understanding Buffering in Fluent Bit

## What is buffering

Buffering in Fluent Bit serves as a crucial mechanism for managing how logs flow through the system. Specifically, it means that Fluent Bit temporarily stores incoming logs in memory at various stages of the pipeline before they are processed or sent to their destination.

This technique enables Fluent Bit to handle bursts of log activity and network instability. By buffering data, the system can smooth out inconsistencies in log production and consumption rates, preventing log loss and reducing the risk of backpressure in any single component of the logging pipeline. Properly configured buffers help maintain system stability and performance, ensuring that logs are reliably processed and forwarded even under varying load conditions.

## The most important question in Buffering

Can you afford to lose logs?

Answering this is crucial. If you have multiple destinations (outputs in Fluent Bit), the question becomes more complex. For which destinations can we afford to lose logs, and which must always receive them?

Below, I will explore the buffering configuration, considering various responses to this question.

## Fluent Bit configuration concepts

There are three types of elements you operate in Fluent Bit: Inputs, Filters, and Outputs.

In simple terms, Inputs retrieve data from sources such as files, streams, and system journals. Filters manipulate this data—enriching, discarding, or routing it. Outputs send the data to its destinations.

Each of these components interacts differently with buffering:

- **Inputs** read logs from sources and send them to filters. However, if filters are busy, Inputs store the logs in their internal buffer.
- **Filters** generally do not have a buffer configuration, as they are expected to simply pass the data through without retaining it. However, there are exceptions:
    - Some filters, like the `kubernetes` filter, possess a `Mem_Buf_Limit` attribute. Despite the naming, this attribute serves a different purpose compared to similar attributes on Inputs. For example, in the `kubernetes` filter, the buffer is used to store responses from the Kubernetes API server when enriching the metadata of log records.
    - A filter, known as `rewrite_tag`, functions somewhat like an Input. After changing the tag of a log record, it can store the record in its own buffer. The size of this buffer can be configured using the differently-called attribute `Emitter_Mem_Buf_Limit`. If this limit (10MB by default) is reached, your entire pipeline could become stuck!
- **Outputs** are complex in that they typically do not have their own buffers and instead create backpressure for Inputs, causing Inputs to pause if Outputs cannot process the log chunks (a "chunk" is a batch of logs). However, there are nuances:
    - Some Output plugins, such as `opensearch`, have a `Buffer_Size` option, which buffers the response from OpenSearch rather than the logs themselves.
    - Enabling filesystem storage allows Outputs to have a dedicated, explicit buffer, which we will discuss separately.

# Talking about an example Fluent Bit configuration

## **Configuration Overview**

In this section, I will present a simplified Fluent Bit configuration to illustrate various scenarios and enhance our understanding of Fluent Bit's behavior. Our setup includes:

- A basic Service section, which will be relevant later on. You can disregard this for the moment.
- Two inputs, both utilizing the tail method plugin.
- Several filters, all with the standard approach to buffering (not using the **`rewrite_tag`** filter for simplicity).
- Two distinct outputs, Loki and OpenSearch.

```toml
[SERVICE]
    Flush        1
    Daemon       Off
    Log_Level    info

[INPUT]
    Name         tail
    Path         /var/log/app1.log
    Mem_Buf_Limit 5MB

[INPUT]
    Name         tail
    Path         /var/log/app2.log
    Mem_Buf_Limit 5MB

[FILTER]
    Name         kubernetes
    Match        *
    Buffer_Size  10MB

[FILTER]
    Name         modify
    Match        *
    Set          new_key new_value

[OUTPUT]
    Name         loki
    Match        *
    Url          <http://your-loki-server:3100>
    Batch_Size   1MB
    Batch_Wait   5
    Mem_Buf_Limit 10MB

[OUTPUT]
    Name         opensearch
    Match        *
    Retry_Limit  1

```

# Debugging Flow

## **Scenario 1: Uncontrolled Memory Consumption or Fluent Bit OOM-Killed**

**Problem:** You notice that memory consumption is getting out of control, or your Fluent Bit instance gets OOM-killed.

**Explanation:** This issue is most likely due to the absence of a **`Mem_Buf_Limit`** on Inputs. While some components do have a default limit, it's generally not universally applied.

**Solution:** It's recommended to ALWAYS set the memory limit for your Inputs to prevent such issues *(except when you use the Filesystem storage. More on it later)*.

## **Scenario 2: Logs Missing Across All Destinations**

**Problem:** No logs show up after a certain point across all your destinations.

**Explanation:** This can happen for various reasons related to both Fluent Bit and your destination engines (e.g., ES, Loki). A common issue arises when your Output becomes congested, applying backpressure on the Fluent Bit pipeline, causing the Input to start filling the buffer. This works until the buffer is full, at which point the Input stops processing. This can be harmless for some Inputs (e.g., **`systemd`**), potentially problematic for others (e.g., **`tail`**), and catastrophic for the rest (e.g., **`tcp`**).

*Why is it “potentially harmful” for **`tail`**?*

*If the file wasn’t rotated while the pipeline was congested, no harm done—Fluent Bit simply marks where it stopped reading. However, if rotation occurred, data in the rotated segment that wasn’t read will be lost. Mitigations include using the **`Rotate_Wait`** attribute for reading already-rotated files or manually reprocessing rotated files.*

**Solution:** Here are a few strategies that might help:

1. Examine your destinations to ensure their write-path is optimized for your load, such as the **`distributor`** or **`gateway`** in Loki's case.
2. If losing logs to a particular destination is acceptable, consider using the **`Retry_Limit`** attribute on the output. This means that after a specified number of attempts, the output will drop a chunk of logs.
3. For input plugins that cannot afford to be suspended (like **`tcp`** Input), significantly increasing the buffer on the Input may be necessary.

> It’s important to note that Fluent Bit operates in a somewhat synchronous manner across its pipeline components. So if one of your outputs get blocked, it will most likely block your whole Fluent Bit pipeline

# Filesystem Buffering. The Silver Bullet

The title, admittedly, is a bit of clickbait—there are no silver bullets in technology, except, perhaps, an actual silver bullet.

However, there is a notable feature in the realm of buffering in Fluent Bit: the option to utilize the filesystem, in contrast to the default memory-based strategy, for storing buffers. Opting for filesystem buffering doesn't mean abandoning the standard in-memory approach. On the contrary, it acts as a complement. Every log record entering the buffer is duplicated, existing both in memory and on disk. Yet, once the memory limit is reached and no longer accepting new entries, the filesystem continues to receive and buffer logs. This approach is particularly beneficial in preventing buffer overflows and can be a lifesaver for applications with numerous network-based inputs, such as the **`tcp`** input.

## Configuring Filesystem Buffering.

You can configure central settings for the filesystem-base buffering in the `[SERVICE]` block:

```toml
[SERVICE]
    flush                     1
    log_Level                 info
    storage.path              /var/log/fluentbit-buffer/
    storage.max_chunks_up     128
```

With this, considering the default chunk size of ~2MB, your inputs will have each about 512MB of in-memory buffer. Consider reducing the `max_chunks_up` setting if it feels like too much for your application.

Additionally you can decide what parts of your pipeline needs to use which type of buffering:

```toml
[INPUT]
    name                      systemd
    storage.type              memory 
[INPUT]
    name                      tcp
    storage.type              filesystem 

[OUTPUT]
    name                      opensearch
    match                     *
    storage.total_limit_size  500M
```

With this you’re telling that your inputs should use different buffering strategies, as well as setting the exact limit on the Output to use 500MB for the buffering on disk.

# Conclusion

Fluent Bit is a widely used log-delivery tool, and understanding how to optimize it for your specific needs is crucial. At my current company, Fluent Bit successfully handles several terabytes of logs daily, and tuning the buffering was a critical task we undertook to ensure its efficiency. Don’t hesitate to invest time in tuning and improving your logging pipeline. Doing so is vital for maintaining the health of your organization.
