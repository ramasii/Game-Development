# AWAN // Tukang Hujan - Concept Keep (Revisi Sketsa)

> Update 21 Sep 2026 sesuai sketsa King. Fun 80% Edu 20%. No angka & huruf. No chocolate-covered broccoli.

## 1. Konsep & Identitas
- **Premis:** Game ini adalah 2D santai mobile di mana pemain menggeser awan, menggabungkan awan jadi besar, dan menghujani tanah biar tanaman tumbuh & berbunga, di atas planet kecil melingkar yang bisa diputar.
- **Genre:** 2D Merge-Drag / Ecosystem Sandbox, Casual
- **Target Platform:** Mobile, 2D, LANDSCAPE, 1 jari (single control: drag/geser doang)
- **USP:** Siklus air dimainin langsung: Stay (evaporasi) > Merge (kondensasi) > Hujan (presipitasi) > Kecil lagi + dunia melingkar yang diputar dengan cara digeser. Bukan teori, tapi verb.
- **Referensi:** Seperti 2048 merge-feel + Tiny Wings chill + Tamagotchi ekosistem + Loop World ala Tiny Planet.

## 2. Core Loop (revisi)
Geser awan ke atas genangan > diem di atas air (evaporasi) > geser + overlap 2 awan buat merge (kondensasi jadi besar & gelap) > drag awan ke edge buat muter daratan ke area target > awan gelap hujan otomatis (presipitasi) > awan menyusut jadi kecil putih > merge lagi atau biarin regen > tanah basah > tanaman tumbuh > subur berbunga > putar lagi cari lahan baru

- **Core Mechanic:** Cuma geser. Overlap = merge. Edge-drag = putar dunia.
- **Daya tarik 5 menit:** Merge pop satisfying + hujan deres + muter planet yang tactile.
- **Daya tarik panjang:** Jaga banyak titik tanah tetap basah di sekeliling planet, rotasi prioritas, kejar bunga sebanyak-banyaknya.

## 3. Sistem Detail (sesuai sketsa)

### LANDSCAPE / Siklus
`Genangan -> (stay/evaporasi) -> Awan kecil -> MERGE (kondensasi) -> Awan besar gelap -> MERGED -> Presipitasi hujan -> jadi kecil lagi -> balik ke genangan`
- Layout landscape: kamera fixed di langit, daratan melengkung di bawah sebagai busur planet.

### WORLD / DARATAN MELINGKAR (baru)
- Player bisa menggeser daratan untuk pindah area.
- Daratan berbentuk lingkaran (planet kecil), sehingga ketika digeser terus menerus bisa kembali ke lokasi awal.
- Dari POV developer ini "memutar" daratan (rotasi angle planet), tapi bagi player ini terasa menggeser daratannya (parallax horizontal).
- Implementasi: planet = circle, kamera diam, putar container daratan di sumbu Z. Awan tetap di screen-space langit.
- Edukasi bonus: planet bulat + horizon melengkung kebaca visual tanpa teks.

### PLAYER (update)
- Player menggerakkan awan dengan cara menggeser awan (drag 1 jari).
- Player menggabungkan awan dengan cara menempatkan dua awan atau lebih secara overlap.
- Player bisa menggeser awan lalu didrag ke paling kanan atau kiri layar untuk otomatis menggeser daratan dan memindahkan awan (edge-scroll).
  - Drag awan ke edge kanan > planet muter kiri (area baru masuk dari kanan).
  - Drag awan ke edge kiri > planet muter kanan.
  - Awan ikut kebawa secara visual, tapi logikanya dunia yang muter.
- Player juga bisa geser daratan langsung (drag tanah kosong kiri/kanan) untuk eksplor tanpa bawa awan — tetap 1 jari, tidak nambah tombol.

### AWAN
- Awan yang sudah menjadi gelap, akan otomatis hujan.
- Hujan berlangsung sampai awan menjadi kecil dan berwarna putih (awan kecil).
- Awan kecil (putih kecil) memiliki waktu hidup sekian detik (pendek, harus cepat di-merge / dipakai).
- Awan normal (putih sedang) memiliki jangka waktu yang lama (awan kerja utama).
- Awan kecil bisa menjadi awan normal dengan cara digabungkan dengan awan lain.
- Rumus: Awan + Awan = Awan lebih besar.
- Awan tidak ikut rotasi planet (tetap di langit), hanya daratan yang muter — jadi pemain harus timing mindahin hujan ke atas lahan target.

### GENANGAN AIR
- Menghasilkan awan (spawner pasif).
- Fungsi: tempat evaporasi — stay in top of water = isi ulang / munculin awan baru.
- Genangan menempel di planet (ikut muter), jadi kadang harus muter dulu buat cari air.

### TANAH
- Tanah bisa kering dalam waktu tertentu.
- Tanah bisa basah jika terkena hujan.
- Tanah tersebar di sekeliling lingkaran planet (slot-slot), menempel dan ikut rotasi.
- Visual only: kering (pucat/retak) vs basah (gelap). Tanpa angka, tanpa teks.

### TANAMAN
- Tanaman bisa kering dan mati dalam jangka tertentu (kalau tanah kering terus).
- Tanaman bisa tumbuh di tanah yang selalu basah.
- Tanaman bisa menumbuhkan bunga jika subur dalam waktu tertentu (reward mastery jaga kelembaban).
- Tanaman juga menempel di planet, jadi jaga kelembaban = main rotasi + prioritas hujan.

## 4. Kenapa Fun Dulu (80%)
- Merge itu candu: geser-overlap-pop-besar.
- Hujan otomatis sebagai reward, bukan hukuman — awan gelap = saatnya panen.
- Muter planet itu tactile & satisfying (kayak putar globe), eksplorasi tanpa loading.
- Edge-drag mindahin awan + dunia sekaligus = 1 gesture 2 fungsi, tetap single control.
- Urgency ringan: awan kecil cepat hilang, tanah cepat kering, tanaman bisa mati → mikir prioritas tanpa stres.

## 5. Edukasi 20% (muncul alami)
- Evaporasi: diem di atas air = dapat awan.
- Kondensasi: merge 2 awan = jadi besar gelap.
- Presipitasi: gelap = hujan sampe kecil lagi.
- Ekologi: tanah basah ↔ tanaman hidup, tanah kering ↔ mati, subur terus ↔ berbunga.
- Planet: dunia bulat, horizon melengkung, area beda butuh perjalanan (rotasi).

## 6. Anti Chocolate-Covered Broccoli Check
- BUKAN: kuis siklus air buat buka hujan.
- INI: siklusnya = cara mainnya. Cabut semua label edukasi, game tetap fun dimainin sebagai merge-hujan + putar planet.

## 7. FTUE (tanpa angka/huruf, full visual)
1. Awan kecil + panah jari: geser ke atas genangan → uap naik (evaporasi paham).
2. Dua awan deketan + hint overlap → merge jadi sedang → merge lagi jadi gelap.
3. Awan gelap digeser ke tanah kering → hujan otomatis → tanah gelap → tunas muncul.
4. Drag awan gelap ke edge kanan → daratan bergeser, lahan baru masuk → pemain paham edge-scroll muter dunia.
5. Biarin: tanah memucat lagi → pemain paham harus hujan rutin + muter prioritas.

## 8. Scope Prototype
- **Solo Unity 6, 1-2 minggu:** 1 scene landscape, drag awan (raycast 2D), overlap check buat merge, state awan: kecil/normal/besar-gelap (scale + color), timer hidup, rain particle + soil wet/dry timer, plant state: benih/tumbuh/kering/mati/bunga.
- **Tambahan planet:** 1 empty PlanetRoot (rotasi Z), slot tanah/genangan/tanaman jadi child di sekeliling lingkaran. Edge-drag: jika awan x > 0.85*halfWidth atau < -0.85*halfWidth → rotate PlanetRoot dengan kecepatan proporsional. Drag tanah kosong → rotate langsung.
- **Risiko:** overlap merge terasa adil (magnet snap), edge-scroll tidak ke-trigger tidak sengaja (deadzone + indikator panah edge), balancing timer jangan frustasi.
- **Go/No-Go:** Playtest 5 menit tanpa teks, pemain bisa merge + hujan + muter planet + numbuhin 1 bunga = lanjut.

---
Next: balancing timer (pakai skill Economy & Balancing) + TDD pola State untuk Awan/Tanah/Tanaman + Rotasi planet.
