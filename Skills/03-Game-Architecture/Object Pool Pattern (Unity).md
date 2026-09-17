# ♻️ Object Pool Pattern (Unity)

#architecture #design-pattern #object-pool #unity #performance

## 🎯 Apa Ini?

Stok GameObject nonaktif yang dipakai ulang biar gak `Instantiate/Destroy` tiap frame (penyebab GC spike).
Pakai saat tembakan, partikel, musuh keroco, atau apapun yang lahir-mati massal.

## 📚 Konsep Utama

Prinsip: init di momen sepi (loading) → `Get()` = nyalakan → `Release()` = matikan + balikin ke pool. Jangan `Destroy`.

```csharp
// Cara modern — pakai bawaan Unity 2021+, jangan bikin manual kecuali buat belajar
using UnityEngine.Pool;
IObjectPool<RevisedProjectile> objectPool;

void Awake() {
  objectPool = new ObjectPool<RevisedProjectile>(
    CreateProjectile, OnGetFromPool, OnReleaseToPool,
    OnDestroyPooledObject, collectionCheck: true,
    defaultCapacity: 20, maxSize: 100);
}
RevisedProjectile CreateProjectile() {
  var p = Instantiate(projectilePrefab);
  p.ObjectPool = objectPool;
  return p;
}
void OnGetFromPool(RevisedProjectile p) => p.gameObject.SetActive(true);
void OnReleaseToPool(RevisedProjectile p) => p.gameObject.SetActive(false);
void OnDestroyPooledObject(RevisedProjectile p) => Destroy(p.gameObject);
```
> `collectionCheck:true` lempar error kalau objek yang udah di pool dibalikin lagi. `maxSize` cegah mem bengkak — kelebihan dihancurkan.

Versi manual buku (biar paham daleman): `ObjectPool` pegang `Stack<PooledObject>`, `SetupPool()` isi `initPoolSize`, `GetPooledObject()` pop/buat baru, `ReturnToPool()` push + `SetActive(false)`, `PooledObject.Release()` manggil balik pool.

## 🧩 Properties (Inspector)

| Field | Rekomendasi | Bahaya jika salah |
|---|---|---|
| `initPoolSize / defaultCapacity` | Sejumlah objek aktif bareng max (peluru di layar) | Kekecilan = alokasi liar saat ramai |
| `maxSize` | Cap keras (mis. 100) | Tanpa cap = mem bengkak |
| `collectionCheck` | `true` saat develop | `false` = double-release lolos diam-diam |
| `objectToPool / prefab` | Prefab + `PooledObject` ref ke pool | Lupa set `Pool` = `Release()` null |

## 🔄 Alur Lengkap

```
Spawn massal? → Pool di Awake/loading → Butuh? → Get() + taruh posisi/rotasi → Selesai/offscreen/timeout? → Release()
→ Pool kosong? → buat 1 baru (darurat) → Pool penuh saat Release? → Destroy kelebihan (maxSize)
→ Multi prefab? → Dictionary<key, Pool> (key = InstanceID prefab)
```

## 🛠️ Cara Pakai di Unity

1. Ganti `Instantiate` di `ExampleGun` jadi `pool.Get()`, ganti `Destroy` di `ExampleProjectile` jadi timer → `Release()`.
2. Set `defaultCapacity` = kebutuhan tempur normal, `maxSize` = batas panik.
3. Sembunyikan tiap ada kesempatan: offscreen, kena ledakan, timeout.
4. Butuh global? Bungkus pool jadi Singleton (akses gampang) atau static. Multi jenis? Dictionary pool.
5. Profiling: cek GC spike hilang, cek mem statis gak over. Pool = tukar GC spike dengan mem diam.

## 🔗 Lihat Juga

- [[Books/Level Up Your Code With Design Pattern.pdf]] — sumber (hlm. 57-65)
- [[Factory Pattern (Unity)]] — factory ambil dari pool, bukan Instantiate
- [[Singleton Pattern (Unity Generic)]] — pool global yang aksesibel
- [[Decoupled Audio System (Event Channel & Pooling)]] — contoh pool buat SFX
- [[Skills]] — indeks kategori
