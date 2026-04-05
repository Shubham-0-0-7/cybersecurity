---
title: "Shush Protocol"
authors: [shubham]
tags: [htb, pcap, networking, wireshark]
---

# HTB Tryout: Shush Protocol

In this challenge, we are provided with a network capture file (`traffic.pcapng`) and tasked with finding the hidden flag.



### Challenge Details
- **Description:** Analyze the PCAP file to uncover the hidden communications related to a custom protocol.
- **Category:** Forensics / Networking

### Analysis

Upon opening the provided `traffic.pcapng` in Wireshark, the first step is to analyze the general traffic statistics and conversations. Noticing a significant amount of standard HTTP/TCP traffic, we can start filtering or simply following the TCP streams.

By right-clicking on a suspicious TCP packet and selecting **Follow > TCP Stream**, we can inspect the raw data exchanged between the client and server.

Scrolling through the streams, we come across a specific stream (Stream 1) where custom protocol messages are visible.

<img width="1376" height="817" alt="image" src="https://github.com/user-attachments/assets/35b61d3d-9c68-49ea-af3a-d66ca130756c" />

### The Flag

In the screenshot above, we can clearly see the hidden data embedded within the stream. The server responds with the flag string after an `operator` message prompt.

**Flag:** `HTB{s0m371m35_cu570m_p2070c01_4r3_n07_3n0u9h7}` 


