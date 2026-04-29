# rtsp-analysis-lab

RTSP/RTP/RTCP packet analysis using tcpdump and Wireshark for troubleshooting streaming issues.

---

# RTSP / RTP / RTCP Analysis Lab

## Overview

This repository documents a hands-on troubleshooting and packet analysis workflow for RTSP streaming using a VIGI camera.

The goal was to identify whether video stuttering was caused by network issues or device/encoding behavior.

---

## Environment

- Camera: 192.168.10.3
- Linux Ubuntu on VirtualBox: 192.168.10.50
- Interface: enp0s8 bridged

## Tools

- ffplay: RTSP client
- tcpdump: packet capture
- Wireshark: packet analysis

---

## Step 1: RTSP Streaming Test

Command:

    ffplay 'rtsp://admin:password@192.168.10.3:554/stream1'

Purpose:

- Reproduce the issue
- Observe streaming behavior

---

## Step 2: Packet Capture

Command:

    sudo tcpdump -i enp0s8 host 192.168.10.3 -w rtsp_cam.pcap

Purpose:

- Capture actual network traffic for later analysis in Wireshark

---

## Step 3: Wireshark Analysis

### 1. Filter by camera IP address

Display filter:

    ip.addr == 192.168.10.3

Purpose:

- Focus only on communication between the Linux PC and the camera

What to check:

- TCP connection
- RTSP control packets
- RTP media packets
- RTCP report packets

---

### 2. RTSP session check

Display filter:

    rtsp

Purpose:

- Confirm that the RTSP session was established successfully

What to check:

- OPTIONS
- DESCRIBE
- SETUP
- PLAY
- GET_PARAMETER

Conclusion:

- The RTSP session was established successfully

---

### 3. RTP analysis

Display filter:

    rtp

Purpose:

- Analyze the actual media stream

What to check:

- RTP sequence number continuity
- Payload type
- SSRC consistency

Example:

    Seq=2077
    Seq=2078
    Seq=2079
    Seq=2080

Conclusion:

- RTP sequence numbers were continuous
- No packet loss was detected

---

### 4. RTCP analysis

Display filter:

    rtcp

Alternative filter:

    udp && ip.addr == 192.168.10.3 && udp.port == 5001

Purpose:

- Check stream quality feedback

What to check:

- Packet loss
- Jitter
- Highest sequence number

Conclusion:

- No packet loss was observed
- RTCP reports showed stable conditions

---

## Key Concepts

### RTSP

RTSP is used to control the streaming session.

Examples:

- OPTIONS
- DESCRIBE
- SETUP
- PLAY

In simple terms, RTSP works like a remote control for the stream.

---

### RTP

RTP carries the actual video and audio data.

In this test, RTP was used to carry the camera stream.

Important field:

- Sequence number

The sequence number is used to detect packet loss.

Example:

    Seq=100
    Seq=101
    Seq=102
    Seq=104

If Seq=103 is missing, it indicates packet loss.

---

### RTCP

RTCP provides feedback about stream quality.

It can be used to check:

- Packet loss
- Jitter
- Sequence number status

In simple terms, RTCP works like a health report for the stream.

---

## Troubleshooting Logic

1. Reproduce the issue using ffplay
2. Capture traffic using tcpdump
3. Open the pcap file in Wireshark
4. Check the RTSP session
5. Check RTP sequence numbers
6. Check RTCP reports
7. Determine whether the issue is network-related or device/encoding-related

---

## Final Conclusion

- RTSP session setup was successful
- RTP sequence numbers were continuous
- No packet loss was observed
- RTCP reports showed stable conditions

Therefore:

The issue was not caused by the network, but was likely related to encoding behavior or device performance.

---

## Interview Explanation

Using Wireshark, I confirmed that there was no packet loss and the RTP sequence numbers were continuous.

Therefore, I concluded that the issue was not caused by the network, but was likely related to encoding behavior or device performance.

---

## Additional Notes

- ffplay was used as an RTSP client for testing
- tcpdump was used to capture raw network traffic
- Wireshark was used to analyze RTSP, RTP, and RTCP
- RTP sequence gaps indicate packet loss
- RTCP provides stream quality feedback


---
## Protocol Flow
```text
Linux PC / ffplay
        |
        | RTSP: OPTIONS / DESCRIBE / SETUP / PLAY
        v
VIGI Camera
        |
        | RTP: Video / Audio stream
        v
Linux PC / ffplay
        |
        | RTCP: Receiver Report / quality feedback
        v
VIGI Camera
```
---
## What I Learned

- RTSP controls the streaming session.
- RTP carries the actual video and audio data.
- RTCP provides feedback about stream quality.
- RTP sequence numbers are useful for detecting packet loss.
- If RTP sequence numbers are continuous, the issue is less likely to be caused by network packet loss.

---
## Safe Notes

- Real passwords are not included in this repository.
- The RTSP URL uses a placeholder password.
- The IP addresses are used only for lab documentation.


---
## Next Steps

- Add Wireshark screenshots
- Add IO Graph analysis
- Add an example of RTP packet loss
- Compare RTSP over UDP and RTSP over TCP


---
## Background

This lab was created based on a general troubleshooting scenario for RTSP streaming analysis.
No customer information, ticket ID, password, or confidential details are included.
