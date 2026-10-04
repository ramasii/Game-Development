# Dokumentasi Script

## Daftar Isi

1. [Prasyarat](#prasyarat)
2. [Script yang Tidak Boleh Diubah](#script-yang-tidak-boleh-diubah)
3. [Aturan Penting: Pooling](#aturan-penting-pooling)
4. [Cara Menambah Platform Baru](#cara-menambah-platform-baru)
5. [Cara Menambah Booster Baru](#cara-menambah-booster-baru)
6. [Cara Menambah Enemy Baru](#cara-menambah-enemy-baru)
7. [Struktur Folder](#struktur-folder)

---

## Prasyarat

Sebelum mulai, ada beberapa hal yang perlu diketahui:

- Project ini menggunakan **DOTween** untuk animasi. Pastikan DOTween sudah terpasang di project.
- Gameplay inti memakai sistem **mode warna**: player punya mode (`PlayerMode`: Red / Blue) dan platform punya warna (`PlatformColor`: Red / Blue / Green). Platform berwarna hanya **solid** (bisa diinjak) kalau warnanya cocok dengan mode player — platform hijau selalu solid. Konsep ini penting dipahami sebelum membuat platform, booster, atau enemy baru.
- Semua objek gameplay (platform, booster, enemy) di-spawn dan didaur ulang lewat **object pooling**. Baca bagian [Aturan Penting: Pooling](#aturan-penting-pooling).

---

## Script yang Tidak Boleh Diubah

Script berikut ini adalah script inti game. **Jangan diubah**:

- `GameManager`
- `CameraFollow`
- `PairJumpInput`
- `FtueHints`
- `AudioChannel`

---

## Aturan Penting: Pooling

Game ini memakai **object pooling** — objek tidak di-destroy, tapi dinonaktifkan lalu dipakai ulang. Karena itu ada dua aturan main:

1. **Reset state di `OnSpawn` / `PrepareForSpawn`, bukan di `Awake`.** `Awake` hanya jalan sekali seumur objek, jadi kalau state di-set di sana, objek hasil recycle akan membawa state lama.
2. **Bersihkan tween/animasi di `OnDespawn` / `OnDisable`.** Kalau tidak, objek hasil recycle bisa membawa sisa animasi dari pemakaian sebelumnya (bug visual biasanya berasal dari sini).

---

## Cara Menambah Platform Baru

### Langkah

1. Buat script baru yang **menurunkan (inherit) dari `NormalPlatform`**, taruh di folder `Scripts/Platform`.
2. Override fungsi yang dibutuhkan sesuai perilaku platform barumu.
3. Buat prefab platform dengan script tersebut.
4. **Daftarkan prefab ke daftar varian di `PlatformVariantPicker`** (lewat inspector `PlatformSpawner`). Tanpa langkah ini, platform tidak akan pernah muncul di game.

### Fungsi yang bisa di-override

| Fungsi | Wajib? | Kegunaan |
| --- | --- | --- |
| `IsSolidFor(mode, isDashing)` | Opsional | Menentukan kapan platform solid. Default: cocok warna / hijau selalu solid / selalu solid saat dash. |
| `RefreshVisual(mode, dash, snap)` | Opsional | Mengubah tampilan saat mode player berubah. |
| `IsFragile` | Opsional | Return `true` kalau platform rapuh (hancur sekali injak). |
| `PrepareForSpawn()` | Opsional | Reset state tiap spawn. Wajib panggil `base.PrepareForSpawn()`. |
| `Boink()` | Opsional | Efek squash saat diinjak. |

### Template minimal

```csharp
using UnityEngine;

public class PlatformBaruKu : NormalPlatform
{
    public override bool IsSolidFor(PlayerMode mode, bool isDashing)
    {
        // contoh: selalu solid apapun modenya
        return true;
    }

    public override void PrepareForSpawn()
    {
        base.PrepareForSpawn();
        // reset state di sini
    }
}
```

### Aturan penting

- Satu platform hanya boleh memiliki **satu sifat**. Jangan gabungkan beberapa sifat dalam satu platform.
- Contoh platform yang sudah ada: `MovingPlatform`, `CrumblingPlatform`, `SpikePlatform` — semuanya turunan dari `NormalPlatform`.

---

## Cara Menambah Booster Baru

### Langkah

1. Buat script baru yang **menurunkan (inherit) dari `BoosterBase`**, taruh di folder `Scripts/Platform/Boosters`.
2. Override fungsi `ApplyEffect` untuk menentukan efek boosternya.
3. Buat prefab booster dengan script tersebut.
4. **Daftarkan prefab sebagai entry di `BoosterSpawner`** (lewat inspector). Isi field-nya:

- `label` — nama bebas untuk identifikasi.
- `boosterPrefab` — prefab booster kamu.
- `minY` — ketinggian minimum booster mulai bisa muncul.
- `chance` — peluang muncul (0 sampai 1).
- `multiplier` — pengali efek (0.5 sampai 5).

Tanpa langkah 4, booster tidak akan pernah muncul di game.

### Yang sudah diurus otomatis oleh `BoosterBase`

Kamu **tidak perlu** menulis ulang hal-hal ini:

- Mencari referensi ke platform induk (`ParentPlatform`) dan komponen visual (`Visual`).
- Setup, visibilitas (booster ikut hilang saat platform jadi ghost), dan persiapan saat spawn.
- Deteksi tabrakan (trigger) dengan player — efek di `ApplyEffect` akan dipanggil otomatis saat player menyentuh booster.

### Fungsi yang bisa di-override

| Fungsi | Wajib? | Kegunaan |
| --- | --- | --- |
| `ApplyEffect(player, surfaceTopY)` | **Wajib** | Efek booster ke player. |
| `PlayTriggerFeedback()` | Opsional | Feedback visual saat booster kepicu (ubah sprite, animasi pop, dll). |
| `OnSetup()` | Opsional | Setup tambahan tiap spawn. |

### Template minimal

```csharp
using UnityEngine;

[RequireComponent(typeof(BoxCollider2D))]
public class BoosterBaruKu : BoosterBase
{
    public float effectStrength = 2f;

    public override void ApplyEffect(PlayerController player, float surfaceTopY)
    {
        if (player == null) return;
        // contoh: lontarkan player ke atas
        player.LaunchUp(effectStrength);
    }

    public override void PlayTriggerFeedback()
    {
        // opsional: ubah sprite / mainkan animasi pop
    }
}
```

### Aturan penting

- Satu booster hanya boleh memiliki **satu sifat**.
- Contoh booster yang sudah ada: `SpringBooster`.

---

## Cara Menambah Enemy Baru

### Langkah

1. Buat script baru yang **menurunkan (inherit) dari `EnemyBehaviour`**, taruh di folder `Scripts/Enemy`.
2. Pasang script tersebut pada objek enemy.
3. Pastikan objek enemy memiliki **dua script**: `Enemy` dan `EnemyBehaviour` (atau turunannya).
4. Buat prefab enemy.
5. **Daftarkan prefab sebagai entry di `EnemySpawner`** (lewat inspector). Isi field-nya:

- `label` — nama bebas untuk identifikasi.
- `enemyPrefab` — prefab enemy kamu.
- `minY` — ketinggian minimum enemy mulai bisa muncul.
- `chance` — peluang muncul (0 sampai 1).
- `spawnColor` — warna spawn (Random / Red / Blue).

Tanpa langkah 5, enemy tidak akan pernah muncul di game.

### Fungsi yang bisa di-override di `EnemyBehaviour`

Semua bersifat opsional — override yang dibutuhkan saja:

| Fungsi | Kegunaan |
| --- | --- |
| `OnSpawn(anchor)` | Dipanggil setiap enemy muncul atau diambil dari pool. **Reset state di sini**, bukan di `Awake`. |
| `Tick()` | Dipanggil setiap frame selama enemy hidup. Logika gerak/AI ditulis di sini. |
| `OnDied()` | Dipanggil sekali saat enemy mati (diinjak atau di-kill). |
| `OnDespawn()` | Dipanggil saat enemy dinonaktifkan / kembali ke pool. **Bersihkan tween di sini.** |
| `SpawnXRange` | Rentang posisi X spawn relatif terhadap anchor (dipakai spawner). |

### Template minimal

```csharp
using UnityEngine;

[DisallowMultipleComponent]
public class EnemyBehaviourBaruKu : EnemyBehaviour
{
    public float speed = 2f;

    public override void OnSpawn(Vector2 anchor)
    {
        // reset state di sini
    }

    public override void Tick()
    {
        // logika gerak / AI di sini
        // Owner = referensi ke script Enemy induk
    }

    public override void OnDespawn()
    {
        // bersihkan tween di sini
    }
}
```

### Aturan penting

- Jika objek hanya diberi script `Enemy` saja (tanpa `EnemyBehaviour`), objek tersebut menjadi enemy biasa yang **diam di tempat**.
- Satu enemy **boleh memiliki beberapa turunan `EnemyBehaviour`**, asalkan behaviour-nya tidak saling bertentangan (conflict).

**Contoh kombinasi yang tidak boleh:**

- `EnemyPatrol` ❌ `EnemyZigZag` — arah geraknya berbeda.

**Contoh behaviour yang sudah ada:** `EnemyPatrol`, `EnemyExplosion`.

---

## Struktur Folder

Taruh script baru di folder yang sesuai jenisnya:

```javascript
Scripts/
├── Core/            → script inti (jangan diubah)
├── Platform/        → platform baru di sini
│   └── Boosters/    → booster baru di sini
├── Enemy/           → enemy dan behaviour baru di sini
│   └── AI/          → behaviour lama (legacy)
├── Player/          → script player
├── Spawning/        → spawner, pool, factory (jarang perlu disentuh)
├── Audio/           → sistem audio
├── UI/              → tampilan UI
└── Utils/           → utilitas
```