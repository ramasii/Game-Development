Project [[Projects/Ball Jumper/Ball Jumper|Ball Jumper]]

Aku ngerjain solo dengan nama tim "icikiwir" sebagai Special Team. Pas uda selesai gamejam, ternyata gameku masuk ke dua game pilihan diantara 20 Special Team. Saat sesi pitching ternyata ada dua masalah yang ditemui, yaitu:
1. **(FIXED)** Main di web mobile (Android dan iOS) suaranya ga muncul 
2. **(FIXED)** Main di web desktop (Windows) suaranya muncul, tapi rasio layar tidak sesuai, posisi UI juga berubah, kadang inputnya ngeblink.

Aku juga nyobain game ini ke teman-teman. Kata mereka gameku feel uda kerasa, gamenya seru, combonya satisfying. Banyak juga yang ngasih saran untuk nambah fitur supaya bisa dijual. Fitur yang mereka minta adalah:
1. Crumbling platform, platform sekali injak
2. Spike platform, platform yang bisa munculin duri
3. Moving platform, platform gerak kanan-kiri
4. Leader board, nampilin ketinggian player
5. Booster item, seperti pegas atau jetpack untuk boost naik ke atas
6. Enemy, objek gerak kanan-kiri harus dihindari, bisa dikalahkan dengan stomp seperti super mario

Kalo dari aku sendiri, game ini butuh progresi selama bermain. 

| Tier (zona)               | gapMin–Max Y | Bailout hijau | Bobot hijau 130+       | Rasa yang dikejar     |
| ------------------------- | ------------ | ------------- | ---------------------- | --------------------- |
| T0: 0–130 (existing)      | 1.5–2.4      | 5             | — (zona scripted)      | tidak berubah         |
| T1: 130–250 (Kata lanjut) | 1.6–2.45     | 6             | 35% → 30%              | napas mulai pendek    |
| T2: 250–400 (Kumite awal) | 1.8–2.5      | 7             | → 25%                  | toggle berantai wajib |
| T3: 400+ (Kumite)         | 1.9–2.55     | 7             | 25%, merah/biru 37/38% | survival              |

---

## 🔧 Progress Fix Web (14 Sep 2026, Digidaw + King)

> Sumber: analisa engine via MCP (port 7890), compile 0 error, smoke Play bersih.

### ✅ Selesai
1. **Input blink** — `DynamicCanvas/HeightText`, `LiveBestText`, `StreakText` → `raycastTarget=false` + `GraphicRaycaster` DynamicCanvas dicabut (0 button di situ, 8/8 tombol Overlay+Worldspace utuh). Scene saved.
2. **Audio mobile** — 12 wav → `preload=true` + `loadInBackground=true` (sebelumnya false/false = first-play silence di HP). `UIManager.menuSfx`: Play bunyi splash (= unlock AudioContext) + tes bunyi saat unmute. `UIManager.cs` 219 → 229 baris.
3. **Mute audit** — `MuteKey=0`, ikon mute wired, smoke `OnPlayPressed` → Playing tanpa error (1 error console cuma adb benign).

### ⏳ Berikutnya (butuh build WebGL + HP)
4. Kunci portrait di WebGL template (canvas 9:16 + pillarbox) + `CanvasScaler` match → width — biar rasio/UI web desktop = rasa HP.
5. Test matrix: Chrome desktop 16:9/4:3, Chrome Android, Safari iOS.
6. Scope SFX hilang (jump/dash/UI-click/gameover belum ada clip) — putuskan bikin baru atau resmi cuma land+splash.

### ✅ Web profile — diterapin King (14 Sep 2026, verif via MCP)
- `defaultWebScreen` 960x600 (landscape) → **540x960 (portrait)** ✅ — sejalan sama game 1080x1920
- Template masih Default, `runInBackground` masih False — disengaja / susulan
- Bundle ID aktual `com.Paganisium.BallJumper` (bukan DefaultCompany), product `Ball Jumper` ✅

### ✅ Build WebGL v2.1 — dibuild King, verif file 14 Sep 2026
- Lokasi `Build/WebGL/`: `index.html` + `Build/` (loader, data, framework, wasm — Brotli `.br`) + `TemplateData/` + zip v2.1 & lama
- `index.html`: canvas **540x960 portrait** ✅, desktop fixed 540x960, mobile fullscreen + viewport meta ✅, title + productVersion 2.1 ✅
- Catatan hosting: file `.br` butuh server yang serve Brotli (itch.io OK; hosting lain cek dulu, kalau 404/decompress error → rebuild tanpa compression atau pakai `.gz`/fallback)
