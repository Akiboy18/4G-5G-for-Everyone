# 03 — Radio Interface Fundamentals

This is usually the hardest conceptual jump in learning LTE — take your time here. Everything in Chapter 4 builds directly on this.

## 3.1 The basic problem radio interfaces solve

A cell tower needs to talk to potentially **thousands of phones** in its coverage area, all wanting to send/receive data *at the same time*, over a *shared, limited* chunk of radio spectrum. The "radio interface" (also called "air interface") is the set of rules for how that shared spectrum gets divided up fairly and efficiently.

Every generation of mobile network is largely defined by *how it solves this specific problem*:

| Gen | Multiple access technique |
|---|---|
| 2G (GSM) | TDMA (time-division) |
| 3G (UMTS) | CDMA (code-division) |
| **4G (LTE)** | **OFDMA (downlink) / SC-FDMA (uplink)** |
| 5G (NR) | OFDMA (both directions, more flexible) |

## 3.2 Frequency bands and duplexing

LTE operates across a wide range of frequency bands (each identified by a number, e.g. "Band 3", "Band 20") — different countries/carriers use different bands, which is why phone compatibility across regions matters.

Two duplexing modes exist — "duplexing" = how uplink (phone→tower) and downlink (tower→phone) are separated so they don't interfere:

- **FDD (Frequency Division Duplex)** — uplink and downlink use *different frequencies* simultaneously. More common globally.
- **TDD (Time Division Duplex)** — uplink and downlink share the *same frequency* but take turns in time. More spectrum-efficient for asymmetric traffic (e.g. lots of downloading, little uploading) since the time split can be adjusted.

## 3.3 Why OFDMA? (the core idea)

**OFDM (Orthogonal Frequency Division Multiplexing)** is the foundational technique. Instead of sending data over one wide radio channel (which is vulnerable to interference wiping out the whole signal), OFDM splits the channel into **many narrow subcarriers** (thousands of them) sent in parallel.

Why this is clever:
- **Resilience** — if interference or fading hits a few subcarriers, only a small fraction of the total data is affected, not everything.
- **Efficiency** — the subcarriers are mathematically "orthogonal" to each other, meaning they can be packed very tightly together without interfering, unlike older techniques that needed guard gaps between channels.
- **Flexibility** — you can allocate different subcarriers to different users at different times, which is exactly what OFDM**A** (the "Access" part) does — it's OFDM plus a scheduling scheme for sharing those subcarriers among many users.

**OFDMA is used only on the downlink** (tower → phone).

## 3.4 Why not OFDMA on the uplink too? → SC-FDMA

You'd think the uplink would just use OFDMA too — and technically it could — but there's a practical problem: **OFDM signals have a high peak-to-average power ratio (PAPR)**. That's fine for a cell tower (which has a wall-plug power supply and can afford inefficient, power-hungry amplifiers), but it's bad for a battery-powered phone.

So the uplink uses **SC-FDMA (Single Carrier Frequency Division Multiple Access)** instead — a modified version that keeps a lower PAPR, which means:
- Better battery life for your phone
- Cheaper/smaller power amplifiers in devices
- Slightly less flexibility than OFDMA, but that's an acceptable tradeoff for the uplink

This asymmetry (OFDMA down, SC-FDMA up) is one of the most commonly-tested "why" questions in LTE fundamentals — the short answer is always: **battery/amplifier efficiency on the phone side.**

## 3.5 Deep dive: orthogonality, intuitively

"Orthogonal" subcarriers means that even though the subcarriers overlap in frequency (visually, their curves cross each other), the *peak* of each subcarrier lines up exactly with the *zero-crossing (null point)* of all the others. So when the receiver samples at the peak of one subcarrier, it picks up zero interference from its neighbors — even though they're packed tightly together with no wasted guard-band space.

This is achieved mathematically using the **Fast Fourier Transform (FFT)** — the receiver uses an FFT to cleanly separate all the overlapping subcarriers back into individual data streams. This is genuinely one of the most elegant ideas in modern telecom engineering, and it's *why* LTE and Wi-Fi (which also uses OFDM) can pack so much data into limited spectrum compared to older techniques.

## 3.6 Deep dive: subcarrier spacing and symbol time

LTE fixes the subcarrier spacing at **15 kHz** for the standard numerology (this becomes flexible/scalable in 5G NR, but is fixed in LTE). This spacing is directly tied to the **useful symbol duration**:

```
Symbol duration = 1 / subcarrier spacing = 1 / 15,000 Hz ≈ 66.7 microseconds
```

A **Cyclic Prefix (CP)** is added on top of that symbol duration to combat multipath interference (signals arriving at slightly different times after bouncing off buildings, etc.). Normal CP adds about 4.7 μs, giving a total OFDM symbol duration of about **71.4 μs** in the common configuration.

---

## Quick recap
- LTE uses OFDMA on the downlink and SC-FDMA on the uplink
- The reason for the split: phone battery/amplifier efficiency (PAPR)
- OFDM's core trick: many narrow, mathematically orthogonal subcarriers packed tightly with zero interference between them, recoverable via FFT
- Subcarrier spacing is fixed at 15 kHz in LTE, giving a ~66.7 μs symbol time

**Next →** [04 - Radio Interface — Deep Dive](04-radio-interface-advanced.md)
