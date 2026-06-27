# 📝 HLSL Outline Shader (Depth & Normal Edge Detection)

## 🎯 Apa Ini?
Shader post-process fullscreen di URP yang mendeteksi tepi (edge) objek menggunakan perbandingan **depth buffer** dan **normal buffer** antar piksel tetangga. Hasilnya: efek outline cel-shading / toon.

---

## 📚 Konsep Utama

### Include Wajib URP
```hlsl
#include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/Core.hlsl"
#include "Packages/com.unity.render-pipelines.core/Runtime/Utilities/Blit.hlsl"
#include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/DeclareDepthTexture.hlsl"
#include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/DeclareNormalsTexture.hlsl"
```

### Tag Wajib
```hlsl
Tags { "RenderPipeline" = "UniversalPipeline" }
Cull Off ZWrite Off ZTest Always
```
> `Cull Off ZWrite Off ZTest Always` = shader ini fullscreen post-process, tidak perlu cek geometri.

---

## 🧠 Teknik: Roberts Cross Edge Detection

### Sampling 4 Titik Diagonal
Alih-alih sampling kiri-kanan-atas-bawah, shader ini pakai **4 titik diagonal** (±45°) untuk deteksi edge yang lebih akurat:

```hlsl
float2 uv0 = uv - texelSize * halfScale;                                // kiri-bawah
float2 uv1 = uv + texelSize * halfScale;                                // kanan-atas
float2 uv2 = uv + float2(texelSize.x * halfScale, -texelSize.y * halfScale); // kanan-bawah
float2 uv3 = uv + float2(-texelSize.x * halfScale, texelSize.y * halfScale); // kiri-atas
```

### Depth Edge
```hlsl
float depthDiff0 = depth1 - depth0; // diagonal 1
float depthDiff1 = depth3 - depth2; // diagonal 2
float edgeDepth = sqrt(pow(depthDiff0, 2) + pow(depthDiff1, 2)) * 100.0;
// Bandingkan dengan threshold → hasilnya 0.0 atau 1.0
```

### Normal Edge
```hlsl
float3 normalDiff0 = normal1 - normal0;
float3 normalDiff1 = normal3 - normal2;
float edgeNormal = sqrt(dot(normalDiff0, normalDiff0) + dot(normalDiff1, normalDiff1));
edgeNormal = edgeNormal > _NormalThreshold ? 1.0 : 0.0;
```

### Gabungkan
```hlsl
float edge = max(edgeDepth, edgeNormal); // Ambil nilai tertinggi
```

---

## 🛠️ Dynamic Outline Scale (Distance Falloff)
Outline menipis sesuai jarak kamera, tapi ada batas minimum agar tidak hilang total:
```hlsl
float distanceScale = 1.0 / (1.0 + linearEyeDepth * _DistanceFalloff * 0.01);
float dynamicScale = max(_MinOutlineScale, _OutlineScale * distanceScale);
```

---

## 🛠️ Fix: Grazing Angle Artifacts

**Masalah**: Di permukaan yang miring (dinding, lantai), outline palsu muncul karena depth difference-nya besar.

**Solusi**: Deteksi sudut miring via View Space Normal, lalu naikkan threshold di sana:

```hlsl
// 1. Konversi normal ke View Space
float3 normalVS = TransformWorldToViewDir(normal0, true);

// 2. Hitung seberapa “miring” permukaan (0 = menghadap kamera, 1 = menyamping)
float grazingMultiplier = 1.0 - saturate(abs(normalVS.z));

// 3. Naikkan threshold secara dinamis di area miring
float depthThreshold = _DepthThreshold * depth0 * (1.0 + grazingMultiplier * _GrazingTolerance);
edgeDepth = edgeDepth > depthThreshold ? 1.0 : 0.0;
```
> `normalVS.z = -1` → permukaan menghadap kamera (threshold normal)  
> `normalVS.z ≈ 0` → permukaan miring (threshold dinaikkan sesuai `_GrazingTolerance`)

---

## 🎨 Output Akhir
```hlsl
float4 outlineResult = float4(_OutlineColor.rgb, _OutlineColor.a * edge);
return float4(lerp(color.rgb, outlineResult.rgb, outlineResult.a), color.a);
```
> Alpha dari `_OutlineColor` mengontrol opacity outline. Alpha = 0 → tidak ada outline.

---

## 🔗 Lihat Juga
- [[URP Renderer Feature]] — C# pass yang menjalankan shader ini
- [[Volume Component]] — Parameter yang dikontrol dari scene
