2.6.4 Pertanyaan Praktikum 
1. Gambarkan rangkaian schematic yang digunakan pada percobaan!
   <img width="1280" height="568" alt="Copy of Seven Segment" src="https://github.com/user-attachments/assets/6d482693-4412-4475-ba21-b6648bdc82ab" />
https://www.tinkercad.com/things/bzY2rX07uz7/editel?sharecode=EQjWfjvIncHhErvODan7q5Gq-9TnicP9JkGtGAxGqhc

2. Mengapa pada push button digunakan mode INPUT_PULLUP pada Arduino Uno? 
Apa keuntungannya dibandingkan rangkaian biasa?
Alasan penggunaan:
Menghindari kondisi floating (nilai tidak stabil)
Membuat input selalu memiliki kondisi default (HIGH)
Cara kerja:
Saat tombol tidak ditekan → nilai = HIGH
Saat tombol ditekan → nilai = LOW
Keuntungan dibanding rangkaian biasa:
Tidak perlu resistor eksternal
Rangkaian lebih sederhana
Mengurangi noise pada sinyal input
Lebih stabil dan mudah diimplementasikan

3. Jika salah satu LED segmen tidak menyala, apa saja kemungkinan penyebabnya dari 
sisi hardware maupun software?
Dari sisi hardware:
Kabel jumper tidak terpasang dengan benar
Resistor rusak atau tidak terpasang
Seven segment rusak
Pin Arduino rusak
Kesalahan wiring (pin salah sambung)
Dari sisi software:
Penulisan pin tidak sesuai dengan rangkaian
Logika HIGH/LOW terbalik
Data array salah
Kesalahan dalam program loop

4. Modifikasi rangkaian dan program dengan dua push button yang berfungsi sebagai 
penambahan (increment) dan pengurangan (decrement) pada sistem counter dan 
berikan penjelasan disetiap baris kode nya dalam bentuk README.md!

C++
'
nt buttonUp = 2;
int buttonDown = 3;

int count = 0;

void setup() {
  pinMode(buttonUp, INPUT_PULLUP);
  pinMode(buttonDown, INPUT_PULLUP);

  for(int i = 4; i <= 11; i++){
    pinMode(i, OUTPUT);
  }
}

void loop() {
  if(digitalRead(buttonUp) == LOW){
    count++;
    delay(200);
  }

  if(digitalRead(buttonDown) == LOW){
    count--;
    delay(200);
  }

  if(count > 15) count = 0;
  if(count < 0) count = 15;

  tampilkan(count);
}

void tampilkan(int num){
  // isi sesuai mapping seven segment
}

2.7 Pertanyaan Praktikum 
1. Uraikan hasil tugas pada praktikum yang telah dilakukan pada setiap percobaan!
Percobaan 2A:
Seven segment berhasil menampilkan angka 0–9 dan huruf A–F secara berurutan tanpa error.
Percobaan 2B:
Push button berhasil digunakan sebagai input untuk mengontrol counter, dan nilai dapat berubah sesuai tombol yang ditekan.
Secara keseluruhan, semua percobaan berjalan sesuai dengan spesifikasi.

2. Bagaimana prinsip kerja dari Seven Segment Display dalam menampilkan angka dan 
karakter?
Cara kerja:
Setiap segmen dinyalakan atau dimatikan dengan sinyal digital
Kombinasi nyala LED membentuk angka
Contoh:
Angka 0 → semua segmen menyala kecuali g
Angka 1 → hanya segmen b dan c
Dengan kombinasi ini, dapat ditampilkan angka 0–9 dan huruf A–F.

3. Jelaskan bagaimana sistem counter bekerja pada program tersebut! 
Alur kerja:
Nilai awal = 0
Jika tombol ditekan → nilai bertambah atau berkurang
Nilai ditampilkan ke seven segment
Program berjalan berulang (loop)
Logika utama:
Increment → count++
Decrement → count--
Batas → 0 sampai 15
Sistem ini bekerja secara real-time berdasarkan input dari user.
