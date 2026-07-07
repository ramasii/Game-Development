# 🎵 Teori Musik Dasar untuk Game BGM
#audio #music #bgm #theory #fl-studio

## 🎯 Apa Ini?
4 pilar teori musik yang paling krusial untuk membuat BGM game dari nol — fokus pada membangun atmosfer dan looping seamless, bukan teknik musik yang rumit. Gunakan skill ini setiap kali mulai mengkomposisi musik untuk area, karakter, atau momen baru di game.

**Kapan digunakan:**
- Saat mulai membuat BGM baru untuk level/area game.
- Saat BGM yang dibuat terasa "flat" atau tidak punya karakter.
- Saat bingung memilih tempo atau skala nada yang tepat untuk suasana tertentu.

---

## 🧠 Poin Penting

### Pilar 1 — Tempo & Rhythm (Detak Jantung Game)
Tempo diukur dalam **BPM (Beats Per Minute)** — fondasi yang menentukan seberapa intens perasaan pemain.

| Range BPM | Nuansa | Cocok Untuk |
|---|---|---|
| 60–80 BPM | Lambat, tenang | Eksplorasi, menu utama, momen sedih |
| 100–120 BPM | Sedang | Platformer santai, teka-teki, kota hub |
| 140–180+ BPM | Cepat, intens | Boss fight, kejar-kejaran, fase aksi |

### Pilar 2 — Harmoni & Skala (Sakelar Emosi)
Pilih skala nada yang tepat untuk langsung mengubah psikologis pemain:
- **Major Scale:** Cerah, bahagia, heroik, aman → desa pertama, layar kemenangan.
- **Minor Scale:** Sedih, misterius, tegang, epik → dungeon, malam hari, area musuh.

> Untuk pemula: mulai dari **A Minor** — hanya pakai tuts putih piano (A, B, C, D, E, F, G), dijamin tidak ada nada sumbang.

### Pilar 3 — Motif / Leitmotif (Identitas Suara)
Buat **Motif** — rangkaian 3–5 nada pendek yang mudah diingat. Diulang-ulang dengan instrumen berbeda sepanjang game → pemain langsung tahu identitas karakter atau area tersebut.
> Contoh: tema 5 nada Super Mario Bros atau tema Zelda — sederhana tapi ikonik karena direpetisi konsisten.

### Pilar 4 — Struktur Looping (Transisi Tanpa Akhir)
BGM game harus berputar terus tanpa pemain sadar kapan lagu berakhir dan mulai kembali.
> **Kunci**: jangan akhiri loop dengan nada dasar (*Tonic*) yang terlalu final. Biarkan nada terakhir "menggantung" agar telinga pemain secara naluriah kembali ke bar pertama.

---

## 🛠️ Cara Pakai — Alur Membuat Loop BGM Pertama

1. **Tentukan emosi & BPM** — misal: "Dungeon Lembab" → 90 BPM (lambat, misterius).
2. **Pilih skala** — untuk pemula gunakan A Minor (tuts putih semua).
3. **Buat progresi akor** — 4 bar dengan Chord Pad. Coba: **Am – F – C – G** (4 ketukan per bar). Kuat untuk nuansa misterius.
4. **Tambahkan melodi & motif** — melodi tipis di atas akor. Buat 4 nada unik di bar pertama, variasikan sedikit di bar ketiga.
5. **Uji sambungan loop** — dengarkan transisi akhir ke awal. Jika patah, geser nada terakhir agar lebih dekat ke nada pembuka.

## 🔗 Lihat Juga
- [[Chord Progression & Emosi untuk Game]] — panduan lanjutan memilih progresi akor sesuai mood.
- [[Adaptive Audio (Vertical Layering & Horizontal Resequencing)]] — cara musik berubah dinamis berdasarkan state game.
- [[Decoupled Audio System (Event Channel & Pooling)]] — implementasi SFX system di Unity yang bisa dikombinasikan dengan BGM.
