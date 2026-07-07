# 🎭 Chord Relationship (CR) Framework
#audio #music #theory #chord-relationship #emotion #film-scoring #fl-studio

## 🎯 Apa Ini?
Sistem formal dari dunia *film scoring* untuk mendeskripsikan dan memilih pasangan dua akor berdasarkan emosi yang ingin dibangkitkan — menggunakan notasi 3-part: **[Tipe Akor Awal] + [Interval] + [Tipe Akor Akhir]**. Lebih presisi dari sekedar "pakai Am–F–C–G" karena langsung mendefinisikan *hubungan antar akor* beserta karakter emosionalnya. Gunakan skill ini saat duduk di FL Studio dan tahu persis emosi apa yang ingin disampaikan oleh suatu momen di game.

**Kapan digunakan:**
- Saat memulai komposisi BGM dan ingin memilih pasangan akor yang tepat secara emosional.
- Saat BGM sudah dibuat tapi terasa "generik" — CR bisa mempertegas karakter emosi spesifik.
- Saat mengkomposisi musik untuk momen krusial: boss fight, cutscene sedih, momen heroik, dll.
- **Prasyarat**: pahami [[Interval Musik & Semitone System]] terlebih dahulu.

---

## 🧠 Poin Penting

### Anatomi Sebuah CR
Setiap CR terdiri dari **3 bagian**:
```
[Tipe Akor 1]  [Interval]  [Tipe Akor 2]
     M      +    III    +      m
```
- **M** = Major Triad (akor mayor)
- **m** = minor Triad (akor minor)
- **Interval** = jarak semitone antara *root note* kedua akor (bukan antar nada individual)

> CR hanya bekerja dengan **Major Triad (M)** dan **Minor Triad (m)**. Akor lain (7th, sus, dll) bisa ditambahkan sebagai ornamen, tapi CR-nya tetap didefinisikan dari dua triad dasar.

### Cara Membaca & Menggunakan CR

**Contoh 1 — M III m (Sad / Kesedihan):**
1. Pilih akor pertama: harus **Major** → ambil **C Major**
2. Root note C, interval III = 4 semitone → **E**
3. Akor kedua harus **minor** → **E minor**
4. Hasil: gerakan **C Major → E minor** = nuansa sedih

**Contoh 2 — m vi M (Resolution):**
1. Pilih akor pertama: harus **minor** → ambil **E minor**
2. Root note E, interval vi = 8 semitone → **C**
3. Akor kedua harus **Major** → **C Major**
4. Hasil: gerakan **E minor → C Major** = nuansa resolusi/lega

> Perhatikan bahwa CR "M III m" dan "m vi M" menggunakan akor yang sama (C Major & E minor) tapi **dibalik urutannya** — dan menghasilkan emosi yang berbeda total. Pasangan CR seperti ini disebut **"Twin CR"**.

### Konsep Twin CR
Dua CR yang menggunakan pasangan akor yang sama tapi dengan urutan terbalik. Twin CR tidak selalu punya emosi yang sama — kadang kontras, kadang mirip.

```
M III m  (C Major → E minor)  =  Sad
m vi M   (E minor → C Major)  =  Resolution

↑ Twin satu sama lain, tapi feel-nya berbeda!
```

### Urutan Akor SANGAT Berpengaruh
Tidak seperti progresi akor konvensional yang bisa dimainkan dalam berbagai inversions tanpa masalah, **urutan akor dalam CR menentukan CR yang berbeda**. Balik urutannya = CR berbeda = emosi berbeda.

> Inversi *nada dalam akor* (1st/2nd inversion) tetap diperbolehkan dan tidak mengubah CR — yang penting root note-nya benar dan urutannya tidak dibalik.

---

## 🧩 Properties — 4 Kategori CR

CR dikelompokkan berdasarkan kombinasi tipe akor awal dan akhir:

| Kategori | Notasi | Karakter Umum |
|---|---|---|
| **Minor → Minor** | m _ m | Gelap, misterius, ominous, evil |
| **Major → Major** | M _ M | Terang, heroik, fantastical, protagonis |
| **Minor → Major** | m _ M | Transisi, ambiguitas emosi (bitter sweet, powerful, resolution) |
| **Major → Minor** | M _ m | Dramatis, dark, misterius, tense |

## 🔄 Alur Penggunaan CR

```
Tentukan momen di game (boss fight? cutscene sedih? area misterius?)
        │
        ▼
Tentukan emosi target → cari CR di [[CR Reference & Emotion Lookup]]
        │
        ▼
Baca notasi CR: [Tipe1] [Interval] [Tipe2]
        │
        ▼
Pilih root note akor pertama (bebas, sesuai kunci lagu)
        │
        ▼
Hitung root note akor kedua menggunakan tabel interval
di [[Interval Musik & Semitone System]]
        │
        ▼
Bangun kedua akor di FL Studio Piano Roll
        │
        ▼
Susun progresi: ulangi CR, tambah variasi, atau sambung ke CR lain
```

## 🛠️ Cara Pakai di FL Studio
1. Buka Piano Roll, tentukan tempo dan kunci nada lagu (misal: root = A, kunci A Minor).
2. Tentukan emosi momen yang ingin dikomposisi — cari CR yang sesuai di [[CR Reference & Emotion Lookup]].
3. Bangun akor pertama sesuai tipe (M atau m) di bar 1.
4. Hitung root note akor kedua: gunakan tabel interval dari [[Interval Musik & Semitone System]].
5. Bangun akor kedua sesuai tipe di bar 2.
6. Dengarkan transisi — jika sudah pas, ulangi atau variasikan inversions untuk warna yang berbeda.
7. Untuk BGM yang lebih panjang, rangkai beberapa CR — misalnya CR untuk membangun ketegangan diikuti CR resolusi di akhir loop.

## 🔗 Lihat Juga
- [[Interval Musik & Semitone System]] — prasyarat wajib untuk membaca notasi CR.
- [[CR Reference & Emotion Lookup]] — tabel lengkap semua CR beserta emosi dan Twin CR-nya.
- [[Chord Progression & Emosi untuk Game]] — pendekatan alternatif yang lebih kasual (Am-F-C-G style) untuk referensi cepat.
- [[Teori Musik Dasar untuk Game BGM]] — konteks penggunaan CR dalam komposisi BGM game secara keseluruhan.
