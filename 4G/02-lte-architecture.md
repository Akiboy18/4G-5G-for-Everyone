# 02 — Network Architecture (EPS)

## 2.1 The big picture: EPS

The entire LTE network system is called the **EPS — Evolved Packet System**. It has exactly two halves:

```
        E-UTRAN                          EPC
   (Radio Access Network)     +      (Core Network)
   "gets your phone           +      "routes your data,
    connected over the air"   +       manages who/what/where"
```

See [`diagrams/eps-architecture.svg`](eps-architecture.svg) for the full visual map — worth opening alongside this chapter.

- **E-UTRAN** (Evolved UMTS Terrestrial Radio Access Network) = the radio side. In LTE this is refreshingly simple compared to 3G: it's just **eNodeBs** (base stations) talking directly to each other and to the core. No separate controller node.
- **EPC** (Evolved Packet Core) = the core side. This is where identity, mobility, billing, and internet routing all happen.

This is called a **"flat architecture"** — in 3G there was an extra controller node (RNC) between the base station and the core. LTE removed it, which reduces latency and complexity.

## 2.2 E-UTRAN: the radio side

Just one type of node here:

### eNodeB (eNB) — the LTE base station
Does everything a 3G Node B *and* its RNC controller used to do combined:
- Radio resource management (deciding who gets which frequency/time slots)
- Mobility management triggers (deciding when to hand a phone off to a neighboring cell)
- Header compression and encryption of user data
- Connects to the core network via the **S1 interface**
- Connects to *neighboring* eNodeBs directly via the **X2 interface** (this is what makes fast handovers possible — eNodeBs can coordinate handovers between themselves without going through the core every time)

## 2.3 EPC: the core side

The EPC has four main logical nodes. Think of each one as answering a specific question:

| Node | Full name | The question it answers |
|------|-----------|--------------------------|
| **MME** | Mobility Management Entity | "Who is this device, and where is it right now?" |
| **S-GW** | Serving Gateway | "Where does this device's data go while it's in my network?" |
| **P-GW** | Packet Data Network Gateway | "How does this device's data get out to the internet?" |
| **HSS** | Home Subscriber Server | "Is this SIM card actually valid, and what's it allowed to do?" |

Plus one more that governs policy:

| Node | Full name | Role |
|------|-----------|------|
| **PCRF** | Policy and Charging Rules Function | Decides QoS rules and billing rules (e.g. "this subscriber's plan gets throttled after 10GB") |

### MME (Mobility Management Entity) — deep dive
- **Control-plane only** — it never touches actual user data (no voice/video/web traffic passes through it), only signaling.
- Handles: UE authentication (working with HSS), tracking area updates (knowing roughly which group of cells your phone is near even when idle), paging (waking your phone up for an incoming call/message), and bearer management (setting up/tearing down the "pipes" your data flows through).
- One MME can serve many eNodeBs, and typically many MMEs are pooled together in a region for load balancing and redundancy.

### S-GW (Serving Gateway) — deep dive
- **User-plane node** — actual data (your Instagram feed, your call audio) physically flows through this.
- Acts as the local mobility anchor: when you move between eNodeBs (within the same operator's area), the S-GW is what keeps your session alive without needing to re-establish it from scratch.
- Buffers downlink data when a UE is idle and needs to be paged.

### P-GW (Packet Data Network Gateway) — deep dive
- **The exit point to the internet.** Your device's IP address is assigned here.
- Enforces per-user policy (QoS, charging rules) as instructed by the PCRF.
- Does packet filtering and can perform deep packet inspection for policy enforcement.
- If you move to a completely different network/region, the P-GW can stay the same to preserve your IP address (important for things like ongoing VoIP calls).

### HSS (Home Subscriber Server) — deep dive
- The master database of subscriber information — think of it as tied to your SIM card / IMSI (International Mobile Subscriber Identity).
- Stores: subscription details, authentication keys, which services you're allowed to use, roaming permissions.
- The MME queries the HSS during the attach process to authenticate you (Chapter 6 walks through this step by step).

## 2.4 Key interfaces — the "wiring diagram"

| Interface | Connects | Carries |
|-----------|----------|---------|
| **Uu** | UE ↔ eNodeB | The actual radio air interface |
| **X2** | eNodeB ↔ eNodeB | Handover coordination, load info |
| **S1-MME** | eNodeB ↔ MME | Control-plane signaling |
| **S1-U** | eNodeB ↔ S-GW | User-plane data |
| **S5/S8** | S-GW ↔ P-GW | User data (S8 = when roaming) |
| **S6a** | MME ↔ HSS | Subscriber authentication data |
| **Gx** | PCRF ↔ P-GW | Policy rules |

## 2.5 Deep dive: control plane vs user plane

This distinction is fundamental to how *all* modern mobile networks (4G, 5G) are designed, so it's worth internalizing here:

- **Control plane** = signaling — the "administrative" messages that set things up, authenticate, and manage mobility (handled by MME).
- **User plane** = the actual payload — your call audio, your video stream, your web page bytes (handled by S-GW → P-GW).

Separating these two paths means the network can scale each independently. If a carrier needs to handle way more signaling (e.g. millions of IoT devices doing brief check-ins) without also needing more user-data-carrying capacity, they can scale the MME layer without touching the S-GW/P-GW layer. This concept becomes even more pronounced in 5G's "Control and User Plane Separation" (CUPS) architecture, which LTE-A Pro also adopted late in its life.

## 2.6 Deep dive: bearers — LTE's version of a "connection"

A **bearer** is LTE's abstraction for a data pipe with defined QoS characteristics between the UE and the P-GW. Every device gets at least one **default bearer** (best-effort, always-on, gives you basic IP connectivity) upon attach, and can get additional **dedicated bearers** for specific needs (e.g. a guaranteed-bitrate bearer for a VoLTE call).

Each bearer is identified by a **QCI (QoS Class Identifier)** — a number that maps to a specific priority/latency/loss profile. E.g., QCI 1 is reserved for conversational voice (VoLTE), QCI 9 is typical for regular web browsing.

---

## Quick recap
- EPS = E-UTRAN (radio) + EPC (core)
- E-UTRAN is just eNodeBs — flat, no separate controller
- EPC's four core nodes: MME (control), S-GW (local user-plane anchor), P-GW (internet gateway), HSS (subscriber database)
- Control plane (signaling) and user plane (actual data) are architecturally separated throughout
- Bearers are the QoS-defined "pipes" your data actually flows through

**Next →** [03 - Radio Interface Fundamentals](03-radio-interface-basics.md)
