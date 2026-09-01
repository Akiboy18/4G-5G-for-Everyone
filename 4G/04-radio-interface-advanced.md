# 04 — Radio Interface: Deep Dive

Now we get into the actual structure telecom engineers work with day-to-day: frames, resource blocks, modulation schemes, and MIMO.

## 4.1 The frame structure hierarchy

LTE organizes time into a strict, nested hierarchy. Memorize this — it's referenced constantly:

```
1 Radio Frame  = 10 ms
   └── 10 Subframes, 1 ms each
         └── 2 Slots per subframe, 0.5 ms each
               └── 7 OFDM Symbols per slot (normal Cyclic Prefix)
                     (6 symbols if using extended CP)
```

See [`diagrams/frame-structure.svg`](diagrams/frame-structure.svg) for the visual breakdown.

- **Radio Frame** — the top-level repeating unit, 10 ms long.
- **Subframe** — 1 ms — this is the fundamental *scheduling* unit. Every time the network decides "who gets to transmit/receive now," it's making that decision per-subframe. This 1 ms subframe is also why LTE's minimum theoretical latency floor is often quoted around 1 ms at the radio layer (though real end-to-end latency is higher once you add processing and core network hops).
- **Slot** — 0.5 ms, contains 7 (or 6) OFDM symbols.

Two frame structure *types* exist matching the duplex modes from Chapter 3:
- **Type 1** — for FDD
- **Type 2** — for TDD (has special subframes for the uplink/downlink switch point)

## 4.2 Resource Blocks (RB) — the actual "unit of allocation"

The network doesn't hand out individual subcarriers to individual users — that would be too fine-grained to manage efficiently. Instead, it allocates in chunks called **Resource Blocks**:

```
1 Resource Block = 12 subcarriers (15 kHz each) × 1 slot (0.5 ms)
                  = 180 kHz × 0.5 ms
```

The smallest addressable unit within scheduling is actually the **Resource Element (RE)** — a single subcarrier for a single OFDM symbol (1 RE = 1 subcarrier × 1 symbol). A Resource Block normally contains 12 × 7 = 84 Resource Elements (with normal CP).

### Channel bandwidth → number of Resource Blocks

| Channel bandwidth | Number of RBs |
|---|---|
| 1.4 MHz | 6 |
| 3 MHz | 15 |
| 5 MHz | 25 |
| 10 MHz | 50 |
| 15 MHz | 75 |
| 20 MHz | 100 |

This scalability (1.4 MHz all the way to 20 MHz, and combinable via Carrier Aggregation in LTE-A — Chapter 7) is one of LTE's headline design features, letting operators fit LTE into whatever spectrum chunk they happen to have available.

## 4.3 Modulation schemes — how many bits per symbol

Modulation determines how much data can be packed into each Resource Element. LTE adaptively picks the modulation scheme per-user, per-moment, based on radio conditions — this is called **AMC (Adaptive Modulation and Coding)**.

| Scheme | Bits per symbol | Used when... |
|---|---|---|
| QPSK | 2 | Poor signal / cell edge |
| 16-QAM | 4 | Moderate signal |
| 64-QAM | 6 | Strong signal |
| 256-QAM (LTE-A) | 8 | Excellent signal, short distance |

The tradeoff is always the same shape: **higher-order modulation = more data per symbol, but requires a cleaner/stronger signal to be decoded correctly.** A phone at the edge of a cell (weak signal) gets dropped down to QPSK; a phone standing right under the tower with a clean line of sight might get 64-QAM or 256-QAM.

## 4.4 MIMO — Multiple Input, Multiple Output

MIMO uses multiple antennas at both the transmitter and receiver to send **multiple independent data streams simultaneously over the same frequency**, exploiting the fact that radio signals bounce and arrive via different physical paths (multipath).

Two different uses of multiple antennas, often confused:

- **Spatial diversity** — using multiple antennas to make the *same* signal more reliable (redundancy against fading). Improves range/reliability, not raw throughput.
- **Spatial multiplexing** — using multiple antennas to send *different* data streams in parallel, directly multiplying throughput. This is what people usually mean by "MIMO" in marketing (e.g. "4x4 MIMO").

Common LTE MIMO configurations:
- **2x2 MIMO** — baseline for most LTE deployments (2 transmit, 2 receive antennas) — up to 2x throughput multiplier
- **4x4 MIMO** — higher-end deployments — up to 4x throughput multiplier
- **8x8 MIMO / Massive MIMO** — LTE-Advanced Pro, bridges toward 5G-style massive MIMO

## 4.5 Deep dive: reference signals and channel estimation

For a receiver to correctly decode which modulation/MIMO layer was used, it needs to know the current radio channel conditions moment to moment. LTE embeds known **Reference Signals (RS)** (also called pilot signals) at fixed positions within the Resource Grid. The receiver compares the *known* reference signal pattern against what it *actually received* to estimate how the channel distorted the signal — then uses that estimate to correctly decode everything else, and to compute a **CQI (Channel Quality Indicator)** it reports back to the network, which is what actually drives the AMC decision described in 4.3.

## 4.6 Deep dive: putting it together — the Resource Grid

Visualize the full allocation for one subframe as a 2D grid:
- **X-axis** = time (OFDM symbols, 14 of them per subframe with normal CP)
- **Y-axis** = frequency (subcarriers, however many fit the channel bandwidth)

Each cell in this grid is a Resource Element. The scheduler's job, every 1 ms, is to decide which Resource Blocks (12-subcarrier-wide columns) go to which users, using which modulation scheme, informed by each user's most recently reported CQI. This entire grid — RE → RB → slot → subframe → frame — is the physical layer's "canvas," and everything in Chapter 5's protocol stack ultimately exists to get data correctly painted onto this canvas.

---

## Quick recap
- Frame (10ms) → 10 Subframes (1ms, the scheduling unit) → 2 Slots each → 7 OFDM symbols each
- Resource Block = 12 subcarriers × 1 slot; number of RBs scales with channel bandwidth (6 to 100)
- Modulation (QPSK/16-QAM/64-QAM/256-QAM) is adaptively chosen per user based on signal quality (AMC)
- MIMO multiplies throughput (spatial multiplexing) or improves reliability (spatial diversity) using multiple antennas
- Reference signals let the receiver estimate channel quality (CQI), which drives AMC decisions

**Next →** [05 - Protocol Stack](05-protocol-stack.md)
