# 🎵 Teori Musik untuk Game (BGM & Adaptive Audio)

Membuat musik untuk game (_Background Music_ atau BGM) dari nol sangat bisa dilakukan! Teori musik untuk game sedikit berbeda dengan musik konvensional karena fokus utamanya adalah membangun **atmosfer** dan **looping (putaran lagu tanpa putus)**, bukan sekadar memamerkan teknik yang rumit.

Sebagai pemula, tidak perlu langsung menghafal seluruh buku teori klasik. Cukup kuasai 4 pilar teori musik yang paling krusial untuk kebutuhan game, lalu pahami teknik _Adaptive Audio_ dan matematika sinkronisasi waktu musik.

---

## 🥁 Bagian 1: 4 Pilar Teori Musik untuk Game BGM

### 1. Tempo & Rhythm (Pengatur Detak Jantung Game)

Tempo diukur dalam **BPM (Beats Per Minute)**. Ini adalah fondasi utama yang menentukan seberapa cepat adrenalin pemain berpacu.

| Range BPM | Nuansa | Cocok Untuk |
|---|---|---|
| 60 – 80 BPM | Lambat, tenang | Area eksplorasi, menu utama, momen sedih |
| 100 – 120 BPM | Sedang | Level platformer santai, teka-teki, kota hub |
| 140 – 180+ BPM | Cepat, intens | Boss fight, kejar-kejaran, fase aksi |

### 2. Harmoni & Skala (Sakelar Emosi Pemain)

Skala musik (_scale_) adalah kumpulan nada yang berkerabat dekat. Pilih skala yang tepat untuk langsung mengubah psikologis pemain:

- **Major Scale (Tangga Nada Mayor):** Terdengar cerah, bahagia, heroik, dan aman. Cocok untuk desa pertama atau layar kemenangan.
- **Minor Scale (Tangga Nada Minor):** Terdengar sedih, misterius, tegang, atau epik. Cocok untuk dungeon, malam hari, atau area musuh.

![[Chords Relationships.png]]

Menggabungkan jenis akor tertentu bisa langsung menciptakan spektrum emosi spesifik, mulai dari _Evil/Ominous_ untuk area bos hingga _Heroic_ untuk sang protagonis. Lihat juga referensi lengkap di [[Chord Relationships and Emotion.pdf]].

### 3. Motif atau Leitmotif (Identitas Suara)

Buat **Motif** — rangkaian 3 sampai 5 nada pendek yang sangat mudah diingat. Ketika motif itu diulang-ulang di sepanjang game dengan instrumen berbeda, pemain akan langsung tahu bahwa itu adalah identitas karakter atau area tersebut.

> Contoh: tema 5 nada pembuka _Super Mario Bros_ atau tema _Zelda_ — sederhana tapi sangat ikonik.

### 4. Struktur Looping (Transisi Tanpa Akhir)

BGM game harus bisa berputar terus-menerus tanpa membuat pemain sadar kapan lagu berakhir dan mulai kembali. Kuncinya ada pada **Resolusi Nada**:

> Jangan akhiri loop dengan nada dasar (_Tonic_) yang terlalu final. Biarkan nada terakhir menggantung agar telinga pemain "menuntut" untuk kembali ke baris pertama.

---

## 🎼 Bagian 2: Alur Membuat Loop BGM Pertama

Langkah terstruktur untuk merancang satu loop musik pendek (4 atau 8 bar) di DAW:

1. **Tentukan Emosi & Kecepatan** — Pilih area di game-mu. Misal: "Dungeon Lembab" → atur tempo ke **90 BPM** (agak lambat, terasa misterius).
2. **Pilih Skala A Minor** — Untuk pemula, skala A Minor hanya menggunakan tuts putih pada piano (A, B, C, D, E, F, G) sehingga dijamin tidak ada nada sumbang.
3. **Buat Progresi Akor** — Buat 4 bar, tiap bar bunyikan 3 nada bersamaan (_Chord Pad_). Coba urutan standar: **Am – F – C – G** (masing-masing 4 ketukan). Progresi ini sangat kuat untuk nuansa misterius.
4. **Tambahkan Melodi & Motif** — Di atas akor, buat melodi tipis menggunakan instrumen utama (suling atau synth). Terapkan prinsip Leitmotif: buat 4 nada unik di bar pertama, lalu variasikan sedikit di bar ketiga.
5. **Uji Sambungan Loop** — Pastikan ketukan terakhir di bar ke-4 mengalir mulus saat berputar ke bar ke-1. Jika terdengar patah, geser nada terakhir agar lebih dekat dengan nada pembuka.

---

## 🔄 Bagian 3: Adaptive Audio (Musik Dinamis)

Aksi pemain yang dinamis membuat musik game tidak boleh monoton. **Adaptive Audio** adalah teknik di mana aransemen dan struktur musik berubah secara _real-time_ berdasarkan _state_ atau kondisi di dalam game.

### Teknik A: Vertical Remixing (Layering)

Membagi satu lagu menjadi beberapa lapisan instrumen terpisah (_stems_) yang berjalan bersamaan secara sinkron. Game mengatur volume tiap lapisan sesuai situasi:

- **Layer 1 (Base):** Drum dan bass — aktif saat pemain menjelajah santai.
- **Layer 2 (Melody):** Gitar elektrik di-_fade in_ — saat musuh mulai mendekat.
- **Layer 3 (Intensity):** Orkestra/synth cepat masuk — saat darah kritis atau boss fight dimulai.

### Teknik B: Horizontal Resequencing

Memotong lagu secara horizontal menjadi segmen-segmen pendek (Intro, Eksplorasi, Ketegangan, Pertarungan, Victory). Game menyambung potongan ini berdasarkan aksi pemain. Agar perpindahan tidak terdengar patah, komposer biasanya membuat **Transition Segment** (jembatan nada) atau memanfaatkan ketukan bar berikutnya (_next bar transition_).

### Cara Implementasi di Game Engine

**Jalur A — Audio Middleware (FMOD / Wwise):** Standar industri. Import aset musik ke middleware, atur parameter logika (misal: variabel `DangerLevel` 0–100). FMOD/Wwise menangani transisi beat-matching otomatis. Game engine tinggal mengirim nilai parameter lewat kode singkat.

**Jalur B — Native Audio Mixer (Bawaan Game Engine):**
1. Ekspor semua layer (Base, Melody, Intensity) dengan **panjang file dan BPM yang persis sama** dari DAW.
2. Buat `Audio Mixer` di engine dengan beberapa Group (Jalur_Eksplorasi, Jalur_Pertarungan).
3. Picu semua `Audio Source` berjalan bersamaan (`Play()`) sejak scene dimulai. Set volume Jalur_Pertarungan ke -80 dB agar tidak terdengar dulu.
4. Saat mendeteksi musuh (`OnTriggerEnter`), gunakan `Mathf.Lerp` atau transisi snapshot mixer untuk naik-turunkan volume antar jalur secara perlahan.

> **Tips Penting:** Untuk _Vertical Layering_, pastikan semua instrumen di layer berbeda menggunakan **kunci nada (Key) yang sama**. Jika Layer 1 di A Minor, Layer 2 wajib di A Minor agar tidak terjadi tabrakan nada sumbang.

---

## 🧮 Bagian 4: Matematika Waktu Musik & DSP Scheduling

Transisi audio yang pas dengan ketukan (_beat-matching_) tidak bisa menggunakan logika coding biasa (`Update()` atau `deltaTime`) karena _frame rate_ game bersifat dinamis, sedangkan audio berjalan di sirkuitnya sendiri secara konstan pada tingkat sampel (_sample rate_, misal 44.100 sampel per detik).

Solusinya: gunakan prinsip **Matematika Waktu Musik** dan **DSP (Digital Signal Processing) Scheduling**.

### Rumus Dasar

**Rumus 1 — Durasi per Ketukan (Beat):**
$$\text{Duration per Beat (detik)} = \frac{60}{\text{BPM}}$$
Contoh (120 BPM): $\frac{60}{120} = 0.5$ detik per ketukan.

**Rumus 2 — Durasi per Bar (Birama):**
$$\text{Duration per Bar (detik)} = \text{Duration per Beat} \times \text{Jumlah Ketukan per Bar}$$
Contoh (birama 4/4): $0.5 \times 4 = 2.0$ detik per bar.

Artinya, setiap kelipatan 2 detik adalah posisi awal bar baru — waktu terbaik untuk memotong atau menyambung musik.

### Kuantisasi Waktu (Quantization)

Ketika pemain memicu state bertarung di tengah permainan, engine tidak boleh langsung memutar musik baru. Engine harus menunggu hingga ketukan berikutnya tiba.

Contoh: lagu sudah berjalan selama $t = 3.3$ detik. Kapan bar berikutnya tiba?
1. Bar yang terlewat: $\frac{3.3}{2.0} = 1.65$ bar.
2. Bulatkan ke atas: $\text{ceil}(1.65) = 2$ bar.
3. Target waktu transisi: $2 \times 2.0 = \textbf{4.0 detik}$.

Kode akan menjadwalkan musik pertempuran tepat pada detik ke-4.0 — transisi mulus tanpa menginterupsi ritme.

### Blueprint Kode (Unity C#)

Gunakan `AudioSettings.dspTime` (bukan `Time.time`) bersama `PlayScheduled()` untuk akurasi absolut:

```csharp
using UnityEngine;

public class BeatManager : MonoBehaviour
{
    public AudioSource explorationAudio;
    public AudioSource combatAudio;

    public float bpm = 120f;
    private float beatsPerBar = 4f;

    private float barDuration;
    private double songStartTime;

    void Start()
    {
        // 1. Hitung durasi satu bar dalam detik
        barDuration = (60f / bpm) * beatsPerBar;

        // 2. Catat waktu DSP saat lagu pertama dimulai
        songStartTime = AudioSettings.dspTime;

        explorationAudio.Play();
    }

    // Dipanggil ketika area berubah menjadi penuh musuh
    public void SwitchToCombat()
    {
        // 3. Ambil waktu DSP saat ini
        double currentTime = AudioSettings.dspTime;
        double timeElapsed = currentTime - songStartTime;

        // 4. Hitung kapan bar selanjutnya akan tiba (Kuantisasi)
        double barsPlayed = System.Math.Floor(timeElapsed / barDuration);
        double nextBarTime = songStartTime + ((barsPlayed + 1) * barDuration);

        // 5. Jadwalkan lagu pertempuran tepat di ketukan bar baru
        combatAudio.PlayScheduled(nextBarTime);

        // 6. Matikan lagu eksplorasi tepat di waktu yang sama
        explorationAudio.SetScheduledEndTime(nextBarTime);
    }
}
```

Dengan `PlayScheduled(nextBarTime)`, komputer memproses data audio beberapa milidetik sebelum lagu berbunyi — sehingga instrumen baru masuk seirama dengan ketukan drum dari lagu sebelumnya.
