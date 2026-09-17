# 🖥️ MVP Pattern (Unity UI)

#architecture #design-pattern #mvp #mvc #unity #ui

## 🎯 Apa Ini?

Pisah UI jadi 3 lapis: Model (data), View (tampil), Presenter (logika perantara, pegang event).
Pakai saat UI gede, tim gede, butuh unit test tanpa Play Mode. UI 1 layar? Skip.

## 📚 Konsep Utama

Keluarga MVC: Model simpan data (tanpa logika), View render (UXML/USS atau UGUI), Controller/Presenter otak (olah data). MVP = varian Unity: **View pegang input**, Presenter jadi makelar bolak-balik via event (Observer).

```csharp
// Model: ScriptableObject, tanpa logika gameplay, cuma data + event
[CreateAssetMenu(fileName = "HealthData", menuName = "DesignPatterns/MVP/HealthModel")]
public class HealthModel : ScriptableObject {
  public event Action HealthChanged;
  public int CurrentHealth; public int MaxHealth; public string LabelName;
  public void Increment(int a) { /* ... */ HealthChanged?.Invoke(); }
  public void Decrement(int a) { /* ... */ HealthChanged?.Invoke(); }
  public void Restore() { /* ... */ HealthChanged?.Invoke(); }
}
```
> View = UXML (health bar + status label + value label) + USS. Presenter mediasi:

```csharp
public class HealthPresenter : MonoBehaviour {
  [SerializeField] UIDocument m_Document;
  [SerializeField] HealthModel m_HealthModelAsset;
  ProgressBar m_HealthBar; Label m_StatusLabel, m_ValueLabel;
  void OnEnable() {
    // query VisualElement, subscribe:
    m_HealthModelAsset.HealthChanged += OnHealthChanged;
    UpdateUI(); RegisterButtons();
  }
  void OnDisable() {
    if (m_HealthModelAsset != null) m_HealthModelAsset.HealthChanged -= OnHealthChanged;
  }
  void OnHealthChanged() => UpdateUI();
  void UpdateUI() {
    float pct = (float)m_HealthModelAsset.CurrentHealth / m_HealthModelAsset.MaxHealth;
    m_HealthBar.value = pct * 100;
    m_StatusLabel.text = pct switch { < 0.33f => "Danger", < 0.66f => "Neutral", _ => "Good" };
    m_ValueLabel.text = m_HealthModelAsset.CurrentHealth.ToString();
  }
  public void ApplyDamage(int d) => m_HealthModelAsset.Decrement(d);
  public void RestoreHealth() => m_HealthModelAsset.Restore();
}
```
> Alur buku: klik target / tombol → Presenter (`ApplyDamage/Restore`) → Model berubah → event → Presenter `UpdateUI()`. Objek luar wajib via Presenter, jangan utak-atik Model langsung. Versi UGUI ada di scene `7_MVP` (matikan SceneBootstrapper dulu).

## 🧩 Properties (Inspector)

| Slot | Isi | Catatan |
|---|---|---|
| Model | `HealthModel` SO asset | 1 asset 1 entitas (jangan share HP musuh) |
| View | `UIDocument` (UXML+USS) | Styling di UI Builder, jangan di code |
| Presenter | `MonoBehaviour` di scene | Query `root.Q<Button/ProgressBar/Label>`, subscribe once |

## 🔄 Alur Lengkap

```
Input user (View) → event → Presenter → Model.Decrement/Increment → HealthChanged → Presenter.UpdateUI → View
→ Butuh test? → mock Model, panggil Presenter tanpa Play Mode → UI kecil? → jangan MVP
→ Sync manual mulai bikin boilerplate? → naik ke [[MVVM Pattern (Unity 6 Binding)]]
```

## 🛠️ Cara Pakai di Unity

1. Bikin Model SO (data + event + method ubah data, tanpa logika tampil).
2. Bikin View UXML/USS di UI Builder (nama elemen konsisten: `reset-button`, `health-bar`).
3. Bikin Presenter: `OnEnable` query + subscribe + `UpdateUI`, `OnDisable` unsubscribe, method publik buat aksi.
4. Semua interaksi via Presenter. Jangan biarkan View/Enemy tulis Model langsung.
5. Bagi kerja: front-end garap View, gameplay garap Presenter/Model.

## 🔗 Lihat Juga

- [[Books/Level Up Your Code With Design Pattern.pdf]] — sumber (hlm. 102-109)
- [[MVVM Pattern (Unity 6 Binding)]] — upgrade otomatis via data binding
- [[Observer Pattern Events]] — event di jantung MVP
- [[SOLID Principles (Unity)]] — SRP per lapis
- [[Skills]] — indeks kategori
