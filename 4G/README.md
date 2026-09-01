# 📡 4G / LTE — Complete Learning Notes

A step-by-step, self-contained set of notes for learning 4G and LTE (Long-Term Evolution) from the ground up — starting at "what even is 4G" and ending at deep technical details (protocol stacks, frame structure, call flows, and link-layer math).

Written as a personal study repo. Each file builds on the last, so read them in order the first time through, then use them as reference afterward.

---

## 🧭 How to use this repo

Every chapter follows the same internal shape:
1. **Plain-English concept** — what it is and why it exists
2. **How it fits the bigger picture** — where it sits in the overall system
3. **Technical deep-dive** — the actual specs, values, and mechanics telecom engineers work with

So even chapter 1 ends with real depth — you don't need to wait until chapter 8 to get to the "real" material.

---

## 📚 Table of Contents

| # | File | What you'll learn |
|---|------|--------------------|
| 01 | [Introduction to 4G & LTE](01-intro-to-4g-lte.md) | What 4G/LTE is, generational history (1G→5G), LTE vs LTE-A vs WiMAX, key vocabulary |
| 02 | [Network Architecture (EPS)](02-lte-architecture.md) | E-UTRAN, EPC, eNodeB, MME, S-GW, P-GW, HSS, PCRF — the full system map |
| 03 | [Radio Interface Fundamentals](03-radio-interface-basics.md) | Frequency bands, duplexing (FDD/TDD), OFDMA vs SC-FDMA, why LTE chose them |
| 04 | [Radio Interface — Deep Dive](04-radio-interface-advanced.md) | Frame/subframe/slot structure, Resource Blocks, modulation (QPSK/16-QAM/64-QAM), MIMO |
| 05 | [Protocol Stack](05-protocol-stack.md) | PHY, MAC, RLC, PDCP, RRC layers — what each one actually does, header by header |
| 06 | [Call & Session Procedures](06-call-procedures.md) | Initial attach, bearer setup, paging, handovers (intra/inter-eNodeB, X2/S1) |
| 07 | [VoLTE & Carrier Aggregation](07-volte-carrier-aggregation.md) | Voice over LTE, IMS, LTE-Advanced, Carrier Aggregation, LTE-A Pro |
| 08 | [Key Formulas & Parameters](08-key-formulas-parameters.md) | Throughput calculations, link budget basics, latency breakdown, cheat sheet |

Diagrams referenced in the notes live in [`diagrams/`](diagrams/).

---

## 🗺️ Suggested learning path

**Day 1** — Chapters 1–2 (concepts + architecture — get the map in your head)
**Day 2** — Chapters 3–4 (radio interface — the hardest conceptual jump, take it slow)
**Day 3** — Chapter 5 (protocol stack — connects architecture to radio interface)
**Day 4** — Chapters 6–7 (procedures — how it all moves in real time)
**Day 5** — Chapter 8 (math + revision — consolidate everything as formulas/cheat sheet)

---

## ⚡ Quick vocabulary primer (before you start)

| Term | Meaning |
|------|---------|
| **4G** | The 4th generation of mobile network technology (successor to 3G) |
| **LTE** | "Long-Term Evolution" — the dominant technical standard used to deliver 4G |
| **LTE-A** | LTE-Advanced — a true "4G" standard per ITU's original requirements, LTE was a stepping stone |
| **EPS** | Evolved Packet System — the overall LTE network system (radio + core) |
| **E-UTRAN** | Evolved UMTS Terrestrial Radio Access Network — the radio (air interface) part |
| **EPC** | Evolved Packet Core — the core network part (all-IP, no circuit-switching) |
| **eNodeB / eNB** | The LTE base station (does what "cell tower" logic means to you) |
| **UE** | User Equipment — your phone/device |

Full glossary expands as you go through each chapter.

---

*Start here → [01-intro-to-4g-lte.md](01-intro-to-4g-lte.md)*
