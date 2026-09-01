# 02 — Network Architecture (5GC)

## 2.1 The big picture

Same two-halves pattern as LTE's EPS, with new names and a fundamentally different internal design for the core:

```
        NG-RAN                            5GC
   (Radio Access Network)     +      (5G Core Network)
   "gNBs — the radio side"    +      "cloud-native, service-based core"
```

See [`diagrams/5gc-architecture.svg`](diagrams/5gc-architecture.svg) for the visual map.

- **NG-RAN** = the radio side, made of **gNBs** (5G's version of LTE's eNodeB). It connects to the 5GC over the **NG interface** (5G's version of LTE's S1).
- **5GC** = the core side — and this is where the *real* architectural break from LTE happens.

## 2.2 The big architectural shift: Service-Based Architecture (SBA)

LTE's EPC (from the LTE notes, Chapter 2) was built from **discrete network nodes with fixed point-to-point interfaces** (MME talks to HSS over S6a, P-GW talks to PCRF over Gx, etc.) — essentially specialized hardware boxes wired together in a fixed topology.

5GC abandons that entirely in favor of **Service-Based Architecture (SBA)**: every core network function is a **software service** that exposes and consumes APIs over a common bus, much like microservices in modern cloud software architecture (because that's explicitly the inspiration). Any network function can call any other network function's API as needed, rather than being restricted to a handful of pre-wired point-to-point links.

Why this matters practically:
- **Faster feature rollout** — adding new capability is closer to a software update than physical box replacement
- **Independent scaling** — if a carrier needs way more authentication capacity but not more session-management capacity, they can scale just that one service
- **This is what makes network slicing (Chapter 7) actually feasible** — slices are essentially different combinations/configurations of these software services spun up for different purposes

## 2.3 gNB — the 5G base station

Functionally parallel to LTE's eNodeB, with a key structural difference: a gNB can be split into two logical parts:
- **CU (Centralized Unit)** — handles higher-layer protocols (RRC, PDCP — Chapter 5), can be centralized and shared across multiple radio sites
- **DU (Distributed Unit)** — handles lower-layer, latency-sensitive protocols (RLC, MAC, PHY), must stay physically close to the antenna

This CU/DU split (optional, not mandatory) lets operators centralize processing to save cost/enable coordination between cells, while keeping the truly latency-critical processing physically close to the radio — a flexibility LTE's monolithic eNodeB didn't offer.

## 2.4 5GC's core functions — the "who answers what question" table

Mirroring the LTE notes' approach — same questions, new answering nodes:

| Function | Full name | The question it answers | LTE equivalent |
|---|---|---|---|
| **AMF** | Access and Mobility Management Function | "Who is this device, and where is it?" | MME (mobility/signaling part) |
| **SMF** | Session Management Function | "What sessions/data connections does this device have?" | MME (session part) + P-GW (control part) |
| **UPF** | User Plane Function | "Where does the actual data flow?" | S-GW + P-GW (data part) |
| **UDM** | Unified Data Management | "Is this subscriber valid, what are they allowed?" | HSS |
| **PCF** | Policy Control Function | "What QoS/charging rules apply?" | PCRF |
| **AUSF** | Authentication Server Function | "Verify this device's credentials" | (was folded into MME/HSS interaction in LTE) |
| **NSSF** | Network Slice Selection Function | "Which network slice should this device use?" | *(new — no LTE equivalent)* |

### The key structural change: control/user plane split, taken further
LTE already separated control plane (MME) from user plane (S-GW/P-GW) — see LTE notes Ch. 2.5. 5GC pushes this further by explicitly splitting what used to be one node's combined responsibilities into **separate, independently-scalable functions**: AMF (pure mobility/access signaling) and SMF (pure session control signaling) are now distinct from each other, and both are fully separate from the UPF (all actual user data). This is formally called **CUPS (Control and User Plane Separation)** — LTE adopted a limited version of this late in its life, but it's foundational to 5GC from the start.

## 2.5 AMF (Access and Mobility Management Function) — deep dive
- Control-plane only, like MME.
- Handles: registration (5G's version of LTE's "attach"), connection/mobility management, paging.
- Terminates NAS signaling from the UE (same NAS concept as LTE — core network signaling, as opposed to radio-layer AS signaling).
- Works with AUSF for authentication and UDM for subscriber data — note these are now separate services it calls via SBA APIs, not fixed point-to-point links.

## 2.6 SMF (Session Management Function) — deep dive
- Manages **PDU Sessions** (5G's version of LTE's "bearers" concept — Chapter 6 covers this terminology shift in detail).
- Allocates UE IP addresses.
- Selects and controls the appropriate UPF(s) for a given session — critically, SMF can select UPFs that are **physically close to the user** (edge computing) to minimize latency, something LTE's more centralized P-GW placement didn't easily support.

## 2.7 UPF (User Plane Function) — deep dive
- The actual data-forwarding function — this is where LTE's S-GW and P-GW's user-plane responsibilities essentially merged and became independently deployable.
- Because UPFs are lightweight, software-defined, and can be deployed close to the network edge (even inside a factory or stadium, in extreme cases), this is a major enabler of **URLLC** (Chapter 7) — less physical distance for data to travel directly reduces latency.

## 2.8 Deep dive: the "N" interfaces

5GC's SBA is often drawn as a "bus" that services attach to, but point-to-point reference interfaces still exist for documentation clarity:

| Interface | Connects | Purpose |
|---|---|---|
| N1 | UE ↔ AMF | NAS signaling |
| N2 | gNB ↔ AMF | Control-plane (RAN ↔ core) |
| N3 | gNB ↔ UPF | User-plane data |
| N4 | SMF ↔ UPF | UPF control (SMF tells UPF how to forward traffic) |
| N6 | UPF ↔ external data network (internet) | Data exit point (parallel to LTE's SGi) |
| N8, N10, N11, N12... | Various inter-function SBA calls | Authentication, policy, session mgmt coordination |

---

## Quick recap
- 5GC = Service-Based Architecture — network functions as software services calling each other's APIs, not fixed hardware wiring (this is *the* defining shift from LTE's EPC)
- gNB can optionally split into CU (centralized, higher layers) and DU (distributed, latency-critical lower layers)
- AMF (mobility/access) and SMF (session control) split what MME did in LTE; UPF absorbs S-GW+P-GW's user-plane role and can be pushed to the network edge
- UPF's edge-deployability is a direct enabler of URLLC's low-latency goals

**Next →** [03 - Radio Interface Fundamentals](03-radio-interface-basics.md)
