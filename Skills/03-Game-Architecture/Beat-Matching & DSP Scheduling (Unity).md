# 🧮 Beat-Matching & DSP Scheduling (Unity)
#audio #music #unity #dsp #beat-matching #adaptive-audio

## 🎯 Apa Ini?
Teknik menjadwalkan transisi musik secara presisi tepat di ketukan bar berikutnya menggunakan `AudioSettings.dspTime` dan `PlayScheduled()` di Unity — bukan `Time.time` biasa yang tidak akurat untuk audio. Gunakan skill ini saat mengimplementasikan [[Adaptive Audio (Vertical Layering & Horizontal Resequencing)]] dan transisi musik harus mulus tanpa terdengar patah di tengah ketukan.

**Kapan digunakan:**
- Saat transisi BGM antar state (eksplorasi → combat) terdengar patah atau tiba-tiba.
- Saat mengimplementasikan Horizontal Resequencing tanpa menggunakan FMOD/Wwise.
- Saat game punya mekanik yang sinkron dengan ketukan musik (rhythm game, efek beat-sync).
- **Tidak perlu** untuk SFX pendek atau BGM sederhana yang tidak butuh transisi — cukup `AudioSource.Play()` biasa.

---

## 🧠 Poin Penting

### Mengapa `Time.time` Tidak Cukup untuk Audio?
Frame rate game bersifat dinamis (bisa 60fps, 30fps, bahkan turun tiba-tiba), sedangkan audio berjalan di sirkuitnya sendiri secara konstan pada tingkat sampel (*sample rate* — misal 44.100 sampel per detik). Jika transisi dijadwalkan lewat `Update()`, hasilnya tidak akurat dan musik bisa terpotong di sembarang tempat.

> Solusi: `AudioSettings.dspTime` adalah jam internal audio engine Unity yang akurat hingga level sampel — independen dari frame rate game.

### Rumus Dasar Matematika Waktu Musik

**Durasi per ketukan (beat):**
$$\text{Beat Duration} = \frac{60}{\text{BPM}}$$
> Contoh 120 BPM: $\frac{60}{120} = 0.5$ detik per ketukan.

**Durasi per bar (birama 4/4):**
$$\text{Bar Duration} = \text{Beat Duration} \times 4$$
> Contoh 120 BPM: $0.5 \times 4 = 2.0$ detik per bar.

### Kuantisasi Waktu — Cara Menemukan Bar Berikutnya
Saat state berubah di tengah permainan, engine harus menunggu bar berikutnya sebelum memotong musik:

```
t_elapsed   = dspTime_sekarang - dspTime_mulai
bars_played = floor(t_elapsed / bar_duration)
next_bar    = dspTime_mulai + ((bars_played + 1) * bar_duration)
```

> Contoh: lagu sudah berjalan 3.3 detik pada 120 BPM (bar duration = 2.0 detik):
> - `bars_played = floor(3.3 / 2.0) = floor(1.65) = 1`
> - `next_bar = 0 + ((1 + 1) * 2.0) = 4.0 detik`
> 
> Musik baru dijadwalkan tepat di detik ke-4.0 — transisi mulus tanpa menginterupsi ritme.

---

## 🧩 Properties — BeatManager Component

| Field | Tipe | Keterangan |
|---|---|---|
| `explorationAudio` | `AudioSource` | Source BGM eksplorasi |
| `combatAudio` | `AudioSource` | Source BGM combat |
| `bpm` | `float` | BPM lagu (harus sama untuk semua layer) |
| `beatsPerBar` | `float` | Ketukan per bar — umumnya 4 untuk birama 4/4 |
| `barDuration` | `float` | Dihitung otomatis: `(60/bpm) * beatsPerBar` |
| `songStartDspTime` | `double` | Waktu DSP saat lagu pertama dimulai (`double`, bukan `float`!) |

> **Penting**: Gunakan tipe `double` (bukan `float`) untuk semua variabel waktu DSP. `float` kehilangan presisi untuk nilai waktu besar (>beberapa menit), menyebabkan drift akumulatif.

---

## 🔄 Alur Lengkap

```
Start()
  │
  ├── Hitung barDuration = (60/bpm) * beatsPerBar
  ├── Catat songStartDspTime = AudioSettings.dspTime
  └── explorationAudio.Play()
              │
    [State berubah → SwitchToCombat() dipanggil]
              │
              ▼
  Ambil AudioSettings.dspTime saat ini
              │
              ▼
  Hitung barsPlayed = floor((dspNow - songStart) / barDuration)
              │
              ▼
  nextBarTime = songStart + ((barsPlayed + 1) * barDuration)
              │
              ├── combatAudio.PlayScheduled(nextBarTime)
              └── explorationAudio.SetScheduledEndTime(nextBarTime)
              │
              ▼
  Audio engine memproses scheduling sebelum waktu tiba
  → Transisi mulus tepat di ketukan bar berikutnya ✅
```

---

## 🛠️ Cara Pakai

**1. Setup `BeatManager.cs`:**

```csharp
using UnityEngine;

public class BeatManager : MonoBehaviour
{
    [Header("Audio Sources")]
    public AudioSource explorationAudio;
    public AudioSource combatAudio;

    [Header("Music Settings")]
    public float bpm = 120f;
    public float beatsPerBar = 4f;

    private float barDuration;
    private double songStartDspTime;

    void Start()
    {
        // Hitung durasi satu bar
        barDuration = (60f / bpm) * beatsPerBar;

        // Catat waktu DSP saat mulai — pakai double untuk presisi
        songStartDspTime = AudioSettings.dspTime;

        // Mulai semua AudioSource bersamaan agar sinkron
        explorationAudio.Play();
        combatAudio.volume = 0f;
        combatAudio.Play();
    }

    public void SwitchToCombat()
    {
        double currentDspTime = AudioSettings.dspTime;
        double elapsed = currentDspTime - songStartDspTime;

        // Kuantisasi — temukan bar berikutnya
        double barsPlayed = System.Math.Floor(elapsed / barDuration);
        double nextBarTime = songStartDspTime + ((barsPlayed + 1) * barDuration);

        // Jadwalkan transisi tepat di bar berikutnya
        combatAudio.SetScheduledStartTime(nextBarTime);
        explorationAudio.SetScheduledEndTime(nextBarTime);
    }

    public void SwitchToExploration()
    {
        double currentDspTime = AudioSettings.dspTime;
        double elapsed = currentDspTime - songStartDspTime;

        double barsPlayed = System.Math.Floor(elapsed / barDuration);
        double nextBarTime = songStartDspTime + ((barsPlayed + 1) * barDuration);

        explorationAudio.SetScheduledStartTime(nextBarTime);
        combatAudio.SetScheduledEndTime(nextBarTime);
    }
}
```

**2. Sambungkan ke GameManager:**
```csharp
// Di GameManager, saat state berubah:
private void HandlePlaying()
{
    BeatManager.instance.SwitchToExploration();
}

private void HandleGameOver()
{
    // Stop semua audio, tidak perlu beat-matching
    explorationAudio.Stop();
    combatAudio.Stop();
}
```

**3. Persiapan aset audio di DAW (FL Studio):**
- Ekspor semua layer/segment dengan **BPM identik** dan **panjang file kelipatan bar yang sama** (misal: semua 8 bar = 4.0 detik di 120 BPM).
- Pastikan **kunci nada sama** untuk semua layer (lihat [[Chord Progression & Emosi untuk Game]]).
- Set loop point di Unity: aktifkan `AudioSource.loop = true` untuk layer yang perlu berulang.

## 🔗 Lihat Juga
- [[Adaptive Audio (Vertical Layering & Horizontal Resequencing)]] — teknik musik dinamis yang membutuhkan beat-matching ini untuk transisi mulus.
- [[Centralized State Manager (GameManager Singleton & Event)]] — sambungkan `OnStateChanged` ke `BeatManager.SwitchToCombat()` / `SwitchToExploration()`.
- [[Single Source of Truth (SSOT)]] — `BeatManager` adalah SSOT untuk semua logika timing musik di scene.
- [[Teori Musik Dasar untuk Game BGM]] — fondasi BPM dan struktur bar yang menjadi dasar rumus di skill ini.
