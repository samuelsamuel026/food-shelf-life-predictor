# 🥦 Aturan Acuan Masa Simpan Pangan (Food Shelf-Life Rules)

Dokumen ini merupakan basis data referensi resmi (*baseline rules*) yang digunakan oleh **Developer 2 (Backend)** untuk mengimplementasikan algoritma kalkulasi masa simpan pada modul `server/app/services/prediction_service.py`.

---

## 📌 1. Prinsip & Batasan Ilmiah (Disclaimer)
1. **Acuan Standar:** Aturan durasi ini dihimpun dari standar *USDA FoodKeeper App* dan pedoman penyimpanan pangan BPOM untuk bahan segar yang belum diolah.
2. **Kondisi Suhu:**
    * **Chiller / Refrigerator:** Suhu stabil 0°C – 4°C.
    * **Freezer:** Suhu stabil ≤ -18°C.
    * **Pantry / Suhu Ruang:** Suhu kamar sejuk (20°C – 25°C).
3. **Batas Jaminan:** Angka di bawah adalah **estimasi kualitas terbaik (*Quality / Best-by Date*)**, bukan jaminan laboratorium bebas mikrobiologi. Pengguna tetap diedukasi memeriksa fisik makanan (aroma, tekstur, lendir, jamur) sebelum konsumsi.

---

## 📊 2. Tabel Matriks 10 Bahan Makanan Awal (Sprint 1 MVP)

| ID | Nama Bahan Makanan | Kategori | Simpan Chiller (0°C – 4°C) | Simpan Freezer (≤ -18°C) | Simpan Suhu Ruang | Catatan Penting & Tips Simpan |
| :---: | :--- | :--- | :---: | :---: | :---: | :--- |
| `egg` | **Telur Ayam** | Protein / Dairy | 21 – 28 hari (3–4 minggu) | *Tidak disarankan (utuh)* | 7 – 10 hari | Simpan di karton/wadah rak dalam, hindari rak pintu kulkas karena suhu fluktuatif. |
| `milk` | **Susu Cair (Pasteurisasi)** | Dairy / Minuman | 5 – 7 hari (setelah buka) | 1 – 3 bulan | 2 – 4 jam | Begitu segel dibuka, konsumsi dalam seminggu meski tanggal expired kemasan masih lama. |
| `chicken` | **Daging Ayam Mentah** | Daging Segar | 1 – 2 hari | 9 bulan | 2 jam | Bungkus rapat kedap udara sebelum masuk freezer untuk mencegah *freezer burn*. |
| `beef` | **Daging Sapi Segar** | Daging Segar | 3 – 5 hari | 6 – 12 bulan | 2 jam | Simpan di bagian terbawah kulkas agar tetesan air daging tidak mencemari makanan lain. |
| `tomato` | **Tomat** | Sayur / Buah | 7 – 14 hari | 2 bulan | 3 – 5 hari | Tomat mentah lebih baik di suhu ruang sampai matang, baru dipindahkan ke chiller. |
| `carrot` | **Wortel** | Sayuran Akar | 14 – 21 hari (2–3 minggu) | 10 – 12 bulan | 3 – 5 hari | Potong tangkai daun hijau di atasnya agar tidak menyerap kelembapan umbi wortel. |
| `spinach` | **Bayam / Sayuran Hijau** | Sayuran Daun | 3 – 5 hari | 8 – 10 bulan (blanched) | 1 hari | Cuci hanya saat mau dimasak. Jangan simpan bayam dalam kondisi basah/lembab. |
| `banana` | **Pisang** | Buah-buahan | 5 – 7 hari (kulit menghitam) | 2 – 3 bulan (kupas) | 3 – 5 hari | Menyimpan pisang di kulkas membuat kulit cepat cokelat, namun daging buah tetap segar. |
| `apple` | **Apel** | Buah-buahan | 21 – 30 hari (3–4 minggu) | 8 bulan | 5 – 7 hari | Apel mengeluarkan gas etilen; pisahkan dari sayuran hijau agar sayuran tidak cepat layu. |
| `tofu` | **Tahu Putih Segar** | Protein Nabati | 3 – 5 hari | 3 – 5 bulan | 1 hari | Rendam dalam air matang bersih di dalam wadah kedap udara; ganti airnya setiap hari. |

---

## 🧮 3. Formula Kalkulasi Backend
```python
# Formula Dasar Penentuan Tanggal Kedaluwarsa:
estimated_expiry_date = storage_date + timedelta(days=default_shelf_life_days)
remaining_days = (estimated_expiry_date - current_date).days

# Penentuan Status Indikator Visual (Eat Me First Sorting):
if remaining_days <= 2:
    status_badge = "CRITICAL_RED"    # Segera Olah / Habiskan
elif 3 <= remaining_days <= 5:
    status_badge = "WARNING_YELLOW"  # Perhatian
else:
    status_badge = "SAFE_GREEN"      # Aman
