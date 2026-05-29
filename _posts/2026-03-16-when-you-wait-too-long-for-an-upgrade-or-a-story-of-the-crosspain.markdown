---
layout: post
title: "When you wait too long for an upgrade, or a story of the Crosspain"
date: 2026-03-16 18:00:00.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- DevOps
- Kubernetes
tags:
- Crossplane
- Kubernetes
- DevOps
- Infrastructure
meta:
  _edit_last: '1'
  hefo_before: '0'
  hefo_after: '0'
author:
  login: graffzon
  email: zonov@duck.com
  display_name: Kirill
  first_name: ''
  last_name: ''
permalink: "/when-you-wait-too-long-for-an-upgrade-or-a-story-of-the-crosspain/"
excerpt: "A story about upgrading Crossplane from v1.16 to v1.20, where everything that could go wrong did go wrong. Featuring ghost resources, infinite reconciliation loops, and two controllers fighting over an empty list like it's the last cookie on the plate."
---

![Crosspain](/assets/2026/03/crosspain.png)

If you're running Crossplane in production and your version is more than a couple of minor releases behind - this post is your gentle nudge to go upgrade it. Right now 🙈 Before it becomes what it became for me: a week-long odyssey through undocumented breaking changes, phantom resources, and two controllers stuck in an endless argument over an empty list.

## The starting point

We were running Crossplane **v1.16**. The latest at the time was **v2.1**. Yeah, I know, we all have been in this situation. What made it even more tricky is that the gap was large enough that we couldn't just jump. Crossplane requires you to upgrade **one minor version at a time**. So the path was: 1.16 → 1.17 → 1.18 → 1.19 → 1.20, and only then we could think about v2.

## The first casualty: ProviderRevisions

The first few hops (1.17, 1.18) went surprisingly smooth on dev. I got confident. Then 1.19 hit, and the providers refused to start.

Crossplane has this concept of **ProviderRevisions** — immutable snapshots of a provider at the moment of installation. The key word here is *immutable*. When the underlying API changes (in our case, `ControllerConfig` was removed in favor of `DeploymentRuntimeConfig`), old revisions can't update themselves. They just sit there, broken, complaining into the void.

The fix was straightforward once I understood it: scale down Crossplane, delete the old revisions one by one, scale back up. New revisions get created automatically. But it was the first sign that this upgrade wasn't going to be a "bump the version and go home" affair.

I documented the procedure, disabled auto-sync on production as a precaution, and moved on to 1.20.

## The silent breaking change

After reaching 1.20 on dev, I noticed that some of our IAM Roles managed through Crossplane Compositions stopped working. Specifically, ephemeral environments that spin up automatically from pull requests were failing to create IAM roles.

The roles would get registered as Kubernetes resources, but they'd never become Ready. They'd hang in this limbo state: the Kubernetes object exists, but no corresponding AWS resource gets created.

And here is the strangest thing. If I restarted the Crossplane pod, everything would sync within a minute. All roles would become Ready, AWS resources would appear. Magic. Then, after some time, it would break again for any new role.

I dug into the Crossplane changelog. Nothing about a breaking change here. I checked our Compositions. They hadn't been modified in months. So what changed?

Turned out, `reclaimPolicy` — a field we were using in our Compositions — was silently deprecated and became invalid in 1.20. Crossplane didn't log it as an error. It didn't reject the manifest. It just... created an endless reconciliation loop, where the resource would keep trying to sync and never converge. Replacing it with `deletionPolicy` fixed that batch of broken roles.

But not all of them 😏

## The ghost hunt

With `reclaimPolicy` fixed, most of our Crossplane-managed resources came back to life. But the IAM roles in ephemeral environments were still misbehaving. New ones would be created in Kubernetes with absolutely no Status section. No `Synced` condition, no `Ready` condition, no events, nothing. The Kubernetes resource existed, but it was like the controller didn't even know about it.

And then the weird part: restart Crossplane → everything works for a few hours → breaks again.

This is the point where I started to run out of ideas. I had already:

- Upgraded the IAM provider (independently versioned from Crossplane itself)
- Verified certificates weren't mismatched
- Checked if it was a race condition in provider startup
- Looked at the DeploymentRuntimeConfig settings
- Read through dozens of GitHub issues

At one point I attempted to switch the provider's DeploymentRuntimeConfig to a different configuration and put the entire Crossplane into CrashLoopBackOff 😅 Had to urgently revert while things were, as I eloquently put it in Slack, "smoking". That was a fun afternoon.

![Wednesday](/assets/2026/03/wednesday.jpeg)

It was already apparent to me at this point that there must be some race condition happening, as the revision kept growing, and I tried to dump the resource's manifest at various time points, but was getting no diff besides that revision.

## The reconciliation duel

Thursday morning, a colleague joined me for a pairing session. Fresh eyes and all that.

We installed [kubectl-watch](https://github.com/lee0c/kubectl-watch), a long-time unmaintained tool that records the complete history of every change to a Kubernetes resource. This turned out to be the key. Instead of staring at a resource that was in some broken state, we could see the exact sequence of mutations happening to it.

And what we saw with the kube-watch was... two controllers fighting 🤦‍♂️

Here's what was happening: our Composition for IAM Roles had a field called `managedPolicyArns` - a list of IAM policy ARNs to attach to the role. For some roles, this list was empty (no additional policies needed). In the Composition, we were explicitly setting it to `[]`.

Before 1.20, Crossplane was easy about this. An empty list? Sure, whatever. But 1.20 became stricter about how it handles empty values, and this created a conflict between the Crossplane controller and the AWS IAM Provider:

1. The Crossplane controller looks at the Composition, sees `managedPolicyArns: []`, and writes it to the managed resource spec
2. The AWS IAM Provider picks it up, sees an empty list, decides "this field shouldn't be here at all", and removes it from the spec
3. The Crossplane controller notices the field is missing, checks the Composition, and adds it back
4. The AWS IAM Provider removes it again
5. GOTO 1 🤪

They were stuck in an infinite loop, each one undoing the other's work, never reaching consensus. And because they never agreed on the spec, the resource never progressed to the point where the provider would actually create the AWS resource. No status, no events, just two controllers fighting over an empty list, hundreds of times per minute.

The fix? Remove the empty `managedPolicyArns: []` from the Composition entirely. If there are no policies to attach, just don't specify the field. One-line change. A few days of debugging.

## What I learned

**Upgrade often.** This is obvious but bears repeating. The jump from 1.16 to 1.20 accumulated four versions of undocumented behavioral changes. Had we been upgrading one version at a time as they were released, each issue would have been isolated and much easier to diagnose.

**Crossplane's strictness is a moving target.** Between versions, the controller changes how it handles edge cases in manifests — empty lists, deprecated fields, default values. These changes rarely make it into the changelog as "breaking." They're just considered "bug fixes" or "improved validation." But if your Compositions relied on the old lenient behavior, they break.

**kubectl-watch turned out to be invaluable.** When two controllers are fighting over a resource, standard `kubectl get` and `kubectl describe` only show you the latest state. You need the full mutation history to understand what's going on. The resource revision counter incrementing rapidly is a hint, but without seeing the actual changes, you're guessing.

**Empty is not the same as absent.** This is the fundamental lesson. In the Kubernetes/Crossplane world, setting a field to `[]` and not setting it at all are two very different things. Different controllers can have different opinions about what an empty list means, and when they disagree, you get an infinite loop.

**Restarting fixes it (temporarily) because of state.** When you restart Crossplane, the controller rebuilds its internal state from scratch. During this brief window, it processes resources in a clean sequence, and things converge. But as the controller settles into its steady-state reconciliation loop, the conflict pattern re-emerges for any new resources.

## The aftermath

After the fix, everything stabilized. Production was upgraded using the documented runbook without major issues (since the Compositions were already fixed from the dev debugging). The other clusters followed a few days later.

The whole endeavor took about a week of semi-active work. We produced a troubleshooting guide, a proper upgrade runbook, and a healthy fear of the phrase "just bump the version."

We internally nicknamed Crossplane "Crosspain" after this experience. The custom emoji in Slack tells more than a thousand words.

If there's one thing I want you to take away from this: go check your Crossplane version right now. If it's more than one minor behind, schedule the upgrade. Future you will thank present you. Trust me.
