# AWAN // Tukang Hujan - Concept Keep (Revisi Sketsa)

> Update 21 Sep 2026 sesuai sketsa King. Fun 80% Edu 20%. No angka & huruf. No chocolate-covered broccoli.

## 1. Konsep & Identitas
- **Premis:** Game ini adalah 2D santai mobile di mana pemain menggeser awan, menggabungkan awan jadi besar, dan menghujani tanah biar tanaman tumbuh & berbunga.
- **Genre:** 2D Merge-Drag / Ecosystem Sandbox, Casual
- **Target Platform:** Mobile, 2D, LANDSCAPE, 1 jari (single control: drag/geser doang)
- **USP:** Siklus air dimainin langsung: Stay (evaporasi) > Merge (kondensasi) > Hujan (presipitasi) > Kecil lagi. Bukan teori, tapi verb.
- **Referensi:** Seperti 2048 merge-feel + Tiny Wings chill + Tamagotchi ekosistem.

## 2. Core Loop (revisi)
Geser awan ke atas genangan > diem di atas air (evaporasi) > geser + overlap 2 awan buat merge (kondensasi jadi besar & gelap) > awan gelap hujan otomatis (presipitasi) > awan menyusut jadi kecil putih > merge lagi atau biarin regen > tanah basah > tanaman tumbuh > subur berbunga

- **Core Mechanic:** Cuma geser. Overlap = merge.
- **Daya tarik 5 menit:** Merge pop satisfying + hujan deres langsung liat tanah berubah warna & tunas muncul.
- **Daya tarik panjang:** Jaga tanah tetap basah, jaga tanaman jangan mati, kejar bunga sebanyak-banyaknya.

## 3. Sistem Detail (sesuai sketsa)

### LANDSCAPE / Siklus
`Genangan -> (stay/evaporasi) -> Awan kecil -> MERGE (kondensasi) -> Awan besar gelap -> MERGED -> Presipitasi hujan -> jadi kecil lagi -> balik ke genangan`
- Layout landscape: genangan kiri, tanah tanaman kanan, langit area main awan.

### PLAYER
- Player menggerakkan awan dengan cara menggeser awan (drag 1 jari).
- Player menggabungkan awan dengan cara menempatkan dua awan atau lebih secara overlap.

### AWAN
- Awan yang sudah menjadi gelap, akan otomatis hujan.
- Hujan berlangsung sampai awan menjadi kecil dan berwarna putih (awan kecil).
- Awan kecil (putih kecil) memiliki waktu hidup sekian detik (pendek, harus cepat di-merge / dipakai).
- Awan normal (putih sedang) memiliki jangka waktu yang lama (awan kerja utama).
- Awan kecil bisa menjadi awan normal dengan cara digabungkan dengan awan lain.
- Rumus: Awan + Awan = Awan lebih besar.

### GENANGAN AIR
- Menghasilkan awan (spawner pasif).
- Fungsi: tempat evaporasi — stay in top of water = isi ulang / munculin awan baru.

### TANAH
- Tanah bisa kering dalam waktu tertentu.
- Tanah bisa basah jika terkena hujan.
- Visual only: kering (pucat/retak) vs basah (gelap). Tanpa angka, tanpa teks.

### TANAMAN
- Tanaman bisa kering dan mati dalam jangka tertentu (kalau tanah kering terus).
- Tanaman bisa tumbuh di tanah yang selalu basah.
- Tanaman bisa menumbuhkan bunga jika subur dalam waktu tertentu (reward mastery jaga kelembaban).

## 4. Kenapa Fun Dulu (80%)
- Merge itu candu: geser-overlap-pop-besar.
- Hujan otomatis sebagai reward, bukan hukuman — awan gelap = saatnya panen.
- Urgency ringan: awan kecil cepat hilang, tanah cepat kering, tanaman bisa mati → mikir prioritas tanpa stres.

## 5. Edukasi 20% (muncul alami)
- Evaporasi: diem di atas air = dapat awan.
- Kondensasi: merge 2 awan = jadi besar gelap.
- Presipitasi: gelap = hujan sampe kecil lagi.
- Ekologi: tanah basah ↔ tanaman hidup, tanah kering ↔ mati, subur terus ↔ berbunga.

## 6. Anti Chocolate-Covered Broccoli Check
- BUKAN: kuis siklus air buat buka hujan.
- INI: siklusnya = cara mainnya. Cabut semua label edukasi, game tetap fun dimainin sebagai merge-hujan.

## 7. FTUE (tanpa angka/huruf, full visual)
1. Awan kecil + panah jari: geser ke atas genangan → uap naik (evaporasi paham).
2. Dua awan deketan + hint overlap → merge jadi sedang → merge lagi jadi gelap.
3. Awan gelap digeser ke tanah kering → hujan otomatis → tanah gelap → tunas muncul.
4. Biarin: tanah memucat lagi → pemain paham harus hujan rutin.

## 8. Scope Prototype
- **Solo Unity 6, 1-2 minggu:** 1 scene landscape, drag awan (raycast 2D), overlap check buat merge, state awan: kecil/normal/besar-gelap (scale + color), timer hidup, rain particle + soil wet/dry timer, plant state: benih/tumbuh/kering/mati/bunga.
- **Risiko:** overlap merge terasa adil (magnet snap), balancing timer jangan bikin frustasi.
- **Go/No-Go:** Playtest 5 menit tanpa teks, pemain bisa merge + hujan + numbuhin 1 bunga = lanjut.

---
Next: balancing timer (pakai skill Economy & Balancing) + TDD pola State untuk Awan/Tanah/Tanaman.
