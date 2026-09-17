# 🏭 Factory Pattern (Unity)

#architecture #design-pattern #factory #unity #clean-code

## 🎯 Apa Ini?

Objek khusus yang tugasnya bikin objek lain (produk) biar kode spawn rapi dan gampang ditambah varian baru.
Pakai saat game spawn banyak varian (musuh, item, efek, UI) yang masing-masing punya logika init sendiri.

## 📚 Konsep Utama

```csharp
public interface IProduct {
  string ProductName { get; set; }
  void Initialize();
}
public abstract class Factory : MonoBehaviour {
  public abstract IProduct GetProduct(Vector3 position);
}
```
> Produk bagi template via interface, factory bagi kode bersama via abstract. Hati-hati LSP saat subclass factory.

```csharp
public class ProductA : MonoBehaviour, IProduct {
  [SerializeField] string productName = "ProductA";
  public string ProductName { get => productName; set => productName = value; }
  public void Initialize() {
    gameObject.name = productName;
    var ps = GetComponentInChildren<ParticleSystem>();
    ps?.Stop(); ps?.Play(); // logika khas A: partikel
  }
}
public class ConcreteFactoryA : Factory {
  [SerializeField] ProductA productPrefab;
  public override IProduct GetProduct(Vector3 position) {
    var instance = Instantiate(productPrefab.gameObject, position, Quaternion.identity);
    var p = instance.GetComponent<ProductA>();
    p.Initialize(); // factory gak tahu detail, cuma panggil Initialize()
    return p;
  }
}
```
> Contoh buku: `ClickToCreate` ganti-ganti factory → ProductA main partikel, ProductB main sfx. Struktur: `IProduct` ← `ProductA/B`, `Factory` ← `ConcreteFactoryA/B`.

## 🧩 Properties (Inspector)

| Field | Isi | Catatan |
|---|---|---|
| `productPrefab` | Prefab `ProductA/B` (+ `ParticleSystem` / `AudioSource`) | 1 factory = 1 prefab biar SRP |
| `position` | `Vector3` spawn | Dilempar via `GetProduct(position)` |
| Varian baru | Class produk + factory konkret baru | Tanpa sentuh factory lama (OCP) |

## 🔄 Alur Lengkap

```
Butuh spawn? → Produk beda init? → Ya: IProduct.Initialize() per produk → Factory.GetProduct() Instantiate + Initialize
→ Varian nambah? → class + factory baru, lama disentuh → Spawn tiap frame + GC spike? → Factory + [[Object Pool Pattern (Unity)]]
→ Cari produk by ID? → Dictionary<string, Type> / factory manager
```

## 🛠️ Cara Pakai di Unity

1. Bikin `IProduct` (Name + Initialize), produk konkret (`MonoBehaviour + IProduct`) per varian.
2. Bikin `Factory` abstract + 1 concrete factory per prefab, serialize prefab di Inspector.
3. Caller panggil `factory.GetProduct(pos)`, jangan `Instantiate` + `if/switch` manual.
4. Upgrade opsional: dictionary ID→produk, factory manager static, gabung Object Pool buat peluru massal.
5. Jangan pakai kalau cuma 1-2 prefab tanpa logika khusus — overkill.

## 🔗 Lihat Juga

- [[Books/Level Up Your Code With Design Pattern.pdf]] — sumber (hlm. 51-56)
- [[Object Pool Pattern (Unity)]] — pasangan buat spawn massal tanpa GC spike
- [[Flyweight Pattern (Unity Shared Data)]] — sharing data antar produk sejenis
- [[SOLID Principles (Unity)]] — OCP + LSP di balik factory
- [[Skills]] — indeks kategori
