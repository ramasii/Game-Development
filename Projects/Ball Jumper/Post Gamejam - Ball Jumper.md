Project [[Projects/Ball Jumper/Ball Jumper|Ball Jumper]]

Aku ngerjain solo dengan nama tim "icikiwir" sebagai Special Team. Pas uda selesai gamejam, ternyata gameku masuk ke dua game pilihan diantara 20 Special Team. Saat sesi pitching ternyata ada dua masalah yang ditemui, yaitu:
1. Main di web mobile (Android dan iOS) suaranya ga muncul
2. Main di web desktop (Windows) suaranya muncul, tapi rasio layar tidak sesuai, posisi UI juga berubah, kadang inputnya ngeblink.

Aku juga nyobain game ini ke teman-teman. Kata mereka gameku feel uda kerasa, gamenya seru, combonya satisfying. Banyak juga yang ngasih saran untuk nambah fitur supaya bisa dijual. Fitur yang mereka minta adalah:
1. Crumbling platform, platform sekali injak
2. Spike platform, platform yang bisa munculin duri
3. Moving platform, platform gerak kanan-kiri
4. Leader board, nampilin ketinggian player
5. Booster item, seperti pegas atau jetpack untuk boost naik ke atas
6. Enemy, objek gerak kanan-kiri harus dihindari, bisa dikalahkan dengan stomp seperti super mario

Kalo dari aku sendiri, game ini butuh progresi, 


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
| Tier (zona)               | gapMin–Max Y | Bailout hijau | Bobot hijau 130+       | Rasa yang dikejar     |
| ------------------------- | ------------ | ------------- | ---------------------- | --------------------- |
| T0: 0–130 (existing)      | 1.5–2.4      | 5             | — (zona scripted)      | tidak berubah         |
| T1: 130–250 (Kata lanjut) | 1.6–2.45     | 6             | 35% → 30%              | napas mulai pendek    |
| T2: 250–400 (Kumite awal) | 1.8–2.5      | 7             | → 25%                  | toggle berantai wajib |
| T3: 400+ (Kumite)         | 1.9–2.55     | 7             | 25%, merah/biru 37/38% | survival              |
