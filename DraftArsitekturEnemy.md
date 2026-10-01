## Hierarki

```
MonoBehaviour
└── Enemy (abstract)              ← sama konsepnya kayak Platform
    ├── EnemyPatrol               ← kode lama, jadi varian
    ├── StaticEnemy               ← nanti (diam)
    ├── FlyingEnemy               ← nanti
    └── ChaserEnemy               ← nanti
```

## Script arsitektur baru

|Script|Status|Tugas|
|---|---|---|
|`Enemy.cs`|BARU|Base abstract, dipindah dari `EnemyPatrol`. Pegang semua yang dipakai semua enemy: warna, solid/ghost via `OnModeChanged`, Animator `isSolid`, tint, deteksi stomp vs kena player, efek mati (hop + fade), cache komponen, subscribe/unsubscribe event yang aman buat pool. Cuma manggil `Tick()` kalau belum mati.|
|`EnemyPatrol.cs`|UBAH|Jadi `EnemyPatrol : Enemy`. Isinya tinggal logika patrol A↔B (`offsetA/B`, `speed`, jalan, balik arah, flip). Nama class dan file tetap, jadi referensi script di prefab nggak putus.|
|`StaticEnemy.cs`|NANTI|Contoh varian paling sederhana: diam di tempat, `Tick()` kosong. Cocok buat enemy berduri yang nggak bisa di-stomp (`IsStompable => false`).|
|`FlyingEnemy.cs`, `ChaserEnemy.cs`|NANTI|Varian berikutnya. Tiap varian cuma override `Tick()` dan hook yang perlu.|
|`EnemySpawner.cs`|UBAH|`EnemyPatrol` diganti `Enemy` di `live`, `pools`, `source`. `CalcX` pakai `SpawnXRange` dari enemy, bukan `offsetA/B`. Logika spawn, recycle, dan pool tetap.|
|`EnemyFactory.cs`|UBAH|`Create()` mengembalikan `Enemy`. Fallback `AddComponent<EnemyPatrol>` dihapus dan diganti error log kalau prefab nggak punya komponen `Enemy`.|
|`EnemySelectionStrategy.cs`|TETAP|Cuma milih prefab + warna per ketinggian. Nggak peduli tipe enemy.|
|`Enemy.prefab`|UBAH|Tetap pakai `EnemyPatrol`. Enemy baru dibuat sebagai Prefab Variant dari prefab ini, lalu komponen `EnemyPatrol` diganti varian barunya.|
|`PlayerController.cs`, `SpawnSystem.cs`, `PlatformColor`|TETAP|Nggak disentuh. Warna enemy tetap pakai `PlatformColor`, jadi satu sumber kebenaran sama platform.|

## Kontrak `Enemy` (yang dilihat varian)

|Member|Jenis|Tugas|
|---|---|---|
|`Tick()`|abstract|Perilaku per frame (gerak dan sebagainya). Satu-satunya yang wajib ditulis varian baru.|
|`OnSpawn(Vector3 anchor)`|virtual|Reset state varian tiap spawn atau ambil dari pool. Menggantikan `SetAnchor` yang sekarang.|
|`SpawnXRange`|virtual|Batas lebar horizontal yang dipakai spawner supaya enemy nggak keluar layar. Default 0, `EnemyPatrol` override dari offset A/B-nya.|
|`IsStompable`|virtual|Default `true`. Varian berduri set `false`, jadi selalu bunuh player.|
|`OnStomped(player)`|virtual|Default: bounce/toggle player lalu efek mati.|
|`OnHitPlayer(player)`|virtual|Default: `player.DieFrom(...)`.|
|`FaceTowards(dx)`|protected|Helper flip sprite, dipakai varian yang bergerak.|

## Alur nambah enemy baru

1. Bikin class `: Enemy`, tulis `Tick()`.
2. Prefab Variant dari `Enemy.prefab`, ganti komponen ke varian baru.
3. Masukkan ke list `entries` di `EnemySpawner`.

Spawner, factory, dan player nggak perlu diubah.