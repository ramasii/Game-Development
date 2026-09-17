# 👑 Singleton Pattern (Unity Generic)

#architecture #design-pattern #singleton #unity

## 🎯 Apa Ini?

Jamin 1 class cuma punya 1 instance + akses global ke dia.
Pakai hemat: cuma buat 2-3 manager sentral (Game, Audio, File). Selebihnya itu code smell.

## 📚 Konsep Utama

```csharp
// Minimal — jaga duplikat
public class SimpleSingleton : MonoBehaviour {
  public static SimpleSingleton Instance;
  void Awake() {
    if (Instance == null) Instance = this;
    else Destroy(gameObject);
  }
}
```
> Masalah: hancur saat ganti scene, harus diset manual di hierarchy.

```csharp
// Generik + persistent + lazy — versi buku yang disempurnakan
public class Singleton<T> : MonoBehaviour where T : Component {
  static T instance;
  public static T Instance {
    get {
      if (instance == null) {
        instance = FindFirstObjectByType<T>();
        if (instance == null) {
          var go = new GameObject(typeof(T).Name);
          instance = go.AddComponent<T>();
          DontDestroyOnLoad(go);
        }
      }
      return instance;
    }
  }
  public virtual void Awake() {
    if (instance == null) { instance = this as T; DontDestroyOnLoad(gameObject); }
    else Destroy(gameObject);
  }
}
// pakai: public class GameManager : Singleton<GameManager> {}
// akses: GameManager.Instance.DoSomething();
```
> `DontDestroyOnLoad` = survive ganti scene. Lazy = dibikin otomatis saat pertama diakses. Generik = `AudioManager` dan `GameManager` bisa coexist tanpa copy-paste.

Jujur dari buku: Singleton langgar SOLID (global state, tight coupling, susah unit test). Game kecil yang gak butuh maintain tahunan masih oke karena cepat + performan (hindari `GetComponent/Find` berulang). Enterprise / live-service lama: hindari.

## 🧩 Properties (Inspector)

| Varian | Kapan | Risiko |
|---|---|---|
| `SimpleSingleton` | Prototype 1 scene | Duplikat, hancur ganti scene |
| `Singleton<T>` persistent | Manager lintas scene | Global state nyebar, test susah |
| Tanpa Singleton (DI/Event/Ref) | Hampir selalu | Butuh wiring manual lebih rapi |

## 🔄 Alur Lengkap

```
Butuh global? → Bisa inject/reference/event? → Ya: jangan Singleton → Tidak (beneran sentral)? → Singleton<T>
→ Duplikat muncul? → Awake Destroy(gameObject) → Ganti scene? → DontDestroyOnLoad → Nambah manager ke-4? → stop, refactor ke Event/DI
```

## 🛠️ Cara Pakai di Unity

1. Warisi `Singleton<T>`, jangan tulis ulang logika instance.
2. Akses via `X.Instance`, jangan `Find` tiap frame.
3. Jaga jumlah ≤3. Audit tiap nambah: "ini beneran harus global?"
4. Gabung seperlunya: pool global = `ObjectPool` + Singleton biar `Get()` gampang.
5. Kalau test saling ngeracunin state, itu sinyal Singleton kebanyakan.

## 🔗 Lihat Juga

- [[Books/Level Up Your Code With Design Pattern.pdf]] — sumber (hlm. 66-72)
- [[Centralized State Manager (GameManager Singleton & Event)]] — contoh GameManager Singleton + event
- [[Object Pool Pattern (Unity)]] — pool global via Singleton
- [[Observer Pattern Events]] — alternatif lepas vs global ketat
- [[Skills]] — indeks kategori
