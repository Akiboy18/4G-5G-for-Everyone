# 05 — Protocol Stack

Chapters 2-4 covered the *nodes* (eNodeB, MME, etc.) and the *radio canvas* (frames, RBs). This chapter covers the **software layers** that run on top of that canvas to actually move your data reliably from an app on your phone to the network and back.

## 5.1 The layer stack, top to bottom

```
   ┌─────────────────────────┐
   │   IP / Applications      │   (your actual apps: browser, etc.)
   ├─────────────────────────┤
   │   PDCP                    │   Packet Data Convergence Protocol
   ├─────────────────────────┤
   │   RLC                     │   Radio Link Control
   ├─────────────────────────┤
   │   MAC                     │   Medium Access Control
   ├─────────────────────────┤
   │   PHY                     │   Physical layer (Ch. 3-4's OFDMA/SC-FDMA world)
   └─────────────────────────┘
```

Plus a control-plane-only layer that sits alongside PDCP:

```
   RRC (Radio Resource Control) — signaling only, not user data
```

This should feel familiar if you know general networking (OSI model) — LTE's stack is essentially a specialized version of the same layered idea, tuned specifically for a wireless link.

## 5.2 PDCP (Packet Data Convergence Protocol)

**What it does:**
- **Header compression** — IP headers are large relative to small payloads (like VoIP packets); PDCP uses **ROHC (RObust Header Compression)** to shrink them, since radio spectrum is precious and every byte matters.
- **Ciphering (encryption)** — encrypts user data and control-plane signaling for security.
- **Integrity protection** — for control-plane (RRC) messages, ensuring they weren't tampered with.
- **In-order delivery and duplicate detection** — especially important during handovers, when packets might arrive from two different eNodeBs briefly.

Think of PDCP as the layer that makes the radio link *look* like a well-behaved IP connection to everything above it.

## 5.3 RLC (Radio Link Control)

**What it does:**
- Segments and reassembles data into sizes that fit into the MAC layer's transport blocks.
- Offers three operating modes depending on what the data needs:

| RLC Mode | Behavior | Used for |
|---|---|---|
| **TM** (Transparent Mode) | No header added, no retransmission | Broadcast/system messages |
| **UM** (Unacknowledged Mode) | Adds headers, no retransmission | VoIP-like traffic (low latency > reliability) |
| **AM** (Acknowledged Mode) | Full ARQ (Automatic Repeat reQuest) — retransmits lost data | Regular data (web, downloads) — reliability matters |

The mode choice is a direct tradeoff between **latency** and **reliability** — this is why voice traffic and file downloads are handled differently even though they travel over the same physical radio.

## 5.4 MAC (Medium Access Control)

**What it does:**
- **Scheduling** — this is the big one. The MAC layer (specifically, the scheduler running in the eNodeB) decides, every 1 ms subframe, which users get which Resource Blocks, and at what modulation/coding scheme, based on reported CQI and other rules.
- **HARQ (Hybrid Automatic Repeat reQuest)** — a fast, low-level retransmission mechanism. When a transport block fails to decode correctly, HARQ doesn't just retransmit — it retransmits with extra redundancy information that gets *combined* with the previously-failed attempt to improve the odds of successful decoding, rather than discarding it. This is a materially smarter version of "just resend it" and is a big part of why LTE handles bad radio conditions gracefully.
- **Multiplexing/demultiplexing** — combines data from multiple logical channels (e.g. control info + user data) into a single transport block for the physical layer.

### HARQ deep dive
HARQ operates with multiple parallel "processes" (typically 8 for FDD) so that while one packet is waiting for its acknowledgment/retransmission, the sender can keep transmitting other packets on other HARQ processes — this keeps the radio link busy instead of idling while waiting for one ACK. Combined with RLC's slower/more complete ARQ layer above it, LTE effectively has a two-tier retransmission system: fast HARQ at the MAC layer for quick recovery, and slower but more thorough ARQ at the RLC layer as a backstop.

## 5.5 PHY (Physical Layer)

This is everything from Chapters 3-4: OFDMA/SC-FDMA, the Resource Grid, modulation, channel coding (adding redundancy bits so errors can be detected/corrected — LTE uses **Turbo Coding**), and MIMO. The MAC layer hands the PHY layer a "transport block" each subframe, and the PHY layer's job is to get that block across the actual air interface correctly (or as close to correctly as radio physics allows).

## 5.6 RRC (Radio Resource Control) — the control-plane layer

RRC runs alongside these layers but is **signaling only** — it never carries your actual app data. It's the layer responsible for:
- Establishing, maintaining, and releasing the RRC connection between UE and eNodeB
- Broadcasting system information (so a phone entering a new cell knows the cell's configuration before it even connects)
- Configuring/reconfiguring bearers
- Triggering and managing handovers
- Managing UE "state": **RRC_IDLE** (not actively connected, saving battery, network doesn't know exact cell — only tracking area) vs **RRC_CONNECTED** (actively connected, network knows the exact serving cell)

Understanding RRC_IDLE vs RRC_CONNECTED matters a lot for Chapter 6 — the "attach" and "paging" procedures are essentially about moving a device between these two states.

## 5.7 Deep dive: how a packet actually flows down the stack

Walking one downlink IP packet (e.g., part of a webpage) through the full stack, top to bottom:

1. **IP packet** arrives at the P-GW, gets routed to the correct S-GW, then to the correct eNodeB.
2. **PDCP** compresses the IP header (ROHC) and encrypts the payload. Adds a PDCP header (sequence number, etc.).
3. **RLC** segments/concatenates this into RLC-layer-sized chunks appropriate for the current radio conditions, adds RLC headers, applies AM/UM/TM behavior.
4. **MAC** multiplexes this with data for other logical channels if needed, decides scheduling (which RBs, this subframe), applies HARQ tracking, hands a Transport Block to PHY.
5. **PHY** encodes it (Turbo coding for error correction), modulates it (QPSK/16-QAM/64-QAM depending on current CQI), maps it onto specific Resource Elements in the current subframe's Resource Grid, and transmits it as an actual OFDM waveform over the air.

The phone's receiver does this exact process in reverse.

---

## Quick recap
- Stack (data path): PDCP → RLC → MAC → PHY, with RRC alongside for signaling only
- PDCP: header compression + encryption + integrity + in-order delivery
- RLC: segmentation + three modes (TM/UM/AM) trading off latency vs reliability
- MAC: scheduling (who gets which RBs each 1ms subframe) + HARQ (smart fast retransmission)
- PHY: the actual OFDMA/SC-FDMA radio transmission from Chapters 3-4
- RRC: connection states (IDLE vs CONNECTED), handover triggers, system broadcast info

**Next →** [06 - Call & Session Procedures](06-call-procedures.md)
