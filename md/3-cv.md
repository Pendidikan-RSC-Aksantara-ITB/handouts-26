# Computer Vision

## Dasar-Dasar Pengolahan Citra Digital

### Representasi Citra

Citra, atau gambar yang kita lihat sehari di layar pada dasarnya tersusun atas piksel-piksel yang merupakan unit terkecilnya.

![alt text](../assets/piksel.png)

Secara matematis, kita bisa bayangkan sebuah citra 2D sebagai matriks dua dimensi berukuran tinggi ($H$) x lebar ($W$), di mana setiap elemen matriks tersebut mewakili nilai intensitas cahaya pada piksel tertentu. Misalnya, dalam citra *grayscale*, setiap piksel hanya memiliki satu nilai intensitas yang menunjukkan seberapa terang atau gelapnya piksel tersebut, biasanya dalam rentang 0 (hitam) hingga 255 (putih) untuk representasi 8-bit.

<figure>
  <img src="../assets/grayscale.png" alt="Representasi Matriks Citra Grayscale" width="400"/>
  <figcaption>Representasi matriks citra grayscale. <a href="https://www.analyticsvidhya.com/blog/2021/03/grayscale-and-rgb-format-for-storing-images/">sumber</a></figcaption>
</figure>

Untuk citra berwarna, seperti RGB (Red, Green, Blue), setiap piksel direpresentasikan oleh tiga nilai intensitas yang menunjukkan kontribusi masing-masing warna primer. Jadi, citra RGB dapat dianggap sebagai tumpukan tiga matriks 2D, masing-masing mewakili saluran (*channel*) warna merah, hijau, dan biru. Model  warna lain yang sering digunakan adalah HSV (Hue, Saturation, Value), yang lebih sesuai untuk analisis warna karena memisahkan informasi warna (*hue*) dari kecerahan (*value*). Gambar di bawah ini menunujukkan sebuah gambar yang dipecah berdasarkan ruang warna RGB dan HSV.

![sumber: https://www.researchgate.net/figure/Comparison-of-RGB-and-HSV-color-space-of-the-No-56-image-67-079-from-the-Berk-ele-y_fig1_372366662](../assets/rgb-hsv.png)
<figure>
  <figcaption>Perbandingan representasi citra dalam ruang warna RGB dan HSV. <a href="https://www.researchgate.net/figure/Comparison-of-RGB-and-HSV-color-space-of-the-No-56-image-67-079-from-the-Berk-ele-y_fig1_372366662">sumber</a></figcaption>
</figure>

### Digitalisasi: Sampling & Quantization

- Sampling: mengubah dunia kontinu menjadi grid piksel (terkait resolusi).
- Quantization: membatasi nilai intensitas/warna menjadi level diskret (mis. 8-bit → 256 level).
- Implikasi umum: aliasing/moiré (sampling), banding/posterization (quantization).

### Operasi Neighborhood

#### Operasi convolution/kernel

Overview ini tuh apa, cara kerjanya gmn
Kasi contoh penggunaan smoothing/denoising: Gaussian blur
Kasi contoh deteksi tepi: Sobel & Canny

#### Operasi morfologi

Overview ini tuh apa, cara kerjanya gmn
erosion
dilation

### Geometri Kamera dan Proyeksi

- Pinhole camera model (konsep; persamaan bisa dijadikan bonus).
- Coordinate frames & transformasi rigid.
- Intrinsic/extrinsic parameters: makna dan contoh penggunaannya.

### Kalibrasi dan Koreksi

- Distorsi lensa (radial/tangential).
- Prosedur kalibrasi kamera.
- Undistortion dan dampaknya ke akurasi pengukuran dan tracking.

## Eksplorasi OpenCV

### Konsep Dasar OpenCV

- `cv::Mat`: tipe data utama untuk menyimpan citra.
- Perbedaan urutan warna: OpenCV sering memakai BGR.
- Akses pixel (row/col), ROI, dan skala nilai (0–255).

### I/O dan Loop Video

- Membaca gambar (`imread`) dan menampilkan (`imshow`).
- Membaca video/kamera (`VideoCapture`) dan konsep loop frame + `waitKey`.


## AI dalam Computer Vision

### Machine Learning

- Supervised vs unsupervised (intuisi dan contoh).
- Dataset & label, pembagian train/val/test.
- Overfitting vs underfitting (gejala dan cara mitigasi umum).

### Evaluasi Model

Klasifikasi
confusion matrix
tabel visualisasi prediksi model
Accuracy
persentase total prediksi yang benar dari keseluruhan data
Precision
tingkat ketepatan prediksi positif ( predict positive that trully positive)
Recall
tingkat keberhasilan menemukan data positive (mengurangi false negative)
 F1-score
harmonic mean dari precision dan recall
deteksi/segmentasi
 IoU dan mAP (untuk deteksi)
mengukur seberapa akurat irisan gambar dengan kelas objek, umumnya pada OBB (object bounding box)
Dice/IoU (untuk segmentasi)
mengukur seberapa akurat irisan gambar dengan segmentasi kelas objek. Bobot nilai irisan diberi nilai lebih


### Deep Learning

Deep learning adalah subset dari *machine learning* yang menggunakan jaringan saraf tiruan dengan banyak lapisan (deep *neural networks*) untuk mempelajari representasi data yang kompleks. Apa maksudnya? Pada dasarnya, salah satu cara membuat model yang bisa mengenali pola seperti otak manusia adalah dengan meniru cara kerja otak itu sendiri, yaitu melalui jaringan saraf. Deep learning memungkinkan kita untuk membangun model yang sangat kuat dan fleksibel, yang dapat belajar dari data dalam jumlah besar dan menangani tugas-tugas yang kompleks seperti pengenalan gambar, pemrosesan bahasa alami, dan banyak lagi.

Salah satu arsitektur deep learning yang paling populer untuk tugas-tugas computer vision adalah Convolutional Neural Networks (CNNs). CNN dirancang khusus untuk memproses data yang memiliki struktur grid, seperti gambar, dan mereka sangat efektif dalam mengenali pola visual. CNN memahami hubungan spasial antar piksel melalui matriks konvolusinya.

Inti dari CNN adalah lapisan konvolusi yang menerapkan filter (kernel) berukuran kecil pada gambar input untuk mengekstrak fitur-fitur penting seperti tepi, sudut, dan tekstur. Lapisan pooling kemudian digunakan untuk mereduksi dimensi data sambil mempertahankan informasi penting, sehingga mengurangi beban komputasi dan membantu mencegah overfitting. Filter digeser di seluruh gambar untuk menghasilkan peta fitur (feature maps) yang menyoroti area-area penting. Setelah beberapa lapisan konvolusi dan pooling, data biasanya diratakan (flatten) dan diteruskan ke lapisan fully-connected (dense layer) yang melakukan klasifikasi berdasarkan fitur yang telah diekstraksi.

![alt text](../assets/cnn.gif)

### Deteksi Objek (Studi Kasus)

- Gambaran cara kerja YOLO: prediksi bounding box + class.
- Post-processing: NMS (konsep).

## Computer Vision dalam UAV (Overview)

Bagian ini bertujuan memberi konteks: bagaimana CV dipakai di sistem UAV, apa tantangan utamanya, dan istilah yang sering muncul.

### Edge AI (Deployment)

- Konsep optimasi inference: batching, quantization (FP16/INT8), dan accelerators.
- Tooling contoh: TensorRT (sebagai gambaran, tidak perlu mendalam).

### Pose Estimation & SLAM

- Intuisi: dari citra → estimasi pose/gerak (visual odometry) → membangun peta (SLAM).
- Kenapa kalibrasi dan geometri kamera jadi fondasi.

## Referensi & Bacaan Lanjutan

[A guide to convolution arithmetic for deep learning oleh Vincent Dumoulin and Francesco Visin](https://arxiv.org/pdf/1603.07285)

[Intuitively Understanding Convolutions for Deep Learning oleh Irhum Shafkat](https://medium.com/data-science/intuitively-understanding-convolutions-for-deep-learning-1f6f42faee1)
