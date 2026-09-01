# 03 — Radio Interface Fundamentals

If you've read the LTE notes' Chapter 3, the core multiple-access idea (OFDM-family techniques) will feel familiar — what's new in NR is that almost every parameter that LTE fixed is now **scalable/configurable**.

## 3.1 Frequency Ranges: FR1 and FR2

NR is defined across two very different frequency ranges — this split doesn't exist in LTE at all, and it's one of the most important practical facts about how 5G actually behaves in the real world:

| | **FR1** | **FR2 (mmWave)** |
|---|---|---|
| Frequency range | 410 MHz – 7.125 GHz | 24.25 GHz – 71 GHz |
| Nickname | "Sub-6" | "mmWave" |
| Coverage per site | Kilometers (similar to LTE) | Often just meters to a few hundred meters |
| Penetration (walls, weather) | Good | Poor — even rain/foliage can block it |
| Bandwidth available | Narrower channels (up to 100 MHz per carrier) | Very wide channels (up to 400 MHz per carrier) |
| Typical use | Broad-area "everyday 5G" coverage | Dense hotspots — stadiums, airports, urban cores |

**This explains a very common real-world confusion:** why "5G" can sometimes be barely faster than LTE (that's FR1/sub-6, prioritizing coverage) and other times absurdly fast but only in a tiny area (that's FR2/mmWave, prioritizing raw bandwidth at the cost of range). Both are legitimately "5G" — they're just serving different jobs from the eMBB pillar (Chapter 1).

## 3.2 Why mmWave exists at all

Radio spectrum below 6 GHz was already crowded — LTE, Wi-Fi, and older systems had claimed most of the useful, well-behaved spectrum. But way up at 24-71 GHz, there's enormous amounts of *unused, contiguous* spectrum available — which directly translates to available bandwidth (recall from the LTE notes: more Hz of channel width = more raw bits/second possible, all else equal). The tradeoff is physics: higher frequency waves travel shorter distances and are blocked more easily by physical obstacles. NR was designed to use FR1 and FR2 together, playing to each one's strengths — this is a core reason 5G needed beamforming (Chapter 4) far more aggressively than LTE ever did, to make FR2 viable at all.

## 3.3 Scalable Numerology — LTE's fixed 15kHz becomes flexible

Recall from the LTE notes (Ch. 3.6): LTE fixed subcarrier spacing at exactly 15 kHz, for every deployment, everywhere. NR instead defines a **numerology** system — a family of subcarrier spacings, each identified by an index **μ (mu)**, where subcarrier spacing = 15 kHz × 2^μ:

| μ | Subcarrier spacing | Slot duration | Typical use |
|---|---|---|---|
| 0 | 15 kHz | 1 ms | Matches LTE exactly — coverage-focused FR1 |
| 1 | 30 kHz | 0.5 ms | Common general-purpose FR1 |
| 2 | 60 kHz | 0.25 ms | FR1/FR2 boundary use |
| 3 | 120 kHz | 0.125 ms | FR2 (mmWave) — standard |
| 4 | 240 kHz | 0.0625 ms | FR2, sync signals |

**Why this matters:** wider subcarrier spacing → shorter symbol duration → lower latency, but requires more precise frequency synchronization and works best where wider bandwidth is available (which is exactly the situation at FR2/mmWave). Narrower subcarrier spacing (μ=0, matching LTE) is more efficient over longer distances and multipath-heavy environments (which is exactly the situation at FR1). NR lets an operator pick the right tool for each deployment instead of LTE's one-size-fits-all 15kHz.

## 3.4 OFDMA and CP-OFDM — what carries over from LTE, and what changed

- **Downlink:** NR uses **CP-OFDM** (Cyclic-Prefix OFDM) — essentially the same core idea as LTE's downlink OFDMA (LTE notes Ch. 3.3), scaled to the new numerology system above.
- **Uplink:** NR supports **both** CP-OFDM *and* LTE's SC-FDMA-style approach (called **DFT-s-OFDM** in NR terminology) — DFT-s-OFDM is used specifically when a device needs the lower PAPR/better battery efficiency (same reasoning as LTE notes Ch. 3.4, e.g. cell-edge devices), while plain CP-OFDM can be used uplink too when a device has good enough power headroom and wants OFDM's greater flexibility. This is a genuinely new option LTE never offered on the uplink.

## 3.5 Deep dive: why NR chose OFDM-family waveforms again, when researchers considered alternatives

During NR's design phase (3GPP Release 15 study period), several alternative "5G waveform" candidates were seriously evaluated — including filter-bank multicarrier and other techniques promising lower out-of-band emissions. Ultimately, **OFDM-family waveforms won** largely because of the enormous existing base of engineering knowledge, chipsets, and implementation experience already built around LTE's OFDM approach — reusing the core waveform while making *everything around it* (numerology, frame structure, channel coding) flexible was judged the more pragmatic path to actually shipping working 5G networks on a realistic timeline, rather than re-solving problems LTE had already solved well.

---

## Quick recap
- NR splits into FR1 (sub-6GHz, coverage-focused) and FR2 (mmWave, capacity-focused, short range) — real-world "5G speed" depends heavily on which one you're on
- Scalable numerology (μ = 0 to 4) replaces LTE's fixed 15kHz subcarrier spacing, trading off latency vs. range/robustness per deployment
- Downlink stays CP-OFDM (LTE's approach, generalized); uplink can use either CP-OFDM or DFT-s-OFDM (LTE's SC-FDMA equivalent), giving devices a choice LTE never had
- The core waveform family didn't change from LTE — what changed is making its parameters configurable

**Next →** [04 - Radio Interface — Deep Dive](04-radio-interface-advanced.md)
