# Backend API Klasifikasi Penyakit Kulit

Repositori ini berisi kode sumber untuk layanan backend yang dirancang untuk menerima gambar penyakit kulit, melakukan klasifikasi menggunakan model *machine learning*, dan menyimpan hasilnya. Proyek ini dibangun dengan Node.js menggunakan *framework* Hapi dan TensorFlow.js untuk inferensi model.

## Deskripsi

Aplikasi ini menyediakan sebuah API endpoint yang memungkinkan pengguna untuk mengunggah gambar. Gambar tersebut kemudian akan diproses dan diklasifikasikan ke dalam beberapa kategori penyakit kulit seperti 'Melanocytic nevus', 'Squamous cell carcinoma', dan 'Vascular lesion'. Hasil prediksi beserta skor kepercayaannya akan disimpan di Google Cloud Firestore.

## Fitur Utama

  - **Server HTTP**: Dibangun menggunakan *framework* Hapi.js yang ringan dan andal.
  - **Klasifikasi Gambar**: Menggunakan model TensorFlow.js (`tfjs-node`) untuk melakukan prediksi pada gambar yang diunggah.
  - **Penyimpanan Data**: Terintegrasi dengan Google Cloud Firestore untuk menyimpan riwayat prediksi.
  - **Penanganan Error**: Implementasi *custom error handling* untuk memberikan respons yang jelas saat terjadi kesalahan input dari klien.
  - **Konfigurasi Fleksibel**: Menggunakan variabel lingkungan (*environment variables*) untuk konfigurasi port, host, dan URL model.

## Prasyarat

Sebelum menjalankan aplikasi ini, pastikan Anda telah menginstal:

  - [Node.js](https://nodejs.org/) (v14 atau lebih baru direkomendasikan)
  - npm (biasanya terinstal bersama Node.js)

## Instalasi

1.  **Clone repositori ini:**

    ```bash
    git clone https://github.com/nama-pengguna/nama-repo.git
    cd nama-repo
    ```

2.  **Instal semua dependensi:**

    ```bash
    npm install
    ```

## Konfigurasi Lingkungan

Aplikasi ini memerlukan beberapa variabel lingkungan untuk berjalan dengan benar. Buat sebuah *file* bernama `.env` di direktori utama proyek dan tambahkan variabel berikut:

```env
# Konfigurasi Server
HOST=0.0.0.0
PORT=3000

# URL Model Machine Learning
# Pastikan URL ini dapat diakses oleh server
MODEL_URL=https://storage.googleapis.com/path/to/your/model.json

# Konfigurasi Google Cloud (jika menjalankan di luar Google Cloud Environment)
# GOOGLE_APPLICATION_CREDENTIALS="/path/to/your/service-account-key.json"
```

**Catatan**: `GOOGLE_APPLICATION_CREDENTIALS` diperlukan jika Anda menjalankan aplikasi di lingkungan lokal atau di luar ekosistem Google Cloud agar dapat terautentikasi dengan Firestore.

## Menjalankan Aplikasi

Anda dapat menjalankan aplikasi dalam dua mode:

1.  **Mode Pengembangan (Development)**
    Menggunakan `nodemon` untuk me-restart server secara otomatis setiap kali ada perubahan pada kode.

    ```bash
    npm run start:dev
    ```

2.  **Mode Produksi (Production)**
    Menjalankan server menggunakan `node`.

    ```bash
    npm start
    ```

Server akan berjalan di `http://localhost:3000` atau sesuai dengan konfigurasi `HOST` dan `PORT` Anda.

## Dokumentasi API

### `POST /predict`

Endpoint ini digunakan untuk mengunggah gambar dan mendapatkan hasil klasifikasi penyakit kulit.

  - **Request Body**: `multipart/form-data`

      - `image`: *File* gambar yang akan diprediksi (contoh: `.jpg`, `.png`).

  - **Contoh Penggunaan (cURL)**:

    ```bash
    curl -X POST \
      http://localhost:3000/predict \
      -F "image=@/path/to/your/image.jpg"
    ```

  - **Respons Sukses (201 Created)**:

    ```json
    {
        "status": "sukses",
        "message": "Model is predicted successfully.",
        "data": {
            "id": "eF5E12-iXVYJdD_z",
            "result": "Squamous cell carcinoma",
            "explanation": "Penjelasan mengenai penyakit...",
            "suggestion": "Saran penanganan...",
            "confidenceScore": 99,
            "createdAt": "2025-06-19T16:00:00.000Z"
        }
    }
    ```

    *Catatan: Nilai `message` akan bervariasi tergantung pada `confidenceScore`.*

  - **Respons Gagal (400 Bad Request)**:
    Terjadi jika *payload* yang dikirim tidak valid (misalnya, bukan gambar).

    ```json
    {
        "status": "fail",
        "message": "Terjadi kesalahan dalam melakukan prediksi"
    }
    ```

## Struktur Proyek

```
.
├── src/
│   ├── exceptions/       # Berisi kelas error kustom (ClientError, InputError)
│   ├── server/           # Konfigurasi dan logika server Hapi
│   │   ├── handler.js    # Logika untuk menangani permintaan API
│   │   ├── routes.js     # Definisi rute-rute API
│   │   └── server.js     # Titik masuk utama, inisialisasi server
│   └── services/         # Layanan pendukung
│       ├── inferenceService.js # Logika untuk inferensi model ML
│       ├── loadModel.js      # Fungsi untuk memuat model TensorFlow.js
│       └── storeData.js      # Logika untuk menyimpan data ke Firestore
├── .gitignore            # File dan folder yang diabaikan oleh Git
└── package.json          # Metadata proyek dan daftar dependensi
```

## Dependensi Utama

  - **@hapi/hapi**: *Framework* web untuk membangun server.
  - **@tensorflow/tfjs-node**: *Library* TensorFlow.js untuk Node.js, digunakan untuk inferensi model.
  - **@google-cloud/firestore**: *Client library* untuk berinteraksi dengan Google Cloud Firestore.
  - **nanoid**: *Library* untuk menghasilkan ID unik.
  - **nodemon**: Alat bantu pengembangan untuk me-restart aplikasi secara otomatis.
  - **@eslint/js**: *Linter* untuk menjaga kualitas dan konsistensi kode.
