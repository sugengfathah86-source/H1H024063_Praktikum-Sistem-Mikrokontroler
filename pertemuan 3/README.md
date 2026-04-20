Jawaban Pertanyaan Praktikum 3.5 (iv)

Jelaskan proses dari input keyboard hingga LED menyala/mati! Proses dimulai ketika pengguna menekan tombol (misalnya '1') pada keyboard di dalam Serial Monitor. Karakter tersebut dikirimkan dari komputer melalui kabel USB dalam bentuk aliran bit serial menuju pin RX Arduino. Di dalam kode, fungsi Serial.available() mendeteksi adanya data masuk di buffer. Karakter kemudian dibaca oleh Serial.read() dan disimpan dalam variabel data. Program mengevaluasi variabel tersebut menggunakan struktur if-else; jika data adalah '1', perintah digitalWrite(PIN_LED, HIGH) dikirimkan ke Pin 8, sehingga arus mengalir dan menyalakan LED.
Mengapa digunakan Serial.available() sebelum membaca data? Apa yang terjadi jika baris tersebut dihilangkan? Fungsi Serial.available() digunakan untuk memastikan bahwa sudah ada data yang benar-benar diterima dan tersimpan di dalam buffer serial sebelum mikrokontroler mencoba membacanya. Jika baris ini dihilangkan, fungsi Serial.read() akan terus dieksekusi setiap kali loop() berjalan. Karena kecepatan loop jauh lebih tinggi daripada kecepatan pengetikan manusia, Serial.read() akan sering membaca nilai -1 (kosong), yang dapat menyebabkan program menjalankan logika "Perintah tidak dikenal" secara terus-menerus dan mengganggu kinerja sistem.
Modifikasi Program (Fitur Blink input '2') #include <Arduino.h>
const int PIN_LED = 8; // Sesuai Tabel 3.1 [cite: 169] int mode = 0; // 0: OFF, 1: ON, 2: BLINK unsigned long prevMillis = 0; bool ledState = LOW;

void setup() { Serial.begin(9600); [cite: 126] Serial.println("1: ON, 0: OFF, 2: BLINK"); [cite: 127] pinMode(PIN_LED, OUTPUT); [cite: 128] }

void loop() { if (Serial.available() > 0) { [cite: 132] char data = Serial.read(); [cite: 133]

if (data == '1') {
  mode = 1;
  digitalWrite(PIN_LED, HIGH);
  Serial.println("LED ON");
} else if (data == '0') {
  mode = 0;
  digitalWrite(PIN_LED, LOW);
  Serial.println("LED OFF");
} else if (data == '2') {
  mode = 2;
  Serial.println("LED BLINKING...");
}
}

// Logika Blink menggunakan millis() agar tetap responsif if (mode == 2) { unsigned long currMillis = millis(); if (currMillis - prevMillis >= 500) { // Interval 500ms prevMillis = currMillis; ledState = !ledState; digitalWrite(PIN_LED, ledState); } } }

Link Simulasi: https://wokwi.com/projects/461830440435135489

Tentukan apakah menggunakan delay() atau millis()! Jelaskan pengaruhnya terhadap sistem Dalam kasus modifikasi ini, sangat disarankan menggunakan millis(). Pengaruh delay(): Jika menggunakan delay(500) untuk membuat LED berkedip, mikrokontroler akan "berhenti" total selama waktu tersebut. Akibatnya, jika Anda mengetik '0' saat LED sedang dalam fase delay, Arduino tidak akan merespons sampai waktu delay selesai. Pengaruh millis(): Dengan millis(), program tetap berjalan terus-menerus (non-blocking). Arduino dapat melakukan tugas lain (seperti mengecek input Serial) sambil menunggu waktu yang tepat untuk mengubah status LED, sehingga sistem menjadi jauh lebih responsif dan efisien.
Jawaban Pertanyaan Praktikum 3.6.4

Jelaskan bagaimana cara kerja komunikasi I2C antara Arduino dan LCD pada rangkaian tersebut! Komunikasi I2C pada rangkaian ini bekerja dengan menggunakan dua jalur kabel utama, yaitu SDA (Serial Data) untuk pengiriman data dan SCL (Serial Clock) untuk sinkronisasi waktu. Arduino Uno bertindak sebagai Master yang mengendalikan aliran data, sedangkan LCD I2C bertindak sebagai Slave. Prosesnya dimulai ketika Arduino mengirimkan sinyal "Start" diikuti dengan alamat I2C unik milik LCD (misalnya 0x27) agar hanya LCD tersebut yang merespons. Setelah koneksi terjalin, Arduino mengirimkan data karakter atau perintah instruksi bit demi bit yang disinkronkan oleh sinyal detak pada jalur SCL.
Apakah pin potensiometer harus seperti itu? Jelaskan yang terjadi apabila pin kiri dan pin kanan tertukar! Konfigurasi standar menempatkan satu kaki luar pada GND dan kaki luar lainnya pada 5V, dengan kaki tengah (wiper) terhubung ke pin analog A0. Secara fungsional, pin kiri dan kanan boleh tertukar karena potensiometer bekerja sebagai pembagi tegangan variabel. Namun, jika keduanya tertukar, arah pembacaan nilai akan berbalik: jika sebelumnya memutar ke kanan (searah jarum jam) meningkatkan nilai ADC, maka setelah tertukar, memutar ke kanan justru akan memperkecil nilai ADC dan tegangan yang terbaca.
Modifikasi Program (UART & I2C) #include <Wire.h> // Library untuk komunikasi I2C [cite: 191] #include <LiquidCrystal_I2C.h> // Library untuk modul LCD I2C [cite: 192] #include <Arduino.h> // Library standar Arduino [cite: 193]
// Inisialisasi LCD pada alamat 0x27 (atau 0x20) dengan ukuran 16 kolom dan 2 baris [cite: 195, 196] LiquidCrystal_I2C lcd(0x27, 16, 2);

const int pinPot = A0; // Pin analog untuk potensiometer [cite: 198]

void setup() { Serial.begin(9600); // Memulai komunikasi serial UART pada baud rate 9600 [cite: 201]

lcd.init(); // Inisialisasi modul LCD [cite: 205] lcd.backlight(); // Menyalakan lampu latar LCD [cite: 207] }

void loop() { // 1. Membaca nilai analog dari potensiometer (0-1023) [cite: 212] int nilaiADC = analogRead(pinPot);

// 2. Kalkulasi Data Tambahan // Menghitung tegangan (Volt) dengan rumus (ADC / 1023) * 5V float volt = (nilaiADC * 5.0) / 1023.0;

// Mengonversi nilai ADC menjadi persentase (0-100%) int persen = map(nilaiADC, 0, 1023, 0, 100);

// Menentukan panjang bar (level) untuk baris kedua LCD (0-16 karakter) [cite: 217] int panjangBar = map(nilaiADC, 0, 1023, 0, 16);

// 3. Tampilan ke Serial Monitor (UART) [cite: 301] // Format: ADC: [nilai] | Persen: [persen]% | Volt: [volt] V Serial.print("ADC: "); Serial.print(nilaiADC); Serial.print(" | Persen: "); Serial.print(persen); Serial.print("% | Volt: "); Serial.print(volt); Serial.println(" V");

// 4. Tampilan ke LCD (I2C) [cite: 227-253] // Baris 1: Menampilkan nilai ADC dan Persentase lcd.setCursor(0, 0); // [cite: 229, 305] lcd.print("ADC:"); lcd.print(nilaiADC); lcd.print(" "); lcd.print(persen); lcd.print("% "); // Spasi tambahan untuk membersihkan sisa karakter sebelumnya

// Baris 2: Menampilkan Bar Level [cite: 237, 305] lcd.setCursor(0, 1); for (int i = 0; i < 16; i++) { if (i < panjangBar) { lcd.print((char)255); // Karakter blok penuh untuk level bar [cite: 245] } else { lcd.print(" "); // Karakter kosong jika level belum tercapai [cite: 249] } }

delay(250); // Delay singkat untuk stabilitas pembacaan dan tampilan [cite: 256] }

Link Simulasi: https://wokwi.com/projects/461829788783804417

Tabel Pengamatan Serial Monitor Data ADC
ADC	Volt (V)	Persen (%)
1	0.005 V	0.1%
21	0.103 V	2.1%
49	0.240 V	4.8%
74	0.362 V	7.2%
96	0.469 V	9.4%
