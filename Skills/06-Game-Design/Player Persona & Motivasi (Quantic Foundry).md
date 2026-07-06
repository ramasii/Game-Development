# 🧠 Player Persona & Motivasi (Quantic Foundry)
#game-design #player-psychology #market-research #quantic-foundry

## 🎯 Apa Ini?
Framework riset motivasi gamer dari **Quantic Foundry** — lembaga riset industri game — untuk memahami *mengapa* pemain memainkan suatu genre dan apa yang mereka cari. Digunakan untuk memastikan keputusan desain mekanik, tingkat kesulitan, dan sistem reward tepat sasaran ke tipe pemain yang dituju.

**Kapan harus digunakan:**
- Sebelum mulai merancang mekanik utama — untuk menentukan "untuk siapa game ini dibuat".
- Saat playtest menunjukkan feedback yang saling bertentangan dari tester yang berbeda (mungkin tipe persona mereka berbeda).
- Saat menentukan apakah game butuh metaprogression, leaderboard, konten sosial, atau narasi dalam sebagai daya tarik utama.
- **Tidak perlu** digunakan untuk proyek game jam singkat — hanya relevan untuk proyek yang butuh validasi pasar.

---

## 🧠 Poin Penting

### 6 Kluster Motivasi Quantic Foundry
Quantic Foundry membagi motivasi bermain game menjadi 6 kluster besar. Setiap pemain punya kombinasi skor yang unik dari kluster ini.

| Kluster | Sub-Motivasi | Ciri Pemain |
|---|---|---|
| **Action** | Destruction, Excitement | Suka ledakan, chaos, aksi cepat |
| **Social** | Competition, Community | Suka PvP, guild, konten multiplayer |
| **Mastery** | Challenge, Strategy | Suka tingkat kesulitan tinggi, theorycrafting |
| **Achievement** | Completion, Power | Suka 100% game, koleksi, build OP |
| **Immersion** | Fantasy, Story | Suka narasi dalam, dunia yang imersif |
| **Creativity** | Design, Discovery | Suka eksplorasi, membangun, eksperimen |

### Profil Pemain Roguelite
Pemain Roguelite secara konsisten skor **sangat tinggi** di dua kluster:

**Kluster MASTERY (Sangat Tinggi):**
- *Challenge*: Termotivasi oleh kesulitan tinggi yang menuntut latihan terus-menerus.
- *Strategy*: Suka pengambilan keputusan taktis di bawah tekanan ketidakpastian — "Apakah aku harus ambil item ini sekarang atau simpan slot untuk item berikutnya?"

**Kluster ACHIEVEMENT (Tinggi):**
- *Power*: Kepuasan dopamin saat kombo item membuat karakter menjadi sangat OP.
- *Completion*: Hasrat membuka semua karakter, koleksi jimat, mengisi ensiklopedia item.

### 2 Arketipe Pemain Roguelite

#### 🎓 The Professor (Sang Profesor)
- **Skor Tinggi**: Strategy + Discovery
- **Cocok untuk**: Turn-based Roguelite, Deckbuilder, Automation Roguelite (*Slay the Spire*, *Balatro*, *CloverPit*)
- **Persona**: Analitis, metodis, suka merencanakan jauh ke depan. Melihat game sebagai teka-teki matematika. Senang dengan variasi tak berujung dari sistem acak karena memberi ruang eksperimen luas.
- **Yang mereka benci**: RNG murni yang tidak bisa dipengaruhi strategi. Merasa "menang karena beruntung" sama tidak memuaskannya dengan kalah.

#### 🥷 The Ninja (Sang Ninja)
- **Skor Tinggi**: Challenge + Action
- **Cocok untuk**: Action Roguelite, Rogue-vania (*Hades*, *Dead Cells*, *The Binding of Isaac*)
- **Persona**: Menyukai tantangan yang menguji otak dan otot sekaligus. Menikmati aksi cepat, presisi input tombol yang ketat, kepuasan parry/dodge di saat kritis.
- **Yang mereka benci**: Game yang terlalu lambat atau terlalu "puzzle-y". Mereka butuh *immediate feedback* dari setiap input.

### Implikasi Desain Kritis
> Jika target pemainmu adalah **The Professor**: jangan buat game yang terlalu bergantung pada RNG murni. Berikan alat taktis untuk **memitigasi** keacakan (pilihan draft 3 item, re-roll terbatas, item yang membaca deck). Kemenangan harus bisa diklaim sebagai "hasil kejeniusan strategi saya", bukan "saya beruntung hari ini".

> Jika target pemainmu adalah **The Ninja**: pastikan *input feel* sangat responsif dan memuaskan. Kontrol yang sedikit saja terasa "slippy" akan merusak pengalaman. Prioritaskan *game feel* di atas segalanya.

---

## 🧩 Properties — Panduan Desain per Arketipe

| Elemen Desain | The Professor | The Ninja |
|---|---|---|
| **Tingkat Kesulitan** | Tinggi tapi bisa dipelajari (bukan RNG) | Tinggi dengan tekanan waktu/refleks |
| **Sistem Item** | Banyak sinergi kompleks, bisa theorycrafting | Item yang langsung terasa kuat saat dipegang |
| **Metaprogression** | Unlock konten baru untuk dieksplorasi | Unlock skill baru yang terasa "keren" dipakai |
| **Kecepatan Run** | Bisa lambat (30–60 menit), asal menarik | Cepat (15–30 menit), energi tinggi terus |
| **Tutorial** | Toleran terhadap onboarding panjang | Ingin langsung beraksi, tutorial harus singkat |
| **Feedback Visual** | Angka damage, log efek, statistik | Efek visual ledakan, slowmo, screen shake |

## 🔄 Alur Menentukan Target Persona

```
Tentukan genre & core mechanic game kamu
        │
        ▼
Tanya: "Momen terbaik di game ini adalah...?"
   Sinergi item OP → The Professor
   Berhasil dodge/parry bos → The Ninja
   Keduanya → coba hybrid, tapi hati-hati scope
        │
        ▼
Desain sistem reward, tingkat kesulitan, dan UI
sesuai preferensi arketipe yang dipilih
        │
        ▼
Pilih tester playtest yang sesuai arketipe target
(jangan minta The Ninja review game The Professor)
```

## 🛠️ Cara Pakai
1. Di awal proyek, tentukan dulu: "Game ini untuk The Professor, The Ninja, atau keduanya?" — menulis ini di GDD agar seluruh tim punya referensi yang sama.
2. Setiap keputusan desain yang ambigu, tanya: "Apa yang dipilih persona target kita di sini?"
3. Saat rekrut tester playtest, cari yang profil motivasinya sesuai arketipe target — feedback dari tipe pemain yang salah bisa menyesatkan.
4. Jika mau menjangkau keduanya: rancang *core loop* untuk The Professor, tapi pastikan *game feel* dan kontrol cukup responsif untuk memuaskan The Ninja juga.

## 🔗 Lihat Juga
- [[Roguelite Design Pillars (4 Pilar Utama)]] — pilar mana yang paling penting bergantung pada persona yang dituju.
- [[Hybrid Genre Design untuk Roguelite]] — pilihan genre hybrid juga bergantung pada persona target.
- [[Framework Kihon-Kata-Kumite (Learning Curve & Encounter Design)]] — The Ninja sangat menikmati struktur Kumite; The Professor lebih menikmati Kata.
- [[Identify Core Loops]] — selaraskan core loop dengan motivasi utama persona target.
