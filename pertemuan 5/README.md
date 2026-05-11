Jawaban Pertanyaan Praktikum 5.5.4

Apakah ketiga task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya! Ketiga task pada program FreeRTOS tidak benar-benar berjalan secara bersamaan secara fisik karena Arduino Uno hanya memiliki satu inti prosesor. Namun, task berjalan secara konkuren (concurrent) melalui mekanisme penjadwalan oleh scheduler FreeRTOS. Scheduler akan membagi waktu eksekusi CPU kepada setiap task secara cepat sehingga terlihat seperti berjalan bersamaan. Pada program Percobaan 5A terdapat tiga task:
TaskBlink1 → mengontrol LED pertama TaskBlink2 → mengontrol LED kedua Taskprint → menampilkan counter di Serial Monitor

Masing-masing task menggunakan vTaskDelay() sehingga ketika satu task sedang delay atau blocked, scheduler memberikan kesempatan kepada task lain untuk berjalan. Mekanisme ini disebut multitasking cooperative/preemptive scheduling.

Bagaimana cara menambahkan task keempat? Jelaskan langkahnya!
Untuk menambahkan task keempat, langkah-langkahnya adalah:

a. Membuat prototype task baru

Tambahkan deklarasi fungsi task:

void Task4(void *pvParameters); b. Membuat task menggunakan xTaskCreate()

Tambahkan pada setup():

xTaskCreate( Task4, "task4", 128, NULL, 1, NULL );

Parameter tersebut berarti:

Task4 → nama fungsi task "task4" → nama task 128 → ukuran stack NULL → parameter task 1 → prioritas task NULL → task handle c. Membuat isi fungsi task

Contoh task baru:

void Task4(void *pvParameters) { while(1) { Serial.println("Task4 berjalan"); vTaskDelay(1000 / portTICK_PERIOD_MS); } } d. Compile dan upload program

Setelah task ditambahkan:

Simpan sketch Compile program Upload ke Arduino Amati output pada Serial Monitor

Jika berhasil, tulisan “Task4 berjalan” akan muncul bersamaan dengan task-task lainnya.

Modifikasilah program dengan menambah sensor (misalnya potensiometer), lalu gunakan nilainya untuk mengontrol kecepatan LED! Bagaimana hasilnya?
https://www.tinkercad.com/things/5GwbChdfdv5-percobaan-5a-multitasking?sharecode=WtqSrJaATrJbmdbtPKkaf8ZoOG9-2tbyAdIXOIrHCJ4

Hasil percobaan menunjukkan bahwa:

Saat potensiometer diputar ke nilai kecil, LED berkedip lebih cepat Saat potensiometer diputar ke nilai besar, LED berkedip lebih lambat Nilai delay tampil pada Serial Monitor secara real-time

Hal ini membuktikan bahwa FreeRTOS mampu menjalankan task sambil tetap membaca input sensor secara dinamis. Sistem menjadi lebih interaktif dan fleksibel dibandingkan program Arduino biasa yang hanya menggunakan loop().

Jawaban Pertanyaan Praktikum 5.6.4

Apakah kedua task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya!
Kedua task pada Percobaan 5B berjalan secara konkuren melalui scheduler FreeRTOS. Task tidak berjalan benar-benar paralel karena Arduino Uno hanya memiliki satu CPU, tetapi scheduler membagi waktu eksekusi antar-task dengan sangat cepat.

Task yang digunakan:

read_data → mengirim data temperatur dan kelembapan ke queue display → menerima data dari queue dan menampilkan ke Serial Monitor

Mekanismenya:

read_data membuat data Data dikirim menggunakan xQueueSend() Task masuk delay menggunakan vTaskDelay() Scheduler menjalankan task display display membaca data menggunakan xQueueReceive() Data ditampilkan ke Serial Monitor

Karena task saling bergantian dengan cepat, sistem terlihat seperti berjalan bersamaan.

Apakah program ini berpotensi mengalami race condition? Jelaskan!
Program ini relatif aman dari race condition karena komunikasi data dilakukan menggunakan queue FreeRTOS. Queue bekerja sebagai media pertukaran data yang dikelola kernel RTOS sehingga akses data dilakukan secara teratur dan sinkron.

Race condition biasanya terjadi ketika:

dua task mengakses variabel yang sama secara bersamaan, salah satu task menulis data sementara task lain membaca data pada waktu yang sama.

Pada program ini:

task read_data tidak langsung mengubah variabel milik task display, data dikirim melalui queue menggunakan xQueueSend(), data diterima menggunakan xQueueReceive().

Queue memastikan bahwa:

data dikirim satu per satu, data tidak tertimpa, task penerima hanya membaca data yang valid.

Namun, race condition tetap dapat terjadi apabila:

menggunakan variabel global tanpa proteksi, beberapa task mengakses resource yang sama tanpa semaphore atau mutex.

Karena itu, penggunaan queue pada FreeRTOS merupakan solusi yang aman untuk komunikasi antar-task.

Modifikasilah program dengan menggunakan sensor DHT sesungguhnya sehingga informasi yang ditampilkan dinamis. Bagaimana hasilnya?
https://wokwi.com/projects/463146960431986689

Hasil percobaan menunjukkan bahwa:

nilai temperatur dan kelembapan berubah secara dinamis sesuai kondisi lingkungan,
data berhasil dikirim dari task pembaca sensor menuju task penampil melalui queue,
Serial Monitor menampilkan data secara periodik tanpa mengganggu task lain.
