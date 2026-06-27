# 🔌 URP Renderer Feature (Unity 6)

## 🎯 Apa Ini?
Cara membuat **custom render pass** di URP Unity 6 menggunakan `ScriptableRendererFeature` + **Render Graph API** (bukan `CommandBuffer` lama).

---

## 📚 Konsep Utama

### Struktur File
- `ScriptableRendererFeature` — Entry point. Didaftarkan di URP Renderer asset.
- `ScriptableRenderPass` — Logika render sebenarnya, override `RecordRenderGraph()`.

### Alur Kerja
```
Create() → Buat Material & OutlinePass
AddRenderPasses() → Daftarkan pass ke renderer
RecordRenderGraph() → Jalankan logika render per frame
```

---

## 🧠 Poin Penting

### 1. `ConfigureInput()` — Minta Buffer Tambahan
Di constructor pass, deklarasikan buffer apa yang dibutuhkan:
```csharp
ConfigureInput(ScriptableRenderPassInput.Depth | ScriptableRenderPassInput.Normal);
```
> Tanpa ini, `SampleSceneDepth()` dan `SampleSceneNormals()` di shader tidak akan bekerja.

### 2. Render Graph Pattern (Unity 6)
Unity 6 mewajibkan penggunaan **Render Graph** menggantikan `CommandBuffer` langsung.
```csharp
using (var builder = renderGraph.AddRasterRenderPass<PassData>("Pass Name", out var passData))
{
    passData.source = activeColor;
    builder.UseTexture(activeColor, AccessFlags.Read);
    builder.SetRenderAttachment(tempTexture, 0, AccessFlags.Write);
    builder.SetRenderFunc((PassData data, RasterGraphContext context) =>
    {
        Blitter.BlitTexture(context.cmd, data.source, new Vector4(1, 1, 0, 0), data.material, 0);
    });
}
```

### 3. Double-Pass Blit (Read → Temp → Write Back)
Tidak bisa baca & tulis ke texture yang sama sekaligus. Solusinya:
1. **Pass 1**: Blit dari `activeColor` → `tempColorTexture` (apply shader)
2. **Pass 2**: Blit dari `tempColorTexture` → `activeColor` (copy balik)

### 4. Ambil Volume Settings
```csharp
var stack = VolumeManager.instance.stack;
outlineSettings = stack.GetComponent<OutlineVolume>();
if (!outlineSettings.IsActive()) return; // Early exit jika tidak aktif
```

### 5. Hindari Camera Preview
```csharp
if (cameraData.cameraType == CameraType.Preview) return;
```
> Preview camera di inspector akan crash kalau tidak di-skip.

### 6. Dispose Material
```csharp
protected override void Dispose(bool disposing) => CoreUtils.Destroy(outlineMaterial);
```

---

## 📦 Cara Pasang ke Project
1. Taruh script di folder `Scripts/` atau `Rendering/`.
2. Buka **URP Renderer asset** (biasanya di `Settings/`).
3. Di bagian **Renderer Features**, klik `+` → pilih `OutlineFeature`.
4. Assign shader ke field `outlineShader`.

---

## 🔗 Lihat Juga
- [[HLSL Outline Shader]] — Shader HLSL yang dipakai pass ini
- [[Volume Component]] — Settings yang dikontrol lewat Volume
