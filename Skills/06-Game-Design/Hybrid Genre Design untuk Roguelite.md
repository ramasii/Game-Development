# 🧬 Hybrid Genre Design untuk Roguelite
#game-design #roguelite #usp #genre #market-research

## 🎯 Apa Ini?
Framework untuk menciptakan *Unique Selling Point* (USP) yang kuat dengan mengawinkan mekanik Roguelite dengan genre yang biasanya bertolak belakang. Hasilnya adalah game yang langsung bisa dijelaskan dalam satu kalimat ("*Factorio* tapi jadi Roguelite") dan memenangkan perhatian pemain dalam 5 detik pertama trailer.

**Kapan harus digunakan:**
- Saat brainstorming ide game baru dan butuh USP yang tajam.
- Saat ide game terasa "biasa saja" atau terlalu mirip kompetitor.
- Saat riset pasar menunjukkan niche tertentu yang belum digarap.
- **Tidak cocok** digunakan saat game sudah punya USP yang kuat dan hanya butuh dipoles.

---

## 🧠 Poin Penting

### Mengapa Hybrid Genre Bekerja di Pasar Steam?
Pemain Steam sangat familiar dengan aturan dasar genre klasik (Catur, Minesweeper, otomasi). Dengan menggunakan "bahasa" yang sudah mereka kenal sebagai fondasi, game tidak perlu mengajarkan aturan dasar dari nol — energi onboarding langsung difokuskan ke **twist Roguelite yang unik**. Ini mengurangi barrier masuk sekaligus meningkatkan daya tarik pasar (*streamable*, mudah dijelaskan).

### 3 Kategori Hybrid yang Terbukti

**Kategori A — Roguelite + Sistem Logika/Otomasi**
Menggantikan kontrol karakter langsung dengan sistem logika (conveyor belt, node, IF/THEN). Pemain membangun mesin, bukan mengendalikan pahlawan.
- Target pemain: *The Professor* — analitis, suka optimasi sistem.
- Contoh konsep: *ManaForge* (pabrik otomatis mempertahankan Core dari serbuan monster).
- Keunggulan: Sangat cocok untuk programmer karena arsitektur datanya murni logika input-output.
- Tantangan: Tutorialnya harus sangat hati-hati — sistem otomasi bisa overwhelm pemain baru.

**Kategori B — Roguelite + Board Game Klasik**
Mengambil aturan board game global (Scrabble, Uno, Battleship, Minesweeper) lalu menambahkan lapisan Roguelite di atasnya. Pemain sudah mengenal aturan dasarnya → langsung fokus ke sinergi item.
- Target pemain: *The Professor* dengan sentuhan nostalgia.
- Contoh konsep dari riset:
  - Scrabble + Dungeon: menyerang musuh dengan menyusun kata dari huruf acak.
  - Minesweeper + Dungeon Crawler: setiap lantai adalah papan ranjau.
  - Uno + Deckbuilder: kartu aksi (Skip/Reverse/Draw 4) bisa dimodifikasi jadi senjata.
- Keunggulan: Marketing/pitching sangat mudah — bisa dijelaskan dalam satu kalimat.

**Kategori C — Roguelite + Mekanik Kematian Subversif**
Bukan mengganti genre, tapi mengubah konsekuensi kematian menjadi sesuatu yang unik dan tidak biasa.
- *Evolusi Ekosistem*: Monster yang membunuhmu berevolusi dan naik jabatan di run berikutnya (mirip Nemesis System dari Shadow of Mordor).
- *Mekanik Warisan*: Karakter berikutnya mewarisi sifat psikologis karakter sebelumnya yang mati (trauma, keahlian, atau debuff unik).
- Keunggulan: Sangat *streamable* — momen "bos ini yang bunuh aku minggu lalu, sekarang dia jadi raja!" adalah konten viral.

### Formula Pitching USP
```
"[Genre Familiar] + [Twist Roguelite Unik]"
= "Pabrik otomatis (Factorio) tapi setiap run mesinnya acak dan pemain bisa mati"
= "Minesweeper tapi jadi dungeon crawler 3D dengan item RPG"
= "UNO tapi kartu aksinya bisa diupgrade jadi senjata mematikan"
```

### Validasi USP Sebelum Produksi
Sebuah USP layak dilanjutkan ke produksi jika memenuhi 3 kriteria:
1. **Bisa dijelaskan dalam 1 kalimat** kepada orang yang tidak main game.
2. **Ada niche pasar yang belum terisi** (cek di SteamDB/VG Insights).
3. **Prototype core mechanic-nya terbukti fun dalam 1–2 minggu** — bukan fun di atas kertas saja.

---

## 🧩 Properties — Matriks Evaluasi Hybrid

| Kombinasi | Target Pemain | Tantangan Utama | Cocok untuk Solo Dev? |
|---|---|---|---|
| Roguelite + Otomasi/Factory | The Professor | Tutorial kompleks | ✅ Sangat cocok (programmer) |
| Roguelite + Board Game Klasik | The Professor + Nostalgia | Hak cipta nama IP | ✅ Cocok |
| Roguelite + Physics | The Ninja + Streamer | Optimasi performa | ⚠️ Butuh effort tinggi |
| Roguelite + Kematian Subversif | Semua tipe | Kompleksitas sistem narasi | ⚠️ Bergantung scope |
| Roguelite + Economy/Trading | The Professor | Keseimbangan ekonomi sangat tricky | ⚠️ Berisiko |

## 🔄 Alur Brainstorming Hybrid

```
Pilih "bahasa familiar" yang sudah dipahami pemain luas
        │
        ▼
Pilih mekanik Roguelite mana yang paling cocok digabungkan
(Broken Build / Permadeath / Metaprogression / Sense of Mastery)
        │
        ▼
Tulis USP dalam 1 kalimat: "[Familiar] + [Twist]"
        │
        ▼
Cek pasar: apakah niche ini sudah ada yang mengisi? (SteamDB)
        │
   Sudah ada ──▶ Apa differentiator tambahan yang bisa ditambahkan?
        │
        ▼
Buat prototype core mechanic dalam 1–2 minggu
        │
        ▼
Playtest: apakah mekanik utamanya terasa fun tanpa art/audio?
        │
   Tidak fun ──▶ Pivot atau ganti kombinasi
   Fun ──▶ Lanjut ke produksi penuh
```

## 🛠️ Cara Pakai
1. Mulai dengan daftar "genre familiar" yang kamu sendiri suka/mengerti aturannya — lebih mudah didesain.
2. Tulis minimal 5 kombinasi kandidat dalam format "X + Roguelite", lalu evaluasi dengan matriks di atas.
3. Pilih 1 kandidat terkuat, buat prototype **hanya core mechanic-nya** (tanpa art, audio, atau metagame) dalam 2 minggu.
4. Jika prototype terasa flat, identifikasi pilar mana dari [[Roguelite Design Pillars (4 Pilar Utama)]] yang belum hadir, lalu perkuat.
5. Setelah prototype fun, baru validasi pasar via SteamDB dan cek kompetitor langsung.

## 🔗 Lihat Juga
- [[Roguelite Design Pillars (4 Pilar Utama)]] — pastikan hybrid yang dipilih tetap memenuhi 4 pilar dasar.
- [[Player Persona & Motivasi (Quantic Foundry)]] — tentukan target pemain dari hybrid yang dirancang.
- [[Merancang Sistem Sinergi & Item Roguelite]] — setelah genre hybrid ditentukan, rancang sistem itemnya.
- [[Deconstruct Mechanics]] — bedah mekanik genre yang dipilih sebelum menggabungkannya.
