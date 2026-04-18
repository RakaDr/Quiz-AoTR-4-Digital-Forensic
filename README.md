# Digital Forensics Report: A Drone in the Snow (HTB Sherlock)

## Ringkasan Kasus
Investigasi ini berfokus pada sebuah drone hitam matte yang ditemukan setengah terkubur salju di Budapest, Hongaria. Temuan ini berkaitan erat dengan rencana operasional sebuah geng kriminal yang ditemukan sebelumnya pada sebuah disk. Laporan ini mendokumentasikan proses rekonstruksi rute, identifikasi target pengintaian, dan pelacakan lokasi asal operator menggunakan teknik *digital forensics* pada artifak *flight plan* dan log telemetri drone.

---

## Alat & Metodologi
Dalam investigasi ini, berikut ini merupakan alat-alat yang digunakan:
* **Visual Studio Code**: Digunakan untuk melihat isi struktur data pada file misi `AOTR_Winter_Blackout.plan`.
* **Ardupilot Web Viewer ([plot.ardupilot.org](https://plot.ardupilot.org/))**: Digunakan untuk visualisasi dari file log `log.bin`.
* **Google Maps**: Digunakan untuk melihat koordinat.

---

## Fase 1: Mission Planning Analysis (.plan)
Analisis dilakukan pada file rencana misi untuk memahami instruksi yang diberikan operator sebelum drone mengudara.

### **Task 1: Menghitung Total Mission Items**
**Langkah-langkah:**
1. Membuka file `AOT_Winter_Blackout.plan` menggunakan VS Code.
2. Mengidentifikasi objek `plannedHomePosition` sebagai titik referensi awal.
3. Menghitung jumlah objek di dalam array `mission.items`.
**Hasil:**
Terdapat 1 titik Home dan 48 instruksi operasional (Takeoff hingga Land).
* **Jawaban :** **49 Mission Items** <img width="1294" height="242" alt="image" src="https://github.com/user-attachments/assets/86ed9aa2-bf6c-48d8-a61a-30b62b4c40bb" />

* **Evidence :** <img width="402" height="58" alt="image" src="https://github.com/user-attachments/assets/4fd9d2e2-9ca9-4ace-9a9b-fe239d787e5d" />




### **Task 2: Mengidentifikasi Spline Waypoints**
**Langkah-langkah:**
1. Melakukan pencarian string `"command": 82` pada file `.plan`. Kode 82 merupakan standar MAVLink untuk `MAV_CMD_NAV_SPLINE_WAYPOINT`.
**Hasil:**
Ditemukan 46 kemunculan instruksi spline waypoint yang dirancang untuk pergerakan halus di area perkotaan.
* **Jawaban:** **46 Spline Waypoints** <img width="1303" height="225" alt="image" src="https://github.com/user-attachments/assets/20ae4081-df62-428a-8ab0-c99349cedc21" />

* **Evidence:** <img width="403" height="39" alt="image" src="https://github.com/user-attachments/assets/8a16d8c4-792e-4f2c-a61b-fa8a0b2ad635" />


### **Task 3: Melacak Target Pengintaian (Landmark)**
**Langkah-langkah:**
1. Mengekstraksi koordinat dari urutan waypoint yang membentuk pola melingkar.
2. Memasukkan koordinat pusat (sekitar 47.486 N, 19.046 E) ke Google Maps.
**Hasil:**
Drone melakukan observasi 360 derajat di atas **Citadella** dan patung **Liberty Statue** di **Gellért Hill**.
* **Jawaban:** **Citadella** <img width="1293" height="225" alt="image" src="https://github.com/user-attachments/assets/fec4f552-955a-4086-bcaf-453165b8eca4" />

* **Evidence:** <img width="951" height="622" alt="image" src="https://github.com/user-attachments/assets/0722dc61-5a5c-42db-9b74-277e677c156b" />


### **Task 4 & 5: Analisis Planned Hold/Loiter**
**Langkah-langkah:**
1. Mencari parameter `params[0]` yang memiliki nilai durasi pada instruksi navigasi.
**Hasil:**
Pada mission item ke-18 (`doJumpId: 18`), ditemukan nilai `params[0]: 30`.
* **Jawaban Task 4:** **18** <img width="1284" height="234" alt="image" src="https://github.com/user-attachments/assets/871173b7-6a6f-4e09-bd9d-4acb00f03a9c" />

* **Jawaban Task 5:** **30 detik** <img width="1296" height="249" alt="image" src="https://github.com/user-attachments/assets/5b3a6c61-7d0c-42a0-a54a-8d88e54ae154" />

* **Evidence:** <img width="767" height="407" alt="image" src="https://github.com/user-attachments/assets/a9ac7362-bd5b-4224-a5a5-d98213de64ea" />


### **Task 6: Identifikasi Jembatan (Bridge Crossing)**
**Langkah-langkah:**
1. Mengamati jembatan yang terdekat dari titik koordinat.
**Hasil:**
Rute drone melintasi jembatan ikonik di Budapest sesuai rencana misi.
* **Jawaban:** **Elisabeth Bridge (Erzsébet híd)**<img width="1289" height="230" alt="image" src="https://github.com/user-attachments/assets/f337a5cc-2562-4547-8fa7-089d3235df53" />

* **Evidence:** <img width="942" height="800" alt="image" src="https://github.com/user-attachments/assets/ad13a640-4d11-4e08-a2d0-cd3cd47cf5f9" />


---

## Fase 2: Telemetry Analysis (.bin)
Analisis pada fase ini beralih ke tingkat yang lebih *advance* menggunakan pendekatan *Command-Line Interface* (CLI) untuk mengekstrak data mentah dari `log.bin`. Alat utama yang digunakan adalah skrip Python `mavlogdump.py`, yang referensi dan tautan unduhannya saya dapatkan dari fitur *Hint* pada Task 7 di platform HackTheBox.

### **Task 7 - 10: Analisis Crash Event (CLI Parsing)**
**Langkah-langkah:**
1. **Memeriksa Pesan Sistem:** Menjalankan perintah `python mavlogdump.py --types MSG log.bin` di terminal PowerShell. Perintah ini mengekstrak seluruh log kejadian penting. Dari *output* tersebut, ditemukan pesan `Mission: 1 Takeoff` (awal terbang) dan pesan `SIM Hit ground` (momen jatuh).
2. **Menghitung Waktu Terbang (Task 7):** Menghitung selisih waktu operasional dari momen *Takeoff* hingga momen jatuhnya drone di salju.
3. **Mencatat Timestamp (Task 8):** Mengambil nilai `TimeUS` yang tertera persis di sebelah pesan `SIM Hit ground`, yaitu **687813098**.
4. **Mencari Koordinat dan Kecepatan (Task 9 & 10):** Mengeksekusi kueri bersyarat menggunakan perintah `python mavlogdump.py --types GPS --condition "TimeUS >= 687813098" log.bin | Select-Object -First 3`. Perintah ini menyaring data sensor satelit agar hanya menampilkan data `Lat`, `Lng`, dan `Spd` tepat pada detik jatuhnya drone.

**Hasil:**
Metode ini berhasil menceritakan kronologi akhir drone secara akurat, mulai dari waktu kejadian, lokasi, hingga energi kinetik saat hantaman terjadi.
* **Jawaban Task 7 (Total Flight Time):** **00:10:38.919** <img width="1294" height="360" alt="image" src="https://github.com/user-attachments/assets/bfb73716-b7aa-4d18-9890-190777acbf6d" />

* **Jawaban Task 8 (Crash TimeUS):** **687813098** <img width="1302" height="320" alt="image" src="https://github.com/user-attachments/assets/83f0ecbf-ba5e-4108-b6ea-5924011278bc" />

* **Jawaban Task 9 (Crash Coordinates):** **47.4903055, 19.0460476** <img width="1313" height="341" alt="image" src="https://github.com/user-attachments/assets/85e3bfc7-e2ba-4537-824a-468771725ddf" />

* **Jawaban Task 10 (Impact Speed):** **12.93254 m/s** <img width="1317" height="342" alt="image" src="https://github.com/user-attachments/assets/c4bebda3-c9df-401a-b190-9c926d192811" />


* **Evidence:** <img width="882" height="469" alt="image" src="https://github.com/user-attachments/assets/a751d248-efe1-423c-8fb9-316149e1945b" />
  <img width="656" height="869" alt="image" src="https://github.com/user-attachments/assets/b74c6e29-c584-4eaa-ab33-65c5a6ed7da0" />
  <img width="1655" height="110" alt="image" src="https://github.com/user-attachments/assets/f5d5f2c1-7aac-4c54-8d10-86fd913e4bbf" />


### **Task 11 - 13: Performa Maksimal & Titik Lepas Landas**
**Langkah-langkah:**
1. Mengonversi data sensor GPS ke dalam format *Comma Separated Values* dengan mengeksekusi `python mavlogdump.py --types GPS --format csv log.bin > gps_data.csv`. 
2. Melakukan *sorting* pada *dataset* tersebut untuk mencari puncak nilai (*peak values*) pada kolom `Alt` dan `Spd`.
3. Memeriksa baris awal (*head/first lines*) dari output GPS sesaat setelah log *ARMED* aktif untuk menemukan koordinat stabil awal.
**Hasil:**
Kapasitas kinerja drone selama beroperasi berhasil dipetakan, dan titik awal peluncuran (takeoff) yang menjadi *Indicator of Compromise* utama bagi operasi kepolisian berhasil didapatkan.
* **Jawaban Task 11 (Max GPS Altitude):** **[Isi Angka Tertinggi Alt] meter** <img width="1284" height="234" alt="image" src="[Link Gambar Pencarian Alt Maksimal]" />
* **Jawaban Task 12 (Fastest Ground Speed):** **[Isi Angka Tertinggi Spd] m/s** <img width="1284" height="234" alt="image" src="[Link Gambar Pencarian Spd Maksimal]" />
* **Jawaban Task 13 (Takeoff Coordinates):** **[Isi Lat, Lon Awal Terminal]** <img width="1284" height="234" alt="image" src="[Link Gambar Output Terminal Awal GPS]" />

---

## 🏁 Kesimpulan
Investigasi digital forensik ini berhasil mengungkap bahwa drone dikendalikan dari koordinat **[Jawaban Task 13]** untuk mengintai area **Citadella**. Kegagalan teknis yang menyebabkan drone terjatuh di **Döbrentei tér** telah memberikan artifak krusial bagi kepolisian Budapest untuk melacak jaringan kriminal yang terlibat.

---
*Laporan ini disusun oleh Raka Dwi Randika sebagai bagian dari penyelesaian tantangan HackTheBox Sherlock - Advent of The Relics.*
