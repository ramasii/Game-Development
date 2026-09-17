# 🔗 MVVM Pattern (Unity 6 Binding)

#architecture #design-pattern #mvvm #unity #ui #uitkoolkit #databinding

## 🎯 Apa Ini?

MVP yang presenternya diganti ViewModel + **runtime data binding** (Unity 6, UI Toolkit) biar sinkron Model→View otomatis.
Pakai saat UI kompleks dengan banyak elemen yang cuma format ulang data yang sama (warna, teks, bar).

## 📚 Konsep Utama

3 lapis sama kayak MVP: Model (SO + logika bisnis) — View (UXML+USS) — ViewModel (`MonoBehaviour` makelar). Bedanya: ViewModel bikin binding sekali, sisanya otomatis. Hemat event manual + `UpdateUI()` raksasa.

```csharp
// Model + converter (ubah int HP jadi warna/teks). Daftar sekali:
[InitializeOnLoadMethod]
public static void RegisterConverters() {
  var g = new ConverterGroup("Int to HealthBar");
  g.AddConverter((ref int v) => new StyleColor(Color.Lerp(Color.red, Color.green, v / (float)k_MaxHealth)));
  g.AddConverter((ref int v) => {
    float r = (float)v / k_MaxHealth;
    return r switch { >= 0 and < 1f/3f => "Danger", >= 1f/3f and < 2f/3f => "Neutral", _ => "Good" };
  });
  ConverterGroups.RegisterConverterGroup(g);
}
```
> Di UI Builder: klik elemen → Add binding → Data Source = asset `HealthData`, Path = `CurrentHealth`, Mode = `ToTarget` (satu arah source→UI), Advanced = pilih converter `Int to HealthBar`. Ikon binding muncul di Inspector, blok `<Binding>` muncul di UXML.

```csharp
// Binding via code (wajib buat sub-elemen yang gak bisa diklik di Builder, mis. fill ProgressBar):
void SetDataBindings() {
  var bar = m_Root.Q<ProgressBar>("health-bar");
  var fill = bar?.Q<VisualElement>(className: "unity-progress-bar__progress");
  if (fill != null) {
    fill.dataSource = m_HealthModelAsset;
    var b = new DataBinding {
      dataSourcePath = new PropertyPath(nameof(HealthModel.CurrentHealth)),
      bindingMode = BindingMode.ToTarget,
    };
    b.sourceToUiConverters.AddConverter((ref int v) =>
      new StyleColor(Color.Lerp(Color.red, Color.green, (float)v / m_HealthModelAsset.MaxHealth)));
    fill.SetBinding("style.backgroundColor", b);
  }
}
```
> Perbandingan buku: MVP = Presenter subscribe event + `UpdateUI` manual; MVVM = Model daftar converter + ViewModel `SetDataBindings`, View update sendiri. UXML/USS sama persis, yang beda cuma cara sync. Runtime instance per-GameObject? Wajib via code (Builder cuma bisa ke asset statis).

## 🧩 Properties (Inspector)

| Setting | Isi | Catatan |
|---|---|---|
| Data Source | `HealthData` asset / instance | Asset = global, instance = per-musuh (via code) |
| Path | `CurrentHealth` / `LabelName` | PropertyPath, bukan string bebas |
| Mode | `ToTarget` | UI ikut data (umum). TwoWay hanya jika UI bisa tulis balik |
| Converter | `Int to HealthBar` group | 1 group bisa ada converter warna + teks |

## 🔄 Alur Lengkap

```
Model berubah → binding (ToTarget) → converter (int→color/string) → View update otomatis
→ elemen klik-able? → Builder cukup → sub-elemen/fill/instance runtime? → SetBinding() via code
→ UI kecil? → MVP manual lebih murah → UI gede + banyak format? → MVVM
```

## 🛠️ Cara Pakai di Unity

1. Siapkan Model + `RegisterConverters()` + View UXML/USS (sama kayak MVP).
2. Builder: bind label/bar ke `CurrentHealth`, pilih converter. Cek ikon binding + blok UXML.
3. Code: `SetDataBindings()` buat fill/progress/instance runtime, panggil sekali di `OnEnable`.
4. Klik target (damage) / tombol reset → Model berubah → verifikasi bar+label update tanpa `UpdateUI()`.
5. Overhead di depan (source/path/mode/converter) baru worth it kalau UI lebar. Kalau 1-2 elemen, tetap MVP.

## 🔗 Lihat Juga

- [[Books/Level Up Your Code With Design Pattern.pdf]] — sumber (hlm. 110-120)
- [[Design Patterns & SOLID (Unity - Level Up Your Code)]] — hub master
- [[MVP Pattern (Unity UI)]] — versi manual sebelum binding
- [[Observer Pattern Events]] — yang digantikan binding otomatis
- [[Skills]] — indeks kategori
