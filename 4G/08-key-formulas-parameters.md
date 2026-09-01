# 08 — Key Formulas & Parameters (Cheat Sheet)

This chapter consolidates the numbers from Chapters 1-7 into working formulas and a single reference sheet. Useful for revision, or if you're doing hands-on link-budget/throughput estimation work.

## 8.1 Theoretical peak throughput — the full calculation

The classic "how fast is LTE, really" calculation, built up from Chapter 4's Resource Block math:

```
Throughput = (Resource Elements per RB per ms)
           × (Number of RBs, from channel bandwidth)
           × (Bits per symbol, from modulation scheme)
           × (Coding rate)
           × (Number of MIMO spatial layers)
           × (1000 ms/s)
           − (Overhead: reference signals, control channels, CP)
```

**Worked example — 20 MHz, 64-QAM, 2x2 MIMO (a fairly typical "good conditions" real-world scenario):**

- 20 MHz → 100 Resource Blocks (Chapter 4, section 4.2)
- 1 RB = 12 subcarriers × 7 symbols = 84 Resource Elements per slot → 168 REs per 1ms subframe (2 slots)
- 64-QAM = 6 bits per RE (ignoring coding rate for a moment)
- Raw bits per subframe (before overhead/coding) = 100 RBs × 168 REs × 6 bits ≈ 100,800 bits per 1 ms
- That's **~100.8 Mbps raw**, per spatial layer, before accounting for ~25-30% overhead (reference signals, control region, CRC, coding redundancy) and before multiplying by MIMO layers
- After realistic overhead/coding-rate deductions: roughly **~75 Mbps per layer**
- With 2x2 MIMO (2 layers): **~150 Mbps** — this lines up with the commonly quoted "single 20MHz LTE carrier, 2x2 MIMO" real-world peak figure

This is why marketing figures like "300 Mbps LTE" specifically assume 4x4 MIMO and/or Carrier Aggregation stacking multiple 20MHz carriers — always check what config a quoted peak speed assumes.

## 8.2 Latency breakdown

| Contributor | Typical value |
|---|---|
| Radio frame/subframe alignment | up to ~1 ms (waiting for next subframe boundary) |
| HARQ round-trip (if retransmission needed) | ~8 ms per retry |
| Processing delay (UE + eNodeB) | a few ms |
| Core network (S-GW/P-GW) transit | varies, often several ms |
| **Typical one-way "user-plane" latency, good conditions** | **~10-20 ms** |

(5G NR's headline "1 ms latency" claims come from further shrinking subframe duration and processing time beyond what LTE's fixed 1ms subframe allows — useful context for why 5G needed a new radio design rather than just extending LTE.)

## 8.3 Frequency/time reference sheet

| Parameter | Value |
|---|---|
| Subcarrier spacing | 15 kHz |
| OFDM symbol duration (useful part) | ~66.7 μs |
| OFDM symbol + normal CP | ~71.4 μs |
| Slot duration | 0.5 ms |
| Subframe duration | 1 ms |
| Radio frame duration | 10 ms |
| Symbols per slot (normal CP) | 7 |
| Symbols per slot (extended CP) | 6 |
| Subcarriers per Resource Block | 12 |
| Resource Elements per RB (normal CP) | 84 |

## 8.4 Bandwidth → Resource Blocks reference

| Bandwidth | Resource Blocks | Subcarriers used |
|---|---|---|
| 1.4 MHz | 6 | 72 |
| 3 MHz | 15 | 180 |
| 5 MHz | 25 | 300 |
| 10 MHz | 50 | 600 |
| 15 MHz | 75 | 900 |
| 20 MHz | 100 | 1200 |

## 8.5 Modulation reference

| Scheme | Bits/symbol | Typical use case |
|---|---|---|
| QPSK | 2 | Cell edge / poor SNR |
| 16-QAM | 4 | Moderate SNR |
| 64-QAM | 6 | Good SNR |
| 256-QAM | 8 | Excellent SNR, LTE-A |

## 8.6 QCI reference (common values)

| QCI | Traffic type | Priority | Bearer type |
|---|---|---|---|
| 1 | VoLTE (conversational voice) | Highest | Guaranteed bit rate |
| 2 | Conversational video | High | Guaranteed bit rate |
| 5 | IMS signaling | High | Non-GBR |
| 8/9 | Web browsing, general data | Lowest (default) | Non-GBR (best effort) |

## 8.7 Full glossary (all chapters combined)

| Term | Meaning |
|---|---|
| AMC | Adaptive Modulation and Coding |
| AKA | Authentication and Key Agreement |
| CA | Carrier Aggregation |
| CQI | Channel Quality Indicator |
| CSFB | Circuit-Switched Fallback |
| eNodeB / eNB | LTE base station |
| EPC | Evolved Packet Core |
| EPS | Evolved Packet System |
| E-UTRAN | Evolved UMTS Terrestrial Radio Access Network |
| FDD | Frequency Division Duplex |
| HARQ | Hybrid Automatic Repeat reQuest |
| HSS | Home Subscriber Server |
| IMS | IP Multimedia Subsystem |
| LTE | Long-Term Evolution |
| LTE-A | LTE-Advanced |
| MIMO | Multiple Input, Multiple Output |
| MME | Mobility Management Entity |
| NAS | Non-Access Stratum |
| OFDMA | Orthogonal Frequency Division Multiple Access |
| PAPR | Peak-to-Average Power Ratio |
| PCRF | Policy and Charging Rules Function |
| PDCP | Packet Data Convergence Protocol |
| P-GW | Packet Data Network Gateway |
| QCI | QoS Class Identifier |
| RB | Resource Block |
| RE | Resource Element |
| RLC | Radio Link Control |
| ROHC | RObust Header Compression |
| RRC | Radio Resource Control |
| S-GW | Serving Gateway |
| SC-FDMA | Single Carrier Frequency Division Multiple Access |
| SIP | Session Initiation Protocol |
| TDD | Time Division Duplex |
| UE | User Equipment |
| VoLTE | Voice over LTE |

---

## You've completed the core notes 🎉

Suggested next steps for going even deeper:
- Read the actual 3GPP specs (start with TS 36.300 — the overall E-UTRAN/EPC description)
- Try packet-level analysis with tools like Wireshark on an LTE-capable modem (with appropriate legal/consent boundaries)
- Move on to a 5G NR notes series — most of this architecture (bearers, PDCP/RLC/MAC, MIMO, OFDM concepts) carries forward directly, with the differences being the interesting part to focus on

**Back to start →** [README.md](README.md)
