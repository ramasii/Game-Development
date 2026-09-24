# Plan — Platform Gerak & Retak masuk keluarga Platform

> Hub: [[Projects/Ball Jumper/Ball Jumper|Ball Jumper]] | Status: 🚧 dieksekusi Digidaw | Disetujui King
> Prinsip: komposisi (Platform = inti), SSOT tanpa variabel ganda, satu jalur landing via `Platform.TryLand`.

## Temuan awal (dari prefab YAML)
- `PlatformGerak`/`PlatformRetak`: Rigidbody2D Dynamic + SimpleHazard (bounce=1, toggle=0) + FlyingPatrolAI. **Tanpa script Platform** → streak gak keitung, gak ikut ghost, gak masuk pool.
- `Retak` belum bisa retak (belum ada script crumble).

## Pemilik variabel
| Pemilik | Variabel |
|---|---|
| `Platform` | color, spawnId, visualRenderer, animator, boink*, booster, TopY, `OnStepped` (event baru) |
| `PlatformMover` (baru) | moveRange, moveSpeed — cuma transform |
| `PlatformCrumbler` (baru) | shakeDuration, goneDelay — subscribe `OnStepped`, reset via OnEnable/Disable |
| `Spawner` | tabel varian (prefab, minY, chance), pool per prefab |
| `PlayerController` | +2 baris `NotifyStepped()` |

## Langkah
1. `Platform.cs`: `event OnStepped` + `NotifyStepped()`.
2. `PlayerController.cs`: panggil `NotifyStepped()` di `TryLand` + `TrySpringBoost`.
3. Baru `PlatformMover.cs` (sine patrol, anchor di OnEnable) + `PlatformCrumbler.cs` (Intact→Shake→Gone → `Spawner.ReleasePlatform()`).
4. `Spawner.cs`: struct `PlatformVariant` + list (Gerak minY 100/10%, Retak minY 150/8%), pool per prefab, `ReleasePlatform()` publik, larang booster di Retak.
5. Bedah prefab via Unity MCP: tambah `Platform` (+Mover/+Crumbler), hapus `SimpleHazard`, `FlyingPatrolAI`, `Rigidbody2D`.

## Anti-softlock
- Bailout hijau → selalu prefab normal. Retak dilarang 2x beruntun. Zona tutorial (<90) steril.

## DoD
- Compile 0 error; prefab check via script; playtest land/streak/crumble; sim 1000 spawn; pool plateau.
