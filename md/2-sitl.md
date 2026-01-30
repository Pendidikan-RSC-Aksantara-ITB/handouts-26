# SITL

## Pendahuluan

Pada materi ini, kita akan berkenalan dengan **SITL** (Software In The Loop) menggunakan [ArduPilot](https://ardupilot.org) sebagai *flight stack* utama karena merupakan *flight stack* yang secara *de facto* dipakai di Aksantara ITB. SITL memungkinkan kita menjalankan dan menguji sistem autopilot drone tanpa menggunakan *hardware* fisik, sehingga sangat cocok untuk proses belajar, eksperimen, dan pengujian awal.
ArduPilot berperan sebagai otak *drone*, yang menjalankan logika penerbangan seperti stabilisasi, navigasi, dan *flight mode*. Dalam mode SITL, ArduPilot dijalankan sebagai program biasa di komputer dan mensimulasikan perilaku *drone* seolah-olah berada di dunia nyata.
Untuk berinteraksi dengan ArduPilot, MAVLink digunakan sebagai protokol komunikasi. Pada praktiknya, interaksi ini difasilitasi oleh MAVProxy, yaitu GCS berbasis command-line yang secara otomatis dijalankan saat SITL berjalan. Melalui MAVProxy, kita dapat mengirim perintah seperti `arm`, `takeoff`, mengganti *flight mode*, serta memantau status *drone*.
Pada tahap awal, SITL dapat dijalankan tanpa simulator fisika. Namun, untuk simulasi yang lebih realistis, kita akan mengintegrasikan ArduPilot dengan Gazebo Harmonic, yang menyediakan simulasi lingkungan, sensor, dan dinamika wahana.

## Pengenalan SITL dan Arsitektur Sistem

## Flight Stack ArduPilot
### Instalasi

Pertama, kita harus *clone* repositori resmi ArduPilot.
```bash
cd ~
git clone --recurse-submodules https://github.com/ArduPilot/ardupilot.git cd ardupilot
```

Untuk install prasyarat dari ArduPilot:
```bash
Tools/environment_install/install-prereqs-ubuntu.sh -y
```

lalu *reload* PATH.
```bash
. ~/.profile
```

> [!NOTE]
> buat setupnya emang agak lama, **pastikan internet aman** aja ya guys :/<

Habis ini kita harus build dulu. Sebenernya di ArduPilot banyak banget *vehicle* yg bisa dipake tapi seringnya kita make ArduCopter, yakni wahana quadcopter.

```bash
./waf configure --board sitl
```
![waf configure](../assets/waf-config.png)


```bash
./waf copter
```
![waf copter](../assets/waf-copter.png)
![waf copter](../assets/waf-copter2.png)

Parameter `--board` itu bisa diganti apa aja, tapi untuk keperluan SITL kita pakai board `sitl`. Buat cek board apa saja yang tersedia bisa pakai command berikut:

```bash
./waf list_boards
```

More information: [https://ardupilot.org/dev/docs/building-setup-linux.html\#building-setup-linux](https://ardupilot.org/dev/docs/building-setup-linux.html#building-setup-linux)  
[https://github.com/ArduPilot/ardupilot](https://github.com/ArduPilot/ardupilot)  
[https://github.com/ArduPilot/ardupilot/blob/master/BUILD.md](https://github.com/ArduPilot/ardupilot/blob/master/BUILD.md)

### Menjalankan ArduPilot SITL
Jadi ArduPilot yg tadi buat apa sih? itu kek kita buat drone-dronean yang palsu (dalam software) tapi seakan akan ada di dunia nyata. Nah ArduPilot itu membuat kita jadi bisa punya drone tanpa punya drone. Gimana cara jalaninnya? 

Ada dua cara, yakni langsung ke folder yang sudah di-*build*,

```bash
cd ardupilot/ArduCopter sim_vehicle.py --console --map -w
```

Atau, kalau di *root,*

```bash
sim_vehicle.py -v copter --console --map -w
```

Habis kaya gitu bakal udah ada Copter sama petanya, kurang lebih kaya gini:
![sim copter](../assets/sim-copter.png)

### Kontrol Drone via MAVProxy
NAHH, kerennya adalah kita bisa kasih command ke drone-nya via MAVProxy. Misal kita mau drone-nya takeoff, kita bisa ketik aja di CLI-nya:
```bash
mode guided 
arm throttle 
takeoff 40
```

Habis *takeoff* *drone*\-nya bisa puter-puter:

```bash
mode circle param set circle_radius 2000
```

Selamat anda lulus sarjana SITL\!  
FYI, banyak banget command yang bisa kita pakai disini buat ngendaliin dronenya dalam mode guided. Buat commandnya bisa dilihat di sini:  [https://ardupilot.org/dev/docs/copter-commands-in-guided-mode.html\#copter-commands-in-guided-mode](https://ardupilot.org/dev/docs/copter-commands-in-guided-mode.html#copter-commands-in-guided-mode)


## Gazebo Harmonic
### Instalasi
Pertama-tama, kita tambahkan repositorinya dulu:
```bash
sudo apt-get update
sudo apt-get install curl lsb-release gnupg
sudo curl https://packages.osrfoundation.org/gazebo.gpg --output /usr/share/keyrings/pkgs-osrf-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/pkgs-osrf-archive-keyring.gpg] https://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/gazebo-stable.list > /dev/null

```

lalu kita instal Gazebo-nya.
```bash
sudo apt-get update
sudo apt-get install gz-harmonic
```

Untuk memeriksa apakah instalasi sudah berhasil, harusnya *command* ini akan menampilkan versi yang sesuai:
```bash
gz sim --versions
```
![gz](../assets/gz%20vers.png)

Kalau sempat muncul output semacam ini saat instalasi tadi:
```bash
E: Could not get lock /var/lib/dpkg/lock-frontend. It is held by process 17521 (unattended-upgr)
```
artinya Ubuntu sedang tengah meng-update package lain di belakang layar sehingga kita perlu tunggu 5-10 menit lalu jalankan instalasi lagi.

### Integrasi ArduPilot-Gazebo
Clone repositorinya:
```bash
cd ~
git clone https://github.com/ArduPilot/ardupilot_gazebo
cd ardupilot_gazebo
```

lalu build:
```bash
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=RelWithDebInfo
make -j4
```

Konfigurasikan environment:
```bash
export GZ_VERSION=harmonic
export GZ_SIM_SYSTEM_PLUGIN_PATH=$HOME/ardupilot_gazebo/build:$GZ_SIM_SYSTEM_PLUGIN_PATH
export GZ_SIM_RESOURCE_PATH=$HOME/ardupilot_gazebo/models:$HOME/ardupilot_gazebo/worlds:$GZ_SIM_RESOURCE_PATH
source ~/.bashrc
```

Untuk memeriksa apakah konfigurasinya sudah benar, coba jalankan 
```bash
echo $GZ_SIM_RESOURCE_PATH
```
Harusnya akan ada seperti ini:

![](../assets/env.png)

### SITL dengan Gazebo

Untuk menguji, coba jalankan ini di satu terminal:
```bash
gz sim -v4 -r iris_runway.sdf
```

lalu jalankan ini di terminal lain:
```bash
sim_vehicle.py -v ArduCopter -f gazebo-iris --model JSON --map --console
```

## Referensi
[https://ardupilot.org/dev/docs/building-setup-linux.html\#building-setup-linux](https://ardupilot.org/dev/docs/building-setup-linux.html#building-setup-linux)  
[https://github.com/ArduPilot/ardupilot](https://github.com/ArduPilot/ardupilot)  
[https://github.com/ArduPilot/ardupilot/blob/master/BUILD.md](https://github.com/ArduPilot/ardupilot/blob/master/BUILD.md)
[https://ardupilot.org/dev/docs/copter-commands-in-guided-mode.html\#copter-commands-in-guided-mode](https://ardupilot.org/dev/docs/copter-commands-in-guided-mode.html\#copter-commands-in-guided-mode)
[Playlist YouTube Drone Software Development Tutorial](https://youtube.com/playlist?list=PLy9nLDKxDN683GqAiJ4IVLquYBod_2oA6&si=D51d0bNMaQjBXplP)

## Credits
- Adhimas Aryo Bimo
- Reysha Syafitri Mulya Ramadhan
- Zulfaqqar Nayaka Athadiansyah