# 📡 5G / NR — Complete Learning Notes

A companion series to the [4G/LTE notes](../4G-LTE-notes/README.md) — same step-by-step format, beginner-to-deep-technical within each chapter. This series assumes you either know LTE already (from the companion repo) or are comfortable picking up the parallels as you go — every chapter calls out what's new vs. what LTE already had.

---

## 🧭 How to use this repo

Same shape as the LTE notes:
1. **Plain-English concept**
2. **Where it fits in the system**
3. **Technical deep-dive**

5G is best understood as **"LTE's ideas, pushed further, plus three genuinely new capabilities"** (higher speed, lower latency, massive device density) — so expect a lot of "remember X from LTE? Here's the 5G version" callouts.

---

## 📚 Table of Contents

| # | File | What you'll learn |
|---|------|--------------------|
| 01 | [Introduction to 5G & NR](01-intro-to-5g-nr.md) | What 5G actually is, NR vs LTE, the three use-case pillars (eMBB/URLLC/mMTC), NSA vs SA |
| 02 | [Network Architecture (5GC)](02-5g-architecture.md) | Service-Based Architecture, gNB, AMF, SMF, UPF, UDM — the full system map |
| 03 | [Radio Interface Fundamentals](03-radio-interface-basics.md) | Frequency ranges (FR1/FR2), scalable numerology, mmWave vs sub-6GHz |
| 04 | [Radio Interface — Deep Dive](04-radio-interface-advanced.md) | Flexible frame structure, Massive MIMO, beamforming, modulation |
| 05 | [Protocol Stack](05-protocol-stack.md) | SDAP, PDCP, RLC, MAC, PHY — what's new vs LTE's stack |
| 06 | [Call & Session Procedures](06-call-procedures.md) | Registration, PDU sessions, handovers, dual connectivity (EN-DC) |
| 07 | [Network Slicing, URLLC & mMTC](07-slicing-urllc-mmtc.md) | Network slicing, ultra-reliable low latency, massive IoT |
| 08 | [Key Formulas & Parameters](08-key-formulas-parameters.md) | Throughput math, latency budget, numerology cheat sheet, glossary |

Diagrams live in [`diagrams/`](diagrams/).

---

## 🗺️ Suggested learning path

**Day 1** — Chapters 1–2 (concepts + architecture)
**Day 2** — Chapters 3–4 (radio interface — mmWave and beamforming take some sitting-with)
**Day 3** — Chapter 5 (protocol stack)
**Day 4** — Chapter 6 (procedures, especially NSA's dual-connectivity quirk)
**Day 5** — Chapters 7–8 (slicing/URLLC/mMTC + consolidation)

---

## ⚡ Quick vocabulary primer

| Term | Meaning |
|------|---------|
| **NR** | New Radio — the actual 5G air-interface standard (parallel to how LTE was 4G's standard) |
| **5GC** | 5G Core — the new all-cloud-native core network |
| **gNB** | The 5G base station (parallel to LTE's eNodeB) |
| **NSA** | Non-Standalone — 5G radio bolted onto an existing LTE core (the common early-rollout mode) |
| **SA** | Standalone — full 5G radio + full 5G core, no LTE dependency |
| **eMBB** | Enhanced Mobile Broadband — the "faster internet" use case |
| **URLLC** | Ultra-Reliable Low-Latency Communication — the "self-driving cars, remote surgery" use case |
| **mMTC** | Massive Machine-Type Communication — the "billions of IoT sensors" use case |

---

*Start here → [01-intro-to-5g-nr.md](01-intro-to-5g-nr.md)*
