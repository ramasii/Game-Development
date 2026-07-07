# 🎹 Chord Progression & Emosi untuk Game
#audio #music #harmony #chord #emotion #fl-studio

## 🎯 Apa Ini?
Panduan memilih progresi akor berdasarkan emosi target — cara paling cepat mengubah "mood" sebuah area game hanya dengan mengganti susunan akor di DAW. Gunakan skill ini saat mulai mengkomposisi BGM baru dan sudah tahu emosi apa yang ingin ditimbulkan.

**Kapan digunakan:**
- Saat mulai membuat BGM dan bingung akor mana yang cocok untuk suasana tertentu.
- Saat BGM terasa "tidak pas" meskipun tempo dan instrumennya sudah benar.
- Sebagai referensi cepat saat ingin menciptakan nuansa spesifik (evil, heroic, sad, tense, dsb).

---

## 🧠 Poin Penting

### Hubungan Akor & Emosi
Kombinasi jenis akor tertentu langsung menciptakan spektrum emosi yang spesifik. Lihat referensi visual lengkap di [[Chord Relationships and Emotion.pdf]].

| Target Emosi | Progresi Akor | Catatan |
|---|---|---|
| **Misterius / Dungeon** | Am – F – C – G | Progresi paling serbaguna untuk Minor |
| **Heroic / Boss Fight Epik** | C – G – Am – F | Sama persis tapi mulai dari Mayor — terasa lebih kuat |
| **Sedih / Perpisahan** | Am – G – F – E | Turun bertahap, E di akhir terasa seperti "bertanya" |
| **Tegang / Bahaya** | Am – Bdim – E – Am | `Bdim` dan `E` menciptakan ketegangan maksimal |
| **Ceria / Desa Pertama** | C – G – Am – F atau C – F – G – C | Mayor klasik, hangat dan aman |
| **Evil / Ominous** | Dm – Bb – C – Dm atau Am – G – Dm – E | Minor dengan pergerakan kromatik |
| **Nostalgia / Melankolis** | F – G – Em – Am | Mulai dari subdominan, rasa "mengenang" |

### Tonic, Subdominant, Dominant — Inti Harmoni
Setiap progresi akor bergerak di antara 3 fungsi harmoni:
- **Tonic (I)** — rumah, stabil, aman (C di tangga C Mayor)
- **Subdominant (IV)** — bergerak menjauh dari rumah, menambah warna (F di C Mayor)
- **Dominant (V)** — tegang, "menuntut" kembali ke Tonic (G di C Mayor)

> Formula paling aman untuk pemula: **I – IV – V – I** (atau variasinya). Hampir semua musik populer pakai ini sebagai fondasi.

### Mayor vs Minor — Pilihan Tonal
- Semua akor Mayor (C, F, G) → terasa cerah, positif, aman.
- Semua akor Minor (Am, Dm, Em) → terasa gelap, dalam, emosional.
- **Campuran Mayor & Minor** dalam satu progresi = nuansa kompleks (heroik tapi dengan bayangan, atau sedih tapi ada harapan).

### Tips Praktis di FL Studio
- Gunakan plugin **MIDI chord pack** atau **Chordify** untuk preview progresi cepat sebelum dikomposisi penuh.
- Semua akor dalam satu BGM harus berada dalam **kunci nada (Key) yang sama** agar tidak ada tabrakan nada — terutama penting saat menggabungkan dengan [[Adaptive Audio (Vertical Layering & Horizontal Resequencing)]].
- Coba **Chord Inversion** (balik urutan nada dalam akor) untuk variasi warna tanpa ganti progresi.

---

## 🧩 Properties — Referensi Kunci Nada

| Kunci | Akor I | Akor IV | Akor V | Akor vi (Relatif Minor) |
|---|---|---|---|---|
| C Mayor | C | F | G | Am |
| G Mayor | G | C | D | Em |
| A Minor | Am | Dm | E | C |
| D Minor | Dm | Gm | A | F |

## 🛠️ Cara Pakai
1. Tentukan emosi target area game (misal: "Boss akhir yang epik tapi menyedihkan").
2. Pilih kunci nada — untuk pemula, C Mayor atau A Minor paling mudah.
3. Ambil progresi dari tabel di atas yang paling dekat dengan emosi target.
4. Masukkan ke FL Studio sebagai Chord Pad (4 bar, masing-masing 4 ketukan).
5. Dengarkan — jika belum pas, coba ganti 1 akor dengan variannya (misal ganti G dengan G7 untuk ketegangan lebih).
6. Setelah progresi terasa benar, baru tambahkan melodi dan motif di atasnya (lihat [[Teori Musik Dasar untuk Game BGM]]).

## 🔗 Lihat Juga
- [[Teori Musik Dasar untuk Game BGM]] — fondasi tempo, skala, motif, dan looping.
- [[Adaptive Audio (Vertical Layering & Horizontal Resequencing)]] — saat BGM perlu berubah dinamis, semua layer harus berbagi kunci nada yang sama.
- [[Chord Relationships and Emotion.pdf]] — referensi visual lengkap hubungan akor dan emosi.
