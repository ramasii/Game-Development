# 📋 Format Game Design Document (GDD)

Gunakan format standar ini untuk mendokumentasikan ide game baru sebelum masuk ke tahap produksi. Format ini dirancang agar setiap proyek memiliki struktur kode yang rapi, modular, dan menerapkan design pattern yang tepat sejak awal.

---

## 💻 1. Arsitektur Data & Design Pattern (Prioritas Utama)
*Rancang arsitektur data dan pola desain (design pattern) agar struktur kode rapi, modular, dan scalable.*

*   **Design Pattern Pilihan**: Tentukan pattern yang akan digunakan sesuai dengan kebutuhan game (contoh: *State Pattern* untuk AI/Player state, *Observer/Signals Pattern* untuk decoupling event, *Factory Pattern* untuk spawning item).
*   **Arsitektur & Penyimpanan Data**: *(Menyesuaikan jenis game)* Tentukan bagaimana data disimpan dan dikomunikasikan (contoh: ScriptableObject-based SSOT untuk Unity, Singleton untuk manager tertentu, atau local JSON saving).
*   **Mermaid Diagram**: Visualisasikan alur hubungan antar class dan sistem di bawah ini:
    ```mermaid
    graph TD
        %% Bagan visualisasi design pattern dan alur data
    ```

---

## 🏛️ 2. Desain FTUE (First-Time User Experience)
*Rancang pengenalan mekanik utama secara intuitif agar pemain langsung paham cara bermain.*

*   **Pendekatan FTUE**: *(Menyesuaikan jenis game)* Tentukan metode tutorial yang cocok untuk genre game ini (contoh: struktur *Kishōtenketsu* 4 langkah untuk linear action-platformer, *Contextual UI Hint* untuk game strategi/sandbox, atau *Sandbox Room* untuk roguelite).

---

## 🚀 3. Struktur Folder Modular & Optimisasi Performa
*Rancang struktur project agar scalable dan tentukan fokus optimisasi performa sejak dini.*

*   **Struktur Folder (Feature-Based/Modular)**: Kelompokkan asset dan script berdasarkan fitur (misal: Player, Enemy, UI) menggunakan Assembly Definitions (`.asmdef`) agar mudah dikelola dan waktu kompilasi cepat.
*   **Rencana Optimisasi**:
    *   *CPU/Memori*: (Contoh: Object Pooling untuk spawning objek berulang).
    *   *Rendering/UI*: (Contoh: Canvas Splitting untuk UI statis & dinamis).
