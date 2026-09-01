# 01 — Introduction to 4G & LTE

## 1.1 What is 4G?

**4G** stands for **Fourth Generation** mobile network technology. It's not a single product — it's a *generation label* the telecom industry uses to mark a jump in capability, the same way we talk about "generations" of game consoles or iPhones.

Each generation solved a different core problem:

| Gen | Era | Core focus | Tech example |
|-----|-----|------------|---------------|
| **1G** | 1980s | Analog voice calls | AMPS |
| **2G** | 1990s | Digital voice + basic texting | GSM, CDMA |
| **3G** | 2000s | Mobile internet (slow) | UMTS, CDMA2000 |
| **4G** | 2010s | Fast mobile broadband, all-IP | **LTE**, LTE-A, WiMAX |
| **5G** | 2020s | Ultra-low latency, massive IoT, huge speed | 5G NR |

The key shift at 4G: **everything became data**. Voice calls, texts, video — all of it travels as IP packets instead of using old circuit-switched telephone technology. This is the single biggest architectural idea to hold onto as you go through these notes — it explains almost every design decision that follows.

## 1.2 What is LTE, and how does it relate to "4G"?

This trips a lot of people up, so let's be precise:

- **LTE (Long-Term Evolution)** is a *technical standard* — a specific set of engineering rules for how radios and networks talk to each other.
- **4G** is a *marketing/performance category* — defined originally by the ITU (International Telecommunication Union) as requiring certain minimum speeds (1 Gbps stationary / 100 Mbps mobile, under the "IMT-Advanced" spec).

Here's the twist: **the first version of LTE didn't actually hit the ITU's official 4G speed requirements.** Carriers marketed it as "4G LTE" anyway, because it was a massive leap over 3G. The version that *does* meet the strict definition is **LTE-Advanced (LTE-A)**. So in casual/marketing use, "4G" and "LTE" are treated as interchangeable — but technically:

```
4G (category)
 ├── LTE            → marketed as "4G", technically closer to "3.9G"
 ├── LTE-Advanced    → meets true ITU 4G requirements ("4G+")
 ├── LTE-A Pro        → further evolution, bridges toward 5G
 └── WiMAX (mobile)  → a competing standard, mostly lost the market to LTE
```

LTE won the standards war against WiMAX largely because it had backward-compatible upgrade paths from GSM/UMTS, which the vast majority of the world's carriers were already running.

## 1.3 Why 4G/LTE was built the way it was

3G networks (UMTS/CDMA2000) inherited a lot of baggage from the voice-call era: separate circuit-switched paths for calls, packet-switched paths for data, and complex interworking between them. As data usage exploded (smartphones, apps, video), this dual system became inefficient and hard to scale.

LTE's designers made a clean-slate decision:

- **All-IP core network** — no legacy circuit switching at all.
- **Flat architecture** — fewer network nodes between your phone and the internet, which cuts latency.
- **New radio technology** (OFDMA/SC-FDMA — covered in Chapter 3) — far more spectrally efficient than 3G's CDMA-based approach.
- **Voice becomes just another data app** — this is why VoLTE (Voice over LTE) had to be invented (Chapter 7); LTE has no native circuit-switched voice channel at all.

## 1.4 Key organizations behind the standard

You'll see these names throughout the specs:

- **3GPP** (3rd Generation Partnership Project) — the standards body that actually writes the LTE specifications (the "Releases" — Release 8 is baseline LTE, Release 10 is LTE-Advanced, etc.)
- **ITU** — sets the high-level performance bar that defines what counts as "4G"
- **GSMA** — industry trade group representing mobile operators globally

## 1.5 LTE Releases — the version history

| 3GPP Release | Year | What it added |
|---|---|---|
| Release 8 | 2008 | Baseline LTE |
| Release 9 | 2009 | Minor enhancements, positioning services |
| Release 10 | 2011 | **LTE-Advanced** — Carrier Aggregation, higher-order MIMO |
| Release 11–12 | 2012–2014 | CoMP, small cells, D2D |
| Release 13+ | 2015+ | **LTE-Advanced Pro** — massive MIMO, NB-IoT, LTE for IoT |

## 1.6 Deep dive: how "generation" is technically defined

The ITU's **IMT-Advanced** requirements (the technical bar for "true 4G") specify, among other things:

- Peak data rate: **1 Gbps** for low-mobility/stationary use, **100 Mbps** for high-mobility use
- Scalable bandwidth up to **40 MHz** (later extended)
- Spectral efficiency targets (bits/s/Hz) far above 3G
- All-IP packet-switched network with support for seamless handover across heterogeneous networks

Base LTE (Release 8) tops out around **300 Mbps downlink / 75 Mbps uplink** theoretically, under ideal 20 MHz spectrum and MIMO conditions — which is why it didn't technically qualify. LTE-Advanced achieves the Gbps figures through **Carrier Aggregation** (combining multiple frequency channels together, Chapter 7) and higher-order MIMO.

---

## Quick recap
- 4G = a performance category; LTE = the standard most networks use to deliver it
- The big architectural idea: **everything is IP data now**, including voice
- LTE-Advanced is the version that actually meets strict "4G" requirements
- 3GPP Releases are how you track LTE's version history

**Next →** [02 - Network Architecture (EPS)](02-lte-architecture.md)
