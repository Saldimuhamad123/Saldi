# Mobile Maps

Aplikasi Android offline-map bergaya **Avenza PDF Maps**, dibangun dari nol.
Nama aplikasi: **Mobile Maps**.

## Fitur

- **Import PDF** — pilih file PDF peta dari penyimpanan hp (atau buka file
  PDF langsung dari file manager dengan "Open with Mobile Maps"). PDF
  dirender jadi gambar resolusi tinggi menggunakan `PdfRenderer` bawaan
  Android (tanpa library eksternal).
- **Kalibrasi (geo-rectification)** — ketuk 2 titik referensi pada gambar
  peta, masukkan koordinat lat/long asli untuk tiap titik. Aplikasi
  menghitung transformasi linear pixel↔geo (`GeoTransform.java`), sama
  seperti cara Avenza mengkalibrasi peta PDF/scan yang belum ber-georeferensi.
- **Peta interaktif** — pan & zoom (pinch), titik GPS biru "Anda di sini",
  tambah titik (placemark) dengan ketuk lokasi di peta, ketuk marker untuk
  lihat detail.
- **Daftar titik** — lihat semua placemark, hapus jika perlu.
- **Export KML / KMZ** — dari toolbar peta, export seluruh placemark ke
  `.kml` atau `.kmz` lalu langsung dibagikan (share sheet Android) ke
  Google Earth, WhatsApp, email, dll.

## Struktur proyek

```
MobileMaps/
├── settings.gradle
├── build.gradle
├── gradle.properties
├── gradle/wrapper/gradle-wrapper.properties
└── app/
    ├── build.gradle
    ├── proguard-rules.pro
    └── src/main/
        ├── AndroidManifest.xml
        ├── java/com/mobilemaps/app/
        │   ├── MainActivity.java          # daftar peta + import PDF
        │   ├── CalibrateActivity.java      # layar kalibrasi
        │   ├── CalibrationView.java        # custom view tap 2 titik
        │   ├── MapViewActivity.java        # layar peta utama (GPS/marker/export)
        │   ├── MapCanvasView.java          # custom view pan/zoom/GPS/marker
        │   ├── PlacemarkListActivity.java  # daftar & hapus titik
        │   ├── PdfRasterizer.java          # PDF -> PNG (PdfRenderer)
        │   ├── GeoTransform.java           # transformasi pixel <-> lat/lon
        │   ├── KmlExporter.java            # export KML/KMZ
        │   ├── MapDocument.java / Placemark.java  # model data
        │   └── MapStore.java               # penyimpanan JSON lokal
        └── res/
            ├── layout/...
            ├── values/ (strings, colors, themes)
            └── xml/file_paths.xml
```

## Cara build APK (WAJIB dilakukan di komputer Anda)

Sandbox tempat proyek ini dibuat **tidak memiliki Android SDK, Gradle,
maupun akses internet ke server Google/Maven**, sehingga file `.apk` tidak
bisa dikompilasi di sini. Silakan build sendiri dengan langkah berikut —
prosesnya cepat dan gratis:

1. **Install Android Studio** (https://developer.android.com/studio) — sudah
   termasuk Android SDK & Gradle.
2. Ekstrak folder `MobileMaps` dari zip ini, lalu buka melalui
   **Android Studio → Open** (pilih folder `MobileMaps`, bukan file).
3. Tunggu proses "Gradle Sync" selesai (Android Studio otomatis mengunduh
   Gradle wrapper & dependency yang dibutuhkan — perlu koneksi internet).
4. Sambungkan HP Android (aktifkan **USB debugging** di Developer Options)
   atau gunakan emulator.
5. Klik tombol **Run ▶** (atau `Build > Build Bundle(s)/APK(s) > Build
   APK(s)`), lalu pilih perangkat Anda.
6. APK hasil build ada di
   `app/build/outputs/apk/debug/app-debug.apk` — file inilah yang bisa
   di-*install* langsung ke HP (via kabel USB, transfer file, atau
   "Build > Build APK" lalu klik "locate" untuk mengambil filenya).

### Build APK release (siap dibagikan ke orang lain)

```
Build > Generate Signed Bundle / APK... > APK
```
Ikuti wizard untuk membuat *keystore* baru (sekali saja, simpan baik-baik),
lalu pilih varian **release**. Hasilnya bisa langsung dikirim/diinstall di
HP mana pun (aktifkan "Install from unknown sources" di HP tujuan).

## Batasan versi ini

- Kalibrasi memakai 2 titik (transformasi linear per-sumbu), cocok untuk
  peta area lokal (jalur pendakian, denah bumi perkemahan, dsb).
  Untuk peta yang mencakup area sangat luas dengan proyeksi rumit,
  Avenza asli memakai transformasi affine/proyektif penuh — bisa
  dikembangkan lebih lanjut di `GeoTransform.java` bila diperlukan.
- Hanya me-render **halaman pertama** PDF (umumnya peta memang 1 halaman).
- Penyimpanan data pakai file JSON lokal (bukan cloud sync).

## Ide pengembangan lanjutan

- Multi-halaman PDF (pilih halaman yang ingin dipakai sebagai peta).
- Import/export format GPX selain KML/KMZ.
- Garis/jalur (polyline), bukan cuma titik.
- Sinkronisasi peta & titik ke Google Drive/cloud pribadi.


## Cara build APK TANPA install Android Studio (via GitHub Actions)

Proyek ini sudah dilengkapi workflow otomatis di
`.github/workflows/build-apk.yml`. Ini cara paling praktis kalau Anda tidak
mau install Android Studio:

1. Buat repository baru di GitHub (bisa lewat github.com, gratis).
2. Upload/push seluruh isi folder `MobileMaps` ini ke repo tersebut
   (lewat web "Upload files" atau `git push` dari komputer).
3. Buka tab **Actions** di repo GitHub Anda — build akan berjalan otomatis
   (memakan waktu sekitar 3-5 menit).
4. Setelah selesai (centang hijau ✅), klik run tersebut, lalu unduh
   artifact bernama **MobileMaps-debug-apk** di bagian bawah halaman.
5. Ekstrak zip artifact tadi → dapat file `app-debug.apk` → kirim/transfer
   ke HP Android → install (aktifkan dulu "Install from unknown sources").

Cara ini membangun APK di server GitHub yang sudah punya Android SDK
lengkap, jadi Anda tidak perlu install apa pun di komputer sendiri selain
akun GitHub.
