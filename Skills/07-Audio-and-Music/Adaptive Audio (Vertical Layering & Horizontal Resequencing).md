# 🔄 Adaptive Audio (Vertical Layering & Horizontal Resequencing)
#audio #music #adaptive #game-feel #unity #fmod

## 🎯 Apa Ini?
Dua teknik membuat musik game yang berubah secara real-time berdasarkan kondisi (*state*) game — sehingga BGM terasa "hidup" dan responsif terhadap aksi pemain, bukan lagu statis yang diputar terus dari awal sampai akhir. Gunakan skill ini saat game sudah punya lebih dari satu state gameplay (eksplorasi, pertarungan, bahaya, victory) dan musiknya perlu transisi mulus antar state tersebut.

**Kapan digunakan:**
- Saat musik terasa monoton atau "tidak nyambung" dengan aksi di layar.
- Saat ingin musik bertransisi dari eksplorasi ke combat tanpa jeda atau potongan tiba-tiba.
- Saat membangun sistem BGM yang scalable untuk banyak area/state berbeda.
- **Tidak perlu** untuk game jam singkat atau prototype awal — implementasikan setelah core loop terbukti fun.

---

## 🧠 Poin Penting

### Teknik A — Vertical Remixing (Layering)
Satu lagu dipecah menjadi beberapa layer instrumen terpisah (*stems*) yang berjalan **bersamaan dan sinkron**. Game mengatur volume tiap layer sesuai situasi.

```
State: Eksplorasi  → Layer Base (drum+bass) ON, Layer Melody OFF, Layer Intensity OFF
State: Musuh Dekat → Layer Base ON,  Layer Melody FADE IN,  Layer Intensity OFF
State: Boss Fight  → Layer Base ON,  Layer Melody ON,        Layer Intensity ON
```

> **Aturan Wajib**: Semua layer harus diekspor dari DAW dengan **BPM dan panjang file yang persis sama**, dan harus berada dalam **kunci nada (Key) yang sama** — jika Layer 1 di A Minor, Layer 2 wajib A Minor juga. Jika berbeda kunci, nada akan sumbang saat dimix.

### Teknik B — Horizontal Resequencing
Lagu dipotong secara horizontal menjadi **segmen-segmen pendek** (Intro, Eksplorasi, Ketegangan, Pertarungan, Victory). Game menyambung segmen sesuai aksi pemain.

```
[Eksplorasi Loop] ──musuh terdeteksi──▶ [Transition Segment] ──▶ [Pertarungan Loop]
                                                                          │
                                                                    musuh mati
                                                                          │
                                                              [Victory Sting] ──▶ [Eksplorasi Loop]
```

> Untuk transisi yang mulus, buat **Transition Segment** (jembatan nada 1–2 bar) atau gunakan *next bar transition* — tunggu sampai bar berikutnya sebelum memotong ke segmen baru. Lihat [[Beat-Matching & DSP Scheduling (Unity)]] untuk implementasi teknis kuantisasi waktu ini.

### Perbandingan Dua Teknik

| | Vertical Layering | Horizontal Resequencing |
|---|---|---|
| **Cara kerja** | Naik-turunkan volume layer bersamaan | Sambung-potong segmen musik |
| **Transisi** | Sangat mulus (fade) | Lebih "klik" tapi butuh transition segment |
| **Kompleksitas aset** | Sedang (semua layer harus sama panjang) | Tinggi (banyak file segmen) |
| **Cocok untuk** | Perubahan intensitas bertahap | Perubahan state yang jelas (masuk boss room) |
| **Tool terbaik** | Native Unity Audio Mixer | FMOD / Wwise |

---

## 🧩 Properties — Pilihan Implementasi

### Jalur A — FMOD / Wwise (Standar Industri)
- Import aset musik ke middleware, atur parameter logika (misal: variabel `DangerLevel` 0–100 atau `GameState` enum).
- FMOD/Wwise menangani beat-matching dan transisi otomatis.
- Game engine hanya perlu kirim nilai parameter lewat 1–2 baris kode.
- **Cocok untuk**: proyek skala medium ke atas, atau jika sudah familiar dengan middleware audio.

### Jalur B — Native Unity Audio Mixer
1. Ekspor semua layer dari DAW dengan BPM & panjang file identik.
2. Buat `Audio Mixer` dengan Group terpisah per layer (Group_Base, Group_Melody, Group_Intensity).
3. Jalankan semua `AudioSource` bersamaan sejak scene load (`audioSource.Play()`). Set volume layer non-aktif ke -80 dB.
4. Saat state berubah, gunakan `AudioMixer.SetFloat()` atau transisi **Snapshot** mixer untuk fade antar layer.

```csharp
// Contoh: transisi ke state Combat menggunakan Snapshot
public AudioMixerSnapshot explorationSnapshot;
public AudioMixerSnapshot combatSnapshot;

public void SwitchToCombat()
{
    combatSnapshot.TransitionTo(transitionTime: 1.5f);
}

public void SwitchToExploration()
{
    explorationSnapshot.TransitionTo(transitionTime: 2.0f);
}
```

> Set `AudioSource.playOnAwake = false` lalu panggil `.Play()` secara manual di `Start()` — ini memastikan semua layer sinkron dari detik yang sama.

---

## 🛠️ Cara Pakai
1. Tentukan state musik yang dibutuhkan game (minimal: Eksplorasi + Pertarungan).
2. Pilih teknik: Vertical Layering untuk transisi bertahap, Horizontal Resequencing untuk perubahan state yang jelas.
3. Di DAW (FL Studio): buat semua layer/segmen dalam **proyek yang sama, BPM sama, kunci nada sama**. Ekspor sebagai file terpisah dengan panjang identik.
4. Untuk Vertical Layering di Unity: import semua layer, buat Audio Mixer dengan Group per layer, atur Snapshot untuk setiap state, sambungkan ke `GameManager.OnStateChanged`.
5. Untuk transisi yang beat-accurate, implementasikan [[Beat-Matching & DSP Scheduling (Unity)]] agar musik tidak dipotong di tengah ketukan.
6. Playtest: minta tester berpindah antar area/state secara cepat — cek apakah transisi terdengar mulus atau ada jeda/nada sumbang.

## 🔗 Lihat Juga
- [[Beat-Matching & DSP Scheduling (Unity)]] — teknis kuantisasi waktu agar transisi terjadi tepat di ketukan bar berikutnya.
- [[Teori Musik Dasar untuk Game BGM]] — fondasi sebelum masuk ke teknik adaptive.
- [[Chord Progression & Emosi untuk Game]] — semua layer wajib pakai kunci nada yang sama.
- [[Centralized State Manager (GameManager Singleton & Event)]] — sambungkan `OnStateChanged` event ke trigger transisi musik.
- [[Decoupled Audio System (Event Channel & Pooling)]] — sistem SFX yang bisa berjalan paralel dengan BGM adaptive ini.
