# 08 — Key Formulas & Parameters (Cheat Sheet)

Same purpose as the LTE notes' Chapter 8 — consolidated numbers for revision and hands-on estimation, now for NR.

## 8.1 Numerology reference table

| μ | Subcarrier spacing | Slot duration | Symbols/slot | Slots/subframe | Typical band |
|---|---|---|---|---|---|
| 0 | 15 kHz | 1 ms | 14 | 1 | FR1 (LTE-equivalent) |
| 1 | 30 kHz | 0.5 ms | 14 | 2 | FR1, general purpose |
| 2 | 60 kHz | 0.25 ms | 14 | 4 | FR1/FR2 boundary |
| 3 | 120 kHz | 0.125 ms | 14 | 8 | FR2 (mmWave) |
| 4 | 240 kHz | 0.0625 ms | 14 | 16 | FR2, sync signals only |

## 8.2 Frequency range reference

| | FR1 | FR2 |
|---|---|---|
| Range | 410 MHz – 7.125 GHz | 24.25 – 71 GHz |
| Max channel bandwidth (single carrier) | 100 MHz | 400 MHz |
| Typical cell radius | Hundreds of meters – several km | Tens – few hundred meters |

## 8.3 Theoretical peak throughput — worked example

Same structure as the LTE notes' Ch. 8.1 calculation, now for a representative NR configuration:

```
Throughput = (REs per RB per slot)
           × (Number of RBs, from bandwidth + numerology)
           × (Bits per symbol, modulation)
           × (Coding rate)
           × (MIMO spatial layers)
           × (Slots per second, from numerology)
           − (Overhead: reference signals, control, CP)
```

**Worked example — 100 MHz (FR1), μ=1 (30 kHz SCS), 256-QAM, 4x4 MIMO:**

- 100 MHz at 30 kHz SCS ≈ 273 Resource Blocks (NR's RB count depends on both bandwidth *and* numerology, unlike LTE's fixed-SCS table)
- 1 RB = 12 subcarriers × 14 symbols = 168 REs per slot
- 256-QAM = 8 bits per RE
- Raw bits per slot (before overhead/coding) ≈ 273 × 168 × 8 ≈ 367,000 bits
- At μ=1, there are 2 slots per ms → 2000 slots/second
- Raw throughput per layer ≈ 367,000 × 2000 ≈ **~734 Mbps per layer**, before overhead/coding-rate deductions
- After realistic overhead: roughly **~550-600 Mbps per layer**
- With 4x4 MIMO (4 layers): **~2.2-2.4 Gbps** — in the right ballpark for commonly-quoted high-end FR1 5G peak figures

For FR2/mmWave with its much wider 400 MHz channels and higher numerology, peak figures climb into the multi-Gbps to ~20 Gbps range quoted as IMT-2020's headline target (Chapter 1.6) — but only under close-range, clean-signal, wide-bandwidth conditions.

## 8.4 Latency budget comparison

| Contributor | LTE (typical) | NR (URLLC-optimized) |
|---|---|---|
| Frame/slot alignment wait | up to ~1 ms | as low as ~0.06-0.25 ms (mini-slot, high μ) |
| HARQ round-trip (if needed) | ~8 ms | ~1-2 ms (short slot) or skipped entirely |
| Processing (UE + RAN) | a few ms | sub-ms with optimized implementations |
| Core network transit | several ms | often <1ms with edge-deployed UPF |
| **Typical one-way user-plane latency** | **~10-20 ms** | **~1-4 ms achievable, 1ms is the aggressive target** |

## 8.5 QoS: 5QI vs QCI

5G's **5QI (5G QoS Identifier)** is the evolved version of LTE's QCI (LTE notes Ch. 8.6) — same basic idea (a number mapping to a specific latency/reliability/priority profile), extended with new standardized values for URLLC-class traffic requiring very low latency and very high reliability, which didn't have a clean LTE QCI equivalent.

## 8.6 Full glossary (5G-specific terms, complementing the LTE glossary)

| Term | Meaning |
|---|---|
| 5GC | 5G Core |
| 5QI | 5G QoS Identifier (evolved QCI) |
| AMF | Access and Mobility Management Function |
| AUSF | Authentication Server Function |
| BWP | Bandwidth Part |
| CU/DU | Centralized Unit / Distributed Unit (gNB split) |
| EN-DC | E-UTRA-NR Dual Connectivity (the NSA mechanism) |
| eMBB | Enhanced Mobile Broadband |
| FR1 / FR2 | Frequency Range 1 (sub-6GHz) / Frequency Range 2 (mmWave) |
| gNB | 5G base station |
| IMT-2020 | ITU's official performance requirements defining "5G" |
| mMTC | Massive Machine-Type Communication |
| NG-RAN | Next Generation RAN (5G's radio access network) |
| NR | New Radio (the 3GPP 5G radio standard) |
| NSA / SA | Non-Standalone / Standalone (5G deployment modes) |
| NSSF | Network Slice Selection Function |
| PCF | Policy Control Function (evolved PCRF) |
| PDU Session | 5G's evolved bearer/session concept |
| QoS Flow | Core-network-level QoS unit (mapped to radio bearers via SDAP) |
| RedCap | Reduced Capability devices (Release 17, mid-tier IoT) |
| S-NSSAI | Single Network Slice Selection Assistance Information |
| SBA | Service-Based Architecture |
| SDAP | Service Data Adaptation Protocol |
| SMF | Session Management Function |
| SUCI | Subscription Concealed Identifier (encrypted identity, replaces exposed IMSI) |
| UDM | Unified Data Management (evolved HSS) |
| UPF | User Plane Function |
| URLLC | Ultra-Reliable Low-Latency Communication |
| μ (mu) | Numerology index determining subcarrier spacing |

---

## You've completed the 5G/NR core notes 🎉

Where to go from here:
- Read 3GPP TS 38.300 (NR/NG-RAN overall description — the NR equivalent of LTE's TS 36.300)
- Explore **6G research directions** if you want to keep going — many early 6G discussions (AI-native networks, further-integrated non-terrestrial/satellite coverage, sensing-communication convergence) build directly on the SBA/slicing foundations laid here
- Revisit the [LTE notes](../4G-LTE-notes/README.md) side-by-side — a lot of NR's design choices only fully make sense as "the thing they changed about LTE, and why"

**Back to start →** [README.md](README.md)
