# 🔊 Decoupled Audio System (Event Channel + Object Pool)
#architecture #design-pattern #audio #object-pool

## 🎯 Apa Ini?
Sistem SFX yang scalable untuk Unity 6, memisahkan total antara **pemicu suara** (Player, Enemy, UI) dengan **pemutar suara** (`AudioSystem`) lewat ScriptableObject sebagai "saluran radio" (*Event Channel*), dipadukan dengan **Object Pool** bawaan Unity agar `AudioSource` tidak dibuat-hancur berulang kali (zero GC spikes) saat suara sering diputar.

## 🧠 Poin Penting

**1. Memicu suara tanpa referensi langsung ke AudioSystem**
```csharp
// Dari skrip manapun (Player, Enemy, UI)
public AudioEventChannel sfxChannel;
public AudioCue cueFootstep;

void OnFootstep()
{
    sfxChannel.RaiseEvent(cueFootstep, transform.position);
}
```
> Karena `AudioEventChannel` adalah ScriptableObject, semua skrip yang menyimpan referensi ke *asset* yang sama otomatis "satu frekuensi". Tidak perlu singleton atau static reference ke `AudioSystem`.

**2. Auto-detect suara 2D vs 3D**
```csharp
audioSource.spatialBlend = (position == Vector3.zero) ? 0f : 1f;
```
> Trik di `AudioEmitter` ini membedakan suara 2D (BGM/UI, kirim `Vector3.zero`) vs 3D spasial (SFX dunia, kirim posisi asli) hanya dari parameter posisi — tidak perlu flag tambahan.

**3. Return-to-pool yang aman dari pause**
```csharp
yield return new WaitWhile(() => audioSource.isPlaying);
```
> `ReturnToPoolRoutine` memakai pengecekan `isPlaying` bukan `WaitForSeconds(clip.length)`. Kalau game di-pause (`Time.timeScale = 0`) dan `AudioSource` ikut di-pause, speaker tidak akan ditarik balik ke pool sebelum suaranya benar-benar selesai.

## 🧩 Properties (Inspector)

| Skrip | Variabel | Tipe | Keterangan |
|---|---|---|---|
| `AudioSystem` | `audioEmitterPrefab` | `AudioEmitter` | Prefab speaker yang akan di-*pool* |
| `AudioSystem` | `defaultPoolSize` | `int` | Jumlah speaker yang langsung disiapkan di awal |
| `AudioSystem` | `maxPoolSize` | `int` | Batas maksimum speaker aktif bersamaan |
| `AudioSystem` | `sfxEventChannel` | `AudioEventChannel` | Saluran radio SFX yang didengarkan |
| `AudioCue` | `clips` | `AudioClip[]` | Variasi klip suara, dipilih acak via `GetRandomClip()` |
| `AudioCue` | `audioMixerGroup` | `AudioMixerGroup` | Routing ke grup mixer (SFX/BGM/UI) |
| `AudioCue` | `volume` | `float` (0–1) | Volume dasar klip |
| `AudioCue` | `useRandomPitch` | `bool` | Aktifkan variasi pitch acak agar tidak monoton |
| `AudioCue` | `minPitch` / `maxPitch` | `float` (0.1–3) | Rentang pitch acak saat `useRandomPitch` aktif |

## 🔄 Alur Lengkap

```
[Player / Enemy / UI Script]
        │  sfxChannel.RaiseEvent(cue, position)
        ▼
[AudioEventChannel] ── OnAudioRequested ──▶ [AudioSystem.PlayRequestedAudio]
                                                      │
                                                      ▼
                                             emitterPool.Get()
                                                      │
                                                      ▼
                                           [AudioEmitter.PlayAudio]
                                             - set posisi & clip acak
                                             - spatialBlend 2D/3D
                                             - audioSource.Play()
                                                      │
                                                      ▼
                                   WaitWhile(isPlaying) → pool.Release(emitter)
```

## 🛠️ Cara Pasang & Pakai di Unity

1. **Buat Event Channel**: klik kanan di Project window → `Create → GameSeed/Audio/Audio Event Channel` → simpan sebagai *asset*, misal `SFXChannel.asset`.
2. **Buat Audio Cue** per efek suara: `Create → GameSeed/Audio/Audio Cue`, isi array `clips`, atur `audioMixerGroup`, `volume`, dan opsi *random pitch* kalau perlu (misal untuk langkah kaki/tembakan).
3. **Siapkan Prefab Emitter**: buat GameObject baru, tambahkan komponen `AudioSource` + script `AudioEmitter`, lalu jadikan Prefab.
4. **Pasang AudioSystem**: taruh GameObject `AudioSystem` di scene persistent/bootstrap. Drag Prefab emitter ke `audioEmitterPrefab`, dan drag `SFXChannel.asset` ke `sfxEventChannel`.
5. **Panggil dari mana saja**: di skrip Player/Enemy/UI, simpan referensi ke `SFXChannel.asset` lalu panggil `sfxChannel.RaiseEvent(cueFootstep, transform.position);` — tidak ada referensi langsung ke `AudioSystem` sama sekali (*fully decoupled*).
6. **Untuk suara 2D** (BGM/UI), kirim `Vector3.zero` sebagai posisi agar otomatis jadi non-spasial.

## 🔗 Lihat Juga
- [[Observer Pattern Events]] — pondasi pola publish-subscribe yang dipakai `AudioEventChannel` agar `AudioSystem` & pemicu suara tetap *decoupled*.
- [[Advanced Architecture Patterns]] — konteks pola arsitektur kendali transformasi data yang lebih luas di proyek ini.
