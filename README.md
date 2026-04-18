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
  Untuk mengetahui total Mission Items yang diprogram pada drone, kita  buka isi file `AOTR_Winter_Blackout.plan` menggunakan vscode. Dengan menghitung titik *Home* (sebagai item awal) dan seluruh objek instruksi yang berada di dalam array `"items"` (dari perintah *Takeoff* hingga *Land*), teridentifikasi bahwa drone tersebut memiliki total **49** *mission items*.
  <img width="804" height="204" alt="image" src="https://github.com/user-attachments/assets/a5615ff9-118c-4072-9830-dcc64351f3c6" />

2. 

