# 🗺️ Planning — Word Space (1 Bulan, Solo Dev)

> Rencana teknis detail implementasi Word Space, disusun sebagai pendamping [[GDD - Word Space]]. Fokus di bagian teknis (§4 Arsitektur Data & Design Pattern) yang belum masuk ringkas ke GDD.

---

## 🗓️ 1. Timeline Mingguan

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

---

## 🛤️ 2. Sistem Jalur Balapan (Curved Track, Multi-Lane)

Karena jalur balapan **berbelok, bukan lurus**, gerakan pembalap gak bisa cuma lerp posisi A→B. Rencana:

- **Tiap pembalap (4 total: 1 pemain + 3 bot) punya jalurnya sendiri (lane terpisah)** — gak ada tabrakan fisik antar racer, sesuai GDD. Artinya butuh **4 spline berbeda**, bukan 1 spline dipakai bareng
- **Authoring jalur:** pakai spline (Unity Splines package kalau tersedia, atau waypoint array manual sebagai fallback) — dibuat di scene, bisa belok bebas. 4 lane idealnya dibuat sejajar/mengikuti bentuk yang mirip (biar visual race-nya masih kerasa "bareng"), tapi secara data tetap independen per lane
- **Pergerakan berbasis jarak, bukan waktu:** tiap pembalap punya `currentDistance` (float, satuan jarak sepanjang lane-nya sendiri). Tiap frame: `currentDistance += currentSpeed * Time.deltaTime`
- **Masalah yang harus diantisipasi:** sampling spline langsung pakai parameter `t` (0-1) itu **gak seragam kecepatannya** — solusinya precompute **arc-length lookup table**, sekarang **satu tabel per lane** (detail implementasi di §3)
- Kalau waktu mepet, fallback sederhana: waypoint array + `Vector3.MoveTowards` antar waypoint berurutan per lane (kurang mulus di tikungan tapi jauh lebih cepat diimplementasi)

---

## 🧮 3. Arc-Length Table — Implementasi Detail (Per Lane)

Karena tiap racer jalan di lane sendiri, data arc-length table di-bake **4 kali** (satu per lane), disimpan sebagai array `LaneTrackData[4]`. Tiap racer di-assign 1 index lane pas masuk lobby (misal urutan slot 0-3), lalu selama race cuma query ke `LaneTrackData` miliknya sendiri.

**Bake (sekali aja pas track dibuat/di-load, bukan tiap frame — diulang buat tiap lane):**

```
struct LaneTrackData:
    positions[]
    cumulativeDistances[]
    totalLength

BakeLane(spline, N = 300-500):
    cumulativeDistances[0] = 0
    positions[0] = spline.EvaluatePosition(0)

    for i in 1..N:
        t = i / N
        positions[i] = spline.EvaluatePosition(t)
        cumulativeDistances[i] = cumulativeDistances[i-1] + Distance(positions[i-1], positions[i])

    totalLength = cumulativeDistances[N]
    return LaneTrackData
```

Idealnya `LaneTrackData` untuk keempat lane di-bake sekali di Editor dan disimpan sebagai asset (array of ScriptableObject atau 1 ScriptableObject berisi array 4 lane), bukan dihitung ulang tiap kali game jalan.

**Runtime lookup, dipanggil tiap racer tiap frame (pakai `LaneTrackData` milik lane-nya sendiri):**

```
GetPositionAtDistance(laneData, distance):
    distance = clamp(distance, 0, laneData.totalLength)
    (lo, hi) = binary search di laneData.cumulativeDistances buat range yang membungkus `distance`
    frac = (distance - cumulativeDistances[lo]) / (cumulativeDistances[hi] - cumulativeDistances[lo])
    pos = Lerp(laneData.positions[lo], laneData.positions[hi], frac)
    tangent = (laneData.positions[hi] - laneData.positions[lo]).normalized   // buat rotasi pesawat
    return pos, tangent
```

Binary search (`O(log N)`) per lane — dengan cuma 4 racer/4 lane, biayanya masih kecil.

**Catatan balancing:** kalau ke-4 lane panjangnya beda-beda (misal karena bentuk kurva berbeda), pastikan `totalLength` tiap lane dipakai konsisten pas hitung persentase progres soal (§5) dan pacing (§4) — biar gak ada lane yang "curang" karena jaraknya lebih pendek/panjang dari lane lain. Paling aman: desain 4 lane dengan `totalLength` yang sama persis (rute beda, jarak sama).

---

## 📐 4. Matematika Pacing (Target Waktu Race)

Target desain: main cepat, rata-rata **20 soal/menit**, jawab benar semua → race selesai **~50 detik**.

Ini yang bikin arc-length approach worth-it: karena `currentDistance` itu abstraksi jarak sepanjang lane (independen dari bentuk kurva), rumus timing di bawah berlaku sama aja mau lane-nya lurus atau belok-belok — curve gak ngerusak balancing. (Rumus ini pakai `totalLength` lane pemain sendiri; lihat catatan di §3 soal kenapa semua lane sebaiknya sama panjang.)

Variabel:
- `L` = total panjang lane (`totalLength` dari §3)
- `v_base` = kecepatan jelajah dasar (tanpa boost aktif)
- `m` = 1.5 (boost multiplier)
- `d` = 2 detik (durasi boost per jawaban benar)
- `T_q` = interval soal = 60/20 = **3 detik**

**Model yang dipakai: drop-off** — karena `d` (2s) < `T_q` (3s), tiap siklus soal ada 1 detik gap balik ke kecepatan normal sebelum boost berikutnya nyala. Kecepatan rata-rata per siklus:

```
avgSpeed = v_base × (m×d + (T_q − d)) / T_q
         = v_base × (1.5×2 + 1) / 3
         = v_base × 1.33
```

Target: `avgSpeed × 50 = L`, jadi:

```
v_base = L / (50 × 1.33) = L / 66.7
```

**Contoh angka** (misal lane panjangnya 400 unit): `v_base ≈ 6 unit/detik`. Ini titik awal buat playtest, bukan angka final — begitu track asli udah jadi dan `totalLength` kepake dari tabel §3, tinggal masukin ke rumus ini buat dapet `v_base` yang pas, daripada nebak-nebak dari nol.

---

## 📚 5. Data & Sistem Soal

- **`WordEntry` (ScriptableObject):** kata Indonesia, jawaban Inggris benar, list distractor (semantically related), tier kesulitan, audio clip voice over
- **`WordBank` (ScriptableObject, Flyweight):** kumpulan `WordEntry` per kategori (mulai 1 kategori dulu sesuai scope GDD)
- **`QuestionManager`:** ambil soal berdasarkan progres race (persentase `currentDistance` racer terhadap `totalLength` lane-nya) → tier gampang di awal, makin susah mendekati finish, sesuai [[Model Progress Curves]]

---

## 🚀 6. Boost / Slow System

- Event: `OnAnswerCorrect` / `OnAnswerWrong` (lewat [[Observer Pattern Events]])
- Tiap racer punya `speedMultiplier` yang di-modifikasi sementara (boost 1.5x selama 2 detik, slow 0.6x selama 1.5 detik), lalu lerp balik ke 1x — model drop-off dipakai (lihat §4), bukan refresh-duration
- Feedback nempel di event yang sama: particle trail (boost), screen-shake/slow-mo ringan (salah), SFX — semua dipicu dari event, bukan dicek manual tiap frame

---

## 🤖 7. Bot AI & Rubber-Banding

- Bot gak perlu AI kompleks — tiap interval waktu random, bot "jawab" benar/salah berdasarkan probabilitas dasar
- Rubber-banding: kalau bot posisinya jauh di belakang pemain (dibandingkan lewat `currentDistance / totalLength` masing-masing, bukan posisi world-space — karena tiap lane beda jalur), naikkan probabilitas benar bot itu sementara (dan sebaliknya kalau bot terlalu jauh di depan) — tuning angka dasarnya pakai [[Establish Math Anchors]]

---

## 🔊 8. Voice Over & Audio

- Playback dipicu lewat event channel ([[Decoupled Audio System (Event Channel & Pooling)]]) — jadi soal, jawaban benar/salah, dan voice line semuanya lewat jalur yang sama, gampang di-reuse
- Minimal viable: rekam/generate voice line kata Indonesia + 1 line feedback benar + 1 line feedback salah dulu, baru nambah variasi kalau waktu masih ada

---

## 🔁 9. Game State & Scene Flow

State (FSM enum, [[Simple FSM Berbasis Enum (Game State Prototyping)]]):
`MainMenu → NameInput → Lobby → Countdown → Racing → Finish → Result`

Dikontrol lewat satu [[Centralized State Manager (GameManager Singleton & Event)]] biar transisi antar scene/UI konsisten dan gampang di-debug.

---

## 🖼️ 10. UI

- Pola [[MVP Pattern (Unity UI)]] — Presenter baca data race (posisi, ranking, soal salah) dan update View, View gak nyentuh data langsung
- Result screen cuma update pas race selesai, pakai [[Dirty Flag Pattern (Unity)]] biar gak refresh tiap frame pas racing

---

## ⚠️ 11. Risiko Teknis (Urutan Prioritas Ditangani)

1. **Arc-length movement multi-lane di jalur belok** — paling berisiko, kerjain di W1 duluan sebelum sistem lain nempel di atasnya; termasuk mastiin 4 lane punya `totalLength` yang konsisten/sama
2. **Balancing bot rubber-banding** — race harus tetep seru walau pemain salah beberapa kali, butuh beberapa iterasi playtest
3. **Voice over asset production** — kalau rekam sendiri makan waktu, siapkan fallback TTS sementara buat prototyping

---

## 🔗 Lihat Juga

- [[GDD - Word Space]] — konsep, core loop, dan scope lengkap
