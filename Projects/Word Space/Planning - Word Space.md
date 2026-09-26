# 🗺️ Planning - Word Space (1 Bulan, Solo Dev)

> Rencana teknis detail buat implementasi Word Space. Referensi: [[GDD - Word Space]].

## 1. Timeline Mingguan

| Target | W1 | W2 | W3 | W4 |
|---|---|---|---|---|
| **Track & Movement System** | x | x | | |
| **Game State & Scene Flow** | x | | | |
| **Word Bank & Question System** | | x | | |
| **Boost/Slow & Answer Feedback** | | x | x | |
| **Bot AI & Rubber-Banding** | | | x | |
| **Voice Over & Audio Integration** | | | x | |
| **UI (Lobby, HUD, Result)** | x | | x | x |
| **Polishing & Playtesting** | | | | x |

## 2. Sistem Jalur Balapan (Curved Track)

Karena jalur balapan **berbelok, bukan lurus**, gerakan pembalap gak bisa cuma lerp posisi A→B. Rencana:

- **Authoring jalur:** pakai spline (Unity Splines package kalau tersedia, atau waypoint array manual sebagai fallback) — dibuat di scene, bisa belok bebas
- **Pergerakan berbasis jarak, bukan waktu:** tiap pembalap punya `currentDistance` (float, satuan jarak sepanjang track). Tiap frame: `currentDistance += currentSpeed * Time.deltaTime`
- **Masalah yang harus diantisipasi:** sampling spline langsung pakai parameter `t` (0-1) itu **gak seragam kecepatannya** — di bagian lurus kerasa cepat, di tikungan kerasa lambat (karena `t` gak proporsional sama jarak asli). Solusinya: precompute **arc-length lookup table** pas track dibikin (sampling banyak titik sepanjang spline, itung jarak kumulatif), terus pas runtime convert `currentDistance → t` lewat tabel ini, baru sample posisi + tangent (buat rotasi pesawat ngikutin arah belokan)
- Kalau waktu mepet, fallback sederhana: waypoint array + `Vector3.MoveTowards` antar waypoint berurutan (kurang mulus di tikungan tapi jauh lebih cepat diimplementasi)

## 3. Data & Sistem Soal

- **`WordEntry` (ScriptableObject):** kata Indonesia, jawaban Inggris benar, list distractor (semantically related), tier kesulitan, audio clip voice over
- **`WordBank` (ScriptableObject, Flyweight):** kumpulan `WordEntry` per kategori (mulai 1 kategori dulu sesuai scope GDD)
- **`QuestionManager`:** ambil soal berdasarkan progres race (persentase `currentDistance` terhadap total panjang track) → tier gampang di awal, makin susah mendekati finish, sesuai [[Model Progress Curves]]

## 4. Boost / Slow System

- Event: `OnAnswerCorrect` / `OnAnswerWrong` (lewat [[Observer Pattern Events]])
- Tiap racer punya `speedMultiplier` yang di-modifikasi sementara (misal boost 1.5x selama 2 detik, slow 0.6x selama 1.5 detik), lalu lerp balik ke 1x
- Feedback nempel di event yang sama: particle trail (boost), screen-shake/slow-mo ringan (salah), SFX — semua dipicu dari event, bukan dicek manual tiap frame

## 5. Bot AI & Rubber-Banding

- Bot gak perlu AI kompleks — tiap interval waktu random, bot "jawab" benar/salah berdasarkan probabilitas dasar
- Rubber-banding: kalau bot posisinya jauh di belakang pemain, naikkan probabilitas benar bot itu sementara (dan sebaliknya kalau bot terlalu jauh di depan) — tuning angka dasarnya pakai [[Establish Math Anchors]]

## 6. Voice Over & Audio

- Playback dipicu lewat event channel ([[Decoupled Audio System (Event Channel & Pooling)]]) — jadi soal, jawaban benar/salah, dan voice line semuanya lewat jalur yang sama, gampang di-reuse
- Minimal viable: rekam/generate voice line kata Indonesia + 1 line feedback benar + 1 line feedback salah dulu, baru nambah variasi kalau waktu masih ada

## 7. Game State & Scene Flow

State (FSM enum, [[Simple FSM Berbasis Enum (Game State Prototyping)]]):
`MainMenu → NameInput → Lobby → Countdown → Racing → Finish → Result`

Dikontrol lewat satu [[Centralized State Manager (GameManager Singleton & Event)]] biar transisi antar scene/UI konsisten dan gampang di-debug.

## 8. UI

- Pola [[MVP Pattern (Unity UI)]] — Presenter baca data race (posisi, ranking, soal salah) dan update View, View gak nyentuh data langsung
- Result screen cuma update pas race selesai, pakai [[Dirty Flag Pattern (Unity)]] biar gak refresh tiap frame pas racing

## 9. Risiko Teknis (urutan prioritas ditangani)

1. **Arc-length movement di jalur belok** — paling berisiko, kerjain di W1 duluan sebelum sistem lain nempel di atasnya
2. **Balancing bot rubber-banding** — race harus tetep seru walau pemain salah beberapa kali, butuh beberapa iterasi playtest
3. **Voice over asset production** — kalau rekam sendiri makan waktu, siapkan fallback TTS sementara buat prototyping

## 🔗 Lihat Juga

- [[GDD - Word Space]] — konsep, core loop, dan scope lengkap
