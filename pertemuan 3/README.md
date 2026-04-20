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

1.Jelaskan bagaimana cara kerja komunikasi I2C antara Arduino dan LCD pada rangkaian tersebut! Komunikasi I2C pada rangkaian ini bekerja dengan menggunakan dua jalur kabel utama, yaitu SDA (Serial Data) untuk pengiriman data dan SCL (Serial Clock) untuk sinkronisasi waktu. Arduino Uno bertindak sebagai Master yang mengendalikan aliran data, sedangkan LCD I2C bertindak sebagai Slave. Prosesnya dimulai ketika Arduino mengirimkan sinyal "Start" diikuti dengan alamat I2C unik milik LCD (misalnya 0x27) agar hanya LCD tersebut yang merespons. Setelah koneksi terjalin, Arduino mengirimkan data karakter atau perintah instruksi bit demi bit yang disinkronkan oleh sinyal detak pada jalur SCL.
2.Apakah pin potensiometer harus seperti itu? Jelaskan yang terjadi apabila pin kiri dan pin kanan tertukar! Konfigurasi standar menempatkan satu kaki luar pada GND dan kaki luar lainnya pada 5V, dengan kaki tengah (wiper) terhubung ke pin analog A0. Secara fungsional, pin kiri dan kanan boleh tertukar karena potensiometer bekerja sebagai pembagi tegangan variabel. Namun, jika keduanya tertukar, arah pembacaan nilai akan berbalik: jika sebelumnya memutar ke kanan (searah jarum jam) meningkatkan nilai ADC, maka setelah tertukar, memutar ke kanan justru akan memperkecil nilai ADC dan tegangan yang terbaca.
3.Modifikasi Program (UART & I2C) #include <Wire.h> // Library untuk komunikasi I2C [cite: 191] #include <LiquidCrystal_I2C.h> // Library untuk modul LCD I2C [cite: 192] #include <Arduino.h> // Library standar Arduino [cite: 193]
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

4.Tabel Pengamatan Serial Monitor Data ADC
ADC	Volt (V)	Persen (%)
1	0.005 V	0.1%
21	0.103 V	2.1%
49	0.240 V	4.8%
74	0.362 V	7.2%
96	0.469 V	9.4%
Jawaban Pertanyaan Praktikum 3.7 Pertanyaan Praktikum 
1. Sebutkan dan jelaskan keuntungan dan kerugian menggunakan UART dan I2C!
UART (Universal Asynchronous Receiver-Transmitter)
UART digunakan untuk komunikasi point-to-point (satu lawan satu) secara asinkron (tidak butuh kabel clock).

Keuntungan:

Sangat Sederhana: Hanya membutuhkan dua kabel data (TX untuk mengirim, RX untuk menerima) plus Ground.

Asinkron: Tidak memerlukan sinyal clock (detak waktu) tambahan.

Jarak Relatif Jauh: Bisa digunakan untuk kabel yang lebih panjang dibandingkan I2C (bisa hingga belasan meter jika digabung dengan standar RS-232/RS-485).

Kompatibilitas Luas: Sangat standar untuk komunikasi dengan PC, modul Bluetooth (HC-05), atau modul GPS.

Kerugian:

Hanya Point-to-Point: Hanya bisa menghubungkan tepat dua perangkat. Tidak bisa menghubungkan tiga perangkat atau lebih dalam satu jalur.

Kecepatan Terbatas: Umumnya lebih lambat dari I2C (standar sering di 9600 bps hingga 115200 bps).

Pengaturan Ketat: Kedua perangkat wajib disetel pada konfigurasi yang persis sama (Baud rate, data bits, parity, stop bits). Jika beda sedikit, data akan hancur (berupa karakter aneh).

I2C (Inter-Integrated Circuit)
I2C adalah protokol bus sinkron yang dirancang untuk komunikasi jarak dekat antar IC di dalam satu papan sirkuit.

Keuntungan:

Multi-Perangkat (Bus): Bisa menghubungkan banyak perangkat (hingga 127 slave) hanya menggunakan dua kabel: SDA (Data) dan SCL (Clock).

Sinkron & Andal: Adanya sinyal clock (SCL) memastikan pengirim dan penerima membaca data di waktu yang persis sama.

Sistem Konfirmasi: Memiliki fitur ACK/NACK (Acknowledge). Penerima akan memberi tahu pengirim jika data berhasil diterima.

Kerugian:

Jarak Sangat Dekat: Dirancang untuk komunikasi dalam satu board (biasanya kurang dari 1 meter). Semakin panjang kabel, semakin tinggi kapasitansinya, yang merusak sinyal.

Kompleksitas Lebih Tinggi: Secara software (protokolnya) lebih rumit karena melibatkan konsep Master/Slave, alamat, kondisi Start/Stop, dan ACK/NACK.

Butuh Resistor Tambahan: Jalur SDA dan SCL memerlukan resistor pull-up agar bisa berfungsi.
2. Bagaimana peran alamat I2C pada LCD (misalnya 0x27 vs 0x20)? Berikan penjelasan! 
Karena I2C menggunakan sistem bus (kabel SDA dan SCL dipakai bersama-sama oleh banyak perangkat), mikrokontroler (sebagai Master) membutuhkan cara untuk memanggil perangkat tertentu (Slave) tanpa membuat perangkat lain ikut merespons.

Di sinilah Alamat I2C (I2C Address) berperan.

Identitas Unik: Alamat I2C berfungsi seperti "Nomor Rumah". Saat Arduino ingin mengirim teks ke LCD, ia akan meneriakkan nomor rumahnya dulu (misal: "Halo 0x27, ini ada data untukmu!"). Hanya modul dengan alamat 0x27 yang akan membalas dan menerima data tersebut.

Kenapa ada 0x27 dan 0x20? Modul konverter I2C ke LCD yang umum di pasaran menggunakan IC tipe PCF8574.

IC varian PCF8574T biasanya memiliki alamat default 0x27.

IC varian PCF8574AT memiliki alamat default 0x3F.

Selain itu, pada modul tersebut terdapat pin A0, A1, dan A2 yang bisa di-solder (di-short) untuk mengubah alamat (misalnya dari 0x27 menjadi 0x20). Ini sangat berguna jika Anda ingin memasang dua atau tiga layar LCD yang sama dalam satu rangkaian Arduino; Anda harus memastikan setiap layar memiliki alamat yang berbeda.

Dampak Salah Alamat: Jika Anda memprogram Arduino untuk memanggil alamat 0x27, padahal layar Anda beralamat 0x20, layar tidak akan menampilkan apa-apa (blank) karena ia merasa tidak dipanggil.

3. Jika UART dan I2C digabung dalam satu sistem (misalnya input dari Serial Monitor, 
output ke LCD), bagaimana alur kerja sistem tersebut? Bagaimana Arduino mengelola 
dua protokol sekaligus?
Alur Kerjanya (Data Flow):

Input dari PC (UART TX -> RX Arduino): Anda mengetik "Halo" di Serial Monitor lalu klik kirim. Komputer mengubahnya jadi sinyal listrik asinkron dan mengirimkannya via kabel USB (yang dikonversi ke UART).

Penerimaan di Latar Belakang (Interrupt): Hardware UART di Arduino mendeteksi sinyal masuk, menyusunnya menjadi byte, dan memicu interrupt. Kode inti Arduino secara otomatis memindahkan byte ini ke dalam Buffer Serial (sebuah ruang tunggu memori sementara), tanpa mengganggu program utama Anda.

Proses di Program Utama (loop): Program Anda (di dalam fungsi loop) terus-menerus mengecek: "Apakah ada data di ruang tunggu?". if (Serial.available() > 0)

Pengambilan Data: Jika ada, Arduino membaca data tersebut dari buffer Serial.read().

Pengiriman ke LCD (I2C SDA/SCL): Arduino (sebagai Master) sekarang menyuruh blok hardware I2C untuk bekerja. Arduino mengirimkan sinyal Start, alamat LCD (misal: 0x27), menunggu balasan ACK, lalu mengirim data teks "Halo" tersebut lewat pin SDA bersamaan dengan detak clock di pin SCL.

Tampilan di Layar: Modul I2C di belakang layar LCD menerima data tersebut dan menerjemahkannya untuk menyalakan piksel pada layar LCD.
