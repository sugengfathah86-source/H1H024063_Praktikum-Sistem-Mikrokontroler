<img width="1280" height="568" alt="Fabulous Blad-Habbi" src="https://github.com/user-attachments/assets/88f43d4a-048c-47f0-9b85-98aa9fb43ec7" />


1.5.4 Pertanyaan Praktikum:

1. Pada kondisi apa program masuk ke blok if? Program akan mengeksekusi instruksi di dalam blok if apabila nilai dari variabel timeDelay sudah mencapai angka kurang dari atau sama dengan 100.
2. Pada kondisi apa program masuk ke blok else? Program akan masuk ke blok else jika kondisi pada pernyataan if tidak terpenuhi, yakni ketika nilai variabel timeDelay masih berada di atas ambang batas 100.
3. Apa fungsi dari perintah delay(timeDelay)? Perintah delay(timeDelay) berfungsi untuk menghentikan sementara eksekusi program selama durasi tertentu dalam satuan milidetik berdasarkan nilai variabel timeDelay. Dalam percobaan ini, perintah tersebut digunakan untuk mengatur lamanya durasi LED menyala (HIGH) maupun mati (LOW) guna menciptakan efek kedipan.
4. Modifikasi Program Kedip LED Dinamis (Cepat ke Sedang)

Program ini memodifikasi alur kedipan LED agar tidak langsung melakukan reset ke kondisi lambat saat mencapai kecepatan maksimal, melainkan mengalami transisi melambat secara bertahap (Cepat -> Sedang -> Mati/Lambat).


int timer = 100;           
// delay. Semakin tinggi angkanya, 
semakin lambat waktunya. 
void setup() { 
// gunakan loop for untuk menginisialisasi setiap pin sebagai 
output: 
for (int ledPin = 2; ledPin < 8; ledPin++) { 
pinMode(ledPin, OUTPUT); 
} 
} 
void loop() { 
// looping dari pin rendah ke tinggi 
for (int ledPin = 2; ledPin < 8; ledPin++) { 
// hidupkan LED pin nya: 
digitalWrite(ledPin, HIGH); 
delay(timer); 
// matikan pin LED nya: 
digitalWrite(ledPin, LOW); 
} 
// looping dari pin yang tinggi ke yang rendah 
for (int ledPin = 7; ledPin >= 2; ledPin--) { 
// menghidupkan pin: 
digitalWrite(ledPin, HIGH); 
delay(timer); 
// mematikan pin: 
digitalWrite(ledPin, LOW); 
} 
}
