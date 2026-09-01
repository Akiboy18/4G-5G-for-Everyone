# 01 — Introduction to 5G & NR

## 1.1 What is 5G, really?

Where 4G/LTE was fundamentally about one thing — **fast mobile internet** — 5G was designed from the outset around **three distinct use-case pillars**, because the ITU's requirements for "5G" (called **IMT-2020**) explicitly demanded it serve very different kinds of traffic well, not just "be faster than 4G."

| Pillar | Full name | What it optimizes for | Real-world example |
|---|---|---|---|
| **eMBB** | Enhanced Mobile Broadband | Raw speed, capacity | 8K video streaming, AR/VR |
| **URLLC** | Ultra-Reliable Low-Latency Communication | Latency + reliability (not speed) | Remote surgery, factory robotics, self-driving cars |
| **mMTC** | Massive Machine-Type Communication | Device density, battery life (not speed) | Smart city sensors, agricultural IoT, utility meters |

This is the single most important idea to hold onto: **5G isn't "LTE but faster" — it's a radio+core system flexible enough to simultaneously serve a phone streaming 4K video, a factory robot needing 1ms reaction time, and ten thousand parking sensors that need to sip battery for a decade — on the same network.** Chapter 7 covers how that flexibility (network slicing) actually works.

## 1.2 What is NR, and how does it relate to "5G"?

Same pattern as Chapter 1 of the LTE notes:

- **5G** = the performance/category label, defined by **IMT-2020** requirements (ITU)
- **NR (New Radio)** = the actual technical standard (3GPP) that delivers it — this is 5G's version of what LTE was for 4G

Unlike LTE (which shared a lot of DNA with 3G's approach in places), **NR was built largely from a clean slate**, informed by lessons learned from LTE. It reuses several core LTE ideas (OFDM-based radio, a similar core-network node/QoS philosophy) but redesigns them to be far more flexible.

## 1.3 Why 5G was built the way it was

By the mid-2010s, three separate pressures were building that LTE's fairly rigid design couldn't cleanly satisfy all at once:

1. **Video/data demand kept climbing** — LTE-Advanced Pro (Chapter 7 of the LTE notes) was already stretching LTE's architecture toward its limits with CA and higher-order MIMO.
2. **New latency-critical applications emerged** — autonomous vehicles and industrial automation need latency LTE's fixed 1ms subframe architecture (LTE notes, Ch. 4) can't comfortably guarantee.
3. **IoT exploded** — LTE's per-device signaling overhead assumed relatively few, relatively "chatty" devices (phones); it wasn't designed for millions of battery-powered sensors that each send a tiny amount of data rarely.

NR's answer to all three: **make almost everything about the radio interface *scalable/configurable* instead of fixed** (Chapters 3-4 dig into exactly what this means), and rebuild the core network around cloud-native, software-defined principles (Chapter 2) so it can flexibly carve out different "virtual networks" (slices) for each use case.

## 1.4 NSA vs SA — the two deployment modes

This is a practical/commercial detail that matters a lot for understanding real-world 5G rollouts, and it trips people up constantly:

- **NSA (Non-Standalone)** — the 5G radio (NR) is used for extra speed, but it "anchors" onto an *existing 4G LTE core network and LTE control-plane connection* underneath it. This was how almost every carrier launched 5G initially, because it let them deploy 5G radios without having to build an entirely new core network on day one. Technically implemented via **EN-DC (E-UTRA-NR Dual Connectivity)** — Chapter 6 covers this.
- **SA (Standalone)** — a full, independent 5G system: NR radio *and* the new 5G Core (5GC) with no LTE dependency at all. This is what unlocks 5G's *actual* headline capabilities — network slicing, the lowest latency figures, and mMTC — because those all depend on 5GC's cloud-native architecture (Chapter 2), not just the faster radio.

**Key insight:** a phone showing a "5G" icon on an NSA network is genuinely faster, but is *not* getting the low-latency/slicing/massive-IoT benefits — those require SA. This is why "5G" experiences vary so much between carriers and countries depending on how far along their SA rollout is.

## 1.5 3GPP Releases for NR

| Release | Year | What it added |
|---|---|---|
| Release 15 | 2018 | Baseline NR (eMBB focus, NSA mode defined first) |
| Release 16 | 2020 | URLLC enhancements, industrial IoT, V2X (vehicle-to-everything) |
| Release 17 | 2022 | RedCap (reduced-capability devices for mid-tier IoT), NTN (non-terrestrial networks / satellite) |
| Release 18+ | 2024+ | "5G-Advanced" — AI/ML-native network optimization, further mMTC/energy efficiency work |

## 1.6 Deep dive: IMT-2020 performance targets

The ITU's official bar for calling something "5G," for comparison against what you learned was LTE-Advanced's bar in the LTE notes:

| Metric | IMT-2020 target |
|---|---|
| Peak data rate | 20 Gbps downlink / 10 Gbps uplink |
| User-experienced data rate | 100 Mbps (downlink), even under real-world load |
| Latency (user plane) | 1 ms |
| Connection density | 1,000,000 devices per km² |
| Mobility | Reliable connectivity up to 500 km/h |
| Spectral efficiency | 3x improvement over IMT-Advanced (LTE-A's bar) |

Notice these targets map directly onto the three pillars: peak/experienced data rate → eMBB, latency → URLLC, connection density → mMTC. No single "one-size" radio configuration hits all of these simultaneously — which is exactly why NR's defining trait is **configurability** rather than one fixed set of parameters, unlike LTE's largely fixed 15kHz-subcarrier, 1ms-subframe design.

---

## Quick recap
- 5G = three pillars (eMBB, URLLC, mMTC), not just "faster LTE" — this shapes every subsequent design choice
- NR is the 3GPP radio standard delivering 5G, built substantially clean-slate but reusing OFDM-based ideas from LTE
- NSA = 5G radio riding on an LTE core (early rollout mode, speed only); SA = full independent 5G system (unlocks slicing/low-latency/mMTC)
- IMT-2020 sets aggressive, pillar-specific targets that no single fixed radio config can hit at once — hence NR's scalability-first design

**Next →** [02 - Network Architecture (5GC)](02-5g-architecture.md)
