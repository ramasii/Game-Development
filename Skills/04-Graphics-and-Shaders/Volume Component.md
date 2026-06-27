# 🎛️ Volume Component (Custom Post-Process URP)

## 🎯 Apa Ini?
Cara membuat **Volume Component** sendiri di URP agar effect bisa dikontrol dari **Post-Process Volume** di scene, termasuk blending antar volume dan override per kamera.

---

## 📚 Struktur Dasar

```csharp
[Serializable, VolumeComponentMenu("Custom/Nama Effect")]
public class MyVolume : VolumeComponent, IPostProcessComponent
{
    // Parameter-parameter effect
    public ColorParameter myColor = new ColorParameter(Color.white);
    public ClampedFloatParameter myFloat = new ClampedFloatParameter(1f, 0f, 10f);

    // Wajib diimplementasikan dari IPostProcessComponent
    public bool IsActive() => myFloat.value > 0f && active;
    public bool IsTileCompatible() => false;
}
```

---

## 🧠 Tipe Parameter

| Tipe | Kegunaan | Contoh |
|------|----------|--------|
| `ColorParameter` | Warna (RGBA) | Warna outline |
| `ClampedFloatParameter` | Float dengan min-max | Ketebalan, threshold |
| `FloatParameter` | Float bebas | Nilai tak terbatas |
| `BoolParameter` | Toggle on/off | Aktifkan fitur |
| `IntParameter` | Integer | Jumlah sample |

### Cara Deklarasi
```csharp
// ClampedFloat: (defaultValue, min, max)
public ClampedFloatParameter outlineScale = new ClampedFloatParameter(3f, 0f, 10f);

// Color
public ColorParameter outlineColor = new ColorParameter(Color.black);
```

---

## 🏷️ `VolumeComponentMenu`
String di attribute ini menentukan path di menu **"Add Override"** di Volume inspector:
```csharp
[VolumeComponentMenu("Custom/Roystan Outline")]
// → Muncul di: Add Override > Custom > Roystan Outline
```

---

## ✅ `IsActive()` — Kapan Effect Dijalankan?
```csharp
public bool IsActive() => outlineColor.value.a > 0f && active;
```
- `active` → property bawaan VolumeComponent (bisa di-toggle dari inspector).
- Tambahkan kondisi lain agar pass tidak berjalan sia-sia (hemat GPU).

---

## 🛠️ Cara Pakai di Renderer Feature
```csharp
var stack = VolumeManager.instance.stack;
var settings = stack.GetComponent<MyVolume>();

if (settings == null || !settings.IsActive()) return;

// Ambil nilai
float scale = settings.myFloat.value;
Color col = settings.myColor.value;
```

---

## 📦 Cara Pasang ke Scene
1. Tambahkan komponen **Volume** ke GameObject (atau gunakan Global Volume).
2. Buat / assign **Volume Profile**.
3. Klik **Add Override** → cari nama menu yang kamu definisikan.
4. Enable parameter yang ingin di-override dan atur nilainya.

---

## 🔗 Lihat Juga
- [[URP Renderer Feature]] — Yang membaca nilai dari Volume ini
- [[HLSL Outline Shader]] — Shader yang menerima nilai ini sebagai uniform
