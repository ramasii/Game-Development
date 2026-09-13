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
- [x] Game states: MainMenu → Play → Pause → GameOver → Retry 1 tap (GameManager singleton + best PlayerPrefs)
- [x] Main Menu: Judul Pair Jump, tombol Play gede, best height, mute toggle, cara main 3 ikon (geser / swipe bawah / hindari jatuh)
- [x] Spawner solvable: 0-30m hijau doang, 30-80 Red+Hijau, 80-130 tutorial dash, 130+ campur wajib toggle. Ghost lawan 25%
- [x] FTUE hint in-world: GESER, SWIPE BAWAH, panah. Gagal FTUE respawn di platform terakhir
- [x] Score height (m) + best + toggle streak. Pause tombol kanan-atas
- [ ] Build Android pertama, tes HP asli: misinput drag vs swipe-down, multi-touch (drag 1 jari + dash jari lain)
- Cut kalau mepet: streak counter, animasi menu

###### DAY 3 — 13 Sep pagi: Visual + Sound + Submit
**Visual Asset (2D shape, kunci palette):**
- [x] Palette: Red #FF6B6B, Blue #4D96FF, Neutral #7BF59B, BG #1A1C2C. Jangan tambah warna lain
- [x] Player bulat + mata, squash-stretch jump/land/dash, flash putih 0.1s pas toggle
- [x] Platform rounded rect solid vs ghost dashed transparan. Trail dash + partikel landing 6 kotak
- [x] UI: Height gede atas, tombol pause 64px, GameOver 1 tombol retry

**Sound Asset (FL Studio, 1 jam):**
- [x] SFX wajib 5: jump (blip naik), dash (whoosh turun), toggle (pop 2 nada), land (thud pendek), UI click + gameover turun
- [ ] BGM loop 8-bar chiptune 140BPM bass + hat, export ogg 30 detik loop, -12dB
- [x] Masuk Unity via AudioSource pool

**Submit siang:**
- [ ] Icon + nama, portrait lock, tes airplane mode, build AAB/APK + PC zip cadangan
- [ ] Rekam 30 detik gameplay buat halaman jam

Prioritas potong: BGM > partikel > streak > skin. Jangan potong buffer kamera + tutorial dash.

## 🐞 Bug Log — Day 1 (11 Sep 2026)

### Bug: Drag horizontal bikin bola beku di sumbu Y
**Gejala:** Bola di udara → geser kanan/kiri → bola gerak di sumbu X tapi berhenti total di sumbu Y (menggantung).
**Status:** ✅ Fixed & A/B-tested di editor (port 7893).

#### Akar masalah 1 — Rigidbody ketiduran di apex lompatan
- Gerak X tidak pernah menyentuh `velocity.x` (teleport position), jadi di titik tertinggi lompatan total velocity ≈ 0 → `Rigidbody2D` masuk sleep → gravitasi berhenti diintegrasi → Y beku, X tetap pindah via teleport.
- **Penanganan:** `rb.sleepMode = RigidbodySleepMode2D.NeverSleep` + `rb.WakeUp()` di `Awake`/`Start`/`TryLand`/`HandleSwipeDown` (`PlayerController.cs`).

#### Akar masalah 2 — MovePosition menimpa velocity.y
- Sempat diganti ke `rb.MovePosition(rb.position + (dx, 0))` demi best practice, tapi bug muncul lagi.
- Penyebab: di body Dynamic, `MovePosition` mengemudikan gerak kinematik dari delta (delta Y = 0) sehingga tiap physics step berantem dengan `velocity.y` dari gravitasi.
- **Penanganan (final):** Geser X via `rb.transform.position += (dx, 0, 0)` — tidak menyentuh velocity, sumbu Y tetap murni fisika. Aman di game ini karena collider lateral hanya trigger yang dicek manual di `TryLand`.
- **Bukti A/B:** varian MovePosition ngesot di ~1.0m & kamera stuck 2.19; varian transform manjat 7.5m+ & kamera ngikut 8.2m; console 0 error, compile 0 error.

#### Catatan lanjutan
- Jangan balik ke `MovePosition` untuk gerak X (sudah ada komentar peringatan di `FixedUpdate()`).
- Kalau drag kencang terlihat judder (teleport + interpolation), jalur yang benar: set `linearVelocity.x` langsung dan pertahankan `y` — bukan MovePosition.

## 🐞 Bug Log — Day 2 (12 Sep 2026, Blok A–F)

### 1. PlayButton mati — listener runtime tidak ikut ke-save
**Gejala:** Semua tombol UI tidak merespons klik.
**Akar:** `onClick.AddListener` yang dipasang runtime (via builder) tidak diserialisasi ke scene file. Hilang tiap load.
**Penanganan:** Wiring pindah ke `UIManager.OnEnable` (jalan tiap load/enable) + remove-then-add idempoten biar selamat dari enable-cycle dan reload domain.

### 2. GameObject.Find buta terhadap objek inactive
**Gejala:** Hanya Play/Mute yang ke-wire; 6 tombol lain (PauseButton + isi panel Pause/GameOver) skip diam-diam + warning.
**Akar:** `GameObject.Find` tidak menemukan objek inactive, padahal 6/8 tombol lahir dalam keadaan mati.
**Penanganan:** Traversal dari `OverlayCanvas` (selalu aktif) pakai `GetComponentsInChildren<Button>(true)` + switch nama.

### 3. File watcher skip import — assembly basi yang jalan
**Gejala:** File di disk baru, compile "clean", tapi console print string kode LAMA; Invoke NRE misterius beruntun.
**Akar:** Perubahan file tidak ke-import (file watcher ke-skip) → Play jalanin assembly basi. Compile check "clean" menipu karena tidak ada yang dikompilasi.
**Penanganan (aturan tetap):** Tiap habis edit → Assets/Refresh → double-clean check → baru Play. Jangan edit code saat Play nyala (recompile tengah jalan = listener/static rontok diam-diam — ini kemungkinan yang membunuh tombol saat user edit sambil main).

### 4. Bola auto-play di MainMenu
**Gejala:** Bola mental-mental sendiri di belakang menu sebelum Play ditekan.
**Akar:** `Start()` kasih velocity + fisika jalan di MainMenu (timeScale 1).
**Penanganan:** Hold total saat bukan Playing (velocity 0 + gravityScale 0). Masuk Playing: kembalikan momentum (resume) atau luncurkan ke atas (fresh/retry). Lubang lanjutan: hold velocity saja tidak cukup — gravitasi tetap narik → jatuh → respawn debug nyentil bola ke 14m. Makanya gravitasi ikut dimatikan (`PlayerController.HandleGameState`).

### 5. Legacy Input throw di project Input-System-only
**Gejala:** `InvalidOperationException` tiap frame — project pakai Active Input Handling = Input System only.
**Penanganan:** `PairJumpInput` ditulis ulang pakai `Mouse.current` (desktop/WebGL-desktop) + `Touchscreen.current` (mobile/WebGL-mobile), multi-touch per-touchId.

### 6. Font & snippet gotcha (Unity 6 + MCP execute)
- `Arial.ttf` deprecated → pakai `Resources.GetBuiltinResource<Font>("LegacyRuntime.ttf")`.
- Snippet execute tidak bawa usings → semua tipe UI harus fully-qualified (`UnityEngine.UI.Button`, dst.).
- `BuildTargetGroup` adanya di `UnityEditor`, bukan `UnityEngine`.
- `component_set_reference` tanpa `componentType` nempel ke komponen yang salah (kena DebugSpawner duluan) → selalu sebutkan tipe eksplisit.

### 7. Testing notes (bukan bug game)
- **Reload deferred 1 frame:** verifikasi Retry/Menu harus dibaca di call berikutnya, bukan call yang sama.
- **Coyote artifact:** 2x `TryLand` dalam 1ms dihitung dash (streak 2) — mustahil di gameplay nyata (bounce butuh detik). Reset 1→0 dibuktikan dengan coyote dimatikan paksa via refleksi.
- **Find null ≠ hilang:** probe hint return null karena sedang hidden sesuai desain (pelajaran #2 kepakai lagi).
- **Sesi ganda:** user ikut main di editor saat verifikasi berjalan (state ke-reset) → tes kritis dibuat atomik 1-call (setup+aksi+assert sekaligus).

### Status Blok F (tanpa build, sesuai request)
- Nama app → "Pair Jump", orientasi Portrait lock, 60fps, icon merah-biru + bola putih terpasang, threshold swipe skala DPI (`max(60, dpi×0.25)` — aktif di HP saja), SafeAreaPad di 4 elemen atas (no-op di editor).
- **Disengaja tidak disentuh:** `applicationIdentifier` (masih com.DefaultCompany… — ganti sebelum submit), build APK/AAB (nunggu lampu hijau).
- Checklist tes HP ada di laporan chat Blok F.

---
## 📈 Plan Difficulty Ramp — Late Game Pressure (Day 3, tanpa obstacle baru)

> *Status: 📝 PLAN (belum dieksekusi) — diusulkan Digidaw 12 Sep 2026, disetujui King.*
> *Motivasi: game sudah fun, tapi butuh tekanan naik di ketinggian. Ganti obstacle baru
> (art + tutorial + balance, H-1 submit = berisiko) dengan ramp angka murni memakai
> sistem yang sudah ada. FTUE 0–130m DIJAMIN tidak tersentuh.*

### Angka baseline (terakhir terpantau via MCP — konfirmasi di inspector Spawner)
| Field | Nilai |
|---|---|
| `gapMinY / gapMaxY` | 1.5 / 2.4 (scene; code default 1.8 / 2.4) |
| `maxGapX` | 2.5 |
| `greenBailoutEvery` | 5 |
| `edgeFraction / maxEdgeStreak` | 0.15 / 2 (tweak King) |
| Zona warna | <30 hijau; 30–80 hijau/merah; 80–130 tutorial deterministik; 130+ acak 35/32/33 |

### Batas fisika (jangan dilanggar)
- Lompat maks = `jumpVelocity² / 2g` ≈ 2.65m → **`gapMaxY` HARD CAP 2.55** (sisakan margin entry diagonal + snap).
- `maxGapX` tetap 2.5 di semua tier (sudah pedas + anti-run menjaga solvable).
- 0–130m (Kihon–Kata) **identik seperti sekarang**, termasuk `TutorialPattern` 12 langkah.

### Tabel tier ramp
| Tier (zona) | gapMin–Max Y | Bailout hijau | Bobot hijau 130+ | Rasa yang dikejar |
|---|---|---|---|---|
| T0: 0–130 (existing) | 1.5–2.4 | 5 | — (zona scripted) | tidak berubah |
| T1: 130–250 (Kata lanjut) | 1.6–2.45 | 6 | 35% → 30% | napas mulai pendek |
| T2: 250–400 (Kumite awal) | 1.8–2.5 | 7 | → 25% | toggle berantai wajib |
| T3: 400+ (Kumite) | 1.9–2.55 | 7 | 25%, merah/biru 37/38% | survival |

### Implementasi (estimasi ±30 menit + tes)
1. `Spawner.cs`: field `rampTier2Y = 250`, `rampTier3Y = 400` + helper
   `GapRangeFor(y)`, `BailoutFor(y)`, `GreenWeightFor(y)` (if-tier sederhana).
   `SpawnNext()` pakai gap dari tier; `PickColor()` pakai bailout & bobot tier.
2. TIDAK disentuh: `TutorialPattern`, `maxGapX`, anti-run, pool/fallback, `TopY`/snap.
3. OPSIONAL berisiko (default OFF, perlu feel-test King): `deathBuffer` 2.5 → 2.2
   di atas 300m (`CameraFollow`, 1 baris + flag inspector).
4. Rollback: nilai lama ada di tabel baseline di atas — revert manual <2 menit.

### Kriteria terima
- [ ] Sim 2000 spawn dari y=130: semua gap ≤ cap tier-nya; jarak antar-bailout sesuai.
- [ ] Manual 0–130m: rasa IDENTIK dengan build kemarin (FTUE utuh).
- [ ] Manual 250m+: jelas lebih panas tapi tidak ada tembok mustahil
      (3 gap beruntun selalu reachable dengan 1 toggle/lompatan).
- [ ] Compile 0 error, smoke Play bersih, tidak perlu ubah TDD/GDD (angka tuning saja).

### Ide obstacle post-jam (parkir, bukan scope jam)
1. Crumbling platform (pecah setelah 1x injak — termurah, reuse trigger+timer).
2. Spike platform (sentuh = mati — butuh telegraph + slot tutorial sendiri).
3. Moving platform (butuh retune gap dinamis).

## 🎬 Bug Log — Day 3 (13 Sep 2026, Doodle GameOver)

### Fitur: GameOver ala Doodle Jump (kamera ngikut jatuh)
**Status:** ✅ Done + tested via MCP (port 7891), compile 0 error, scene saved.
**Desain:** fase baru `Dying` di `GameState` — `Playing → Dying → GameOver`.
- `Playing`: kamera naik-only + `WorldspaceCanvas` standby di `camY - ortho*2 - 3`.
- `Dying`: score freeze, panel STAY, kamera `MoveTowards` bola (`fallSpeed 12`) sampai `camY == panelY`.
- `GameOver`: `timeScale 0`, panel dunia (WOTitle + WOScore) + Overlay tombol muncul.
**File:** `GameState.cs` (+Dying), `GameManager.cs` (Dying ts=1, save cuma GameOver),
`CameraFollow.cs` (overhaul), `PlayerController.cs` (Dying = fisika jalan, input/land mati),
`FtueHints.cs` (hide pas Dying/GameOver), `PlayerSquashStretch.cs` (stretch jalan pas Dying).
**Scene:** `WorldspaceCanvas` scale 0.01 (700x1000px = 7x10 unit) + `WOTitle/WOScore`,
`CameraFollow.gameOverAnchor + worldScoreText` ter-wire. TDD §6.2 "kandidat hapus" BATAL — panel dipakai.
**Tes:** idle bounce cam 0→3.53; drop → Dying → GameOver parkir camY=anchorY=-15.47,
score freeze 3.53 (world 3m + overlay 3m), best 92.39 utuh; Retry → fresh Playing.

### Fix: Retry + Menu mati setelah panel pindah ke world (13 Sep 2026)
**Gejala:** tombol Retry & Main Menu di panel GameOver tidak merespons.
**Akar:** King pindah `GameOverPanel` Overlay → `WorldspaceCanvas` (sesuai sketsa),
tapi `UIManager.WireButtons()` cuma nyisir `OverlayCanvas` → 2 tombol dunia tak ter-wire.
**Penanganan:** `WireButtons()` sekarang nyisir DUA canvas (Overlay + Worldspace);
`CameraFollow.worldScoreText` di-rewire WOScore (dangling) → `FinalHeightText`.
Ref text (final/newBest) selamat karena ikut kepindah (instanceID sama).
**Bukti:** raycast di posisi Retry = [Text, RetryButton, GameOverPanel] (tak ada yang blokir);
invoke GOMenu → MainMenu; invoke Retry → fresh Playing. Compile 0 error, scene saved.
