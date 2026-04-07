
1.5.4 Pertanyaan Praktikum:

1. Pada kondisi apa program masuk ke blok if? Program akan mengeksekusi instruksi di dalam blok if apabila nilai dari variabel timeDelay sudah mencapai angka kurang dari atau sama dengan 100.
2. Pada kondisi apa program masuk ke blok else? Program akan masuk ke blok else jika kondisi pada pernyataan if tidak terpenuhi, yakni ketika nilai variabel timeDelay masih berada di atas ambang batas 100.
3. Apa fungsi dari perintah delay(timeDelay)? Perintah delay(timeDelay) berfungsi untuk menghentikan sementara eksekusi program selama durasi tertentu dalam satuan milidetik berdasarkan nilai variabel timeDelay. Dalam percobaan ini, perintah tersebut digunakan untuk mengatur lamanya durasi LED menyala (HIGH) maupun mati (LOW) guna menciptakan efek kedipan.
4. Modifikasi Program Kedip LED Dinamis (Cepat ke Sedang)
Program ini memodifikasi alur kedipan LED agar tidak langsung melakukan reset ke kondisi lambat saat mencapai kecepatan maksimal, melainkan mengalami transisi melambat secara bertahap (Cepat -> Sedang -> Mati/Lambat).
## Source Code

```cpp
const int ledPin = 6;          // Menetapkan pin digital 6 sebagai output LED [cite: 123]
int timeDelay = 1000;          // Inisialisasi awal durasi delay 1000ms (Lambat) [cite: 124]
bool percepat = true;          // Flag untuk menentukan arah perubahan kecepatan

void setup() {
  pinMode(ledPin, OUTPUT);     // Mengonfigurasi pin 6 sebagai output [cite: 127]
}

void loop() {
  // Fase Eksekusi Fisik (Kedip LED)
  digitalWrite(ledPin, HIGH);  // Menyalakan LED [cite: 130]
  delay(timeDelay);            // Menahan status nyala sesuai nilai variabel timeDelay [cite: 131]
  digitalWrite(ledPin, LOW);   // Mematikan LED [cite: 132]
  delay(timeDelay);            // Menahan status mati sesuai nilai variabel timeDelay [cite: 134]

  // Fase Logika Kontrol Dinamis
  if (percepat) {              
    if (timeDelay <= 100) {    // Jika sudah mencapai batas tercepat (100ms) 
      percepat = false;        // Ubah arah logika menjadi melambat (tidak langsung reset)
    } else {
      timeDelay -= 100;        // Jika belum, terus percepat dengan mengurangi delay [cite: 140]
    }
  } else {                     
    if (timeDelay >= 1000) {   // Jika sudah kembali ke kondisi terlambat (1000ms)
      percepat = true;         // Siapkan fase untuk kembali mempercepat pada siklus berikutnya
      delay(3000);             // Jeda reset selama 3 detik sebelum mulai lagi 
    } else {
      timeDelay += 450;        // Tambah delay secara signifikan untuk efek transisi ke "Sedang"
    }
  }
}

1.6.4 Pertanyaan Praktikum
1. Gambarkan rangkaian schematic 5 LED running yang digunakan pada percobaan!
https://www.tinkercad.com/things/lJ7asnfqmml-modul-1-perulangan?sharecode=NnY-rBQmyowj89p1c1Dd5aabo8-jz74xKRGeU2EzMMo
2. Penjelasan Efek LED Berjalan dari Kiri ke KananEfek berjalan dari kiri ke kanan dihasilkan melalui penggunaan struktur perulangan for yang melakukan inkrementasi (penambahan) pada variabel pin. Program memulai siklus dari pin terendah (Pin 2) dan bergerak menuju pin tertinggi (Pin 7). Di dalam setiap tahapan perulangan, perintah digitalWrite(ledPin, HIGH) akan menyalakan LED, diikuti dengan delay(timer) untuk memberikan jeda visual, kemudian digitalWrite(ledPin, LOW) untuk mematikan LED tersebut sebelum berpindah ke pin berikutnya.
3. Penjelasan Efek LED Kembali dari Kanan ke KiriEfek kembali dari kanan ke kiri dicapai dengan menggunakan struktur perulangan for yang melakukan dekrementasi (pengurangan). Program menginisialisasi variabel pin mulai dari angka tertinggi (Pin 7) dan terus berkurang hingga mencapai pin terendah (Pin 2). Logika eksekusi di dalam blok tetap sama, yaitu menghidupkan pin, memberikan jeda, dan mematikan kembali pin tersebut sehingga secara visual LED tampak bergerak mundur
4. README.md: Program Tiga LED Kanan dan Kiri Bergantian
Program ini membagi 6 LED menjadi dua kelompok: Kelompok Kiri (Pin 2, 3, 4) dan Kelompok Kanan (Pin 5, 6, 7). Kedua kelompok akan menyala secara bergantian.
<img width="1280" height="568" alt="Fabulous Blad-Habbi" src="https://github.com/user-attachments/assets/88f43d4a-048c-47f0-9b85-98aa9fb43ec7" />

void setup() {
  // Menggunakan perulangan untuk menginisialisasi pin 2 sampai 7 sebagai OUTPUT
  for (int ledPin = 2; ledPin < 8; ledPin++) {
    pinMode(ledPin, OUTPUT); 
  }
}

void loop() {
  // --- MENYALAKAN 3 LED KIRI ---
  for (int i = 2; i <= 4; i++) digitalWrite(i, HIGH); // Pin 2, 3, 4 aktif
  for (int i = 5; i <= 7; i++) digitalWrite(i, LOW);  // Pin 5, 6, 7 mati
  delay(1000); // Jeda 1 detik

  // --- MENYALAKAN 3 LED KANAN ---
  for (int i = 2; i <= 4; i++) digitalWrite(i, LOW);  // Pin 2, 3, 4 mati
  for (int i = 5; i <= 7; i++) digitalWrite(i, HIGH); // Pin 5, 6, 7 aktif
  delay(1000); // Jeda 1 detik
}

<img width="1018" height="1014" alt="code89" src="https://github.com/user-attachments/assets/e47f10c1-3fc6-4950-b259-889bc1e44d7e" />
