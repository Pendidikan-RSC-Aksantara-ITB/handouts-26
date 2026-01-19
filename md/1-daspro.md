# **Object Oriented Programming (C++)**

### *“Kenapa sih butuh OOP?”*

Setelah menamatkan mata kuliah Berpikir Komputasional yang mengharuskan kita untuk membuat proyek sederhana, pasti paham rasanya, awalnya simpel, tapi makin lama kode programnya lama-lama mekar ke mana-mana. Misalkan kita membuat aplikasi monitor drone yang menerima data dari beberapa sumber (GPS, IMU, battery), lalu menampilkannya di UI, dan kirim command balik. Kalau pendekatannya procedural, biasanya mulai muncul pola semacam:

```cpp
// data untuk tiap drone
float drone1_lat, drone1_lon;
float drone2_lat, drone2_lon;

void readDrone1Telemetry() { ... }
void readDrone2Telemetry() { ... }

void drawDrone1OnMap() { ... }
void drawDrone2OnMap() { ... }
```

Saat menambah drone baru, atau menambah satu jenis data baru (mis. `battery`), jumlah variabel dan fungsi nambahnya *nggak wajar*. Ini yang bikin *spaghetti code*.

Nah, di sini **Object Oriented Programming (OOP)** jadi penyelamat. OOP itu cara mikir di mana program dianggap kumpulan *Object* yang punya data + perilaku. Jadi kita cukup bikin satu *blueprint* (mis. `DroneConnection` atau `TelemetrySource`), lalu instansiasi (membuat objek) sebanyak yang kita butuhkan. Ini bikin kode lebih terstruktur, gampang di-*maintain*, dan *scalable*.

### 

1. ### **Class and Object**

   Biar kebayang, bayangin **Class** itu kayak skematik atau *datasheet* komponen. Dia cuma gambar rancangan, belum ada bentuk fisiknya. Dia mendefinisikan benda itu punya apa aja (***attribute***) dan bisa ngapain aja (***method***).  
   Sedangkan **Object** itu adalah **komponen fisik** yang udah kalian beli atau rakit berdasarkan schematic tadi. Kalian bisa bikin banyak Object dari satu Class yang sama.  
   Contoh, kita mau bikin sistem kontrol motor DC. Kita buat dulu Class-nya.  
     
   class *MotorDC* {  
   public:  
      // Attribute (Data yang dimiliki)  
      int pin\_pwm;  
      int pin\_dir;  
      int speed;  
     
      // Method (Apa yang bisa dia lakukan)  
      void maju() {  
          // Logic buat motor maju  
          cout \<\< "Motor di pin " \<\< pin\_pwm \<\< " maju dengan speed " \<\< speed \<\< endl;  
      }  
     
      void stop() {  
          speed \= 0;  
          cout \<\< "Motor berhenti" \<\< endl;  
      }  
   };  
     
   int main() {  
      // Membuat Object (Realisasi dari Class)  
      *MotorDC* motorKiri;  // Object 1  
      motorKiri.pin\_pwm \= 5;  
      motorKiri.speed \= 100;  
     
      *MotorDC* motorKanan; // Object 2  
      motorKanan.pin\_pwm \= 6;  
      motorKanan.speed \= 100;  
     
      // Menyuruh object bekerja  
      motorKiri.maju();  
      motorKanan.maju();  
   }  
     
   *See*? Kita cuma nulis logic maju() sekali di dalam Class, tapi bisa dipake sama motorKiri  dan motorKanan.

### 

2. ### **Encapsulation**

   Pernah gak sih kalian pakai *library* orang, terus kalian iseng ubah variabel yang harusnya internal, eh alat yg kalian buat itu malah error atau bahkan kebakar? Nah, **Encapsulation** itu tujuannya buat mencegah hal itu.  
   Encapsulation itu seperti **membungkus** data sensitif biar nggak bisa diakses sembarangan dari luar. Kita pake *access modifier*: 

| Modifier | Own Class | Derived Class | Main() |
| :---- | :---- | :---- | :---- |
| **Public** | Yes | Yes | Yes |
| **Protected** | Yes | Yes | No |
| **Private** | Yes | No | No |

   

   Bayangkan kasus *Electronic Speed Controller*. Nilai PWM itu biasanya cuma boleh 1000-2000 (cmiiw). Kalau ada *programmer* iseng nge-set nilainya jadi 99999, ESC bisa meledak. Makanya, variabel nilai PWM kita bikin **private**, dan kita kasih “pintu” lewat fungsi **public** yang ada pengecekannya. Contohnya fungsi **getter** dan **setter**.

   

   class *ESC* {

   private:

      int pwm\_value; // Private, gak bisa diakses langsung dari main()

   

   public:

      // Setter: Pintu masuk untuk ubah data

      void setPwm(int *nilai*) {

          if (*nilai* \> 2000) {

              cout \<\< "Bahaya\! Nilai ketinggian. Di-cap di 2000\." \<\< endl;

              pwm\_value \= 2000;

          } else if (*nilai* \< 1000) {

              pwm\_value \= 1000;

          } else {

              pwm\_value \= *nilai*;

          }

      }

   

      // Getter: Pintu buat mengintip data

      int getPwm() {

          return pwm\_value;

      }

   };

   

   Jadi di main(), kita nggak bisa tulis esc1.pwm\_value \= 99999; (bakal error). Kita dipaksa lewat esc1.setPwm(99999); yang aman karena ada filternya.

### 

3. ### **Abstraction**

   **Abstraction** itu intinya kita nggak peduli cara kerjanya gimana, yang penting ada hasilnya, sehingga menyembunyikan kerumitan di belakang layar.  
   Contohnya adalah, misal kalian adalah pilot *drone*. Kalian cuma perlu tau kalau *stick* dimajuin, drone bakal maju. Kalian nggak perlu tau (dan nggak mau tau) itung-itungan PID Controller, algoritma *sensor fusion*, atau *inverse kinematics* yang terjadi di dalam Flight Controller biar drone itu stabil.  
   Di kodingan, kita biasanya pakai *Header file* atau *Interface* buat *abstraction*. Programmer lain yang make *code* kalian cuma perlu tau fungsi flyTo(koordinat), tanpa perlu pusing liat rumus matematika di dalemnya.  
     
   class *Drone* {  
   public:  
      void takeOff() {  
          // Berbagai proses:  
          // 1\. Cek Battery  
          // 2\. Calibrate Gyro  
          // 3\. Spin motors pelan-pelan  
          // 4\. PID calculation loop  
          cout \<\< "Drone take off..." \<\< endl;  
      }  
   };  
     
   int main() {  
      *Drone* dji;  
      dji.takeOff(); // simpler hehe  
   }  
 


4. **Inheritance**  
   **Inheritance** atau pewarisan itu punya prinsip “*Don't Repeat Yourself*” (DRY). Bayangkan, kalian mau bikin *code* buat macam-macam sensor robot, seperti Sensor Ultrasonic, Sensor Lidar, dan Sensor Gyro.  
   ![][image1]  
   Semua sensor itu pasti punya kesamaan, yaitu punya ID, punya status aktif/nggak, dan butuh power. Dibandingkan menulis ulang variabel id dan isActive di tiap class sensor, mending kita bikin satu ***Parent Class*** (Base Class) namanya Sensor, terus sensor-sensor spesifik jadi ***Child Class*** (Derived Class) yang mewarisi sifat orang tuanya.  
     
   // Base Class  
   class *Sensor* {  
   public:  
      int id;  
      bool isActive;  
     
      void powerOn() {  
          isActive \= true;  
          cout \<\< "Sensor nyala." \<\< endl;  
      }  
   };  
     
   // Derived Class  
   // Lidar mewarisi semua sifat Sensor  
   class *Lidar* : public *Sensor* {  
   public:  
      int range\_max; // Ini sifat khusus Lidar, parents tidak punya  
     
      void scanArea() {  
          cout \<\< "Lidar scanning..." \<\< endl;  
      }  
   };  
     
   int main() {  
      *Lidar* myLidar;  
      myLidar.powerOn(); // Fungsi ini punya parents, tapi Lidar bisa pake  
      myLidar.scanArea(); // Fungsi ini punya Lidar sendiri.  
   }  
     
   Lumayan hemat baris kode kan? Kalau mau nambah sensor baru kayak Camera, tinggal  class Camera : public Sensor.

### 

5. ### **Polymorphism**

   Ini konsep yang namanya paling keren tapi sering bikin bingung. **Polymorphism** artinya “banyak bentuk”. Intinya, satu perintah yang sama bisa direspon beda-beda tergantung siapa object-nya.  
   Balik lagi ke sensor. Semua sensor pasti punya fungsi buat baca data, sebut aja readData(). Tapi cara kerjanya beda kan? Contohnya Ultrasonic baca jarak pake suara, Camera baca gambar, Gyro baca sudut.  
   Dengan *Polymorphism*, kita bisa kumpulin semua sensor itu dalam satu wadah (misalnya satu array), lalu kita suruh semuanya readData(). Kita nggak perlu tau dia sensor apa, pokoknya baca data. Ini butuh fitur C++ namanya **Virtual Function**.  
     
   class *Sensor* {  
   public:  
      // Virtual function (Cuma definisi kosong/default, nanti ditimpa sama anaknya)  
      virtual void readData() {  
          cout \<\< "Baca data generic..." \<\< endl;  
      }  
   };  
     
   class *Ultrasonic* : public *Sensor* {  
   public:  
      // Override (implementasi sesungguhnya untuk Ultrasonic)  
      void readData() override {  
          cout \<\< "Ultrasonic: Jarak 50 cm" \<\< endl;  
      }  
   };  
     
   class *Gyro* : public *Sensor* {  
   public:  
      // Override (implementasi sesungguhnya untuk Gyro)  
      void readData() override {  
          cout \<\< "Gyro: Sudut 90 derajat" \<\< endl;  
      }  
   };  
     
   int main() {  
      // buat pointer ke base class  
      *Sensor*\* s1 \= **new** *Ultrasonic*();  
      *Sensor*\* s2 \= **new** *Gyro*();  
     
      s1\-\>readData(); // Output: Ultrasonic: Jarak 50 cm  
      s2\-\>readData(); // Output: Gyro: Sudut 90 derajat  
   }  
     
   Bayangin kalau robot kalian punya 50 sensor beda jenis. Kalian bisa masukin semuanya ke dalam satu *loop*, terus panggil sensor\[i\]-\>readData(). Robot bakal otomatis tau cara baca masing-masing sensor. Jadi ga ribet lagi xixi.  
   

6. ### **Template Classes**

   Untuk template class, bayangkan kalian mengembangkan kode flight controller / ground station drone. Banyak struktur data di drone itu **formatnya sama**, tapi **tipe datanya bisa beda tergantung kebutuhan**.  
     
   Contohnya:  
   Drone sering mengirim “packet telemetry” yang isinya mirip:  
* timestamp  
* source (contoh: "GPS", "IMU", "BAT")  
* value (nilai sensornya)  
* unit  
    
  Nah, variabel *value* bisa memiliki tipe yang berbeda:  
* GPS altitude: *float*  
* Battery percentage: *int*  
* Status flight mode: *string* atau *enum*  
* Error code: *uint16\_t*  
    
  Kalau kalian tidak memakai template, kalian akan tergoda bikin banyak class yang isinya sama:  
* TelemetryFloat  
* TelemetryInt  
* TelemetryString  
    
  Padahal 95% isi class-nya identik, yang beda hanya tipe data *value*. Di sinilah Template Class dipakai.  
    
  **Template Class** adalah mekanisme di C++ untuk membuat *blueprint* class yang **generik terhadap tipe data tertentu**. Artinya, kita menulis class **sekali**, lalu saat membuat objek kita bisa menentukan tipe data yang dipakai.  
    
  Lalu bagaimana kita bisa membuat template class? Template class dibuat dengan menggunakan sintaks \<template T\>, T disini adalah suatu tipe yang bisa menjadi int, string, double, atau tipe lainnya.  
    
  Saat kalian menulis:  
* Telemetry\<float\> berarti compiler membuat versi class dengan T \= float  
* Telemetry\<int\> berarti compiler membuat versi class dengan T \= int  
  Jadi satu kode template bisa menghasilkan beberapa “versi class” secara otomatis saat compile-time.  
    
  Contoh template class:  
  template \<typename T\>  
  class TelemetryPacket {  
  public:  
     unsigned long timestamp\_ms;  
     string source;  
     T value;         // tipe value fleksibel  
     string unit;  
    
     TelemetryPacket(unsigned long ts, string src, T val, string u):  
         timestamp\_ms(ts), source(src), value(val), unit(u) {}  
    
     void print() {  
         cout \<\< "\[" \<\< timestamp\_ms \<\< "ms\] "  
              \<\< source \<\< " \= " \<\< value \<\< " " \<\< unit \<\< endl;  
     }  
  };  
    
  int main() {  
     TelemetryPacket\<float\> alt(1200, "GPS\_ALT", 420.69f, "m");  
     TelemetryPacket\<int\> batt(1200, "BATTERY", 67, "%");  
     TelemetryPacket\<string\> mode(1200, "FLIGHT\_MODE", "ANGLE", "");  
    
     alt.print();  
     batt.print();  
     mode.print();  
  }  
    
  Hal diatas lebih optimal dibandingkan bila tidak memakai template class:  
  // value \= float (mis. altitude)  
  class TelemetryPacketFloat {  
  public:  
     unsigned long timestamp\_ms;  
     string source;  
     float value;  
     string unit;  
    
     TelemetryPacketFloat(unsigned long ts, string src, float val, string u)  
         : timestamp\_ms(ts), source(src), value(val), unit(u) {}  
    
     void print() const {  
         cout \<\< "\[" \<\< timestamp\_ms \<\< "ms\] " \<\< source \<\< " \= " \<\< value \<\< " " \<\< unit \<\< endl;  
     }  
  };  
    
  // value \= int (mis. battery %)  
  class TelemetryPacketInt {  
  public:  
     unsigned long timestamp\_ms;  
     string source;  
     int value;  
     string unit;  
    
     TelemetryPacketInt(unsigned long ts, string src, int val, string u)  
         : timestamp\_ms(ts), source(src), value(val), unit(u) {}  
    
     void print() {  
         cout \<\< "\[" \<\< timestamp\_ms \<\< "ms\] " \<\< source \<\< " \= " \<\< value \<\< " " \<\< unit \<\< endl;  
     }  
  };  
    
  // value \= string (mis. flight mode)  
  class TelemetryPacketString {  
  public:  
     unsigned long timestamp\_ms;  
     string source;  
     string value;  
     string unit;  
    
     TelemetryPacketString(unsigned long ts, string src, string val, string u)  
         : timestamp\_ms(ts), source(src), value(val), unit(u) {}  
    
     void print() {  
         cout \<\< "\[" \<\< timestamp\_ms \<\< "ms\] " \<\< source \<\< " \= " \<\< value \<\< " " \<\< unit \<\< endl;  
     }  
  };  
    
  int main() {  
     TelemetryPacketFloat alt(1200, "GPS\_ALT", 123.45f, "m");  
     TelemetryPacketInt batt(1200, "BATTERY", 87, "%");  
     TelemetryPacketString mode(1200, "FLIGHT\_MODE", "ANGLE", "");  
    
     alt.print();  
     batt.print();  
     mode.print();  
    
     return 0;  
  }  
    
  Kalau tidak memakai **Template Class**, selain kode yang diperlukan lebih panjang, akan timbul masalah lain,  
  Kalau nanti kalian mau:  
* menambah field baru: int seq; (nomor urut packet),  
* atau menambah method baru: bool isFrom(const string& s) const;,  
* atau mengubah format print(),  
    
  maka Anda harus mengubahnya di TelemetryPacketFloat, TelemetryPacketInt, TelemetryPacketString satu per satu. Ini rawan:  
* lupa update salah satu class,  
* bug tidak konsisten,  
* maintenance lama.  
    
  Itulah alasan template class berguna, kalian mempunyai satu definisi class,yang tipe valuenya bisa diganti saat instansiasi, dan ketika ingin mengupdate sesuatu yang berhubungan dengan class tersebut, kalian hanya perlu mengupdate satu class saja.

7. ### **Pointer (\*) vs Reference (&)**

   Misalnya kita punya sebuah pernyataan:

| int pwm \= 1500 |
| :---- |

   

   Pernyataan tersebut artinya sebuah variabel memiliki sebuah tipe dan menyimpan suatu nilai/value. Eits\! Tapi tunggu\! Selain ketiga hal diatas, variabel juga menyimpan suatu “alamat” (misal 0x67C) dimana dia menyimpan value tersebut. 

   

   Nah, berbeda dengan variabel, suatu **Pointer (\*)** berisi sebuah alamat dari suatu data. Bayangkan pointer adalah suatu **kotak yang bisa dibuka dan memiliki suatu isi**.

   

| ESC esc1; ESC\* p \= \&esc1; |
| :---- |

   Dari contoh diatas, **esc1** adalah objek yang “tinggal” di suatu alamat memori (mis. 0x67C), **p** menyimpan angka “0x100” (alamat esc1). Saat p-\>setPwm(1500), program pergi ke alamat 0x67C dan memanggil fungsi objek di sana.

   

   Pointer dipakai ketika:

1. Objek boleh tidak ada → butuh nullptr sebagai “tidak menunjuk apa-apa”  
2. Target bisa berubah (pointer dapat diarahkan ke objek lain)  
3. Struktur data dinamis (linked list, tree, buffer), atau interoperabilitas dengan C API  
     
   Tetapi perlu diperhatikan, bila pointer dibuka dan tidak menunjuk kemanapun (null), hal tersebut dapat menimbukan **crash**. Maka dari itu, perlu sebuah **checker** sebelum suatu pointer dioperasikan.

| if (esc \== nullptr) return; |
| :---- |

   Lalu, apa itu sebuah **Reference**? **Reference** adalah **nama lain** atau bisa disebut juga alias untuk variabel/objek yang sudah ada. Bukan objek baru seperti pointer.

| ESC esc1; ESC& r \= esc1; |
| :---- |

   Pada contoh diatas, r bukanlah “kotak/objek baru”, r adalah “label kedua” untuk esc1. Saat kita memanggil r.setPwm(1000), itu sama saja seperti esc1.setPwm(1000).

   

   Reference sering dipakai dalam:

1. Parameter fungsi yang wajib valid (tidak boleh null)  
2. Menghindari copy objek besar (efisien)  
3. Menyatakan “fungsi ini akan memodifikasi objek asli” dengan jelas  
     
   Perbedaan utama dalam **Pointer** dan **Reference** yang palin utama adalah sebuah Pointer dapat berubah tujuan alamatnya, sedangkan Reference tidak. Lalu, ketika mengoperasikan keduanya, sebuah pointer harus “dibuka” (dengan \-\> pada p-\>setpwm(67)) sedangkan sebuah Reference bisa dioperasikan seperti objek aslinya (dengan ‘.’ pada r.setpwm(67))  
   


## Credits
- Nisrina Zakiyah
- Muhammad Rafi' Abdurrahman
- Zulfaqqar Nayaka Athadiansyah