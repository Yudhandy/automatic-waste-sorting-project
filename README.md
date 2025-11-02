# Proyek Pemilah Sampah Cerdas & Monitoring Ketinggian (Dual-Board)

![Edge Impulse](https://img.shields.io/badge/Machine%20Learning-Edge%20Impulse-blue.svg) ![ESP32](https://img.shields.io/badge/Hardware-ESP32%20|%20ESP32--CAM-purple.svg)

Proyek ini menggunakan arsitektur **dua mikrokontroler** untuk menciptakan sistem pemantauan dan pemilahan sampah yang efisien:

1.  **Board 1: ESP32-CAM (Pemilah Sampah)**
    * Bertanggung jawab penuh untuk tugas *Computer Vision* (TinyML).
    * Menjalankan model **Edge Impulse** untuk mengidentifikasi sampah (misal: organik vs. anorganik).
    * Mengontrol **Motor Servo** untuk menggerakkan aktuator pemilah.

2.  **Board 2: ESP32 (Monitoring Ketinggian)**
    * Bertanggung jawab untuk pemantauan dan konektivitas IoT.
    * Membaca **Sensor Ultrasonik** untuk mengukur ketinggian tumpukan sampah.
    * Mengirimkan data ketinggian secara *real-time* ke **Aplikasi Dashboard** (Blynk, Firebase, dll) melalui Wi-Fi.

---

## 📂 Struktur Folder Proyek

* **/trashbin/**
    * Berisi kode `.ino` yang di-upload ke **ESP32-CAM**.
    * Kode ini mencakup inferensi model Edge Impulse dan logika kontrol motor servo.
* **/Monitoring/**
    * Berisi kode `.ino` yang di-upload ke **ESP32 standar**.
    * Kode ini mencakup pembacaan sensor ultrasonik dan pengiriman data ke aplikasi (Blynk/Firebase/dll).
* **/Collect_Images_for_EdgeImpulse/**
    * Berisi kode `.ino` khusus untuk **ESP32-CAM** yang hanya digunakan saat proses pengambilan *datasheet* (gambar latihan) untuk Edge Impulse.
* **/library_edge_impulse_model/**
    * **FOLDER KOSONG (Placeholder):** buat folder kosong disinilah Anda harus meletakkan *library* model `.zip` yang telah Anda *download* dari Edge Impulse.

---

## ⚙️ Kebutuhan Hardware & Software

### Hardware
* **Board 1:** 1x **ESP32-CAM** (dengan modul programmer FTDI).
* **Board 2:** 1x **ESP32** (NodeMCU, WROOM, atau sejenisnya).
* **Sensor:** 2x **Sensor Ultrasonik** (misal: HC-SR04).
* **Aktuator:** 2x **Motor Servo** (misal: SG90).
* **Catu Daya:** Catu daya 5V yang stabil (disarankan 2A atau lebih, untuk memberi daya kedua board dan servo).
* Kabel Jumper, LED, Resistor, dll.

### Software
* [Arduino IDE](https://www.arduino.cc/en/software)
* Driver Board **ESP32** untuk Arduino IDE.
* **Library Model Edge Impulse** (Dibuat di Langkah 1).
* Library Sensor (misal: `NewPing` untuk ultrasonik).
* Library Aplikasi (misal: `BlynkSimpleEsp32.h`, `Firebase_ESP_Client.h`, dll. *Hanya diinstal untuk kode ESP32*).

---

## 🔌 Rangkaian & Pinout

Berikut adalah skematik rangkaian dan detail pinout yang digunakan dalam proyek ini.

![Skematik Rangkaian](image_af81c0.png)

### 1. Board 1: ESP32-CAM (Pemilah Sampah)
| Komponen | Pin Kontrol |
| :--- | :--- |
| Servo 1 | GPIO 14 |
| Servo 2 | GPIO 15 |
| Power | VCC (+5V) & GND |

### 2. Board 2: ESP32 (Monitoring Ketinggian)
| Komponen | Pin Trigger | Pin Echo |
| :--- | :--- | :--- |
| Sensor Ultrasonik 1 | GPIO 21 | GPIO 19 |
| Sensor Ultrasonik 2 | GPIO 5 | GPIO 18 |
| Power | VCC (+5V) & GND |

**Catatan Penting:** Pastikan **GND** dari catu daya 5V, **GND** ESP32-CAM, dan **GND** ESP32 terhubung semua menjadi satu **Common Ground**.

---

## 🚀 Langkah-Langkah Implementasi

Proyek ini dibagi menjadi dua alur kerja paralel untuk masing-masing board.

### Bagian 1: (Wajib) Melatih Model untuk ESP32-CAM

1.  **Kumpulkan Dataset:**
    * Buka folder `/Collect_Images_for_EdgeImpulse/`.
    * Upload kodenya ke **ESP32-CAM** Anda.
    * Gunakan skrip ini untuk mengambil banyak gambar *datasheet* (latihan) untuk setiap kategori sampah.
2.  **Upload & Latih di Edge Impulse:**
    * Buat proyek *Image Classification* baru di [Edge Impulse](https://www.edgeimpulse.com/).
    * Upload semua gambar Anda dan beri **Label** yang benar.
    * Buat *Impulse* (misal: `96x96`, `Image`, `Classification`).
    * Latih model Anda di tab "Classifier".
3.  **Deploy (Download Library):**
    * Buka tab **Deployment**, pilih **Arduino**.
    * Klik **Build** untuk men-download file `.zip` model Anda.
    * Simpan file `.zip` ini di folder `/library_edge_impulse_model/` sebagai referensi.

---

### Bagian 2: Setup ESP32-CAM (Pemilah Sampah)

1.  **Install Library Model:**
    * Buka Arduino IDE.
    * Pilih **Sketch** > **Include Library** > **Add .ZIP Library...**
    * Pilih file `.zip` yang Anda download dari Edge Impulse (Langkah 1).
2.  **Buka Proyek Pemilah:**
    * Buka file `.ino` dari dalam folder library nanti akan ada kode ino hasil training atau gunakan code /trashbin.ino lalu add library
3.  **Konfigurasi Kode:**
    * Sesuaikan pin untuk **Motor Servo** (`SERVO_PIN`) agar sesuai dengan rangkaian Anda (Pin 14 dan 15 berdasarkan skematik).
4.  **Upload Kode:**
    * Hubungkan **ESP32-CAM** (gunakan FTDI programmer).
    * Pilih *board* "AI Thinker ESP32-CAM" dan port yang benar.
    * Upload kode.
    * **Catatan:** Board ini sekarang akan fokus menjalankan inferensi dan menggerakkan servo.

---

### Bagian 3: Setup ESP32 (Monitoring Ketinggian)

1.  **Install Library Pendukung:**
    * Buka Arduino IDE.
    * Gunakan Library Manager untuk menginstal library sensor `NewPing` dan library aplikasi Anda (misal: `Blynk`).
2.  **Buka Proyek Monitoring:**
    * Buka file `.ino` dari dalam folder `/Monitoring/`.
3.  **Konfigurasi Kode:**
    * **Kredensial:** Masukkan `SSID` dan `PASSWORD` WiFi Anda.
    * **Koneksi Aplikasi:** Masukkan *Auth Token* atau *API Key* untuk aplikasi Anda (misal: Blynk, Firebase).
    * **Pinout:** Sesuaikan pin untuk Sensor Ultrasonik (`TRIG_PIN`, `ECHO_PIN`) agar sesuai dengan skematik (Pin 21, 19, 5, 18).
4.  **Upload Kode:**
    * Hubungkan **ESP32 standar** Anda.
    * Pilih *board* yang sesuai (misal: "NodeMCU-32S" atau "ESP32 Dev Module").
    * Upload kode.

### Bagian 4: Uji Coba

1.  Nyalakan kedua perangkat.
2.  **ESP32** akan terhubung ke WiFi dan mulai mengirim data ketinggian ke *dashboard* aplikasi Anda.
3.  **ESP32-CAM** akan mulai menjalankan kamera dan model.
4.  Arahkan sampah ke kamera, dan amati **servo** bergerak sesuai hasil klasifikasi (organik/anorganik).
5.  Periksa *dashboard* Anda untuk melihat data ketinggian sampah.

## 📄 Lisensi
MIT License.
