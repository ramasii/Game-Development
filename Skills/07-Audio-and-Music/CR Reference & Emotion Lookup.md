# 📖 CR Reference & Emotion Lookup
#audio #music #chord-relationship #emotion #reference #fl-studio

## 🎯 Apa Ini?
Tabel referensi lengkap seluruh Chord Relationship (CR) dari buku *Chord Relationships and Emotion* — diorganisir dua cara: **per kombinasi tipe akor** (untuk yang sudah tahu CR-nya) dan **per target emosi** (untuk yang tahu feel-nya tapi belum tahu CR-nya). Gunakan skill ini sebagai kamus cepat saat duduk di FL Studio dan butuh CR yang tepat untuk momen tertentu di game.

**Kapan digunakan:**
- Saat tahu emosi yang diinginkan tapi bingung mau pakai akor apa → gunakan Tabel B (Emotion Lookup).
- Saat sudah punya pasangan akor dan ingin tahu emosi yang dihasilkan → gunakan Tabel A (CR Reference).
- Saat mengkomposisi BGM untuk momen spesifik: boss fight, cutscene, area eksplorasi, dll.
- **Prasyarat**: pahami [[Chord Relationship (CR) Framework]] dan [[Interval Musik & Semitone System]].

---

## 🧠 Poin Penting

> Tidak ada CR yang "mutlak" — emosi yang dihasilkan bersifat subjektif dan bisa bervariasi per pendengar. Gunakan tabel ini sebagai titik awal, bukan hukum baku.

> **Twin CR** = dua CR yang menggunakan pasangan akor yang sama tapi urutan terbalik. Mereka tidak selalu punya emosi yang sama — bisa kontras atau mirip.

---

## 🧩 Tabel A — CR Reference (Diurutkan per Tipe Akor)

### Minor → Minor (m _ m)

| CR | Twin | Emosi |
|---|---|---|
| m ii m | m VII m | Mysterious & Tense |
| m II m | m vii m | A Little Uneasy |
| m iii m | m VI m | Otherworldly & Ominous (dipakai di dungeon Skyrim) |
| m III m | m vi m | Ominous & Dark |
| m IV m | m V m | Tragic |
| m IV+ m | — | Antagonism & Danger (less character-based) |
| m V m | m IV m | Tragic |
| m vi m | m III m | Antagonism & Evil (more character-based) |
| m VI m | m iii m | Otherworldly & Ominous |
| m vii m | m II m | A Little Uneasy |
| m VII m | m ii m | Mysterious & Tense |

### Major → Major (M _ M)

| CR | Twin | Emosi |
|---|---|---|
| M ii M | M VII M | Exotic & Enchanting / Cowboy / Enchanted Forest |
| M II M | M vii M | Protagonism |
| M iii M | M VI M | Heroic (terkenal dari Lord of the Rings) |
| M III M | M vi M | Fantastical |
| M IV M | M V M | Good Energy |
| M IV+ M | — | Outer Space |
| M V M | M IV M | Good Energy |
| M vi M | M III M | Fantastical (dipakai di film E.T.) |
| M VI M | M iii M | Heroic |
| M vii M | M II M | Protagonism |
| M VII M | M ii M | Exotic & Enchanting / Cowboy / Enchanted Forest |

### Minor → Major (m _ M)

| CR | Twin | Emosi |
|---|---|---|
| m ii M | M VII m | Cautious but Optimistic |
| m II M | M vii m | Mysterious / Dark Comedy |
| m iii M | — | Rising Action / Tension |
| m III M | M vi m | Powerful & Mysterious |
| m IV M | M V m | Wonder & Transcendence |
| m IV+ M | M IV+ m | Outer Space |
| m V M | — | Bittersweet |
| m vi M | — | Resolution |
| m VI M | M iii m | Tense & Mysterious |
| m vii M | M II m | Bittersweet |
| m VII M | M ii m | Dramatic (populer di awal abad 21) |

### Major → Minor (M _ m)

| CR | Twin | Emosi |
|---|---|---|
| M ii m | m VII M | Dramatic |
| M II m | m vii M | Bittersweet |
| M iii m | m VI M | Tense & Mysterious |
| M III m | — | Sadness & Loss |
| M IV m | — | Romantic / Exotic (rising tension) |
| M IV+ m | m IV+ M | Outer Space |
| M V m | m IV M | Wonder & Transcendence |
| M vi m | m III M | Powerful & Mysterious |
| M VI m | — | Otherworldly / Heavenly |
| M vii m | m II M | Mysterious / Dark Comedy |
| M VII m | m ii M | Cautious but Optimistic |

---

## 🧩 Tabel B — Emotion Lookup (Diurutkan per Target Emosi)

### 😢 Sad / Dark

| Target Emosi | CR yang Bisa Dipakai |
|---|---|
| Sadness & Loss | M III m |
| Tragic | m IV m / m V m |
| Ominous & Dark | m III m / m vi m |
| Otherworldly & Ominous | m iii m / m VI m |
| A Little Uneasy | m II m / m vii m |
| Antagonism & Danger | m IV+ m |
| Antagonism & Evil (character) | m vi m / m III m |
| Mysterious & Tense | m ii m / m VII m |

### 😐 Neutral / Ambiguous

| Target Emosi | CR yang Bisa Dipakai |
|---|---|
| Bittersweet | m V M / m vii M / M II m |
| Dramatic | m VII M / M ii m |
| Mysterious / Dark Comedy | m II M / M vii m |
| Outer Space | m IV+ M / M IV+ m / M IV+ M |
| Tense & Mysterious | m VI M / M iii m |
| Cautious but Optimistic | M VII m / m ii M |
| Rising Action / Tension | m iii M |
| Powerful & Mysterious | m III M / M vi m |

### 😊 Good / Positive

| Target Emosi | CR yang Bisa Dipakai |
|---|---|
| Resolution | m vi M |
| Romantic / Exotic | M IV m |
| Otherworldly / Heavenly | M VI m |
| Wonder & Transcendence | m IV M / M V m |
| Protagonism | M II M / M vii M |
| Heroic | M iii M / M VI M |
| Fantastical | M III M / M vi M |
| Good Energy | M IV M / M V M |
| Exotic / Cowboy / Enchanted Forest | M ii M / M VII M |

---

## 🛠️ Cara Pakai
1. **Tahu emosinya?** → Buka Tabel B, temukan emosi target, ambil CR-nya.
2. **Tahu CR-nya?** → Buka Tabel A, cari CR untuk konfirmasi emosi dan Twin-nya.
3. Baca CR menggunakan [[Chord Relationship (CR) Framework]] → hitung root note akor kedua menggunakan [[Interval Musik & Semitone System]].
4. Bangun kedua akor di FL Studio Piano Roll.
5. Coba juga Twin CR-nya — kadang Twin punya nuansa yang lebih pas untuk konteks spesifik.
6. Jika CR terasa kurang pas, coba CR lain dari kategori emosi yang sama di Tabel B.

## 🔗 Lihat Juga
- [[Chord Relationship (CR) Framework]] — cara membaca dan menggunakan notasi CR.
- [[Interval Musik & Semitone System]] — cara menghitung root note akor kedua dari interval CR.
- [[Chord Progression & Emosi untuk Game]] — referensi alternatif dengan format progresi akor konvensional (Am-F-C-G style).
- [[Teori Musik Dasar untuk Game BGM]] — konteks penggunaan CR dalam komposisi BGM game secara keseluruhan.
- [[Chord Relationships and Emotion.pdf]] — sumber asli seluruh tabel di skill ini.
