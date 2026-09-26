# 📄 GDD - Word Space (Ringkas)

> Tugas Kuliah — Gamifikasi Edukasi. Racing Quiz Bahasa Inggris, single player vs bot, Unity, target anak TK.

## 1. Identitas

| Info | Detail |
|------|--------|
| **Judul** | Word Space |
| **Jenis** | Gamifikasi (1 dari 3 tugas game edukasi kuliah) |
| **Target Subject** | B2B — Guru/sekolah TK swasta |
| **End User** | Anak TK (belum fasih baca) |
| **Engine** | Unity 6 (URP) |
| **Mode** | Single player vs 3 bot, race, tanpa multiplayer |
| **Visual** | Space race — pesawat luar angkasa |

## 2. Konsep Game

Balapan pesawat luar angkasa 4 peserta (1 pemain asli + 3 bot nama random). Sepanjang race, pemain dikasih soal cocok-kata: 1 kata Bahasa Indonesia (contoh "kursi") + beberapa pilihan kata Bahasa Inggris (contoh "chair", "table", "sofa"). Jawaban benar → pesawat dapet boost. Jawaban salah → pesawat melambat. Siapa yang sampai finish duluan, menang.

Karena target-nya anak TK yang belum fasih baca, semua soal dibantu **voice over** (kata dibacain) + **ilustrasi gambar**, teks cuma pelengkap, bukan satu-satunya jalur pemahaman.

Tiap pembalap (pemain + 3 bot) punya **jalurnya masing-masing (lane terpisah)** — gak ada tabrakan antar pembalap sepanjang race. Posisi menang/kalah murni ditentuin dari siapa yang paling jauh/cepat nyampe finish, bukan dari senggolan fisik.

## 3. Core Gameplay Loop

`MAIN MENU → START → INPUT NAMA → NEXT → LOBBY → START → LEVEL → COUNTDOWN → BERMAIN → FINISH → RESULT → PLAY AGAIN → (kembali ke INPUT NAMA)`

- **Main Menu → Start:** entry point
- **Input Nama:** pemain ketik nama sendiri (manual, bukan avatar-picker)
- **Lobby:** nampilin 4 pembalap — 1 pemain asli + 3 bot dengan nama random
- **Level → Countdown → Bermain:** race berjalan, soal muncul berkala; benar = boost, salah = slow down
- **Finish → Result:** race selesai begitu semua pembalap nyampe / waktu abis
- **Play Again:** balik ke input nama buat sesi baru

## 4. Result Screen

Disederhanain, cuma 2 komponen:
- **Ranking** 4 pembalap (posisi finish)
- **List pertanyaan yang dijawab salah** — soal + semua pilihan jawaban, jawaban benar di-highlight

(Waktu tempuh, akurasi, dan rata-rata soal/menit sengaja dihilangin biar result screen fokus dan gak kebanyakan angka buat anak TK.)

## 5. Desain Soal

- Distractor **semantically related** (misal semua furniture: "chair", "table", "sofa") — biar bener-bener nguji vocab, bukan tebak-tebakan dari familiarity
- **Progresi kesulitan** sepanjang race — awal kata umum/gampang, makin ke finish makin jarang dipakai
- **Bot rubber-banding** — bot yang ketinggalan jauh dikasih sedikit speed-up, biar race tetep deg-degan sampai akhir
- Feedback boost/slow harus kerasa instan: particle trail pas boost, screen-shake/slow-mo ringan pas salah

## 6. Referensi

- Racing-quiz mechanic (benar = boost, salah = slow, ranking di akhir) — pola umum game quiz-race edukasi
- Voice over + gambar + teks sebagai jalur ganda pemahaman, dirancang khusus buat audiens pra-literasi (anak TK)

## 7. Arsitektur & Skill Vault yang Dipakai

### Game state & flow
- [[Simple FSM Berbasis Enum (Game State Prototyping)]] — state Menu/InputNama/Lobby/Countdown/Playing/Finish/Result
- [[Centralized State Manager (GameManager Singleton & Event)]] — single source kontrol transisi antar state di atas

### Event & komunikasi antar sistem
- [[Observer Pattern Events]] — broadcast event jawaban benar/salah ke sistem boost pesawat, UI, dan audio tanpa coupling langsung
- [[Decoupled Audio System (Event Channel & Pooling)]] — voice over kata + SFX boost/slow dipicu lewat event channel, gampang di-reuse tiap soal

### Data soal & bot
- [[Flyweight Pattern (Unity Shared Data)]] — bank kata (pasangan Indonesia-Inggris + distractor) disimpan sebagai shared data (ScriptableObject), dipakai bareng oleh semua instance soal
- [[Factory Pattern (Unity)]] — spawn 3 bot racer dengan nama random & profil kecepatan berbeda tiap sesi

### UI
- [[MVP Pattern (Unity UI)]] — pemisahan logic vs tampilan buat layar Lobby, HUD soal, dan Result
- [[Dirty Flag Pattern (Unity)]] — update UI ranking/hasil cuma pas datanya berubah, hindari refresh tiap frame

### Balancing
- [[Model Progress Curves]] — kurva progresi kesulitan soal sepanjang race
- [[Establish Math Anchors]] — angka dasar buat tuning kecepatan boost/slowdown & rubber-banding bot

### Prinsip umum
- [[SOLID Principles (Unity)]] — dipegang sebagai baseline semua sistem di atas

## 8. Scope (1 Bulan, Solo Dev)

- Fokus 1 kategori kata dulu (misal benda sehari-hari) — cukup buat validasi loop utama sebelum nambah kategori lain
- 1 track race, tiap pembalap di lane terpisah, 3 bot dengan variasi kecepatan/rubber-banding sederhana
- Voice over minimal: kata soal + feedback benar/salah, belum perlu full narasi cerita

## 🔗 Lihat Juga

- Konsep dibahas dari sesi brainstorming gamifikasi kuliah (3 tipe game edukasi: serious game, gamifikasi, game edu)
- [[Planning - Word Space]] — rencana teknis detail implementasi
