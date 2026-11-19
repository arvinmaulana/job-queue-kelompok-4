
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

Sistem menggunakan metode FIFO (First In First Out), yaitu pekerjaan yang masuk lebih dahulu akan diproses lebih dahulu oleh worker.

Fitur utama sistem meliputi:
- Pengajuan pekerjaan menggunakan script submit.
- Pengelolaan antrean pekerjaan menggunakan metode FIFO.
- Penguncian worker agar tidak terjadi pemrosesan ganda.
- Pemrosesan pekerjaan oleh worker.
- Pencatatan status pekerjaan, PID, dan exit status.
- Pemisahan pekerjaan berdasarkan status pending, running, done, dan failed.
- Monitoring hasil pekerjaan melalui script status.
- Pengaturan hak akses pengguna dan proses menggunakan permission Linux.

Proyek dikerjakan secara berkelompok dengan pembagian tanggung jawab masing-masing anggota menggunakan Git dan GitHub.

## 3. Repository dan Pengembangan

### Repository utama kelompok

https://github.com/h44dziq/job-queue-kelompok-4

### Repository Fork pribadi

https://github.com/arvinmaulana/job-queue-kelompok-4

### File yang berkaitan dengan pengerjaan

File utama yang menjadi fokus tanggung jawab saya adalah:

https://github.com/h44dziq/job-queue-kelompok-4/tree/feat/arvinmaulana-workerfifo

File pendukung yang digunakan dalam proses pengujian:

https://github.com/h44dziq/job-queue-kelompok-4/blob/feat/arvinmaulana-workerfifo/scripts/submit.sh

File `worker.sh` digunakan untuk mengambil dan menjalankan pekerjaan dari antrean. File `submit.sh` digunakan untuk memasukkan pekerjaan ke dalam antrean, sedangkan `status.sh` digunakan untuk melihat hasil akhir pekerjaan.

Pengembangan dan pengujian dilakukan menggunakan Ubuntu Server dengan Bash Script. Git dan GitHub digunakan untuk mengelola perubahan kode serta menyimpan hasil pengembangan proyek.

## 4. Tanggung Jawab dan Implementasi

Sebagai Anggota 2, saya bertanggung jawab pada bagian pengelolaan antrean pekerjaan dan mekanisme Lock pada worker.

File utama yang berkaitan dengan pengerjaan:

https://github.com/h44dziq/job-queue-kelompok-4/blob/feat/arvinmaulana-workerfifo/scripts/worker.sh

Implementasi yang menjadi fokus pekerjaan meliputi:

1. Menerapkan mekanisme FIFO (First In First Out) untuk memastikan pekerjaan diproses berdasarkan urutan masuk.
2. Mengatur proses pengambilan pekerjaan oleh worker dari direktori antrean.
3. Memastikan pekerjaan yang telah masuk ke antrean dapat dijalankan oleh worker.
4. Menerapkan mekanisme Lock untuk mencegah lebih dari satu worker berjalan secara bersamaan.
5. Menangani kondisi ketika worker lain masih aktif dengan menolak proses worker berikutnya.
6. Memastikan worker berhenti ketika antrean sudah kosong.
7. Melakukan pemeriksaan hasil pekerjaan menggunakan script `status.sh`.

### A. Mekanisme FIFO

FIFO merupakan metode antrean yang menggunakan prinsip pekerjaan pertama masuk akan diproses pertama.

Pada sistem ini, pekerjaan yang dikirimkan oleh pengguna akan dimasukkan ke dalam antrean. Worker kemudian mengambil dan menjalankan pekerjaan sesuai urutan antrean.

Mekanisme ini digunakan agar pekerjaan dapat diproses secara teratur dan tidak dijalankan secara acak.

### B. Mekanisme Lock

Lock merupakan mekanisme penguncian yang digunakan untuk mencegah beberapa worker menjalankan antrean secara bersamaan.

Ketika worker pertama sedang aktif, worker kedua tidak diperbolehkan menjalankan proses. Sistem akan menampilkan pesan penolakan apabila terdapat worker lain yang masih berjalan.

Dengan adanya mekanisme ini, proses pengambilan dan pemrosesan pekerjaan dapat dikendalikan agar tidak terjadi pemrosesan ganda.

### C. Monitoring Pekerjaan

Setelah pekerjaan selesai diproses, statusnya dapat diperiksa menggunakan script `status.sh`.

Informasi yang ditampilkan meliputi:
- ID pekerjaan.
- Status pekerjaan.
- Process ID (PID).
- Exit code.

Informasi tersebut digunakan untuk mengetahui apakah pekerjaan berhasil dijalankan atau mengalami kegagalan.

## 5. Pengujian Fitur FIFO dan Lock

Pengujian dilakukan untuk memastikan mekanisme FIFO dapat menjalankan pekerjaan sesuai urutan masuk serta mekanisme Lock dapat mencegah dua worker berjalan secara bersamaan.

### A. Pengujian Positif — Mekanisme FIFO

**Tujuan:**

Memastikan pekerjaan dijalankan berdasarkan urutan masuk ke dalam antrean.

Pengujian dilakukan dengan memasukkan dua pekerjaan, yaitu `job_ringan` dengan parameter 1 dan `job_gagal` dengan parameter 2.

Perintah pengajuan pekerjaan pertama:

```bash
sudo -u pengguna1 /opt/job-queue/scripts/submit.sh job_ringan 1
```

Hasil:

```text
Job diterima: 20261118155309173118149_job_ringan (parameter 1)
```

Perintah pengajuan pekerjaan kedua:

```bash
sudo -u pengguna1 /opt/job-queue/scripts/submit.sh job_gagal 2
```

Hasil:

```text
Job diterima: 20261118155324171744530_job_gagal (parameter 2)
```

Setelah kedua pekerjaan masuk ke antrean, worker dijalankan menggunakan akun `operator1`.

```bash
sudo -u operator1 /opt/job-queue/scripts/worker.sh
```

Hasil:

```text
Menjalankan 20261118155309173118149_job_ringan
Menjalankan 20261118155324171744530_job_gagal
Antrean kosong, worker berhenti.
```

Berdasarkan hasil pengujian, `job_ringan` dijalankan terlebih dahulu, kemudian dilanjutkan dengan `job_gagal`.

**Kesimpulan:** Mekanisme FIFO berhasil menjalankan pekerjaan sesuai urutan masuk ke dalam antrean. Setelah seluruh pekerjaan selesai diproses, worker berhenti karena antrean sudah kosong.

### B. Pengujian Negatif — Pencegahan Worker Ganda (Lock)

**Tujuan:**

Memastikan sistem dapat menolak worker kedua ketika worker pertama masih aktif menjalankan pekerjaan.

Pengujian dilakukan dengan memasukkan pekerjaan `job_lama` menggunakan akun `pengguna1`.

Perintah pengajuan pekerjaan:

```bash
sudo -u pengguna1 /opt/job-queue/scripts/submit.sh job_lama 5
```

Hasil:

```text
Job diterima: 20261118155353828052330_job_lama (parameter 5)
```

Selanjutnya, worker pertama dijalankan di background dan worker kedua langsung dijalankan.

Perintah pengujian:

```bash
sudo -u operator1 /opt/job-queue/scripts/worker.sh & sudo -u operator1 /opt/job-queue/scripts/worker.sh
```

Hasil:

```text
[1] 4028
Ditolak: worker lain sedang berjalan
```

Setelah worker kedua ditolak, worker pertama tetap melanjutkan proses pekerjaan.

```text
Menjalankan 20261118155353828052330_job_lama
Antrean kosong, worker berhenti.
```

Berdasarkan hasil pengujian, worker kedua tidak dapat berjalan karena worker pertama masih aktif. Worker pertama berhasil menyelesaikan pekerjaan `job_lama` dan berhenti setelah antrean kosong.

**Kesimpulan:** Mekanisme Lock berhasil mencegah dua worker berjalan secara bersamaan. Sistem dapat mendeteksi worker yang sedang aktif dan menolak eksekusi worker berikutnya.

### C. Pengujian Monitoring Status Pekerjaan

**Tujuan:**

Memastikan sistem dapat menampilkan hasil akhir pekerjaan yang telah diproses oleh worker.

Pemeriksaan dilakukan menggunakan script `status.sh`.

Perintah:

```bash
sudo -u pengguna1 /opt/job-queue/scripts/status.sh
```

Hasil:

```text
ID_JOB                               STATUS     PID      EXIT
20261118155309173118149_job_ringan   BERHASIL   3966     0
20261118155353828052330_job_lama    BERHASIL   4062     0
20261118155324171744530_job_gagal   GAGAL      3997     2
```

Berdasarkan hasil pemeriksaan, diperoleh informasi sebagai berikut:

**1. job_ringan**
- PID: 3966
- Status: BERHASIL
- Exit code: 0

**2. job_lama**
- PID: 4062
- Status: BERHASIL
- Exit code: 0

**3. job_gagal**
- PID: 3997
- Status: GAGAL
- Exit code: 2

Hasil tersebut menunjukkan bahwa sistem dapat membedakan pekerjaan yang berhasil dan pekerjaan yang mengalami kegagalan berdasarkan exit code masing-masing proses.

**Kesimpulan:** Script `status.sh` berhasil menampilkan status akhir pekerjaan beserta PID dan exit code sehingga hasil pemrosesan dapat dipantau.

## 6. Ringkasan Hasil Pengujian

Berdasarkan pengujian yang telah dilakukan, diperoleh hasil sebagai berikut:

| No. | Skenario Pengujian | Hasil | Keterangan |
|---|---|---|---|
| 1 | Pengiriman job_ringan | Berhasil | Pekerjaan berhasil masuk ke antrean |
| 2 | Pengiriman job_gagal | Berhasil | Pekerjaan berhasil masuk ke antrean |
| 3 | Pengujian FIFO | Berhasil | job_ringan dijalankan sebelum job_gagal |
| 4 | Pengujian Lock | Berhasil | Worker kedua ditolak ketika worker pertama masih aktif |
| 5 | Eksekusi job_lama | Berhasil | Pekerjaan selesai dijalankan oleh worker pertama |
| 6 | Monitoring pekerjaan | Berhasil | Status, PID, dan exit code berhasil ditampilkan |
| 7 | Penanganan pekerjaan gagal | Sesuai skenario | job_gagal memiliki status GAGAL dan exit code 2 |

Secara keseluruhan, pengujian menunjukkan bahwa mekanisme FIFO dan Lock berjalan sesuai skenario yang telah dilakukan. Sistem juga mampu menampilkan status akhir pekerjaan melalui fitur monitoring.

## 7. Analisis Teori Sistem Operasi

### A. First In First Out (FIFO)

FIFO merupakan metode pengelolaan antrean yang menggunakan prinsip pekerjaan pertama masuk akan diproses pertama.

Dalam proyek ini, FIFO diterapkan untuk mengatur urutan pekerjaan yang dijalankan oleh worker. Hasil pengujian menunjukkan bahwa `job_ringan` diproses sebelum `job_gagal` karena pekerjaan tersebut dimasukkan lebih dahulu ke dalam antrean.

### B. Process

Process merupakan program yang sedang dieksekusi oleh sistem operasi.

Dalam proyek ini, setiap pekerjaan yang dijalankan oleh worker menjadi proses yang memiliki PID. PID digunakan untuk mengidentifikasi proses yang sedang atau telah dijalankan.

### C. Process Synchronization

Process Synchronization merupakan mekanisme untuk mengatur jalannya beberapa proses agar tidak menimbulkan konflik ketika mengakses sumber daya bersama.

Dalam proyek ini, mekanisme Lock digunakan untuk membatasi worker yang aktif agar tidak ada worker lain yang menjalankan antrean pada waktu bersamaan.

### D. Lock

Lock merupakan mekanisme penguncian yang digunakan untuk mengendalikan akses terhadap suatu sumber daya.

Pada proyek ini, Lock diterapkan untuk memastikan hanya satu worker yang dapat menjalankan proses antrean pada satu waktu. Pengujian menunjukkan worker kedua ditolak ketika worker pertama masih aktif.

### E. Concurrency

Concurrency merupakan kondisi ketika beberapa proses dapat berlangsung dalam periode waktu yang saling tumpang tindih.

Dalam pengujian ini, dua perintah worker dijalankan secara berdekatan. Mekanisme Lock memastikan bahwa hanya worker pertama yang dapat melanjutkan proses, sedangkan worker kedua ditolak.

### F. Exit Status

Exit status merupakan nilai yang menunjukkan hasil akhir eksekusi suatu proses.

Dalam proyek ini:
- Exit code 0 menunjukkan pekerjaan berhasil dijalankan.
- Exit code 2 menunjukkan pekerjaan mengalami kegagalan.

Nilai tersebut digunakan oleh sistem untuk membedakan hasil akhir setiap pekerjaan.

## 8. Aktivitas Pengerjaan Proyek

Selama pengerjaan proyek, saya melakukan beberapa aktivitas yang berkaitan dengan pengembangan dan pengujian sistem antrean.

### A. Persiapan Lingkungan Linux

- Menggunakan Ubuntu Server melalui lingkungan virtual.
- Menjalankan perintah Linux melalui terminal.
- Memahami struktur direktori sistem antrean.
- Menggunakan akun `pengguna1` untuk mengirimkan pekerjaan.
- Menggunakan akun `operator1` untuk menjalankan worker.

### B. Pengembangan dan Pemahaman Sistem

- Mempelajari alur kerja sistem antrean pekerjaan.
- Memahami mekanisme FIFO pada proses worker.
- Mempelajari mekanisme Lock untuk mencegah worker ganda.
- Memahami hubungan antara script `submit.sh`, `worker.sh`, dan `status.sh`.
- Mempelajari proses pemindahan dan pengelolaan pekerjaan dalam antrean.

### C. Pengujian Sistem

- Mengirimkan pekerjaan `job_ringan` dengan parameter 1.
- Mengirimkan pekerjaan `job_gagal` dengan parameter 2.
- Menjalankan worker untuk menguji urutan pemrosesan FIFO.
- Mengirimkan pekerjaan `job_lama` dengan parameter 5.
- Menjalankan dua worker secara berdekatan untuk menguji mekanisme Lock.
- Memeriksa hasil akhir pekerjaan menggunakan script `status.sh`.
- Mengamati PID, status, dan exit code setiap pekerjaan.

### D. Pengelolaan Git dan GitHub

- Menggunakan repository proyek kelompok.
- Memahami penggunaan Git untuk pengelolaan kode proyek.
- Menggunakan GitHub sebagai tempat penyimpanan repository.
- Mendokumentasikan kontribusi pribadi melalui repository Fork.

## 9. Bukti Dokumentasi

Bukti pengembangan dan pengujian bagian FIFO dan Lock dapat ditelusuri melalui hasil praktik serta repository proyek.

Dokumentasi yang berkaitan dengan kontribusi saya meliputi:

| No. | Dokumentasi | Keterangan |
|---|---|---|
| 1 | Repository utama kelompok | Sumber kode sistem antrean pekerjaan |
| 2 | File `worker.sh` | Implementasi proses worker, FIFO, dan Lock |
| 3 | File `submit.sh` | Proses pengiriman pekerjaan ke antrean |
| 4 | File `status.sh` | Pemeriksaan status pekerjaan |
| 5 | Pengujian FIFO | Hasil pemrosesan job_ringan dan job_gagal |
| 6 | Pengujian Lock | Hasil penolakan worker kedua ketika worker pertama aktif |
| 7 | Hasil monitoring | Informasi status, PID, dan exit code pekerjaan |

Repository utama:

https://github.com/h44dziq/job-queue-kelompok-4

## 10. Kesimpulan dan Pembelajaran

Melalui proyek akhir Sistem Operasi ini, saya memperoleh pengalaman dalam memahami dan menerapkan konsep pengelolaan proses Linux menggunakan Bash Script.

Fokus utama pekerjaan saya adalah mekanisme antrean FIFO dan Lock pada worker. Berdasarkan hasil pengujian, mekanisme FIFO berhasil menjalankan pekerjaan sesuai urutan masuk ke dalam antrean. Selain itu, mekanisme Lock berhasil mencegah worker kedua berjalan ketika worker pertama masih aktif.

Pengujian juga menunjukkan bahwa sistem dapat memproses pekerjaan dengan hasil yang berbeda, yaitu pekerjaan yang berhasil maupun pekerjaan yang mengalami kegagalan. Informasi hasil pemrosesan tersebut dapat diperiksa melalui script `status.sh` berdasarkan status, PID, dan exit code.

Selain memperoleh pemahaman mengenai FIFO, Lock, process, dan concurrency, saya juga mendapatkan pengalaman menggunakan Ubuntu Server, menjalankan Bash Script, melakukan pengujian sistem, serta memahami penggunaan Git dan GitHub dalam pengembangan proyek secara berkelompok.

Proyek ini memberikan pemahaman secara langsung mengenai bagaimana konsep sistem operasi diterapkan dalam pengelolaan antrean pekerjaan dan pengendalian proses pada Linux.

---
