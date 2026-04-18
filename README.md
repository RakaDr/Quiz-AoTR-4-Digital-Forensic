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
  <img width="1294" height="242" alt="image" src="https://github.com/user-attachments/assets/86ed9aa2-bf6c-48d8-a61a-30b62b4c40bb" />



### **Task 2: Mengidentifikasi Spline Waypoints**
**Langkah-langkah:**
1. Melakukan pencarian string `"command": 82` pada file `.plan`[cite: 1]. Kode 82 merupakan standar MAVLink untuk `MAV_CMD_NAV_SPLINE_WAYPOINT`.
**Hasil:**
Ditemukan 46 kemunculan instruksi spline waypoint yang dirancang untuk pergerakan halus di area perkotaan.
* **Jawaban:** **46 Spline Waypoints**
* **Evidence:**
![Screenshot Command 82 Search]

### **Task 3: Melacak Target Pengintaian (Landmark)**
**Langkah-langkah:**
1. Mengekstraksi koordinat dari urutan waypoint yang membentuk pola melingkar.
2. Memasukkan koordinat pusat (sekitar 47.486 N, 19.046 E) ke Google Maps.
**Hasil:**
[cite_start]Drone melakukan observasi 360 derajat di atas **Citadella** dan patung **Liberty Statue** di **Gellért Hill**[cite: 1].
* **Jawaban:** **Citadella / Gellért Hill**
* **Evidence:**
![Screenshot Google Maps Citadella]

### **Task 4 & 5: Analisis Planned Hold/Loiter**
**Langkah-langkah:**
1. Mencari parameter `params[0]` yang memiliki nilai durasi pada instruksi navigasi.
**Hasil:**
[cite_start]Pada mission item ke-18 (`doJumpId: 18`), ditemukan nilai `params[0]: 30`[cite: 1].
* **Jawaban Task 4:** **18**
* **Jawaban Task 5:** **30 detik**
* **Evidence:**
![Screenshot Loiter Param]

### **Task 6: Identifikasi Jembatan (Bridge Crossing)**
**Langkah-langkah:**
1. Mengamati trajektori drone saat menyeberangi Sungai Danube menuju area Döbrentei tér.
**Hasil:**
Rute drone melintasi jembatan ikonik di Budapest sesuai rencana misi.
* **Jawaban:** **Elisabeth Bridge (Erzsébet híd)**
* **Evidence:**
![Screenshot Trajektori Jembatan]

---

## 📈 Fase 2: Telemetry Analysis (.bin)
Analisis dilakukan dengan mengunggah file `log.bin` ke Ardupilot Web Viewer untuk melihat data sensor aktual saat kejadian.

### **Task 7 - 10: Analisis Crash Event**
**Langkah-langkah:**
1. Membuka tab *Messages* pada log viewer untuk mencari pesan error `CRASH`.
2. Menentukan selisih waktu dari pesan `ARMED` hingga pesan `CRASH`.
**Hasil:**
* **Total Flight Time (Task 7):** [Isi Durasi dari Log]
* **Crash Timestamp (Task 8):** [Isi TimeUS dari Log]
* **Crash Coordinates (Task 9):** [Isi Lat, Lon dari Log]
* **Impact Speed (Task 10):** [Isi Spd m/s dari Log]
**Evidence:**
![Screenshot Log Crash Message]

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
