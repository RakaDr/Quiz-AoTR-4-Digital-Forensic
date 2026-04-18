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
Analisis dilakukan dengan mengunggah file `log.bin` ke Ardupilot Web Viewer untuk melihat data sensor aktual saat kejadian.

### **Task 7 - 10: Analisis Crash Event (Telemetri Dinamis)**
**Langkah-langkah:**
1. Memeriksa panel `Messages` pada file `log.bin` menggunakan Ardupilot Web Viewer untuk mencari *timestamp* saat drone `ARMED` dan saat mengalami `Crash`.
2. Menyinkronkan nilai `TimeUS` dari pesan Crash tersebut dengan grafik data modul `GPS` (`GPS.Lat`, `GPS.Lng`, dan `GPS.Spd`) tepat pada detik terakhir log terputus.
**Hasil:**
Ditemukan log teks yang mengonfirmasi waktu presisi terjadinya kegagalan sistem, beserta data lokasi dan kecepatan darat saat drone menghantam permukaan.
* **Jawaban Task 7 (Total Flight Time):** **17 menit 55 detik** <img width="1284" height="234" alt="image" src="[Masukkan Link Gambar Bukti Waktu Armed ke Crash]" />

* **Jawaban Task 8 (Crash TimeUS):** **1090427977** <img width="1296" height="249" alt="image" src="[Masukkan Link Gambar Pesan Crash: Crash]" />

* **Jawaban Task 9 (Crash Coordinates):** **[Isi Lat, Lon dari grafik GPS]** <img width="1284" height="234" alt="image" src="[Masukkan Link Gambar Kursor di Ujung Grafik Lat Lng]" />

* **Jawaban Task 10 (Impact Speed):** **[Isi Kecepatan dari GPS.Spd] m/s** <img width="1284" height="234" alt="image" src="[Masukkan Link Gambar Kursor di Ujung Grafik Spd]" />

* **Evidence:** <img width="942" height="800" alt="image" src="[Masukkan Link Gambar Keseluruhan Layar Ardupilot Web Viewer]" />

### **Task 11 - 12: Metrik Performa Terbang**
**Langkah-langkah:**
1. Memeriksa grafik `GPS.Alt` (ketinggian) dan `GPS.Spd` (kecepatan).
**Hasil:**
* **Max Altitude (Task 11):** [Isi Nilai Tertinggi Alt] meter.
* **Max Ground Speed (Task 12):** [Isi Nilai Tertinggi Spd] m/s.
**Evidence:**
![Screenshot Grafik Alt/Spd]

### **Task 13: Melacak Lokasi Lepas Landas (Takeoff)**
**Langkah-langkah:**
1. Melihat koordinat GPS pada saat pertama kali status drone berubah menjadi `Armed` atau saat ketinggian mulai naik.
**Hasil:**
Koordinat ini menjadi titik krusial bagi kepolisian untuk melakukan penggerebekan basis operator.
* **Jawaban:** [Isi Lat, Lon Takeoff]
* **Evidence:**
![Screenshot Takeoff Coordinates]

---

## 🏁 Kesimpulan
Investigasi digital forensik ini berhasil mengungkap bahwa drone dikendalikan dari koordinat **[Jawaban Task 13]** untuk mengintai area **Citadella**. Kegagalan teknis yang menyebabkan drone terjatuh di **Döbrentei tér** telah memberikan artifak krusial bagi kepolisian Budapest untuk melacak jaringan kriminal yang terlibat.

---
*Laporan ini disusun oleh Raka Dwi Randika sebagai bagian dari penyelesaian tantangan HackTheBox Sherlock - Advent of The Relics.*
