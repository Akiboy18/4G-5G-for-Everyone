# 06 — Call & Session Procedures

## 6.1 Registration — 5G's version of LTE's "Attach"

Same overall purpose and shape as LTE's Attach Procedure (LTE notes Ch. 6.1), renamed and adjusted for 5GC's new node names:

1. **Cell selection** — UE scans, picks a gNB, same idea as LTE.
2. **RRC Connection Establishment** with the gNB.
3. **Registration Request** sent to the **AMF** (parallel to LTE's Attach Request → MME).
4. **Authentication** — AMF works with **AUSF** and **UDM** (parallel to MME/HSS in LTE) using an evolved version of LTE's AKA procedure called **5G-AKA** (or the alternative **EAP-AKA'** method) — conceptually the same challenge-response idea (LTE notes Ch. 6.1 deep dive), extended with additional protection specifically against a known class of attacks that could track a device's identity across cells (**SUCI — Subscription Concealed Identifier** — the device's permanent identity is now encrypted before ever being sent over the air, unlike LTE's IMSI, which had edge cases where it could be exposed in plaintext).
5. **Security setup** — NAS security context established between UE and AMF.
6. **Registration Accept** — UE is now registered.

Note: registration establishes the device's presence on the network, but — unlike LTE's Attach, which directly set up a default bearer and IP address as part of the same flow — 5G **separates registration from actually getting a data connection**. That's a distinct next step:

## 6.2 PDU Session Establishment — 5G's version of LTE's "Bearer Setup"

Where LTE bundled "join the network" and "get a default bearer/IP address" into one Attach flow, NR explicitly splits them: after registering, the UE separately requests a **PDU Session** (Protocol Data Unit Session) — this is 5G's evolved concept of what LTE called a bearer/EPS session.

1. UE sends a **PDU Session Establishment Request** to the AMF, which forwards it to the **SMF**.
2. SMF selects an appropriate **UPF** (potentially one physically close to the UE, for low latency — see LTE-vs-5G architecture notes, Ch. 2.6).
3. SMF coordinates with **PCF** to determine the right QoS Flow rules (Chapter 5.7) for this session.
4. UE gets an IP address and the QoS Flow(s) it needs are established, mapped down to actual radio bearers via SDAP (Chapter 5.2).

**Why the split matters:** a single UE can have *multiple* PDU Sessions simultaneously — for example, one PDU session connected to a regular internet-facing slice, and a separate PDU session connected to a private enterprise network slice (Chapter 7) — all while remaining registered to the network just once. LTE's tighter bearer-to-attach coupling made this kind of flexible, per-purpose session separation much more awkward.

## 6.3 Dual Connectivity — how NSA mode (from Chapter 1) actually works

Recall from Chapter 1.4: **NSA** deployments run 5G radio on top of an existing LTE core. The mechanism that makes this work is called **EN-DC (E-UTRA-NR Dual Connectivity)**:

- The UE maintains a control-plane connection through an LTE eNodeB (talking to the LTE EPC's MME, exactly as described in the LTE notes) — this is the **Master Node (MN)**.
- Simultaneously, the UE also connects to a 5G gNB purely for extra user-plane data throughput — this is the **Secondary Node (SN)**.
- All signaling/mobility management still flows through the LTE side; the NR side is purely a "bonus data pipe," similar in spirit to how Carrier Aggregation (LTE notes Ch. 7.3) added extra component carriers, except here the extra carrier is an entirely different radio technology (NR) rather than another LTE carrier.

This is precisely why NSA-mode "5G" delivers faster downloads but *doesn't* deliver 5G's lower-latency or slicing benefits (Chapter 1.4) — the control-plane brain of the connection is still LTE's MME-based EPC the whole time.

In full **SA** mode, by contrast, both control and user plane go entirely through NR/gNB/5GC — no LTE dependency at all, and it's this configuration that actually engages AMF/SMF/UPF as described in Chapter 2, unlocking the full feature set.

## 6.4 Handovers — same shape as LTE, extended for beams

The overall handover shape from the LTE notes (Ch. 6.3) — measurement reports, handover request/acknowledge, make-before-break switch, data forwarding — carries over conceptually unchanged for **inter-gNB handovers** (via the **Xn** interface, NR's version of X2).

What's new: because of beamforming (Chapter 4.4), NR also has to handle **intra-cell beam switching** as a lighter-weight event that doesn't require a full handover procedure at all — the network can simply reassign which beam serves a moving UE within the same cell, using the beam management measurement/reporting process described in Chapter 4.4. This handles a category of "mobility" (moving within a beam's coverage area) that simply didn't exist as a distinct concept in LTE, where cells didn't project narrow steerable beams.

## 6.5 Deep dive: why registration/session were split — the mMTC connection

This design choice connects directly back to Chapter 1's mMTC pillar: think of a fleet of battery-powered sensors that stay **registered** to the network for years, but only occasionally need an actual active data **session** to briefly report a reading and go back to a deep sleep/idle state. Cleanly separating "the device's ongoing relationship with the network" (registration) from "an active data-carrying session" (PDU Session) makes this kind of intermittent, long-lived-but-rarely-active device behavior far more natural to support efficiently than LTE's more tightly bundled Attach-plus-bearer model was designed for.

---

## Quick recap
- Registration (AMF/AUSF/UDM, via 5G-AKA + SUCI-protected identity) replaces LTE's Attach — but no longer automatically creates a data session
- PDU Session Establishment (AMF→SMF→UPF, with PCF for QoS rules) is now a separate step, letting one device hold multiple independent sessions (e.g., across different network slices)
- NSA mode = EN-DC dual connectivity: LTE eNodeB stays the control-plane "master," gNB is a pure extra-throughput "secondary" — explains why NSA 5G is fast but not low-latency/sliced
- Inter-gNB handovers mirror LTE's make-before-break shape (via Xn); intra-cell beam switching is a new, lighter-weight mobility event unique to beamforming

**Next →** [07 - Network Slicing, URLLC & mMTC](07-slicing-urllc-mmtc.md)
