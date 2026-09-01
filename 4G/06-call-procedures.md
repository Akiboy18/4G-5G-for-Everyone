# 06 — Call & Session Procedures

This chapter puts Chapters 2-5 into motion — walking through the actual step-by-step message flows for the most important real-world events: turning your phone on, getting a call/message, and moving between cells.

## 6.1 Initial Attach Procedure — "phone turns on and joins the network"

This is the single most important procedure to understand — almost everything else in LTE builds on it. High-level steps:

1. **UE selects a cell** — scans available frequencies, reads broadcast system information from nearby eNodeBs, picks the best one.
2. **RRC Connection Establishment** — UE moves from RRC_IDLE to RRC_CONNECTED with that eNodeB.
3. **Attach Request sent** — UE sends an Attach Request (via the eNodeB) to the MME, including its identity (IMSI or a temporary ID from a previous session).
4. **Authentication** — MME talks to the HSS to verify the UE is legitimate (this uses a challenge-response mechanism with keys stored on the SIM and in the HSS — neither side ever transmits the raw secret key over the air).
5. **NAS Security Setup** — encryption and integrity protection get established for the signaling between UE and MME (NAS = Non-Access Stratum, meaning "core network signaling" as opposed to "AS/Access Stratum" signaling between UE and eNodeB).
6. **Default Bearer Setup** — the MME coordinates with S-GW and P-GW to set up the UE's default bearer (Chapter 2, section 2.6) and assign an IP address.
7. **Attach Accept / Complete** — UE is now fully attached, has an IP address, and is sitting in RRC_CONNECTED (it will typically drop back to RRC_IDLE shortly after if there's no active data, to save battery — network keeps track of it at the Tracking Area level while idle).

### Why authentication matters here (deep dive)
LTE uses a mechanism called **EPS-AKA (Authentication and Key Agreement)**. The core idea: both the UE's SIM and the HSS independently derive the same set of session keys from a shared secret key (never transmitted) plus a random challenge — if the UE's calculated response matches what the network expected, it's authenticated, and both sides now separately possess matching keys they can use to encrypt the session, without ever having sent the encryption key itself over the air.

## 6.2 Paging — "how the network finds you when you're idle"

When your phone is idle (RRC_IDLE) to save battery, the network doesn't know your *exact* cell — only which **Tracking Area** you're in (a group of nearby cells). So when a call/message arrives for you:

1. Incoming data hits the P-GW → S-GW, which buffers it and notifies the MME.
2. MME sends a **Paging** message to *every eNodeB in your last known Tracking Area* — essentially a "is UE-X here?" broadcast.
3. Your phone, which periodically wakes up briefly to listen for paging messages (a battery-efficient technique called **DRX — Discontinuous Reception**), sees the page and responds.
4. UE transitions RRC_IDLE → RRC_CONNECTED with whichever eNodeB it's actually camped on, and the data/call can now flow.

This Tracking-Area-based approach is a deliberate efficiency tradeoff: broadcasting a page to a handful of cells in one Tracking Area is far cheaper than having every idle phone constantly report its exact cell.

## 6.3 Handovers — "staying connected while moving"

There are several handover types depending on what's changing:

| Type | What's happening |
|---|---|
| **Intra-eNodeB handover** | Moving between sectors/cells of the *same* eNodeB |
| **Inter-eNodeB, via X2** | Moving to a neighboring eNodeB that has a direct X2 link (fast path) |
| **Inter-eNodeB, via S1** | Moving to an eNodeB with no direct X2 link — has to go through the MME instead (slower path) |
| **Inter-RAT handover** | Moving between LTE and a different radio access tech entirely (e.g. LTE ↔ 3G, for coverage gaps) |

### X2-based handover — step by step
1. Source eNodeB continuously receives **measurement reports** from the UE (signal strength of current + neighboring cells).
2. Source eNodeB decides a handover is needed (target cell signal now beats current cell by some margin) and sends a **Handover Request** directly to the target eNodeB over X2.
3. Target eNodeB reserves resources, replies with a **Handover Request Acknowledge**.
4. Source eNodeB sends the UE a **Handover Command** (via RRC Reconfiguration) telling it to switch to the target cell.
5. UE synchronizes with the target eNodeB and sends a **Handover Confirm**.
6. **Data path switch**: the S-GW is updated (by the MME, informed by the target eNodeB) to now route downlink data to the target eNodeB instead of the source. Any data still in flight to the old eNodeB gets forwarded to the new one directly over X2 during the transition, so packets aren't lost mid-handover.
7. Source eNodeB releases its resources for that UE.

This is designed to be a **"make-before-break"** handover — the UE has already prepared the target connection before it drops the source connection, which is why LTE handovers are typically seamless enough that you don't notice them during a call or download.

## 6.4 Deep dive: Tracking Areas vs Cells vs eNodeBs

A useful mental hierarchy:
```
eNodeB → can control multiple Cells (typically 3, one per sector)
Cells → grouped into Tracking Areas (dozens of cells per TA, operator-defined)
Tracking Areas → grouped further under specific MMEs (an MME serves a "MME Pool Area")
```
This layered grouping is a recurring pattern throughout mobile network design: fine-grained detail (exact cell) is only tracked when a device is *actively* using the network (RRC_CONNECTED); coarser-grained detail (Tracking Area) is enough while idle, trading precision for signaling/battery efficiency.

---

## Quick recap
- Attach procedure: cell selection → RRC connection → authentication (EPS-AKA) → NAS security → default bearer setup → IP address assigned
- Paging uses Tracking Areas (not exact cell) to find idle UEs efficiently, working with DRX for battery savings
- X2-based handovers are "make-before-break" — target is prepared before source connection drops, with in-flight data forwarded to avoid loss
- eNodeB → Cells → Tracking Areas → MME Pool Areas is the location-tracking hierarchy

**Next →** [07 - VoLTE & Carrier Aggregation](07-volte-carrier-aggregation.md)
