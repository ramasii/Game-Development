# 🎮 IFEST 2026 — Dukun Chain Reaction

> *Gamejam IFEST 2026 (Tema: Chain Reaction) — Player sebagai dukun urban yang meracik ramuan sesajen secara berurutan untuk memenuhi pesanan customer. Tiap bahan memicu reaksi berantai, salah urutan = gagal.*

---

## 📋 Dokumen Proyek

- [[GDD - Dukun Chain Reaction]] — Game Design Document lengkap: konsep, core loop, mekanik, arsitektur, FTUE, scope & feasibility.
- [[Roadmap]] — Timeline gamejam, task breakdown & Go/No-Go gate.
- [[Moodboard - Research|Moodboard]] — Vibes Nusantara Modern Urban x Misterius Fun Mistis.

---

## 🔗 Skill Vault yang Relevan

### 🎮 Game Design
- [[Player Persona & Motivasi (Quantic Foundry)]] — Fokus persona: Achiever & Explorer.
- [[Roguelite Design Pillars (4 Pilar Utama)]] — Referensi untuk progression & mastery loop (adaptasi ke jam).

### 📊 Economy & Balancing
- [[Economy & Balancing]] — Balancing loop Money -> Shop -> Bahan untuk gamejam economy.

### 💻 Game Architecture
- [[Single Source of Truth (SSOT)]] — Recipe & Item sebagai ScriptableObject SSOT.
- [[Observer Pattern Events]] — Event Channel untuk Order -> Combine -> Output decoupling.
- [[Simple FSM Berbasis Enum (Game State Prototyping)]] — FSM untuk Shop / Ritual / Deliver phase.

---

## 📊 Status Proyek

| Info | Detail |
|------|--------|
| **Nama** | Dukun Chain Reaction |
| **Event** | IFEST 2026 Game Jam |
| **Tema** | Chain Reaction |
| **Genre** | Crafting Puzzle / Shop Management |
| **Platform** | PC (WebGL optional) |
| **Engine** | Unity 6 |
| **Tim** | Team IFEST |
| **Status** | 🔧 Aktif — Game Jam (On Going) |

### 🚦 Progress

- [x] Ide & Core Loop (Combine urut -> Order -> Output -> Shop)
- [x] Moodboard (Nusantara Modern Urban, Kramat Mantra Dukun Sesajen Ramuan)
- [x] GDD dibuat
- [x] Roadmap dibuat
- [ ] Prototype Combine 3-slot + validasi urutan
- [ ] Implementasi Order System + Customer
- [ ] Ekonomi Shop + Grimoire (Achiever/Explorer)
- [ ] Polish VFX Chain Reaction + Audio
- [ ] Build & Submit IFEST

> Proyek aktif sebelumnya: [[Rama's TD]] (⏸️ Paused selama IFEST)
