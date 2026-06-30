# 🥋 Framework Kihon-Kata-Kumite (Learning Curve & Encounter Design)
#game-design #learning-curve #encounter-design #mechanic-mastery

## 🎯 Apa Ini?
Framework berorientasi aksi untuk merancang kurva belajar (*player learning curve*) dan arsitektur pertempuran (*encounter design*) lewat 3 tahap progresif: isolasi mekanik tanpa risiko (**Kihon**), repetisi pola terstruktur (**Kata**), lalu pelepasan ke situasi dinamis tak terprediksi (**Kumite**). Beda dari Kishōtenketsu yang fokus ke struktur naratif/eksplorasi level, framework ini fokus ke **membangun memori otot, penguasaan refleks, dan eksekusi mekanik presisi tinggi**.

**Kapan harus menggunakan skill ini:**
- Game punya *skill ceiling* tinggi yang mengandalkan refleks & presisi tangan — precise platformer (Celeste, Super Meat Boy), action hack & slash/soulslike (Sekiro, Elden Ring), fighting game (Street Fighter, Tekken).
- Mau menghilangkan "infodump tutorial" — mekanik/kombo kompleks diajarkan lewat *learning by doing*, bukan teks panduan panjang.
- Sedang merancang variasi musuh (*encounter & enemy design*) — kroco vs mini-boss vs main boss butuh tingkat prediktabilitas yang beda.
- **Tidak cocok** dipaksakan untuk genre santai/puzzle/eksplorasi yang lebih pas pakai [[Identify Core Loops]] atau struktur naratif Kishōtenketsu.

## 🧠 Poin Penting

**Kihon (基本 — Fondasi Dasar)**
Mengisolasi satu mekanik baru di area steril dari bahaya (*zero-risk environment*) — tanpa musuh, tanpa jurang, dengan telegraf visual jelas (kontras tinggi, UI prompt tombol). Pemain bebas eksperimen memahami jarak, kecepatan, momentum mekanik tanpa takut Game Over.
> Contoh Grappling Hook: ruangan tertutup dengan satu titik jangkar bercahaya — gerbang level baru terbuka kalau pemain berhasil eksekusi mekanik minimal 3 kali.

**Kata (型 — Bentuk/Pola Terstruktur)**
Memasukkan mekanik ke skenario nyata dengan tantangan berulang, ritmis, dan polanya mudah diprediksi — membangun memori otot lewat repetisi konsisten. Checkpoint harus dekat agar kegagalan tidak merusak ritme bermain (*Low Stakes, High Frequency*).
> Contoh: jurang dengan 3 titik jangkar berurutan, pola statis Sabet→Lepas→Sabet→Lepas→Mendarat. Gagal = jatuh ke air, respawn dengan sedikit HP berkurang.

**Kumite (組手 — Pertarungan Bebas/Aplikasi Dinamis)**
Melepas semua pola kaku — situasi tidak terprediksi, cepat, kompleks, risiko tinggi (*High Stakes*). Otak pemain dipaksa memproses lebih dari satu ancaman sekaligus secara bersamaan.
> Contoh: Boss Fight/Escape Sequence — lantai runtuh jadi lava, musuh menembak dari berbagai arah, titik jangkar pengait bergerak acak atau hancur sekali pakai. Di sinilah *sense of mastery* tercapai.

## 🧩 Properties / Matriks Perbandingan

| Atribut | Kishōtenketsu (Nintendo Style) | Kihon-Kata-Kumite (Martial Arts Style) |
|---|---|---|
| Fokus Utama | Struktur naratif & eksplorasi variasi ide dalam level | Penguasaan refleks, memori otot, aksi presisi tinggi |
| Pemicu Kepuasan | Momen "Aha!" saat melihat elemen baru (twist) | Rasa bangga (*sense of mastery*) menaklukkan tantangan sulit |
| Tingkat Risiko | Cenderung aman, fokus kegembiraan bermain | Meningkat tajam dari Zero-Risk hingga High-Stakes |
| Genre Terbaik | Puzzle, Platformer santai, Adventure | Action-heavy, Soulslike, Fighting, Roguelike, Shooter |

| Tahap | Risiko | Pola | Checkpoint |
|---|---|---|---|
| Kihon | Zero-risk | Bebas eksperimen | Tidak relevan (tanpa kegagalan) |
| Kata | Low stakes | Statis, berulang, ritmis | Sangat dekat (jaga ritme) |
| Kumite | High stakes | Acak/adaptif, tak terprediksi | Jauh (konsekuensi besar) |

## 🔄 Alur Penerapan

```
Mekanik baru muncul
        │
        ▼
KIHON — Area steril, zero-risk, telegraf jelas
   (validasi: pemain eksekusi sukses min. 3x)
        │
        ▼
KATA — Pola statis berulang, low-stakes high-frequency
   (checkpoint dekat, mulai kombinasikan 2 gerakan dasar)
        │
        ▼
KUMITE — Tekanan dinamis, high-stakes, multi-tasking
   (pola acak/AI-driven, konsekuensi gagal besar)
        │
        ▼
Sense of Mastery tercapai
```

## 🛠️ Cara Pakai (Step-by-Step)

**Langkah 1 — Tahap Isolasi Mekanik (Kihon)**
1. Matikan semua variabel pengganggu: musuh, jebakan, batas waktu, penalti kematian.
2. Berikan telegraf jelas: kontras visual tinggi atau UI prompt tombol pada objek interaktif.
3. Validasi pemahaman: gerbang level hanya terbuka setelah pemain eksekusi mekanik minimal 3 kali.
> Desain area ini sebagai koridor/ruangan transisi tepat sebelum tantangan besar dimulai.

**Langkah 2 — Rancang Rantai Ritme Berpola (Kata)**
1. Terapkan progresi spasial: tingkatkan kesulitan bertahap lewat tata letak ruang (jarak diperjauh, objek bergerak lambat dengan rute linear).
2. Gunakan "Low Stakes, High Frequency": biarkan pemain coba berulang, checkpoint dekat agar ritme tidak rusak akibat frustrasi.
3. Gabungkan kompleksitas: mulai kombinasikan dua gerakan dasar (contoh: Lari → Slide → Grappling Hook).

**Langkah 3 — Eksekusi Tekanan Dinamis (Kumite)**
1. Rusak prediktabilitas pola: ubah rintangan jadi acak, terikat AI musuh, atau dipengaruhi fisika dinamis.
2. Tingkatkan risiko kematian (high stakes): kegagalan berkonsekuensi besar (kembali ke checkpoint utama, kehilangan resource berharga).
3. Hadirkan multi-tasking: paksa pemain memproses lebih dari satu ancaman sekaligus.

**Untuk Enemy/Encounter Design:**
- Pakai prinsip **Kata** untuk musuh kroco: serangan berpola, lambat, telegraf jelas.
- Pakai prinsip **Kumite** untuk mini-boss/main boss: bisa membaca gerakan pemain, membatalkan serangan, memodifikasi kombo secara adaptif.

## 🔗 Lihat Juga
- [[Identify Core Loops]] — pilih framework ini kalau genre membutuhkan presisi refleks tinggi; pakai Core Loop biasa untuk genre yang lebih santai.
- [[Deconstruct Mechanics]] — pisau analisis untuk membedah mekanik sebelum dipecah ke 3 tahap Kihon-Kata-Kumite.
- [[Prospect & Refuge Spatial Design]] — tahap Kumite sering memanfaatkan hazard & ruang terbuka yang selaras dengan pola prospect-refuge.
