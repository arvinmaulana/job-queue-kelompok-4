# PORTOFOLIO
# PROYEK AKHIR SISTEM OPERASI

## Sistem Antrean Pekerjaan dan Monitoring Proses Linux

---

## 1. Identitas Mahasiswa

| Keterangan | Identitas |
|---|---|
| Nama | Arvin Maulana |
| NIM | 2510512021 |
| Program Studi | Sistem Informasi |
| Mata Kuliah | Sistem Operasi |
| Kelompok | 4 |
| Pilihan Proyek | 5B |
| Posisi | Anggota 2 |
| Tanggung Jawab | Antrean dan Lock (FIFO Worker) |

## 2. Deskripsi Proyek

Proyek akhir Sistem Operasi ini berjudul **Sistem Antrean Pekerjaan dan Monitoring Proses Linux**.

Proyek ini bertujuan membuat sistem pengelolaan pekerjaan komputasi sederhana menggunakan Bash Script pada sistem operasi Linux.

Sistem menggunakan metode FIFO (First In First Out), yaitu pekerjaan yang masuk lebih dahulu akan diproses lebih dahulu oleh worker. Selain itu, sistem menerapkan mekanisme lock untuk mencegah beberapa worker memproses antrean secara bersamaan.

Fitur utama sistem meliputi:
- Pengajuan pekerjaan dengan parameter tervalidasi.
- Pengelolaan antrean pekerjaan menggunakan metode FIFO.
- Penguncian worker agar tidak terjadi pemrosesan secara bersamaan.
- Pemindahan pekerjaan dari antrean ke proses yang sedang berjalan.
- Pencatatan aktivitas pekerjaan pada log.
- Pencatatan status pekerjaan dan hasil eksekusi.
- Pembatasan waktu eksekusi pekerjaan (timeout).
- Monitoring CPU dan memori.
- Pembatasan akses menggunakan permission dan group Linux.
- Pembuatan laporan dan integrasi Docker dengan bind mount read-only.

Proyek dikerjakan secara berkelompok dengan pembagian tanggung jawab masing-masing anggota menggunakan Git dan GitHub.

## 3. Repository dan Pengembangan

### Repository utama kelompok

https://github.com/h44dziq/job-queue-kelompok-4

Repository tersebut digunakan sebagai tempat pengembangan utama proyek Sistem Antrean Pekerjaan dan Monitoring Proses Linux.

### Repository Fork pribadi

Repository Fork pribadi dapat ditambahkan apabila sudah dibuat.

### File utama yang dikerjakan

`scripts/worker.sh`

File tersebut menjadi bagian utama pengerjaan karena digunakan untuk menjalankan pekerjaan dari antrean serta menerapkan mekanisme FIFO dan lock.

## 4. Tanggung Jawab dan Implementasi

Sebagai Anggota 2, saya bertanggung jawab mengembangkan bagian antrean pekerjaan dan mekanisme lock pada worker.

Fokus utama pengerjaan adalah memastikan pekerjaan diproses berdasarkan urutan masuk serta mencegah lebih dari satu worker menjalankan proses antrean secara bersamaan.

Implementasi yang dilakukan:

1. Mengembangkan mekanisme pengambilan pekerjaan dari direktori antrean.
2. Menerapkan metode FIFO (First In First Out) agar pekerjaan diproses berdasarkan urutan masuk.
3. Memastikan pekerjaan yang sudah diambil masuk ke tahap pemrosesan.
4. Menerapkan mekanisme lock pada worker.
5. Mencegah worker kedua berjalan ketika worker pertama masih aktif.
6. Memberikan informasi apabila terdapat worker lain yang sedang berjalan.
7. Menggunakan exit status 6 ketika worker tidak dapat berjalan karena lock sedang digunakan.
8. Melakukan pengujian terhadap urutan pemrosesan pekerjaan.
9. Melakukan pengujian untuk memastikan mekanisme lock berjalan sesuai fungsinya.

### Konsep FIFO

FIFO merupakan metode pengelolaan antrean yang menerapkan prinsip pekerjaan yang pertama masuk akan menjadi pekerjaan pertama yang diproses.

Dalam proyek ini, pekerjaan yang diajukan akan masuk ke direktori `pending`. Worker kemudian mengambil pekerjaan dari antrean untuk diproses sesuai urutan masuk.

### Konsep Lock

Lock merupakan mekanisme penguncian yang digunakan untuk memastikan hanya satu worker yang menjalankan pemrosesan antrean pada satu waktu.

Apabila worker pertama masih aktif dan sedang memegang lock, worker kedua tidak diperbolehkan menjalankan proses pemrosesan antrean.

Pada pengujian proyek ini, worker kedua akan menampilkan pesan bahwa worker lain sedang berjalan dan menghasilkan exit status 6.

## 5. Pengujian Fitur FIFO dan Lock

Pengujian dilakukan dalam dua skenario, yaitu pengujian FIFO dan pengujian mekanisme lock. Setiap pengujian dilakukan menggunakan akun Linux yang telah disediakan dalam proyek.

### A. Pengujian Positif — FIFO (First In First Out)

**Tujuan:**

Memastikan pekerjaan diproses sesuai dengan urutan ketika pekerjaan tersebut dimasukkan ke dalam antrean.

**Langkah 1. Membersihkan antrean**

```bash
sudo rm -f /var/lib/job-queue/{pending,running,done,failed}/*
```

**Langkah 2. Mengajukan pekerjaan pertama**

```bash
sudo -u pengguna1 /opt/job-queue/scripts/submit.sh job_ringan 1
```

**Langkah 3. Mengajukan pekerjaan kedua**

```bash
sudo -u pengguna1 /opt/job-queue/scripts/submit.sh job_gagal 2
```

**Langkah 4. Menjalankan worker**

```bash
sudo -u operator1 /opt/job-queue/scripts/worker.sh
```

**Langkah 5. Memeriksa urutan pekerjaan melalui log**

```bash
tail /var/lib/job-queue/queue.log
```

**Hasil yang diharapkan:**

- `job_ringan` diproses terlebih dahulu karena dimasukkan ke antrean pertama.
- `job_gagal` diproses setelah `job_ringan`.
- Urutan pemrosesan sesuai dengan urutan pengajuan pekerjaan.

**Kesimpulan:**

Berdasarkan pengujian, mekanisme FIFO digunakan untuk memastikan pekerjaan diproses berdasarkan urutan masuk ke dalam antrean. Pekerjaan yang diajukan lebih dahulu akan diproses lebih dahulu oleh worker.

### B. Pengujian Negatif — Lock pada Worker

**Tujuan:**

Memastikan sistem dapat mencegah worker kedua berjalan ketika worker pertama masih aktif memproses pekerjaan.

**Langkah 1. Membersihkan antrean**

```bash
sudo rm -f /var/lib/job-queue/{pending,running,done,failed}/*
```

**Langkah 2. Mengajukan pekerjaan dengan durasi lebih lama**

```bash
sudo -u pengguna1 /opt/job-queue/scripts/submit.sh job_lama 5
```

**Langkah 3. Menjalankan worker pertama di background**

```bash
sudo -u operator1 /opt/job-queue/scripts/worker.sh &
```

Worker pertama dijalankan di background agar terminal tetap dapat digunakan untuk menjalankan perintah berikutnya.

**Langkah 4. Menjalankan worker kedua**

```bash
sudo -u operator1 /opt/job-queue/scripts/worker.sh; echo "exit=$?"
```

**Hasil yang diharapkan:**

```text
Worker lain sedang berjalan
exit=6
```

**Analisis hasil:**

- Worker pertama berhasil berjalan dan memproses pekerjaan.
- Worker pertama masih memegang lock selama proses berlangsung.
- Worker kedua mencoba menjalankan worker ketika lock masih digunakan.
- Sistem menolak proses worker kedua.
- Worker kedua menghasilkan exit status 6.

**Langkah 5. Menunggu worker pertama selesai**

```bash
wait
```

**Kesimpulan:**

Pengujian menunjukkan bahwa mekanisme lock berhasil mencegah dua worker berjalan secara bersamaan. Ketika worker pertama masih aktif, worker kedua mendapatkan penolakan sehingga tidak terjadi pemrosesan antrean secara bersamaan.

## 6. Ringkasan Hasil Pengujian

| No. | Skenario | Hasil yang Diharapkan |
|---|---|---|
| 1 | FIFO dengan job_ringan dan job_gagal | job_ringan diproses terlebih dahulu |
| 2 | Worker pertama berjalan di background | Worker pertama berhasil berjalan |
| 3 | Worker kedua dijalankan ketika lock digunakan | Worker kedua ditolak |
| 4 | Pemeriksaan exit status worker kedua | Exit status 6 |
| 5 | Worker pertama menyelesaikan pekerjaan | Worker pertama selesai dan lock dilepaskan |

Berdasarkan skenario pengujian tersebut, fitur FIFO dan lock digunakan untuk mengatur urutan pemrosesan pekerjaan serta mencegah terjadinya eksekusi worker secara bersamaan.

## 7. Analisis Teori Sistem Operasi

### A. FIFO (First In First Out)

FIFO merupakan metode pengelolaan antrean yang menggunakan prinsip pekerjaan yang masuk pertama akan diproses pertama.

Dalam proyek ini, FIFO diterapkan untuk mengatur urutan pekerjaan yang berada di dalam direktori `pending`.

### B. Process

Process merupakan program yang sedang dijalankan oleh sistem operasi.

Dalam proyek ini, worker bertindak sebagai proses yang bertugas mengambil dan menjalankan pekerjaan dari antrean.

### C. Lock

Lock merupakan mekanisme penguncian yang digunakan untuk membatasi akses terhadap suatu sumber daya agar tidak digunakan secara bersamaan oleh beberapa proses yang tidak diizinkan.

Dalam proyek ini, lock digunakan untuk membatasi jumlah worker yang dapat memproses antrean pada satu waktu.

### D. Background Process

Background process merupakan proses yang dijalankan di latar belakang sehingga terminal tetap dapat digunakan untuk menjalankan perintah lainnya.

Dalam pengujian lock, worker pertama dijalankan menggunakan tanda `&` agar worker kedua dapat langsung diuji ketika worker pertama masih aktif.

### E. Concurrency

Concurrency merupakan kondisi ketika beberapa proses memiliki aktivitas yang berlangsung dalam periode waktu yang sama.

Dalam proyek ini, mekanisme lock digunakan untuk mengendalikan akses worker terhadap antrean agar tidak terjadi pemrosesan secara bersamaan.

### F. Exit Status

Exit status merupakan nilai yang menunjukkan hasil akhir eksekusi suatu perintah atau proses.

Dalam pengujian lock:
- `0` menunjukkan perintah berhasil dijalankan.
- `6` menunjukkan worker ditolak karena terdapat worker lain yang sedang berjalan.

## 8. Aktivitas Pengerjaan Proyek

Selama pengerjaan proyek, saya melakukan beberapa aktivitas yang berkaitan dengan implementasi FIFO dan lock.

### A. Persiapan Lingkungan Linux

- Menggunakan Ubuntu Server melalui VirtualBox.
- Membuka terminal Ubuntu untuk menjalankan perintah Linux.
- Memeriksa struktur direktori proyek.
- Memeriksa direktori antrean pekerjaan.
- Menggunakan akun Linux sesuai pembagian hak akses.
- Menjalankan script pengajuan pekerjaan dan worker.
- Memeriksa hasil eksekusi melalui log.

### B. Implementasi dan Pengujian

Aktivitas yang dilakukan meliputi:

1. Memahami alur pengajuan pekerjaan ke dalam antrean.
2. Memeriksa mekanisme pemrosesan pekerjaan oleh worker.
3. Menguji urutan pekerjaan menggunakan metode FIFO.
4. Menguji worker pertama dalam kondisi background.
5. Menjalankan worker kedua ketika worker pertama masih aktif.
6. Memeriksa pesan penolakan worker kedua.
7. Memeriksa exit status hasil pengujian.
8. Memastikan worker pertama dapat menyelesaikan pekerjaan.

### C. Pengelolaan Git dan GitHub

Aktivitas pengelolaan repository yang berkaitan dengan pengerjaan proyek meliputi:

- Menggunakan repository GitHub kelompok.
- Memeriksa struktur file proyek.
- Mengikuti perkembangan perubahan kode kelompok.
- Menggunakan Git untuk mengelola perubahan proyek.
- Mendokumentasikan hasil pengerjaan dan pengujian.

## 9. Bukti Dokumentasi

Bukti dokumentasi yang perlu dilampirkan pada repository portofolio:

| No. | Bukti | Keterangan |
|---|---|---|
| 1 | Screenshot struktur direktori proyek | Lingkungan pengerjaan Ubuntu |
| 2 | Screenshot kode `worker.sh` | Implementasi FIFO dan lock |
| 3 | Screenshot pengajuan job pertama | Pengujian FIFO |
| 4 | Screenshot pengajuan job kedua | Pengujian urutan antrean |
| 5 | Screenshot worker dijalankan | Proses pemrosesan pekerjaan |
| 6 | Screenshot `queue.log` | Bukti urutan pemrosesan FIFO |
| 7 | Screenshot worker pertama background | Pengujian lock |
| 8 | Screenshot penolakan worker kedua | Bukti mekanisme lock |
| 9 | Screenshot exit status 6 | Hasil pengujian negatif |
| 10 | Screenshot worker pertama selesai | Bukti penyelesaian proses |

## 10. Kesimpulan dan Pembelajaran

Melalui proyek akhir Sistem Operasi ini, saya memperoleh pengalaman dalam memahami dan mengimplementasikan pengelolaan antrean pekerjaan menggunakan Bash Script pada sistem operasi Linux.

Fokus utama pekerjaan saya adalah penerapan metode FIFO dan mekanisme lock pada worker. FIFO digunakan untuk memastikan pekerjaan diproses berdasarkan urutan masuk, sedangkan lock digunakan untuk mencegah beberapa worker menjalankan pemrosesan antrean secara bersamaan.

Melalui pengujian positif dan negatif, saya dapat memahami bagaimana worker mengambil pekerjaan dari antrean serta bagaimana sistem menangani kondisi ketika terdapat worker lain yang masih aktif.

Selain itu, proyek ini memberikan pengalaman dalam menggunakan terminal Linux, menjalankan proses di background, memeriksa log, serta memahami konsep process, concurrency, dan locking dalam sistem operasi.

Proyek ini membantu saya memahami penerapan teori sistem operasi secara langsung melalui pengembangan sistem antrean pekerjaan yang menggunakan Bash Script.

---

**Dokumentasi ini disusun sebagai portofolio kontribusi pribadi dalam Proyek Akhir Sistem Operasi Kelompok 4.**
