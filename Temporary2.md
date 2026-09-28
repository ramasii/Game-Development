
# 🧩 Plan Refactor Spawner.cs (Ball Jumper)

#plan #refactor #architecture #unity #ball-jumper

## 🎯 Tujuan

Pecah `Assets/_PairJump/Core/Spawner.cs` (857 baris, ±8 tanggung jawab) jadi class kecil pakai SRP + facade tipis.
Referensi: [[SOLID Principles (Unity)]], [[Object Pool Pattern (Unity)]], [[Runtime State Separation]], [[Factory Pattern (Unity)]].

## 🗂️ Struktur Baru

| File | Tanggung jawab | Diambil dari Spawner |
|---|---|---|
| `Spawner.cs` (facade, ±120 baris) | Wiring, dengerin `GameManager.OnStateChanged`, loop `Update` (spawn ke atas, cull ke bawah), registry `live` | `HandleGameState`, `EnsureSpawn`, `ClearAll`, `Update`, `ReleasePlatform`, `RefreshAllPlatformVisuals` |
| `PlatformPool.cs` | Pool per prefab, prewarm, Get/Release, fallback kotak prosedural | `pools`, `sourcePrefab`, `PoolFor`, `GetFromPool`, `ReleaseToPool`, `CreatePlatformObject`, `MakeSquare` |
| `PlatformLayoutGenerator.cs` (plain C#) | Posisi berikutnya: gap Y, `ReflectX`, bound, anti edge-run | `SpawnNext`, `GetBound`, `GetPlatformHalfWidth`, `ReflectX`, `edgeStreak/edgeSide` |
| `PlatformColorPicker.cs` (plain C#) | Zona warna, tutorial pattern, bailout hijau | `PickColor`, `TutorialPattern`, `sinceGreen`, `tutorialStep` |
| `PlatformVariantPicker.cs` (plain C#) | Pilih prefab varian, retak tidak boleh 2x beruntun | `PickVariant`, `VariantLabel`, `PlatformVariant`, `lastWasCrumbler` |
| `BoosterSpawner.cs` | Roll, attach, posisi, tint pegas | `MaybeAttachBooster`, `ApplyBoosterTint`, `ResetBoosterTint`, `CreateBoosterObject` + semua setting booster |
| `PlatformIntro.cs` | Intro pop DOTween, show/hide renderer, delay bola | `PlayPlatformIntro`, `KillPlatformIntro`, `SetPlatformsVisible`, `GetBallIntroDelay` |

## 📌 Keputusan Desain

- **Pool:** multi-prefab pakai `Dictionary<key, Pool>`. Opsional ganti ke `UnityEngine.Pool.ObjectPool` (punya `collectionCheck` buat nangkep double-release).
- **Picker & generator = plain C#** (bukan MonoBehaviour) biar bisa di-unit-test. `ReflectX` udah static, tinggal pindah.
- **Factory belum perlu:** varian belum punya logika init khusus, pool sudah nutup kebutuhan (skill Factory bilang overkill kalau 1-2 prefab tanpa logika khusus).
- **API publik Spawner tetap sama:** `ReleasePlatform`, `RefreshAllPlatformVisuals`, `GetBallIntroDelay`, `TotalCreated`, `TotalSpawned`. Jadi `Platform`, `PlayerController`, `PlatformCrumbler` tidak perlu diubah.

## 🐛 Bau yang Ikut Dibenahi

1. **Reset state duplikat:** `EnsureSpawn` dan `ClearAll` sama-sama reset `nextY/lastX/edgeStreak/...` manual. Pisah config (inspector) vs runtime state, reset di satu tempat ([[Runtime State Separation]]).
2. **Hidden coupling:** `PickColor` ngisi `lastPickWasBailout`, `PickVariant` baca. Ganti: `ColorPicker` me-return flag bailout, dioper eksplisit.
3. **Cek retak di booster:** `MaybeAttachBooster` cek `GetComponent<PlatformCrumbler>()` langsung. Ganti: flag `allowBooster` per varian (OCP).
4. **Nama membingungkan:** `prewarm` (platform awal) vs `prewarmPool` (objek pool). Rename.

## ⚠️ Risiko

- Field inspector pindah ke komponen baru, jadi **nilai tuning di scene bakal reset** (zona Y, chance booster, intro delay, dll). **Catat/export nilai sekarang sebelum mulai.**
- Jangan lompat ke pola canggih sebelum SRP beres (KISS).

## ✅ Urutan Kerja (tiap langkah dites dulu sebelum lanjut)

- [ ] 0. Catat semua nilai inspector Spawner di scene + commit git bersih
- [ ] 1. `PlatformIntro` (paling independen)
- [ ] 2. `BoosterSpawner`
- [ ] 3. `PlatformPool`
- [ ] 4. `PlatformColorPicker` + `PlatformVariantPicker` (sekalian fix hidden coupling #2)
- [ ] 5. `PlatformLayoutGenerator`
- [ ] 6. Rapikan `Spawner` jadi facade + reset state satu tempat (#1) + rename (#4)
- [ ] 7. Re-wire inspector, isi ulang nilai tuning, playtest (menu → play → pause → resume → game over → retry)

## 🧪 Cek Setelah Tiap Langkah

- Tidak ada compile error / warning baru di console
- Intro platform tetap pop bawah → atas, bola muncul terakhir
- Pool plateau (`TotalCreated` tidak naik terus, `TotalSpawned` naik saat manjat)
- Platform retak tidak 2x beruntun, bailout hijau tetap normal
- Booster hanya muncul di atas `boosterMinY`, tidak di platform retak
- Tidak ada spawn pinggir beruntun > `maxEdgeStreak`

## 📋 Nilai Inspector Spawner (dicatat sebelum refactor)
Tanggal: 2026-09-28. Scene: SampleScene, GO: Spawner.
- player=Player, platformPrefab=Platform
- prewarm=12, gapMinY=0.5, gapMaxY=2, maxGapX=2.5
- greenBailoutEvery=5, greenOnlyUntilY=30, twoColorUntilY=60, tutorialUntilY=90
- edgeFraction=0.15, maxEdgeStreak=2
- enableBooster=true, boosterMinY=140, boosterChance=0.1, boosterMultiplier=1.8
- boosterPrefab=Booster Spring, boosterOnGreen/Red/Blue=true
- boosterSpawnOffset=(0, 0.1), changeBoosterColor=false (King tes OFF)
- variants: Gerak/PlatformGerak/minY90/ch0.1, Retak/PlatformRetak/minY110/ch0.08, Spike/Spike Platform/minY130/ch0.08
- logVariantSpawns=true, prewarmPool=15, maxPoolSize=40
- introStepDelay=0.06, introPopDuration=0.35, ballExtraDelay=0.15
- Git status bersih (HEAD c1a1774 Fix font conflict)

## ✅ Progress Refactor
- [x] 0. Nilai inspector dicatat (lihat atas), git bersih
- [x] 1. PlatformIntro terekstrak (Spawner 856→798 baris). 0 error. Nilai 0.06/0.35/0.15 dipulihkan ke komponen baru, scene saved.
- [x] 2. BoosterSpawner terekstrak (Spawner 798→644 baris). 0 error. Nilai booster dipulihkan, OnValidate pindah.
- [x] 3. PlatformPool terekstrak (Spawner →548 baris). 0 error. Nilai 15/40 dipulihkan. Get() auto-create saat kosong.
