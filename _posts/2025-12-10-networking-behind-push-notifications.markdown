---
layout: post
title: "Networking behind iOS push notifications"
date: 2025-12-10 17:00:00.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Networking
- Mobile
tags:
- Networking
- iOS
- Mobile
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
permalink: "/networking-behind-push-notifications/"
excerpt: "Today morning, I had a strange thought about how push notifications on my mobile phone work. I’m curious to know if they send HTTP requests every second. Are there requests sent per app or per the entire mobile device? Additionally, I’m interested in understanding how these notifications are protected. Come join me and let’s try to uncover the answers!"
---

![Me trying to grasp the push notifications mechanic](/assets/2025/12/push-notifications-questions.png)

This morning I was poking at my phone and suddenly realized:
How do the push notifications actually work???

Does the server know my phone’s public IP?
Does the phone poll every second?
Is every app keeping its own connection open?

I had no answers. So I went down the rabbit hole — at least the networking side — and here’s the short version of what I learned.

## Polling? Websocket? Nope, it's not

iOS apps don't poll Apple’s servers for push notifications. There’s no loop of “got anything for me yet?”
It is quite natural to assume, and I honestly would be shocked if it were the case :) 
Nice that it's not.

Then what is the mechanism?

Instead, there is an iOS [system daemon](https://developer.apple.com/documentation/usernotifications/setting-up-a-remote-notification-server) called `apsd` (Apple Push Services Daemon) that opens **one** long-lived TLS connection to Apple’s Push Notification service (APNs). All apps on your phone share this connection. And the daemon knows a unique so-called Device Token for each app, that helps it to route the notifications to the correct app.

In fact, it is the same mechanism for the OSX.

![Apsd process on OSX](/assets/2025/12/apsd-on-osx.png)

Also worth mentioning that your backend never talks to the phone — only to APNs — and APNs pushes data into that already-open connection.

## How does Apple reach my phone?

So how does APNs know where to send anything?

Most phones on mobile networks sit behind Carrier-Grade NAT (CGNAT) and do not have public IPv4 addresses.

So how can Apple “reach” your phone?
- It is **your phone** that initiates an outbound TCP connection to APNs.
- NAT on your ISP creates a mapping (public_port <-> private_port).
- APNs simply sends packets back through the existing TCP session.
- NAT on your ISP allows return traffic to your phone.

APNs never needs your public IP — the TCP socket is already open, authenticated, and routed.
It is essentially the same mechanism as the one used for the web servers.

This is classic NAT behavior: outbound connection -> NAT entry -> return traffic allowed.

No polling. No IP-based routing. Just one long-lived socket.

## Why the connection stays alive?

Mobile carriers aggressively drop idle connections, so iOS sends periodic keep-alive packets to prevent NAT expiration. Apple doesn’t document the exact intervals, but from what I found:
- They are infrequent (battery-friendly)
- They depend on network conditions
- They ensure APNs can always reach the device

When you toggle Wi-Fi, turn on Airplane mode, or switch networks, apsd seems to reconnect automatically.

## Can I see the connection?

Yes, you can definitely play with it!

One way would be to check the logs of the apsd process.
```bash
$ log stream --predicate 'process == "apsd"' --info
```

For me it showed an interesting detail:
```bash
Timestamp                       Thread     Type        Activity             PID    TTL  
2025-12-11 12:13:46.489671+0100 0x1215aa   Default     0x0                  360    0    apsd: (libxpc.dylib) [com.apple.xpc:connection] [0xc9732ca00] activating connection: mach=false listener=false peer=true name=com.apple.apsd.peer[49350].0xc9732ca00
2025-12-11 12:13:46.490382+0100 0xd5b      Default     0x0                  360    0    apsd: Failed entitlement check 'com.apple.private.aps-connection-initiate' for 49350
2025-12-11 12:13:46.490449+0100 0xd5b      Activity    0x1a5c23             360    0    apsd: (libsystem_trace.dylib) Activity for state dumps
2025-12-11 12:13:46.490439+0100 0xd5b      Fault       0x1a5c23             360    14   apsd: Peer connection [pid=49350] lacks APSConnectionInitiateEntitlement
```

The PID of 49350 is `/System/Library/Frameworks/CoreTelephony.framework/Support/CommCenter`, which seems to be somehow related to the cellular or eSIM connectivity. I couldn't figure out why is it attempting to connect to APNs directly but I guess it is some sort of fallback mechanism or a legacy thing related to the daemon being present on both OSX and iOS.

You can also use the `netstat` command to see the connection:
```bash
$ netstat -anv | grep apsd
```

For me it produced the following output:
```bash
tcp4       0      0  <my local ip>.<ephemeral local port>    <apple APN ip>.5223      ESTABLISHED        17867       175129  131072  132240             apsd:<apsd PID>    00000 00000000 0000000000000000 00000000 00000000      0      0 000000

fd119f3c5f66d88b stream      0      0                0 4f2ee168ea61008f                0                0            4          164    8192    8192             apsd:<apsd PID>    00082 00000000 00000000000b4e2b 00000001 00000000      2      0 000000

kctl       0      0       775040       249120  131072    2048             apsd:<apsd PID>    00182 00000000 000000000000121a 00008001 00000000      1      0 000000     12      7 com.apple.netsrc
```

Here is a rough breakdown of the output line by line:
1. It shows the local and remote IP addresses and ports and mentiones the TCP4 connection being established.
2. It shows a stream connection, however, it is not a network socket, it is some internal IPC streaming mechanism.
3. `kctl` is another kernel control connection, it is also an internal kernel-related communication channel.

## Conclusion

There is no real conclusion here, as I simply satisfied my curiosity and found it interesting.
I hope you enjoyed reading this as much as I enjoyed writing it, and managed to learn something new about how your phone works on the networking level.

![My dog satisfied with my research](/assets/2025/12/yorkie-networking.png)

## References

- [APN troubleshooting guide](https://the-sequence.com/how-to-troubleshoot-apns)
- [Apple User Notifications Programming Guide](https://developer.apple.com/documentation/usernotifications)
- [Apple Remote Notification Server](https://developer.apple.com/documentation/usernotifications/setting-up-a-remote-notification-server)
 