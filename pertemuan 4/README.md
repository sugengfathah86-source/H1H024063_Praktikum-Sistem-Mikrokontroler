Pertanyaan Praktikum Percobaan 1

Apa fungsi perintah analogRead() pada rangkaian praktikum ini? Pada percobaan ini, fungsi analogRead() digunakan untuk membaca tegangan analog dari potensiometer. Tegangan keluaran potensiometer berubah sesuai posisi putarannya. Arduino tidak langsung memahami tegangan analog sebagai nilai kontinu, sehingga ADC mengubah tegangan tersebut menjadi nilai digital 0 sampai 1023. Nilai inilah yang kemudian digunakan sebagai dasar untuk menentukan sudut servo. Contohnya, jika potensiometer berada pada posisi minimum, nilai analogRead() mendekati 0. Jika potensiometer berada pada posisi tengah, nilai pembacaan mendekati 512. Jika potensiometer berada pada posisi maksimum, nilai pembacaan mendekati 1023. Dengan demikian, analogRead() berfungsi sebagai jembatan antara input analog dan pemrosesan digital pada Arduino [6].

Mengapa diperlukan fungsi map() dalam program tersebut? Fungsi map() diperlukan karena rentang nilai ADC dan rentang sudut servo berbeda. Nilai ADC dari potensiometer berada pada rentang 0–1023, sedangkan motor servo umumnya dikendalikan dalam rentang sudut 0°–180°. Jika nilai ADC langsung diberikan ke servo, maka nilainya tidak sesuai dengan rentang sudut servo. Dengan menggunakan map(), nilai ADC dapat dikonversi menjadi sudut servo. Contohnya:

pos = map(val, 0, 1023, 0, 180);

Perintah tersebut berarti nilai val dari rentang 0–1023 diubah menjadi nilai pos dalam rentang 0–180. Jika nilai ADC 0, maka sudut servo 0°. Jika nilai ADC sekitar 512, maka sudut servo sekitar 90°. Jika nilai ADC 1023, maka sudut servo 180°. Fungsi ini membuat gerak servo menjadi proporsional terhadap putaran potensiometer.

Modifikasi program agar servo hanya bergerak pada rentang 30° hingga 150°
Program dapat dimodifikasi dengan mengubah parameter keluaran pada fungsi map() dari 0–180 menjadi 30–150. Program modifikasi adalah sebagai berikut:

#include <Servo.h>

Servo myservo;

const int potensioPin = A0; const int servoPin = 9;

int pos = 0; int val = 0;

void setup() { myservo.attach(servoPin); Serial.begin(9600); }

void loop() { val = analogRead(potensioPin);

// Mengubah nilai ADC 0–1023 menjadi sudut servo 30–150 derajat pos = map(val, 0, 1023, 30, 150);

// Membatasi nilai agar tetap aman pada rentang 30–150 pos = constrain(pos, 30, 150);

myservo.write(pos);

Serial.print("ADC Potensio: "); Serial.print(val);

Serial.print(" | Sudut Servo: "); Serial.println(pos);

delay(50); }

Berikut adalah link simulasi: https://www.tinkercad.com/things/6mumewRxzQS-brilliant-bruticus/editel?returnTo=https%3A%2F%2Fwww.tinkercad.com%2Fdashboard%2Fdesigns%2Fall&sharecode=LbtUWt31SK0khN0aZBqWYfuwSdAaTBnRXP4XX7vbMZ0

Pertanyaan Praktikum Percobaan 2

Mengapa LED dapat diatur kecerahannya menggunakan fungsi analogWrite()? LED dapat diatur kecerahannya menggunakan analogWrite() karena fungsi tersebut menghasilkan sinyal PWM. Sinyal PWM bukan benar-benar tegangan analog murni, melainkan sinyal digital yang berubah cepat antara HIGH dan LOW. Ketika duty cycle kecil, LED lebih sering berada pada kondisi mati dibanding menyala, sehingga tampak redup. Ketika duty cycle besar, LED lebih lama berada pada kondisi menyala, sehingga tampak terang. Mata manusia tidak dapat mengikuti perubahan nyala-mati yang sangat cepat, sehingga yang terlihat adalah intensitas rata-rata LED. Dengan demikian, perubahan duty cycle PWM akan terlihat sebagai perubahan tingkat kecerahan LED. Nilai analogWrite() pada Arduino berada dalam rentang 0 sampai 255. Nilai 0 menghasilkan LED mati, nilai sekitar 128 menghasilkan kecerahan sedang, dan nilai 255 menghasilkan kecerahan maksimum [3], [7].

Apa hubungan antara nilai ADC 0–1023 dan nilai PWM 0–255? Hubungan antara nilai ADC dan nilai PWM adalah hubungan pemetaan skala. Nilai ADC memiliki rentang 0–1023 karena ADC Arduino Uno menggunakan resolusi 10-bit. Sementara itu, nilai PWM pada analogWrite() umumnya memiliki rentang 0–255 karena menggunakan resolusi 8-bit. Oleh karena itu, nilai ADC perlu dikonversi agar sesuai dengan rentang PWM. Contohnya, jika nilai ADC adalah 0, maka nilai PWM adalah 0. Jika nilai ADC sekitar 512, maka nilai PWM sekitar 128. Jika nilai ADC 1023, maka nilai PWM 255. Pada program Arduino, konversi ini dapat dilakukan menggunakan fungsi:

pwm = map(nilaiADC, 0, 1023, 0, 255);

Dengan pemetaan ini, perubahan putaran potensiometer dapat mengubah kecerahan LED secara bertahap.

Modifikasi program agar LED hanya menyala pada rentang kecerahan sedang, yaitu PWM 50 sampai 200
Program dapat dimodifikasi dengan menambahkan kondisi if. Jika nilai PWM berada pada rentang 50 sampai 200, maka LED diberi sinyal PWM sesuai nilai tersebut. Jika nilai PWM kurang dari 50 atau lebih dari 200, maka LED dimatikan.

#include <Arduino.h>

const int potPin = A0; const int ledPin = 9;

int nilaiADC = 0; int pwm = 0;

void setup() { pinMode(ledPin, OUTPUT); Serial.begin(9600); }

void loop() { nilaiADC = analogRead(potPin);

// Mengubah nilai ADC 0–1023 menjadi PWM 0–255 pwm = map(nilaiADC, 0, 1023, 0, 255);

// LED hanya menyala pada rentang PWM 50 sampai 200 if (pwm >= 50 && pwm <= 200) { analogWrite(ledPin, pwm); } else { analogWrite(ledPin, 0); }

Serial.print("ADC: "); Serial.print(nilaiADC);

Serial.print(" | PWM: "); Serial.println(pwm);

delay(50); }

berikut adalah link simulasi: [https://www.tinkercad.com/things/eDgpy5lqqoO-incredible-gogo-hillar?sharecode=SDgmd2Q9WzDe0g0SOA2tkBANf_F0-_1UGbYz_o2qwOM](https://www.tinkercad.com/things/3WJPvxt9qb5/editel?sharecode=0Fn0YctEFcT3JMLjJGRntow28m-gWDRojSZgZCIovzA)
