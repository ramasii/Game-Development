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

###### DAY 1 — 11 Sep: Prototype mentah (yang penting bisa dimainin)
Fokus: input + fisika, no menu, no cantik.
- [x] Project setup: Unity 6 2D, portrait 1080x1920, 60fps lock
- [x] Player placeholder (kotak/bulat): auto-jump fixed hang 0.85s, drag relatif 1:1.2 + wrap kanan-kiri
- [x] Dash: swipe-down (>60px, <0.3s) → gravity x3.5 + trail debug. Normal = ghost tembus, dash = semua solid
- [x] Toggle: dash sentuh platform apa pun → Red <-> Blue + bounce. Cooldown 0.15s
- [x] Spawner kotak debug + kamera naik only + death buffer 2.5m
- Done kalau: bisa loop 2 menit lompat → drag → dash → toggle → naik tanpa null

###### DAY 2 — 12 Sep: Core Loop + Main Menu jadi game utuh
- [ ] Game states: MainMenu → Play → Pause → GameOver → Retry 1 tap (GameManager singleton + best PlayerPrefs)
- [ ] Main Menu: Judul Pair Jump, tombol Play gede, best height, mute toggle, cara main 3 ikon (geser / swipe bawah / hindari jatuh)
- [ ] Spawner solvable: 0-30m hijau doang, 30-80 Red+Hijau, 80-130 tutorial dash, 130+ campur wajib toggle. Ghost lawan 25%
- [ ] FTUE hint in-world: GESER, SWIPE BAWAH, panah. Gagal FTUE respawn di platform terakhir
- [ ] Score height (m) + best + toggle streak. Pause tombol kanan-atas
- [ ] Build Android pertama, tes HP asli: misinput drag vs swipe-down, multi-touch (drag 1 jari + dash jari lain)
- Cut kalau mepet: streak counter, animasi menu

###### DAY 3 — 13 Sep pagi: Visual + Sound + Submit
**Visual Asset (2D shape, kunci palette):**
- [ ] Palette: Red #FF6B6B, Blue #4D96FF, Neutral #7BF59B, BG #1A1C2C. Jangan tambah warna lain
- [ ] Player bulat + mata, squash-stretch jump/land/dash, flash putih 0.1s pas toggle
- [ ] Platform rounded rect solid vs ghost dashed transparan. Trail dash + partikel landing 6 kotak
- [ ] UI: Height gede atas, tombol pause 64px, GameOver 1 tombol retry

**Sound Asset (FL Studio, 1 jam):**
- [ ] SFX wajib 5: jump (blip naik), dash (whoosh turun), toggle (pop 2 nada), land (thud pendek), UI click + gameover turun
- [ ] BGM loop 8-bar chiptune 140BPM bass + hat, export ogg 30 detik loop, -12dB
- [ ] Masuk Unity via AudioSource pool

**Submit siang:**
- [ ] Icon + nama, portrait lock, tes airplane mode, build AAB/APK + PC zip cadangan
- [ ] Rekam 30 detik gameplay buat halaman jam

Prioritas potong: BGM > partikel > streak > skin. Jangan potong buffer kamera + tutorial dash.
