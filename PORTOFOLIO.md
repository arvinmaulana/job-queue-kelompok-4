
# PORTOFOLIO KONTRIBUSI INDIVIDU

## 1. Identitas

- **Nama:** Arvin Maulana
- **NIM:** 2510512021
- **Kelas:** A
- **Kelompok:** 5B
- **Mata Kuliah:** Sistem Operasi
- **Peran:** Anggota 2 – Antrean dan Lock

## 2. Deskripsi Proyek

Proyek ini merupakan pengembangan sistem antrean pekerjaan dan monitoring proses pada Linux. Sistem dirancang untuk menerima pekerjaan, memasukkannya ke dalam antrean, menjalankan pekerjaan melalui worker, serta mencatat hasil proses pekerjaan.

## 3. Tanggung Jawab Individu

Dalam proyek ini, saya bertanggung jawab pada implementasi mekanisme antrean pekerjaan menggunakan metode FIFO (First In First Out) dan mekanisme lock untuk mencegah beberapa worker menjalankan pekerjaan secara bersamaan.

## 4. Implementasi yang Dikerjakan

### 4.1 Implementasi FIFO

Menerapkan mekanisme FIFO pada `worker.sh` agar pekerjaan diproses berdasarkan urutan masuk ke dalam folder `pending`. Job yang dipilih kemudian dipindahkan ke folder `running` untuk dieksekusi.

### 4.2 Implementasi Lock

Menggunakan perintah `flock` untuk mengunci proses worker. Mekanisme ini bertujuan mencegah lebih dari satu worker memproses antrean secara bersamaan.

### 4.3 Pengelolaan Hasil Pekerjaan

Mendukung proses pemindahan hasil pekerjaan ke folder `done` apabila berhasil dan ke folder `failed` apabila mengalami kegagalan.

## 5. Pengujian

Pengujian yang telah dilakukan meliputi:

1. Menjalankan worker ketika antrean berisi pekerjaan.
2. Memasukkan beberapa job untuk memastikan urutan pemrosesan FIFO.
3. Menjalankan dua worker secara bersamaan untuk menguji mekanisme lock.
4. Menguji job yang berhasil dan job yang gagal.
5. Menguji job yang melewati batas waktu eksekusi.

Hasil pengujian menunjukkan bahwa mekanisme FIFO berjalan sesuai urutan masuk dan worker kedua ditolak ketika worker pertama masih menggunakan lock.

## 6. Bukti Kontribusi

- **Repository kelompok:** https://github.com/h44dziq/job-queue-kelompok-4
- **Personal fork:** https://github.com/arvinmaulana/job-queue-kelompok-4
- **Branch pekerjaan:** `feat/arvinmaulana-workerfifo`
- **File utama:** `scripts/worker.sh`

## 7. Evaluasi dan Pengembangan

Pengembangan selanjutnya difokuskan pada penyempurnaan mekanisme lock, pengujian dengan jumlah pekerjaan yang lebih banyak, serta pengujian penggunaan sumber daya sistem seperti CPU dan memori.

## 8. Kesimpulan

Melalui pengerjaan proyek ini, saya memperoleh pengalaman dalam mengimplementasikan sistem antrean pekerjaan pada Linux, memahami penggunaan FIFO, serta menerapkan mekanisme sinkronisasi proses menggunakan `flock` untuk menghindari eksekusi worker secara bersamaan.

