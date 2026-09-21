# AWAN // Tukang Hujan - Concept Keep

> Disimpan dari brainstorming 21 Sep 2026. Fun 80% Edu 20%. No angka & huruf. No chocolate-covered broccoli.

## 1. Konsep & Identitas
- **Premis:** Game ini adalah 2D chill-skill mobile di mana pemain jadi awan gendut untuk menyiram hutan kering dan bertahan dari matahari.
- **Genre:** 2D Physics Sandbox / Casual Skill, Single Control
- **Target Platform:** Mobile (Android), Portrait, 2D, 1 jari
- **USP:** Lu BUKAN nyiram tanaman, lu JADI cuacanya. Tahan = hujan, lepas = nguap naik. Siklus air sebagai verb, bukan materi hafalan.
- **Referensi:** Seperti Tamagotchi cuaca + Alto's Adventure + Tiny Wings vibe, tapi fluid sim sederhana.

## 2. Core Loop
Isap air di atas danau (auto-isap) > ketiup angin > tahan untuk hujanin pohon kering > hindari / ngumpet dari matahari > pohon mekar > buka area baru yang lebih panas & berangin

- **Core Mechanic:** Tahan 1 jari = hujan (berat turun), Lepas = menguap (ringan naik). Massa awan = resource + fisika.
- **Daya tarik 5 menit:** Langsung satisfying mainin hujan + liat pohon mekar satu-satu.
- **Daya tarik panjang:** Mastery rute angin, kombo siram beruntun, selamatkan biome baru.

## 3. Mekanik Utama (max 3-5)
1. **Hujan / Nguap (Hold-Release):** Tahan turun + keluar air, lepas naik. Makin banyak air makin berat & gelap.
2. **Angin Pasif:** Angin dorong horizontal beda tiap ketinggian. Pemain belajar baca arus, bukan melawan.
3. **Matahari Predator:** Kena sinar langsung = susut. Ngumpet di bayangan tebing / balik jadi kecil biar cepat.
4. **Pohon Haus (Win-state visual):** Pohon kering > disiram > mekar > kasih benih / buka jalan. No angka, cuma warna & animasi.

## 4. Kenapa Fun Dulu (80%)
- Rasa jadi OP (jadi cuaca)
- Risk/reward tiap hujan: mau nyiram tapi jadi berat & gampang kena matahari
- Visual satisfying: hujan, mekar, pelangi tipis

## 5. Edukasi 20% (muncul alami, tanpa teks)
- Siklus air: evaporasi (naik pas lepas/panas), kondensasi (membesar di atas danau/dingin), presipitasi (hujan pas berat)
- Termodinamika intuitif: panas = susut, teduh/dingin = aman, ketinggian = angin beda
- Ekologi dasar: hutan butuh air bertahap, bukan sekaligus

## 6. Anti Chocolate-Covered Broccoli Check
- BUKAN: jawab diagram siklus air untuk buka pintu.
- INI: lu ngalamin jadi siklusnya. Kalau semua label edukasi dicabut, game tetap asik dimainin.

## 7. FTUE (tanpa angka/huruf, full visual)
- Mulai: awan kecil di atas danau, auto-isap membesar + ikon jari tahan.
- Tahan: hujan keluar, pohon bawah seneng. Lepas: naik lagi.
- Matahari nongol: awan menyusut + bunyi desis, pemain reflek cari bayangan. Langsung paham tanpa tutorial teks.

## 8. Scope & Feasibility Cepat
- **Prototype 2 minggu (solo Unity 6):** 1 biome, 1 awan blob (scale + alpha = air), angin sine, matahari patrol, 3 state pohon (kering/haus/mekar).
- **Risiko teknis:** Fluid visual murah (particle + shader unlit), hindari sim beneran.
- **Go/No-Go:** Dalam 5 menit playtest orang ketawa / bilang "lagi" tanpa disuruh = lanjut.

---
Next: kalau oke, bikin GDD mini ikut Format Game Design + TDD Object Pool untuk hujan & awan.
