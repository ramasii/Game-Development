# 🎮 Awan Tukang Hujan

> *2D Merge-Drag Ecosystem Survival Sandbox — geser awan, merge jadi hujan, jaga tanaman di planet muter selama mungkin — **Endless, no win**.*
> Update: satu planet memiliki 80 slot (64 soil + 16 water).

---

## 📋 Dokumen Proyek

- [[GDD - Awan Tukang Hujan]] — GDD lengkap: konsep, core loop endless, mekanik 80 slot Opsi A, arsitektur, FTUE tanpa teks, folder modular, scope.
- [[Planning - Awan Tukang Hujan]] — Planning eksekusi terpisah: flow MainMenu → InGame → Pause → GameOver, scene breakdown 80 slot, task 2 minggu, balancing anchors, Go/No-Go.
- [[Prompts - Eksekusi]] — 8 prompt AI siap copy (P0–P7) ngunci GDD + wajib MCP Unity.

Sumber konsep: [[Temporary 2]] (arsip brainstorming + sketsa King).

---

## 🔗 Skill Vault yang Relevan

### 🏗️ Game Architecture
- [[Single Source of Truth (SSOT)]] — BalanceConfigSO untuk semua timer + counts (64/16/32).
- [[Centralized State Manager (GameManager Singleton & Event)]] — GameState: Boot/MainMenu/Playing/Paused/GameOver.
- [[Simple FSM Berbasis Enum (Game State Prototyping)]] — FSM ringan untuk prototype flow.
- [[State Pattern (Unity FSM)]] — Cloud / Soil x64 / Plant state machine.
- [[Observer Pattern Events]] — EventChannel decoupled.
- [[Factory Pattern (Unity)]] + [[Object Pool Pattern (Unity)]] — spawn awan/hujan/uap (wajib untuk 80 slot).
- [[MVP Pattern (Unity UI)]] — UI ikon-only, HUD cuma Pause.
- [[Dirty Flag Pattern (Unity)]] + [[Flyweight Pattern (Unity Shared Data)]] — hemat update 64 soil.
- [[Decoupled Audio System (Event Channel & Pooling)]] — SFX + adaptive layer.

### 🎮 System & Design
- [[Identify Core Loops]] — loop evaporasi > merge > hujan > bertahan.
- [[Designing FTUE (First-Time User Experience)]] — FTUE 5 langkah visual tanpa angka/huruf.
- [[Tutorial Level Building Blocks]] — layout planet 80 slot + onboarding spasial.
- [[Framework Kihon-Kata-Kumite (Learning Curve & Encounter Design)]] — Kihon merge, Kata hujan tepat, Kumite rotasi multi-lahan.

### 🏛️ Level & Balancing
- [[Level Design Workflow (Whiteblocking & Modular)]] — whitebox 80 slot (64 soil + 16 water) + tanaman initial 32.
- [[Apply Balance Foundations]] + [[Map Resource Flows]] + [[Establish Math Anchors]] + [[Spreadsheet Setup]] — timer survival endless + rasio air:tanah 16:64.

---

## 📊 Status Proyek

| Info | Detail |
|---|---|
| **Nama** | Awan Tukang Hujan |
| **Genre** | 2D Merge-Drag / Ecosystem Survival, Casual Edukasi (Fun 80% Edu 20%) |
| **Platform** | Mobile Landscape, 2D, 1 jari |
| **Engine** | Unity 6 |
| **Tim** | Solo Dev (Rama - Programmer) |
| **Status** | 🔧 Konsep Locked / Pre-Production |
| **Aturan** | No angka & huruf di gameplay, no chocolate-covered broccoli, no win, lose = tanaman habis, planet 80 slot |
| **Planet** | 80 slot (64 soil + 16 water), tanaman initial 32 maks 64 |

### 🚦 Phase Progress

- [x] Konsep + sketsa siklus + planet melingkar 80 slot
- [x] GDD dibuat (80 slot)
- [x] Planning dibuat (80 slot)
- [x] Prompts eksekusi dibuat (P0–P7)
- [ ] **Phase 0** — Core + Planet 80 slot whitebox + PlantManager AliveTracker
- [ ] **Phase 1** — Prototype merge-hujan-muter + UI flow + Go/No-Go endless (>3 menit bertahan)
