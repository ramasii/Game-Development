# 🎮 Pair Jump — GT Jam Internal 2026

> *2D Hyper-casual Vertical Platformer — Red & Blue terjebak 1 badan, swap via swipe-down dash (Poinpy-style). Solo Jam 3 hari sampai 13 Sep 2026.*

---

## 📋 Dokumen Proyek

- [[GDD - Pair Jump]] — GDD mini v2.1 toggle bebas: drag move + wrap, swipe-down dash toggle, auto-jump, spawner solvable, arsitektur FSM + Observer + Pooling.
- [[Projects/Ideas/Pair Jump/Pair Jump|Ide Awal]] — Konsep awal dari Ideas (tap-swap → dash convert evolution).

---

## 🔗 Skill Vault yang Relevan

### 🏗️ Game Architecture
- [[Simple FSM Berbasis Enum (Game State Prototyping)]] — PlayerMode Red/Blue + MoveState Normal/Dashing, GameState Play/Pause/GameOver.
- [[Centralized State Manager (GameManager Singleton & Event)]] — GameManager SSOT untuk height, best, state.
- [[Observer Pattern Events]] — OnModeChanged, OnDashStart/End decoupling Player ↔ Platform ↔ Visuals.
- [[Single Source of Truth (SSOT)]] — Height/best hanya di GameManager, save PlayerPrefs.

### 🎮 Game Design
- [[Framework Kihon-Kata-Kumite (Learning Curve & Encounter Design)]] — FTUE 0-30 hijau, 30-80 kenal ghost, 80-130 tutorial dash, 130+ berantai.
- [[Deconstruct Mechanics]] — Bedah Poinpy: hold-drag aim vs flick-down dash.

### 🏛️ Level & Spatial Design
- [[Tutorial Level Building Blocks]] — FTUE contextual hint + safe sandbox, ghost 25%.
- [[Level Design Workflow (Whiteblocking & Modular)]] — Whiteblock kotak rounded sesuai mockup sebelum art pass.

---

## 📊 Status Proyek

| Info | Detail |
|------|--------|
| **Nama** | Pair Jump |
| **Event** | Gamejam Internal GT 2026 — Tema Unexpected Pair |
| **Genre** | 2D Hyper-casual Vertical Platformer (Score-attack) |
| **Platform** | Mobile Android Portrait (primary), PC build submit |
| **Engine** | Unity 6 |
| **Tim** | Solo Dev (Rama - Programmer) |
| **Status** | 🔧 Aktif — Jam Day 1-3 |
| **Kontrol Locked** | Drag = gerak + wrap, Swipe-down = dash toggle, Button = pause |

### 🚦 Progress Jam

- [x] Ide + mockup + GDD mini v2.1 (toggle bebas, bukan ikut warna platform)
- [ ] Day 1 (11 Sep): Prototype move + auto-jump + wrap + dash toggle + spawner debug
- [ ] Day 2 (12 Sep): Core loop + MainMenu/Pause/GameOver + spawner solvable + build HP
- [ ] Day 3 (13 Sep): Visual palette + SFX FL Studio + final build + submit
