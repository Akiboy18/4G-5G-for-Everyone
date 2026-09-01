# 04 — Radio Interface: Deep Dive

## 4.1 The frame structure — flexible, but same nested shape as LTE

NR keeps the same overall nesting pattern as LTE (LTE notes Ch. 4.1), but slot/symbol durations now scale with numerology (Chapter 3.3):

```
1 Radio Frame  = 10 ms                          (fixed, same as LTE)
   └── 10 Subframes, 1 ms each                  (fixed, same as LTE)
         └── 2^μ Slots per subframe               (scales with numerology!)
               └── 14 OFDM Symbols per slot        (fixed, always 14 in NR — normal CP)
```

The key difference from LTE: **the number of slots per subframe changes with numerology** (1 slot at μ=0, up to 8 slots at μ=3), and each slot always has 14 symbols (vs LTE's fixed 7-per-slot). This is what actually delivers NR's lower latency floor — at μ=3 (typical FR2), a slot is only 0.125ms instead of LTE's fixed 0.5ms slot.

See [`diagrams/numerology-scaling.svg`](diagrams/numerology-scaling.svg) for a visual comparison of how slot count changes across numerologies.

## 4.2 Mini-slots — going below the slot boundary

LTE's minimum scheduling granularity was the 1ms subframe (LTE notes Ch. 4.1) — you couldn't schedule anything faster than that. NR adds **mini-slots**: scheduling can start at *any* symbol boundary and use as few as 2 symbols, rather than always waiting for a full slot boundary. This is specifically aimed at URLLC (Chapter 7) — a latency-critical packet doesn't have to wait for the next full slot to start transmitting, which directly attacks one of the latency contributors identified in the LTE notes' Chapter 8 (frame/subframe alignment delay).

## 4.3 Massive MIMO — LTE's MIMO, scaled up an order of magnitude

Recall LTE-Advanced Pro flirted with 8x8 MIMO (LTE notes Ch. 7.4) as a bridge to 5G. NR standardizes **Massive MIMO** as a core feature: base stations with **64, 128, or even more antenna elements** (vs. LTE's typical 2-8), enabling:
- Far more spatial multiplexing layers (more simultaneous independent data streams — LTE notes Ch. 4.4)
- Serving many more users simultaneously on the same time/frequency resources, via fine-grained spatial separation

## 4.4 Beamforming — the technique that makes both mmWave and Massive MIMO actually work

This is genuinely new territory beyond what LTE notes covered (LTE used only limited beamforming in some optional/advanced configs).

**The core idea:** instead of a base station's antenna radiating signal roughly equally in all directions (like an omnidirectional light bulb), a large antenna array can shape its transmission into a **narrow, directional beam** aimed specifically at one device — by precisely controlling the phase/timing of the signal sent from each individual antenna element so their waves constructively interfere in the target direction and destructively cancel out elsewhere.

Two big benefits this delivers:
- **Overcomes mmWave's range problem (Chapter 3.1)** — concentrating energy into a narrow beam instead of spreading it in all directions dramatically extends usable range at FR2 frequencies, which is what makes mmWave commercially viable at all.
- **Enables Massive MIMO's capacity gains** — precisely aimed beams to different users, even ones close together, reduce interference between them, letting the network genuinely serve more simultaneous spatial streams rather than just adding antennas with no real gain.

### Beam management (deep dive)
Because beams are narrow, a moving device can walk right out of one — so NR defines an entire **beam management procedure**: the gNB periodically sweeps through a set of candidate beams (a bit like a lighthouse sweeping), the UE measures signal quality on each, reports back the best one(s), and the network can proactively switch which beam serves that device as it moves — all without a full handover (Chapter 6) in many cases, since this can happen entirely within one gNB's own beam set.

## 4.5 Modulation schemes — same menu, extended

NR reuses LTE's modulation menu (LTE notes Ch. 4.3) and extends it:

| Scheme | Bits/symbol | Note |
|---|---|---|
| QPSK | 2 | Cell edge / poor SNR |
| 16-QAM | 4 | Moderate SNR |
| 64-QAM | 6 | Good SNR |
| 256-QAM | 8 | Excellent SNR (also existed in LTE-A) |
| **1024-QAM** | **10** | NR-only, extremely high SNR, very short range — mostly relevant for fixed wireless access scenarios with a very clean, close, stable link |

## 4.6 Deep dive: Bandwidth Parts (BWP)

A genuinely new NR-only concept: a device doesn't have to monitor the *entire* channel bandwidth at all times — the network can configure it with one or more **Bandwidth Parts (BWPs)**, essentially subsets of the total carrier bandwidth. A device can be told to operate on a narrow BWP most of the time (saving battery — less radio frontend active) and switch to a wider BWP only when it actually has a lot of data to send/receive. This is a direct response to a real pain point: NR's carriers can be up to 400 MHz wide (Chapter 3.1), and constantly monitoring that entire width would be a significant battery drain for a device that's mostly idle or doing light background traffic — something LTE's much narrower max 20MHz carrier never had to worry about as acutely.

---

## Quick recap
- NR frame structure keeps LTE's 10ms/1ms nesting shape, but slot count per subframe scales with numerology (more, shorter slots at higher μ) — this is where NR's lower latency comes from
- Mini-slots let scheduling start below the slot boundary, directly serving URLLC's latency needs
- Massive MIMO (dozens-to-hundreds of antenna elements) + beamforming (precisely directed signal) work together: beamforming overcomes mmWave's range limits and unlocks Massive MIMO's real capacity gains
- Bandwidth Parts let a device monitor only the spectrum slice it currently needs, saving battery on NR's much wider carriers

**Next →** [05 - Protocol Stack](05-protocol-stack.md)
