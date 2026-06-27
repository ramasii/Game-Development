# 🎨 Toon Shader Graph (URP)

## 🎯 Apa Ini?
Implementasi **Toon / Cel-Shading** menggunakan Unity Shader Graph untuk URP. Shader ini menghasilkan tampilan kartun dengan bayangan bertingkat (*stepped shading*), specular highlight keras, dan rim light — semua tanpa menulis HLSL secara manual.

**Target**: URP | Surface: Opaque | Output: `BaseColor` (lighting dihitung manual)

---

## 🧩 Properties (Inspector)

| Property | Tipe | Reference Name | Kegunaan |
|----------|------|----------------|----------|
| Base Color | Color | `_Base_Color` | Warna dasar objek |
| Shadow Color | Color | `_Shadow_Color` | Warna area bayangan |
| Rim Color | Color | `_Rim_Color` | Warna efek rim light |
| Rim Power | Float | `_Rim_Power` | Ketajaman batas rim |
| Texture2D | Texture | `_Texture2D` | Albedo texture |
| Tiling | Vector2 | `_Tiling` | Tile UV texture utama |
| Offset | Vector2 | `_Offset` | Offset UV texture utama |
| Tiling Macro | Vector2 | `_Tiling_Macro` | Tile noise macro |
| Offset Macro | Vector2 | `_Offset_Macro` | Offset noise macro |
| Specular Size | Float (0-1) | `_Specular_Size` | Ukuran highlight specular |
| Glossiness | Float | `_Glossiness` | Kekerasan tepi specular |
| Noise | Float | `_Noise` | Intensitas noise di batas shadow |
| Nose Multiplier | Float | `_Nose_Multiplier` | Pengali noise |

---

## 🗂️ Struktur Grup Node

Shader dibagi dalam 3 grup bernama **Step 1-3**, **Step 4**, dan **Step 5**:

```
Step 1-3 : Custom Lighting (GetMainLight + Shadow bertingkat)
Step 4   : Rim Light (Fresnel)
Step 5   : Specular Highlight
```

---

## 🧠 Node-Node Utama & Fungsinya

### GetMainLight (Custom Function)
Node HLSL custom untuk mengambil data cahaya utama dari URP.
```
Output: Direction, Color, DistanceAttenuation, ShadowAtten
```
> Shader Graph tidak punya node built-in GetMainLight, jadi perlu Custom Function node + file .hlsl.

### Shadow Bertingkat (Step 1-3)
```
Normal Vector (World)
  → Dot Product dengan Light Direction   (= NdotL)
  → Noise + Remap (variasi organik di batas)
  → Step Node  (gradien halus jadi 0/1, batas keras)
  → Lerp(Shadow Color, Base Color, mask)
```
- **Step Node** adalah kunci efek toon: mengubah shading halus menjadi batas hitam-putih.
- **Noise** ditambahkan sebelum Step agar batas shadow tidak terlalu kaku.

### Specular Highlight (Step 5)
```
View Direction + Light Direction → Normalize (Half Vector)
  → Dot Product dengan Normal
  → Power (_Specular_Size)      (ukuran highlight)
  → Smoothstep (_Glossiness)    (ketajaman tepi)
  → Multiply dengan Light Color
  → Add ke hasil shadow
```

### Rim Light (Step 4)
```
Fresnel Node (Normal vs View Direction)
  → Power (_Rim_Power)
  → Remap ke [0, 1]
  → Lerp(hasil shadow, Rim Color, fresnel mask)
```
- **Fresnel Node**: menghasilkan cahaya di tepi objek yang membelakangi kamera.

### Texture + UV
```
Tiling And Offset (_Tiling, _Offset)
  → Sample Texture 2D (_Texture2D)
  → Multiply dengan Base Color
```
> Ada 2 set UV: satu untuk texture albedo, satu untuk noise macro (detail batas shadow).

---

## 🔄 Alur Lengkap

```
[Texture] x [Base Color]
      |
      v
[Shadow Mask: NdotL + Noise + Step]
      |
      v
[+ Rim Light: Fresnel + Lerp]
      |
      v
[+ Specular: HalfVector + Power + Smoothstep]
      |
      v
 --> BaseColor Output (Fragment)
```

---

## ⚠️ Hal Penting

- Output **hanya BaseColor** — lighting dihitung manual agar kontrol toon lebih bebas dari sistem Lit Unity.
- **Custom Function `GetMainLight`** butuh file `.hlsl` di project (biasanya `ShaderLibrary/GetMainLight.hlsl`).
- SurfaceType: **Opaque**. Tidak ada transparansi.
- Vertex stage (Normal, Tangent, Position) tidak dimodifikasi — murni fragment shader.
- Shader Graph ini kompatibel dengan **Unity 6 + URP**.

---

## 🔗 Lihat Juga
- [[URP Renderer Feature]] — Untuk menambahkan pass outline di atas shader ini
- [[HLSL Outline Shader]] — Outline berbasis depth+normal yang cocok dikombinasikan
- [[Volume Component]] — Untuk buat settings yang bisa dikontrol dari scene
