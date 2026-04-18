# Digital Forensics Report: A Drone in the Snow (HTB Sherlock)

## Ringkasan Kasus
Sebuah disk yang berhasil dipulihkan mengungkap rencana operasional, teknis, dan logistik yang matang dari sebuah geng kriminal. Tak lama kemudian, sebuah drone hitam matte ditemukan setengah terkubur salju di Budapest, Hongaria. Drone ini sangat cocok dengan rencana yang ditemukan. Laporan ini mendokumentasikan proses investigasi forensik terhadap *flight plan* dan log telemetri drone untuk merekonstruksi rute, mengidentifikasi titik jatuh, dan melacak lokasi asal operator.

---

## Alat & Metodologi
Dalam investigasi ini, berikut ini merupakan alat-alat yang digunakan :
* Visual Studio Code : Digunakan untuk melihat isi file misi `.plan`.
* Ardupilot Web Viewer (https://plot.ardupilot.org/) : Digunakan untuk memvisualisasikan log`.bin`.
* Google Maps : Digunakan untuk mengecek koordinat.

---

## Pertanyaan
1. Task 1: How many total mission items are defined in the flight plan, including Home, Takeoff, and Land commands?
2. Task 2: How many spline waypoints are in the mission?
3. Task 3: Which landmark/building does the drone fly around during the planned route?
4. Task 4: At which mission item did the drone have a planned hold/loiter?
5. Task 5: How long was the planned hold time at that mission item (seconds)?
6. Task 6: Which bridge is the drone planned to pass by while crossing the Danube in order to reach the other part of the city? (English name)
7. Task 7: What is the total flight time from takeoff to crash?
8. Task 8: What is the exact log timestamp of the crash event (TimeUS)?
9. Task 9: What are the coordinates where the drone crashed (lat, lon)?
10. Task 10: What was the ground impact speed reported at the moment of crash (m/s)?
11. Task 11: What was the maximum GPS altitude reached during the flight (meters)?
12. Task 12: What was the fastest GPS ground speed recorded (m/s)?
13. Task 13: What are the coordinates where the drone took off from (lat, lon), and therefore a good location for the police raid to catch the gang member?

---

## Jawaban
1. Task 1: Menghitung Total Mission Items
  Melalui analisis struktur JSON pada file `AOTR_Winter_Blackout.plan`, daftar perintah penerbangan terbagi dalam dua komponen utama. Komponen pertama adalah titik referensi Home ("plannedHomePosition") yang dihitung sebagai 1 item awal. Komponen kedua berisi rincian instruksi penerbangan dari Takeoff hingga Land yang terletak di dalam array "items". Berdasarkan parameter urutan eksekusi ("doJumpId"), array tersebut memuat tepat **48** perintah. Jumlah dari kedua data ini (1 titik Home + 48 instruksi operasional) membuktikan secara akurat bahwa drone menjalankan total **49 mission items**.
<img width="397" height="35" alt="image" src="https://github.com/user-attachments/assets/94c5f59f-ecd1-4f26-a1f9-4a43b694ae27" />

<img width="804" height="204" alt="image" src="https://github.com/user-attachments/assets/a5615ff9-118c-4072-9830-dcc64351f3c6" />

2. Task 2: Mengidentifikasi Spline Waypoints
  Untuk memahami kompleksitas rute yang diprogram, investigasi diarahkan untuk menghitung jumlah spline waypoints pada misi tersebut. Berdasarkan standar protokol MAVLink yang digunakan dalam file `AOTR_Winter_Blackout.plan`, setiap instruksi navigasi untuk belokan melengkung yang halus direpresentasikan oleh kode perintah 82 (MAV_CMD_NAV_SPLINE_WAYPOINT). Analisis dilakukan dengan membuka file rencana misi menggunakan Visual Studio Code dan memanfaatkan fitur pencarian (Ctrl+F) untuk mencari seluruh kemunculan string *"command": 82*. Berdasarkan hasil pencarian tersebut, teridentifikasi bahwa operator menggunakan total **46** spline waypoints guna memastikan drone dapat bermanuver dengan presisi tinggi di area perkotaan Budapest.
<img width="406" height="39" alt="image" src="https://github.com/user-attachments/assets/5eceb181-de6d-49c8-ac92-5f8cb85a1c50" />

<img width="1485" height="151" alt="image" src="https://github.com/user-attachments/assets/ad7eb7b5-e498-415e-ade3-90d27cb6d6cb" />


3. Task 3: Melacak Target Pengintaian Udara (Landmark)

