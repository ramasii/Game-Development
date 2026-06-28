# 🏗️ Level Design Workflow (Whiteblocking & Modular)
#level-design #workflow #prototyping

## 🎯 Apa Ini?
Proses berurutan buat mengubah konsep mentah jadi level yang bisa dimainkan: dari menentukan bentuk berdasarkan mekanik inti, bikin "parti" sebagai ide pemandu, ngatur pacing, prototype di atas kertas, whiteblocking digital, sampai playtest berulang — lalu disusun secara modular biar efisien dibangun & diubah.

## 🧠 Poin Penting

**Form Follows Function → Form Follows Core Mechanics**
Prinsip arsitektur klasik "form follows function" diadaptasi Totten jadi "form follows core mechanics" — bentuk/layout level harus mengikuti mekanik inti gameplay, bukan sebaliknya (bikin level keren dulu baru dipaksain mekaniknya masuk).
> Kalau core mechanic Gameseed 2026 adalah parkour, bentuk levelnya (jarak antar platform, ketinggian, sudut) harus didikte oleh kebutuhan mekanik parkour itu — bukan oleh "level ini harus keliatan kayak hutan".

**Level Progression dengan Scaffolding Mechanisms**
Level-level berikutnya menumpuk (scaffold) kemampuan yang sudah diajarkan di level sebelumnya — tiap level baru menambahkan 1 elemen baru di atas fondasi yang sudah dikuasai pemain, bukan mengajarkan semuanya sekaligus.

**Level Design Parti**
Dipinjam dari konsep arsitektur "parti" — sketsa/ide sentral yang sangat sederhana yang merangkum gagasan inti level, dibuat sebelum detail apapun digambar. Semua keputusan desain berikutnya harus bisa ditarik balik ke parti ini.
> Kalau parti levelmu adalah "ruangan melingkar yang mengecil", semua keputusan layout berikutnya (jalur, rintangan, reward) harus konsisten sama ide itu — kalau enggak, berarti desainnya keluar jalur.

**"Scenes" dan Readability**
Level dipecah jadi "scenes" — area/vignette yang masing-masing punya identitas visual & gameplay yang jelas, biar pemain bisa "membaca" ruang (readability) dan tahu posisi mereka dalam progres level.

**Prototipe Non-Digital Dulu**
Sebelum buka Unity, gambar dulu di kertas (pakai toolkit dari [[Architectural Drawing Toolkit untuk Level Design]]) — cara paling murah buat coba-coba ide tanpa biaya produksi.

**Whiteblocking / Grayboxing Digital**
Bikin level dengan geometri primitif kasar (kubus, silinder) di engine, tanpa art final — fokus ke ukuran, ritme, dan apakah mekanik kerasa enak duluan.
> Ini tahap paling murah buat "gagal cepat" — ubah ukuran ruangan jauh lebih murah saat masih berbentuk kubus polos ketimbang udah jadi mesh detail dari Blender.

**Pacing pakai Nintendo Power Method**
Diadaptasi dari majalah Nintendo Power era 80–90an yang biasa bikin peta level berwarna dengan bubble callout menunjuk lokasi secret/strategi/item. Totten memakai gaya ini untuk memetakan pacing level — taruh "bubble" penanda di titik mana intensitas naik/turun, di mana reward muncul, di mana hazard nongol — sebelum level dibangun.

**Iterative Design dengan Playtesting**
Whiteblock dimainkan berulang-ulang, direvisi, dimainkan lagi — bukan proses sekali jadi.

**Modular Level Design**
Bangun level dari kit potongan/modul yang bisa dipakai ulang (dinding, lantai, pilar standar) — bukan custom-build tiap bagian dari nol. Lebih cepat dibangun, lebih konsisten skalanya, dan lebih mudah di-rebalance.

## 🧩 Properties / Tahapan

| Tahap | Output | Tools |
|---|---|---|
| Form Follows Core Mechanics | Prinsip layout dasar | — |
| Level Design Parti | 1 sketsa/ide sentral | Sketsa kertas |
| Scenes & Readability | Pembagian area/vignette | Plan/Section |
| Non-Digital Prototype | Sketsa kertas lengkap | Plan, Section, Axonometric |
| Nintendo Power Pacing Map | Peta pacing dengan bubble callout | Sketsa berwarna |
| Whiteblocking | Level kasar di engine | Unity primitive (cube, cylinder) |
| Iterative Playtesting | Catatan revisi | Playtest session |
| Modular Build | Level final dari kit modul | Prefab/modular asset Blender |

## 🔄 Alur Lengkap

```
Tentukan Core Mechanic
        │
        ▼
Form Follows Core Mechanics (bentuk level mengikuti mekanik)
        │
        ▼
Level Design Parti (1 ide sentral, sketsa sederhana)
        │
        ▼
Pecah jadi "Scenes" (area dengan identitas jelas, demi readability)
        │
        ▼
Prototype Non-Digital (Plan/Section/Axonometric di kertas)
        │
        ▼
Petakan Pacing (Nintendo Power Method — bubble callout intensitas)
        │
        ▼
Whiteblocking Digital (geometri kasar di Unity)
        │
        ▼
Playtest ──Revisi──▶ (ulangi sampai ritme & mekanik kerasa pas)
        │
        ▼
Modular Build (susun dari kit modul reusable, ganti primitive jadi asset final)
```

## 🛠️ Cara Pakai di Proyek Kamu
1. Sebelum sketsa apapun, tulis 1 kalimat: "core mechanic level ini adalah ___" — semua keputusan layout harus balik ke kalimat ini.
2. Bikin 1 parti sketsa simpel (bisa pakai Plan dari toolkit) sebelum mendetailkan apapun.
3. Bagi level jadi beberapa "scene" dengan identitas visual/gameplay yang beda, biar pemain selalu tahu progress mereka.
4. Petakan pacing pakai gaya Nintendo Power: gambar rute level, tempel "bubble" catatan di titik reward/hazard/climax.
5. Bangun whiteblock di Unity pakai cube/cylinder primitive — uji mekanik dulu sebelum mikirin visual.
6. Playtest, revisi ukuran/jarak, ulangi sampai ritme kerasa benar.
7. Setelah whiteblock fix, gantikan primitive satu-satu dengan modular asset dari Blender yang skalanya konsisten.

## 🔗 Lihat Juga
- [[Architectural Drawing Toolkit untuk Level Design]] — alat gambar yang dipakai di tahap prototype non-digital.
- [[Prospect & Refuge Spatial Design]] — pertimbangan spasial yang masuk saat menyusun "scenes".
- [[Identify Core Loops]] — selaraskan tahap "Form Follows Core Mechanics" dengan core loop gameplay yang sudah diidentifikasi duluan.
