# PRD — E-Voting HIMABISDI

**Versi:** 1.0 — baseline hasil interview, dengan keputusan terbuka yang ditandai.  
**Tanggal:** 4 November 2026.  
**Pemilik produk / pengelola teknis awal:** Rifqy Maulana.  
**Target:** versi pertama siap dalam dua minggu sejak pekerjaan dimulai; tanggal acara belum diberikan.  
**Sumber kebutuhan:** brief pengguna, PDF alur lama, dan 52 jawaban interview.  
**Catatan keputusan:** [docs/KEPUTUSAN-INTERVIEW.md](docs/KEPUTUSAN-INTERVIEW.md).

Dokumen ini menjadi acuan pembangunan, pengujian, dan operasional pemilihan. Keputusan pengguna menjadi baseline produk. Detail arsitektur, batas ukuran, dan target performa merupakan rekomendasi implementasi yang harus dibuktikan melalui pengujian. Implementasi aplikasi belum dilakukan dalam tahap penyusunan PRD ini.

**Navigasi:** [Alur pengguna](#6-alur-pengguna) · [Aturan bisnis](#7-status-dan-aturan-bisnis) · [Fitur](#8-persyaratan-fungsional) · [UX/UI](#9-ux-ui-motion-dan-audio) · [Stack](#10-rekomendasi-teknologi) · [Database](#11-rancangan-database-minimal) · [Keamanan](#12-keamanan-dan-kerahasiaan) · [Biaya](#13-biaya-kapasitas-dan-batas-layanan-gratis) · [Pengujian](#15-rencana-pengujian-dan-penerimaan) · [Keputusan terbuka](#17-keputusan-terbuka-dan-dependensi).

## 1. Ringkasan produk

Aplikasi pemilihan ketua umum Himpunan Mahasiswa Bisnis Digital Universitas Pakuan yang digunakan secara langsung di lokasi pemilihan. Mahasiswa diverifikasi di meja registrasi, diarahkan ke salah satu bilik, lalu memilih melalui website fullscreen.

Pengalaman utama:

> Datang → verifikasi kartu/NPM → ditugaskan ke bilik → Mulai → tap kartu atau ketik NPM → cocokkan identitas → pilih kandidat → konfirmasi → suara tersimpan → welcome kembali.

Panitia melihat kehadiran, partisipasi, dan status bilik. Perolehan kandidat hanya tersedia setelah pemilihan ditutup. Admin memeriksa hasil sebelum memublikasikannya. Aplikasi digunakan kembali setiap tahun dengan pemilihan dan daftar pemilih baru.

### 1.1 Keputusan inti

| Aspek | Keputusan |
| --- | --- |
| Skala | 500–1.000 pemilih; 2–3 bilik bersamaan |
| Surat suara | Memilih satu individu calon ketua; 2–4 kandidat |
| Bobot | Pengurus: 3; mahasiswa biasa: 1; setiap orang hanya mengirim sekali |
| Abstain | Tidak tersedia; sesi boleh ditinggalkan sebelum mengirim |
| Identitas | NPM sembilan digit, disimpan sebagai teks |
| RFID | Reader USB seperti keyboard di meja registrasi dan setiap bilik |
| Pemetaan RFID | Sudah ada pada pihak lain; akses menunggu perizinan |
| Jalur pengganti | Input NPM, setelah verifikasi fisik dan penugasan sesi oleh operator |
| Otorisasi bilik | Perangkat didaftarkan sekali saat persiapan; sesi terikat ke pemilih dan bilik |
| Kerahasiaan | Panitia tidak dapat melihat hubungan mahasiswa dengan pilihannya |
| Akun panitia | Admin dan operator; email + password individual; tanpa MFA pada MVP |
| Kendali acara | Buka, jeda, lanjutkan, dan tutup secara manual |
| Penutupan | Hentikan sesi baru; sesi yang sudah ada diberi tenggang maksimal dua menit |
| Hasil | Bobot terbanyak; hasil seri menunggu aturan resmi |
| Realtime | Partisipasi saja selama voting; target pembaruan 1–2 detik |
| UX | Terang, bersih, premium, teks singkat; mendukung mouse dan touchscreen |
| Reset layar | Lima detik setelah sukses, atau lebih cepat melalui tombol Selesai |
| Sesi diam | 90 detik tanpa aktivitas + peringatan 15 detik; bisa dilanjutkan |
| Layanan | Target Rp0, Vercel Hobby + Supabase Free, subdomain vercel.app |
| Koneksi putus | Jeda dan gunakan internet cadangan |
| Retensi identitas | Sampai pemilihan tahun berikutnya; daftar baru tetap diimpor setiap tahun |

## 2. Latar belakang dan perbaikan

PDF `Alur-Pengguna-E-Voting-HIMABISDI.pdf` menjelaskan plugin OXS E-Voting v0.1.3 dengan dua peran dan data backup 30 Juni 2026: 582 pemilih, 104 pengurus, 478 mahasiswa biasa, 192 suara, dan dua kandidat. Angka tersebut merupakan data historis, bukan daftar pemilih untuk acara baru.

| Alur terdahulu dalam PDF | Kebutuhan aplikasi baru |
| --- | --- |
| Mahasiswa cukup mengisi NIM | NPM/RFID harus cocok dengan sesi yang telah diverifikasi dan ditugaskan ke bilik |
| Suara langsung dikirim tanpa konfirmasi | Konfirmasi khusus dengan foto, nomor, dan nama calon |
| Kembali ke awal perlu klik | Reset otomatis setelah sukses dan pembersihan sesi |
| Membuka/menutup melalui status halaman WordPress | Status pemilihan eksplisit, dengan jeda dan fase penyelesaian sesi |
| Rekap perlu refresh | Dashboard partisipasi realtime dengan indikator koneksi |
| Reset menghapus semua data | Pemilihan baru per tahun, arsip tetap terpisah |
| NIM pendek otomatis ditambah nol | Validasi NPM sembilan digit; data ambigu diperbaiki, tidak ditebak |

PDF tidak cukup untuk menilai implementasi keamanan atau transaksi plugin lama. PRD ini menetapkan persyaratan yang harus dibuktikan pada aplikasi baru.

## 3. Tujuan dan ukuran keberhasilan

### 3.1 Tujuan

1. Mahasiswa memahami alur tanpa penjelasan panjang di dalam bilik.
2. Setiap pemilih yang memenuhi syarat dapat mengirim tepat satu surat suara dengan bobot resmi.
3. Pilihan individu tidak muncul di dashboard, laporan, URL, ataupun aktivitas panitia.
4. Panitia dapat mengelola registrasi, bilik, dan penutupan dengan langkah singkat.
5. Gangguan koneksi tidak menyebabkan suara ganda atau status sukses palsu.
6. Operasional tetap berada dalam batas layanan gratis pada skala yang ditetapkan.

### 3.2 Target penerimaan

| Indikator | Target / cara ukur |
| --- | --- |
| Keberhasilan tugas | Minimal 90% dari 5–10 peserta simulasi selesai tanpa bantuan di bilik |
| Kejelasan konsekuensi | Semua peserta simulasi memahami bahwa suara final setelah dikirim |
| Waktu memilih | Median maksimal 45 detik dari Mulai sampai sukses, di luar membaca detail opsional |
| Waktu kirim | p95 maksimal tiga detik pada koneksi venue yang sehat; kondisi gagal tetap jelas |
| Realtime | p95 perubahan partisipasi tampil maksimal dua detik setelah commit, pada koneksi sehat |
| Integritas | Tidak ada suara ganda dalam uji race, retry, refresh, dan gangguan koneksi |
| Rekonsiliasi | Jumlah surat suara, status sudah memilih, dan bobot cocok sebelum pengarsipan |
| Privasi antarpemilih | Sesi berikutnya tidak dapat menampilkan identitas atau pilihan pemilih sebelumnya |
| Kesesuaian layar | Alur utama muat tanpa scroll pada 1366×768 sampai 1920×1080; detail visi-misi boleh scroll |
| Biaya layanan | Rp0 dengan fitur dan kuota yang berlaku; penggunaan aktual dicatat saat simulasi |

**Perencanaan antrean:** jika satu pemilih memakai bilik rata-rata 60 detik, dua bilik melayani sekitar 120 orang/jam dan tiga bilik sekitar 180 orang/jam, sebelum jeda. Seribu pemilih membutuhkan sekitar 8,3 atau 5,6 jam. Durasi acara dan arus kedatangan harus cocok dengan kapasitas ini.

## 4. Pengguna dan hak akses

| Kemampuan | Mahasiswa di bilik | Operator | Admin | Publik |
| --- | --- | --- | --- | --- |
| Memilih | Hanya sesi sendiri | — | — | — |
| Mencari data mahasiswa | — | Untuk registrasi | Ya | — |
| Verifikasi hadir / penugasan bilik | — | Ya | Ya | — |
| Membatalkan sesi belum terkirim | Keluar dari sesi sendiri | Ya, tercatat | Ya, tercatat | — |
| Import pemilih / mengatur kandidat | — | — | Hanya sebelum pembukaan pertama | — |
| Mengatur akun dan perangkat bilik | — | — | Ya | — |
| Melihat partisipasi live | — | Ya | Ya | — |
| Membuka / menjeda / menutup | — | — | Ya | — |
| Memeriksa hasil setelah ditutup | — | — | Ya | — |
| Memublikasikan / mengekspor laporan | — | — | Ya | — |
| Melihat hasil yang dipublikasikan | Ya, di luar alur bilik | Ya | Ya | Ya |
| Melihat pilihan individu | — | — | — | — |

Administrator aplikasi berbeda dari pemilik akun Supabase/Vercel. Pemilik infrastruktur memiliki akses lebih kuat; batas jaminan kerahasiaan dijelaskan pada bagian keamanan.

## 5. Ruang lingkup MVP

### 5.1 Termasuk

- Pemilihan tahunan terpisah, dengan satu pemilihan resmi aktif pada satu waktu.
- Akun panitia individual dan pembatasan admin/operator.
- Import XLSX dengan pratinjau, validasi, dan laporan kesalahan per baris.
- Pengelolaan 2–4 kandidat: nomor urut, nama, foto, visi-misi ringkas.
- Registrasi menggunakan RFID atau NPM dan penandaan kehadiran terpisah dari sudah memilih.
- Penugasan sesi ke bilik, pairing perangkat sekali, dan pencabutan perangkat.
- Pengalaman bilik lengkap, konfirmasi suara, timeout, auto-reset, serta pemulihan gangguan.
- Dashboard agregat partisipasi realtime dan status bilik.
- Penutupan bertahap, rekap berbobot, pemeriksaan hasil, publikasi, dan layar proyektor.
- Export Excel hasil serta kehadiran/partisipasi.
- Audit tindakan administratif, arsip tahunan, backup manual, dan simulasi terpisah.

### 5.2 Di luar MVP

- Voting dari HP pribadi atau dari luar venue, voting offline, dan sinkronisasi suara offline.
- Pasangan calon, beberapa jabatan, abstain, kotak kosong, dan calon tunggal.
- Pembatalan atau perubahan surat suara yang sudah tersimpan.
- Sistem antrean bernomor, SMS/WhatsApp OTP, biometrik, dan aplikasi native.
- PDF berita acara, rincian partisipasi per kelas/angkatan, serta perolehan live kandidat.
- Integrasi otomatis database akademik/Google Sheets atau pembacaan kartu lewat SDK khusus.
- Database master mahasiswa lintas tahun dan mekanisme pemilihan ulang akibat seri.

## 6. Alur pengguna

### 6.1 Persiapan admin

1. Login dan buat pemilihan: nama, periode, deskripsi singkat, dan identitas acara.
2. Unduh template lalu impor daftar pemilih. Perbaiki kesalahan dan konfirmasi pratinjau.
3. Impor pemetaan RFID berizin; cocokkan ke NPM. Pemilih tanpa pemetaan tetap dapat memakai jalur NPM terverifikasi.
4. Siapkan kandidat; ukuran dan kualitas foto, batas isi, serta nomor urut diperlakukan setara.
5. Buat akun operator dan daftarkan bilik melalui kode pairing sekali pakai.
6. Jalankan pemeriksaan reader, jaringan, fullscreen, audio, dan data.
7. Latihan menggunakan data dummy di lingkungan simulasi.
8. Saat siap, admin membuka pemilihan. Daftar pemilih, bobot, dan kandidat terkunci.

### 6.2 Registrasi dan penugasan bilik

1. Mahasiswa menunjukkan identitas kepada operator.
2. Operator tap kartu atau mencari NPM sembilan digit.
3. Sistem menunjukkan nama, NPM, kelayakan, status hadir, status memilih, dan sesi yang masih aktif.
4. Operator mencocokkan identitas fisik. Kepemilikan kode RFID/NPM sendiri tidak dianggap cukup.
5. Operator menandai hadir. Scan ulang tidak menambah angka hadir.
6. Operator memilih bilik kosong. Sistem membuat satu sesi yang terikat pada mahasiswa dan bilik tersebut.
7. Jika semua bilik sibuk, mahasiswa menunggu. Sudah hadir belum berarti sedang memilih atau sudah memilih.
8. Sistem menolak penugasan ganda untuk mahasiswa maupun bilik yang sama.

Pembatalan penugasan tidak menghapus kehadiran. Jika perlu pindah bilik, operator mengakhiri sesi lama secara atomik sebelum membuat sesi baru. Sesi dengan status pengiriman belum pasti harus direkonsiliasi terlebih dahulu.

### 6.3 Pemilih di bilik

| Tahap | Isi dan tindakan utama |
| --- | --- |
| Welcome | Identitas acara, satu ilustrasi 3D, tombol **Mulai**, kontrol suara yang tidak dominan |
| Verifikasi | **Tempelkan kartu atau masukkan NPM**; input manual selalu tersedia |
| Cocokkan identitas | Nama lengkap dan NPM lengkap; **Lanjutkan** atau **Bukan saya** |
| Pilih | 2–4 kandidat dengan foto, nomor, dan nama; detail visi-misi opsional |
| Konfirmasi | Kandidat terpilih ditampilkan besar; **Ganti pilihan** dan **Kirim suara** |
| Mengirim | Tindakan kirim dikunci; status jelas; belum boleh menampilkan sukses |
| Sukses | **Suara tersimpan. Terima kasih!**; tombol **Selesai** dan hitung mundur lima detik |
| Reset | Bersihkan seluruh data pemilih dan pilihan, lalu kembali ke welcome |

Aturan alur:

- Klik kartu kandidat hanya memilih sementara; tidak langsung mengirim suara.
- Kartu/NPM harus cocok dengan sesi yang ditugaskan pada bilik itu.
- Jika belum ada penugasan, tampilkan instruksi menghubungi panitia tanpa membuka daftar mahasiswa.
- **Bukan saya** menghapus tampilan identitas dan menghentikan sesi tersebut untuk diperiksa operator.
- Identitas lengkap hanya tampil di tahap pencocokan; tidak terus-menerus menjadi header di layar kandidat.
- Tidak ada kandidat yang otomatis dipilih atau mendapat penekanan visual lebih besar.
- Nomor urut mengikuti nomor resmi yang ditetapkan sebelum pembukaan.
- Scan tambahan dan tombol Enter dari reader tidak boleh mengirim surat suara.
- Sukses tidak menampilkan kembali kandidat terpilih atau bukti pilihan yang dapat dibawa keluar.

### 6.4 Sesi tidak aktif dan pemulihan

- Setelah 90 detik tanpa aktivitas, tampilkan peringatan selama 15 detik dengan tombol **Lanjutkan**.
- Jika dijawab, waktu tidak aktif dimulai ulang. Peringatan dapat diperpanjang lagi bila diperlukan.
- Jika tidak dijawab, batalkan sesi tanpa mencatat suara, hapus tampilan, dan beri tahu operator. Mahasiswa dapat ditugaskan ulang.
- Masa berlaku penugasan sebelum mahasiswa menekan Mulai: usulan lima menit; operator dapat menerbitkan ulang setelah memeriksa status.
- Timeout normal tidak boleh menghapus proses yang sedang menunggu kepastian commit.
- Saat refresh, sistem memeriksa sesi di server. Suara yang sudah diterima menghasilkan status sukses generik; sesi belum mengirim meminta pemilihan ulang tanpa mengambil pilihan dari penyimpanan browser.
- Browser Back, cache halaman, dan pemulihan tab tidak boleh menghidupkan kembali identitas/konfirmasi sesi lama.

### 6.5 Gangguan jaringan saat mengirim

```text
Kirim → server melakukan transaksi
  ├─ commit terkonfirmasi → tampil sukses → reset
  ├─ ditolak / rollback terkonfirmasi → tampil alasan → pulihkan langkah yang sesuai
  └─ jawaban tidak diterima → status belum pasti → cek hasil sesi yang sama
       ├─ sudah commit → sukses, tanpa mengirim surat suara baru
       └─ belum commit → retry idempoten atau batalkan secara atomik sebelum penugasan ulang
```

Tidak boleh menampilkan “gagal” hanya karena request timeout: server mungkin sudah menyimpan suara. Bilik dengan status belum pasti tetap tidak tersedia untuk pemilih berikutnya. Setelah jaringan pulih, operator menyelesaikan pemeriksaan sebelum melanjutkan.

### 6.6 Penutupan dan pengumuman

1. Admin menekan **Tutup pemilihan** dan melihat jumlah sesi yang belum selesai.
2. Sistem masuk fase penyelesaian: tidak ada penugasan baru, termasuk dari operator lain.
3. Sesi yang sudah dibuat sebelum fase ini dapat selesai dalam maksimal dua menit sejak penutupan dimulai.
4. Saat semua sesi selesai atau batas server tercapai, sesi belum mengirim dibatalkan dan sistem melakukan penutupan final.
5. Server merekonsiliasi jumlah surat suara, status sudah memilih, bobot, dan angka dashboard.
6. Admin melihat rekap hasil. Total bobot tertinggi menjadi dasar penetapan; jika seri, tampilkan **Hasil seri**, tanpa memilih pemenang otomatis.
7. Admin memublikasikan hasil setelah pemeriksaan. Halaman publik dan proyektor menggunakan data agregat yang sama.
8. Admin mengunduh laporan dan membuat backup akhir.

## 7. Status dan aturan bisnis

### 7.1 Status pemilihan

| Status | Perilaku |
| --- | --- |
| `draft` | Pemilih, kandidat, dan konfigurasi dapat disiapkan; tidak menerima suara |
| `open` | Registrasi, penugasan, verifikasi, dan pengiriman aktif |
| `paused` | Penugasan dan commit baru ditolak; suara yang sudah commit tetap sah |
| `closing` | Tidak ada sesi baru; sesi terdahulu dapat mengirim sampai tenggat server |
| `closed` | Tidak menerima suara; hasil dapat diperiksa dan dipublikasikan admin |
| `archived` | Pemilihan baca-saja; digunakan untuk arsip tahunan |

Alur: `draft → open ↔ paused → closing → closed → archived`. Penutupan juga dapat dimulai dari `open`. Publikasi memakai `published_at`, terpisah dari status pemilihan. Arsip yang sudah dipublikasikan tetap dapat dibaca publik.

- Setelah pembukaan pertama, tidak kembali ke draft dan tidak membuka kembali hasil yang sudah final.
- Jeda tidak membuka hak mengubah kandidat atau bobot. Sesi belum terkirim tetap tunduk pada timeout; pesan jeda jelas terlihat.
- Batas waktu ditegakkan pada setiap pengiriman memakai waktu database/server, walaupun dashboard admin terputus.
- Finalisasi dapat dipicu request aplikasi/admin setelah tenggat; penerimaan suara tidak bergantung pada cron atau tab browser yang tetap hidup.
- Admin tidak bisa memublikasikan hasil selama masih `open`, `paused`, atau `closing`.

### 7.2 Aturan suara

1. Seorang mahasiswa mempunyai satu hak memilih per pemilihan, dikenali melalui pasangan `election_id + npm`.
2. Pengurus memperoleh bobot 3 dan mahasiswa biasa bobot 1; nilai ditentukan dari data resmi sebelum pembukaan.
3. Browser tidak menentukan bobot, status pengurus, kelayakan, atau apakah seseorang sudah memilih.
4. Suara valid harus memilih tepat satu kandidat aktif dalam pemilihan yang sama.
5. Minimal dua dan maksimal empat kandidat untuk MVP; pembukaan di luar batas ditolak.
6. Suara yang sudah tersimpan final. Tidak ada endpoint admin untuk mengubah, membatalkan, atau mengatur ulang hak suara tersebut.
7. Check-in dan pemilihan adalah kejadian berbeda. Scan kartu atau penugasan bilik tidak dihitung sebagai suara.
8. Tidak ada kuorum atau ambang mayoritas tambahan dalam baseline yang dipilih pengguna.
9. Aturan pemilih susulan dan penyelesaian seri belum disahkan. Sampai diputuskan, aplikasi tidak menyediakan perubahan daftar setelah dibuka dan tidak membuat putaran baru otomatis.

### 7.3 Rumus rekap

- `terdaftar = jumlah pemilih dalam daftar resmi yang dikunci`.
- `hadir = jumlah pemilih unik yang telah diverifikasi hadir`.
- `sudah_memilih = jumlah pemilih unik dengan commit berhasil`.
- `belum_memilih = terdaftar - sudah_memilih`.
- `hadir_belum_memilih = hadir - sudah_memilih`.
- `partisipasi = sudah_memilih / terdaftar × 100%`.
- `jumlah_orang_kandidat = jumlah surat suara kandidat tersebut`.
- `bobot_kandidat = jumlah bobot surat suara kandidat tersebut`.
- `persentase_hasil = bobot_kandidat / total_bobot_terkirim × 100%`.

Persentase pembagian nol ditampilkan sebagai 0%. Pembulatan hanya untuk tampilan, bukan penentuan urutan atau pemenang. Hasil nol suara tidak menghasilkan pemenang. Laporan membedakan jumlah orang dan bobot secara eksplisit.

## 8. Persyaratan fungsional

| ID | Persyaratan | Kriteria penerimaan utama |
| --- | --- | --- |
| FR-01 | Akun panitia | Akun individual, signup publik mati, admin/operator diverifikasi server pada setiap tindakan |
| FR-02 | Pemilihan tahunan | Pemilihan baru tidak mengubah hasil atau status pemilih tahun sebelumnya |
| FR-03 | Import XLSX | Template, pratinjau, kesalahan per baris, dan konfirmasi sebelum perubahan; input tidak valid tidak diimpor sebagian diam-diam |
| FR-04 | Validasi pemilih | NPM tepat sembilan digit, unik per pemilihan, nama wajib, `is_pengurus` eksplisit, RFID tidak boleh terhubung ke dua orang |
| FR-05 | Kandidat | Dua sampai empat kandidat lengkap, nomor unik, foto teroptimasi, visi-misi teks biasa; terkunci setelah dibuka |
| FR-06 | Registrasi | Scan/input menemukan pemilih; check-in idempoten; status hadir dan sudah memilih terpisah |
| FR-07 | Penugasan | Satu sesi belum dilepas per bilik dan per pemilih; pemindahan membatalkan sesi lama secara aman |
| FR-08 | Perangkat bilik | Pairing sekali, credential bisa dicabut; URL yang dibuka di perangkat lain tidak mendapat hak memilih |
| FR-09 | Verifikasi bilik | Kartu/NPM harus cocok dengan penugasan; mahasiswa tidak membuat akun atau memasukkan OTP |
| FR-10 | Memilih dan konfirmasi | Pilihan dapat diganti sebelum kirim; konfirmasi menampilkan kandidat dan konsekuensi final |
| FR-11 | Penyimpanan suara | Satu transaksi database untuk suara, penggunaan hak, penyelesaian sesi, dan pembaruan ringkasan |
| FR-12 | Retry dan pemulihan | Request berulang, respons hilang, dan refresh tidak menambah surat suara; status diperiksa dari server |
| FR-13 | Timeout dan reset | 90 + 15 detik untuk sesi diam; sukses lima detik; pilihan dan identitas tidak tersisa pada sesi baru |
| FR-14 | Dashboard live | Kehadiran dan partisipasi agregat, status bilik, koneksi dan waktu pembaruan; tidak ada perolehan kandidat saat voting |
| FR-15 | Kendali acara | Open/pause/resume/closing/closed memiliki pemeriksaan server dan audit; grace period maksimal dua menit |
| FR-16 | Hasil dan publikasi | Hasil hanya setelah closed; publik baru setelah tindakan admin; hasil seri dan nol suara ditangani eksplisit |
| FR-17 | Export Excel | Rekap hasil dan daftar kehadiran/partisipasi terpisah; tidak ada pasangan pemilih–kandidat |
| FR-18 | Presentasi hasil | Halaman fullscreen dari snapshot hasil yang telah dipublikasikan, tanpa data pribadi |
| FR-19 | Audit dan arsip | Tindakan administratif tercatat; pemilihan resmi tidak mempunyai tombol reset/hapus semua suara |
| FR-20 | Simulasi | Data dummy dan label simulasi jelas; credential dan database produksi tidak dipakai oleh preview/simulasi |

### 8.1 Kontrak import yang disarankan

Template utama satu worksheet: `npm`, `nama`, `is_pengurus`, `rfid_uid`.

- `npm`: teks sembilan digit. Nilai numerik yang telah kehilangan nol di depan ditandai untuk koreksi; tidak ditambah nol otomatis.
- `nama`: teks wajib, usulan maksimal 120 karakter.
- `is_pengurus`: wajib 0 atau 1; nilai kosong/ambigu ditolak untuk menghindari salah bobot.
- `rfid_uid`: opsional sampai izin data tersedia; format mengikuti uji reader, bukan asumsi format NPM.
- Pemetaan terpisah dapat diimpor dengan worksheet `npm`, `rfid_uid` untuk melengkapi daftar yang sama sebelum pembukaan.
- NPM ganda, UID ganda, kolom tidak lengkap, formula, dan baris tidak valid dilaporkan sebelum commit import.
- Usulan batas: 2 MB, maksimal 2.000 baris, satu worksheet yang dikenali; tolak XLSM/makro, formula, serta arsip terkompresi yang melampaui batas hasil ekstraksi.
- Import ulang pada draft menampilkan daftar tambah/perbarui dan tidak menghapus baris lain diam-diam.
- File mentah tidak disimpan permanen. Ekspor menulis NPM sebagai teks dan menetralkan nilai yang dapat dieksekusi sebagai formula spreadsheet.

### 8.2 Pengelolaan akun

- Admin membuat akun operator dengan email dan password sementara melalui server; password wajib diganti sebelum operasi.
- Password diproses Supabase Auth, bukan disimpan dalam tabel aplikasi atau dapat dibaca kembali oleh admin.
- Kebijakan usulan: password minimal 12 karakter, dukungan password manager/paste, rate limiting, dan pesan login yang tidak mengungkap keberadaan email.
- Pengiriman email bukan dependensi jalur kritis. Pemulihan operator dapat dilakukan admin setelah memeriksa identitas, dengan pencatatan audit dan pencabutan sesi terkait.
- Pemulihan admin utama mengikuti runbook pemilik infrastruktur. Rahasia tidak dicantumkan dalam dokumen serah terima biasa.
- Logout dan penonaktifan akun menutup akses aplikasi; pemeriksaan akun aktif tidak hanya mengandalkan role dalam UI atau metadata yang bisa diubah pengguna.

## 9. UX, UI, motion, dan audio

### 9.1 Prinsip desain

1. Satu tujuan dan satu tindakan utama per layar bilik.
2. Teks hanya untuk orientasi, tindakan, dan konsekuensi penting.
3. Tampilan terang, ruang lega, tipografi jelas, dan komponen konsisten.
4. Warna merujuk situs resmi Bisnis Digital Unpak; layout dan gaya visual mengikuti rancangan modern serta referensi Pinterest pengguna.
5. Semua kandidat mendapat ukuran kartu, foto, efek suara, dan perlakuan visual setara.
6. Kegagalan selalu memiliki tindakan pemulihan yang bisa dipahami mahasiswa atau operator.

### 9.2 Warna dan aset

- URL acuan: https://bisnisdigital.unpak.ac.id/.
- Akses pengambilan halaman pada tahap PRD menghasilkan HTTP 403; kode warna HEX belum diverifikasi.
- Setelah screenshot atau akses tersedia, turunkan token `brand-primary`, `brand-accent`, `background`, `surface`, `text`, `border`, serta warna status.
- Warna status tetap perlu memenuhi kontras dan semantik; tidak semua teks/tombol harus memakai warna merek.
- Ikon memakai koleksi awal/V1 3dicons.co berlisensi CC0. Koleksi Pro atau aset lain tidak otomatis dianggap gratis.
- Gunakan render WebP/AVIF/PNG yang dioptimasi, disajikan dari deployment sendiri. Animasi melalui transform CSS; tidak membutuhkan scene 3D/WebGL.
- Utamakan ikon 3D untuk welcome, verifikasi, menunggu, dan sukses. Kartu kandidat tetap mengutamakan foto manusia.
- Foto yang ditemukan di `asset himabisdi/` perlu dikonfirmasi periode, identitas, dan kelayakan penggunaannya sebelum menjadi konten produksi.
- Satu keluarga font berlisensi gratis, disajikan lokal. Referensi Pinterest dan logo resmi masih menunggu pengguna.

### 9.3 Keterbacaan dan aksesibilitas

- Layar bilik: 1366×768 hingga Full HD, mouse/keyboard/touchscreen, Chrome/Edge stabil pada perangkat acara.
- Dashboard: semua fungsi responsif; formulir dan tabel tetap dapat digunakan pada layar HP.
- Target tombol bilik minimal 48×48 px, tindakan utama lebih besar; teks utama usulan 18–24 px.
- Kontras mengikuti WCAG AA; status tidak dibedakan hanya melalui warna atau suara.
- Kontrol mempunyai label, fokus terlihat, urutan Tab masuk akal, dan dukungan keyboard.
- Detail visi-misi terbuka secara sengaja, dapat ditutup, dan fokus kembali ke pemicu.
- Timer memberikan peringatan dan perpanjangan. Mode reduced motion dihormati.
- Fullscreen dapat dimulai oleh petugas atau gesture pengguna; website tidak mengasumsikan dapat memaksa fullscreen tanpa izin browser.

### 9.4 Motion

- Setiap tombol memiliki state idle, hover bila relevan, focus, pressed, disabled, dan loading.
- Usulan pressed: skala 0,97–0,99 dan penyesuaian bayangan; durasi 100–160 ms.
- Usulan perpindahan layar: opacity/transform 180–250 ms, tanpa menggeser sasaran klik secara mendadak.
- Animasi tidak menunda request atau menjadi syarat penyimpanan suara.
- Efek sukses singkat dan netral; tidak menyebut atau memberi warna berbeda menurut kandidat.
- Gunakan `transform` dan `opacity`; reduced motion tetap memberi feedback warna/fokus.

### 9.5 Audio melalui Howler.js

- Klik pendek dan lembut, verifikasi berhasil, peringatan, dan sukses; tanpa musik latar atau suara hover berulang.
- Audio semua kandidat sama. Tidak ada pengucapan nama, nomor kandidat, atau bobot suara.
- Tombol Mulai menjadi gesture awal untuk membuka audio browser. Reload dapat mengharuskan gesture baru.
- Jika autoplay diblokir, aset gagal dimuat, atau speaker mati, seluruh alur tetap dapat diselesaikan melalui feedback visual.
- Volume default usulan 20%; tersedia mute yang mudah ditemukan dan pengaturan volume saat setup bilik.
- Preferensi suara dapat disimpan per perangkat; identitas dan pilihan pemilih tidak boleh ikut disimpan.
- Bunyi sukses hanya diputar setelah server mengonfirmasi commit. Jangan menumpuk bunyi akibat klik ganda.
- Howler.js adalah mesin pemutaran, bukan paket file SFX. Pilih audio CC0 atau audio orisinal dengan izin jelas, simpan lisensi asal, dan host file lokal.

### 9.6 Microcopy awal

| Situasi | Teks | Tindakan |
| --- | --- | --- |
| Welcome | “Pemilihan ketua HIMABISDI” | Mulai |
| Identifikasi | “Tempelkan kartu atau masukkan NPM” | Lanjutkan |
| NPM kurang/lebih digit | “NPM harus 9 digit.” | Perbaiki input |
| Sesi tidak cocok | “Sesi belum sesuai. Hubungi panitia.” | Kembali |
| Konfirmasi identitas | “Ini data kamu?” | Lanjutkan / Bukan saya |
| Memilih | “Pilih calon ketua” | Lihat visi-misi / Lanjutkan |
| Konfirmasi suara | “Pilihanmu sudah tepat?” + “Setelah dikirim, suara tidak bisa diubah.” | Ganti pilihan / Kirim suara |
| Menyimpan | “Menyimpan suara…” | Tidak ada kirim ulang manual |
| Hasil request belum pasti | “Sedang memastikan suara tersimpan. Tunggu sebentar.” | Hubungi panitia jika berkepanjangan |
| Sukses | “Suara tersimpan. Terima kasih!” | Selesai |
| Timeout segera | “Masih ingin memilih?” | Lanjutkan |
| Jeda | “Pemilihan dijeda. Tunggu arahan panitia.” | — |
| Sudah memilih | “Suara kamu sudah tercatat.” | Kembali |
| Belum ada sesi | “Hubungi panitia untuk mulai memilih.” | Kembali |

Pesan internal teknis tidak ditampilkan mentah. Operator dapat memperoleh kode insiden yang tidak memuat NPM atau kandidat.

## 10. Rekomendasi teknologi

| Lapisan | Pilihan | Alasan |
| --- | --- | --- |
| Framework | Next.js App Router + React + TypeScript | UI dan endpoint server dalam satu project/deployment; kontrak data bertipe |
| Styling | Tailwind CSS + CSS variables | Token konsisten dan CSS hasil build; mudah membentuk tampilan ringan |
| Motion | CSS transitions/keyframes | Cukup untuk tactile buttons, perpindahan layar, dan ikon dekoratif |
| Audio | Howler.js | Pengelolaan efek, volume, mute, dan kompatibilitas browser |
| Database | Supabase PostgreSQL Free | Relasi, constraint, transaksi, fungsi SQL, dan RLS |
| Akun panitia | Supabase Auth + `@supabase/ssr` | Email/password, session refresh, dan integrasi server |
| Akses data | `@supabase/supabase-js` + fungsi SQL terkontrol | Sedikit lapisan, tipe database dapat dihasilkan dari schema |
| Validasi input | Zod | Validasi runtime untuk request dan import, selain tipe TypeScript |
| Realtime | Supabase Realtime | Pembaruan ringkasan partisipasi untuk akun panitia |
| XLSX | ExcelJS di server | Import/export Excel tanpa membebani bundle bilik |
| Foto | Supabase Storage, dibatasi pada aset kandidat | Admin dapat mengubah konten sebelum pemilihan dibuka |
| Hosting | Vercel Hobby, subdomain vercel.app | Deployment sederhana dengan batas dan kelayakan free tier yang perlu dipenuhi |

Gunakan rilis stabil yang masih didukung dan telah menerima patch keamanan pada saat implementasi. Kunci versi melalui lockfile. Route bilik tidak memuat library spreadsheet, chart berat, atau modul admin. Server memakai runtime Node.js yang didukung Vercel dan dependensi terpilih.

### 10.1 Arsitektur

```text
Browser bilik ── cookie perangkat/sesi ──┐
                                       ├─ Next.js Route Handlers / DAL server-only
Dashboard panitia ── Supabase Auth ──────┘        │
                                                ├─ fungsi transaksi PostgreSQL
                                                ├─ data operasional berizin
                                                └─ Supabase Storage

Dashboard panitia ── JWT + RLS ── Supabase Realtime: ringkasan partisipasi saja
Halaman publik ── endpoint hasil terpublikasi ── agregat tanpa identitas
```

- Semua mutasi penting melewati pemeriksaan otorisasi server dan aturan database.
- Database menjadi sumber kebenaran; state React, cookie, dan pesan realtime bukan bukti bahwa suara diterima.
- Credentials privileged Supabase hanya tersedia dalam modul server-only/environment server.
- Koneksi data memakai API/RPC Supabase sehingga setiap instance serverless tidak membuka pool Postgres sendiri.
- Asset dekoratif dapat dicache lama. Identitas, sesi, dashboard privat, dan respons autentikasi memakai kebijakan cache privat/no-store yang benar.

### 10.2 Realtime yang tidak membocorkan pilihan

- Simpan counter partisipasi dan versi ringkasan pada baris `elections`; tidak ada perolehan kandidat dalam baris tersebut.
- Snapshot pada baris ini juga hanya berisi partisipasi. Hasil kandidat dibaca melalui jalur terpisah yang memeriksa status closed dan hak admin/publikasi.
- Perubahan counter dilakukan dalam transaksi yang sama dengan check-in atau commit suara.
- Hanya tabel ringkasan aman tersebut masuk publikasi realtime untuk dashboard; RLS memeriksa akun panitia aktif.
- Tabel surat suara, pemilih, sesi, dan credential bilik tidak disiarkan melalui realtime.
- Saat tersambung, ambil snapshot awal; saat event diterima, gunakan nilai absolut/versi, bukan menambah angka secara optimistis.
- Saat reconnect, ambil snapshot baru agar event yang terlewat tidak membuat rekap salah.
- Fallback polling ringkasan usulan lima detik, hanya selama dashboard aktif, dengan backoff saat gagal dan indikator data belum mutakhir.
- Status bilik dapat memakai endpoint DTO yang aman dengan polling ringan; jangan menyiarkan seluruh baris yang berisi credential atau identitas.

## 11. Rancangan database minimal

Rekomendasi: **delapan tabel domain**, satu tabel pendukung pembatasan request berumur pendek, dan tabel Auth bawaan Supabase. Tidak ada tabel akun untuk setiap mahasiswa.

| Tabel | Kolom / isi utama | Tujuan |
| --- | --- | --- |
| `elections` | ID, slug, nama, periode, status, waktu buka/tutup, tenggat closing, published_at, counter/snapshot partisipasi | Konfigurasi, siklus acara, dan sumber ringkasan realtime |
| `candidates` | ID, election_id, nomor, nama, foto, visi, misi | Konten kandidat; tidak menyimpan counter hasil live untuk dibaca dashboard |
| `voters` | ID, election_id, npm, nama, weight, rfid_lookup, checked_in_at, has_voted | Kelayakan dan partisipasi tanpa pilihan kandidat |
| `booths` | ID, election_id, nama bilik, hash credential/pairing, kedaluwarsa, dicabut, last_seen | Perangkat yang diizinkan; field rahasia tidak masuk DTO |
| `voting_sessions` | ID acak, election_id, voter_id, booth_id, state, expiry, operation token hash, released_at | Penugasan, otorisasi satu kali, idempotensi, dan pemulihan; tanpa kandidat/ballot_id |
| `ballots` | UUID acak, election_id, candidate_id, weight | Surat suara tanpa voter_id, NPM, RFID, session_id, booth_id, atau waktu pengiriman presisi |
| `staff_profiles` | auth_user_id, nama, role, is_active, must_change_password | Hak admin/operator, pergantian password awal, dan penonaktifan akun |
| `audit_events` | ID, actor, action, target, waktu, alasan/metadata terbatas | Riwayat administratif dan insiden; tidak menyimpan pasangan identitas–pilihan |
| `rate_limits` | bucket hash, window, counter, expires_at | Batas request bersama lintas instance serverless; dibersihkan berkala |

### 11.1 Constraint dan invariants

- `UNIQUE(election_id, npm)`; NPM diperlakukan sebagai string yang cocok pola sembilan digit.
- UID yang tersedia unik dalam satu pemilihan; normalisasi UID dibakukan dari perangkat asli.
- `UNIQUE(election_id, candidate_number)` dan `CHECK(weight IN (1, 3))`.
- Relasi surat suara memastikan kandidat berasal dari pemilihan yang sama, misalnya composite foreign key.
- Unique partial constraint memastikan hanya satu sesi yang belum dilepas untuk pemilih dan bilik yang sama.
- Sesi committed tetap menahan bilik sampai reset/rekonsiliasi selesai; ketidakpastian jaringan tidak otomatis membuat bilik kosong.
- Tidak ada foreign key atau token penghubung dari surat suara ke identitas/sesi.
- Jumlah `ballots` harus sama dengan jumlah pemilih `has_voted=true` sebelum pembersihan identitas.
- Jumlah bobot `ballots` harus sama dengan jumlah bobot pemilih yang telah memilih, dan cocok dengan ringkasan.
- Sesudah pembersihan identitas, audit agregat memakai snapshot resmi yang dibekukan, bukan menghitung ulang denominator dari tabel pemilih yang sudah dihapus.
- Hindari menyimpan waktu commit individu yang presisi pada data partisipasi; timestamp operasional yang memang diperlukan tetap dibatasi akses dan retensinya.

### 11.2 Transaksi pengiriman suara

Satu fungsi transaksi database menjalankan seluruh langkah berikut:

1. Memastikan credential bilik, sesi, operasi, dan otorisasi request valid.
2. Mengunci baris yang relevan dengan urutan konsisten: pemilihan, sesi, pemilih, dan bilik.
3. Jika operasi yang sama sudah commit, mengembalikan status diterima kepada pemanggil yang berwenang, tanpa menambah surat suara atau mengungkap kandidat. Pemeriksaan ini tetap tersedia setelah batas voting berakhir.
4. Untuk commit baru, memeriksa status pemilihan, tenggat server, sesi belum dibatalkan/kedaluwarsa, dan kecocokan penugasan.
5. Memeriksa pemilih sudah hadir, belum memilih, dan kandidat valid untuk pemilihan ini.
6. Mengambil bobot dari daftar pemilih yang dikunci; mengabaikan bobot atau identitas pemilih kiriman browser.
7. Menulis surat suara tanpa kaitan identitas, menandai hak telah digunakan, menyelesaikan sesi, dan memperbarui counter.
8. Commit seluruhnya atau rollback seluruhnya.

Idempotency key merupakan token acak untuk operasi sesi, bukan NPM atau hash kandidat. Pengiriman ulang dengan kandidat berbeda tidak boleh mengubah suara yang sudah commit. Pencegahan suara ganda tidak boleh hanya bergantung pada tombol disabled atau urutan request JavaScript.

Pembatalan/pemindahan sesi memakai aturan penguncian yang sama. Jika operator membatalkan sesi sebelum commit, request lama yang datang terlambat ditolak; jika commit lebih dahulu, hak memilih tidak dapat diterbitkan ulang.

### 11.3 Retensi dan arsip

- Pertahankan identitas sampai proses persiapan pemilihan tahun berikutnya; tetapkan tanggal pembersihan saat jadwal tahun berikutnya disahkan.
- Tahun berikutnya membuat daftar baru, bukan membuka ulang hak suara pada pemilihan lama.
- Pembersihan identitas mencakup pemetaan kartu, sesi terkait, file export/backup yang masih mengandung identitas, dan metadata audit yang dapat mengidentifikasi pemilih.
- Hasil agregat, metadata acara, kandidat, serta surat suara tanpa kaitan identitas tetap dapat diarsipkan sesuai kebijakan organisasi.
- Jangan menghapus data yang masih berada dalam masa sengketa aktif; kebutuhan perpanjangan retensi harus dicatat.
- File laporan yang sudah diunduh mengikuti pengendalian akses dan retensi yang sama; menghapus database tidak otomatis menghapus salinan lokal.

## 12. Keamanan dan kerahasiaan

Tujuannya mengurangi risiko penyamaran, manipulasi, akses ilegal, dan kegagalan integritas melalui kontrol yang dapat diuji. Tidak ada klaim “100% tidak bisa diretas”.

### 12.1 Kontrol wajib

| Risiko | Kontrol yang harus dibangun |
| --- | --- |
| Orang mengetahui NPM mahasiswa lain | Verifikasi fisik di registrasi, sesi per bilik, pencocokan NPM/RFID, satu sesi aktif |
| UID kartu ditiru / input reader dipalsukan | UID diperlakukan sebagai identifier; kewenangan berasal dari pemeriksaan operator dan penugasan server |
| Akses dari perangkat luar | Credential bilik acak, pairing sekali pakai, masa berlaku, pencabutan, dan validasi pada setiap endpoint bilik |
| Klik ganda / request paralel / replay | Transaksi, row lock, idempotensi, constraint sesi, dan pemeriksaan hak memilih di database |
| Bobot atau kandidat dimanipulasi | Nilai bobot server-side; validasi kandidat dan pemilihan di dalam transaksi |
| Operator mencoba aksi admin | Pemeriksaan role/akun aktif pada setiap endpoint dan fungsi yang relevan, bukan hanya menyembunyikan tombol |
| Membaca database lewat API publik | Grant minimum dan RLS; surat suara/PII tidak dapat dibaca oleh anon atau akun tanpa hak |
| Fungsi SQL menjadi jalan pintas | Cabut EXECUTE default PUBLIC/anon; grant eksplisit; SECURITY DEFINER bila diperlukan memakai search_path aman dan pemeriksaan hak |
| Kunci backend bocor | Environment server-only, tidak memakai prefix publik, tidak masuk log, repository, atau bundle browser |
| XSS / SQL injection / unggahan berbahaya | Validasi runtime, parameter/RPC terstruktur, output escaping, teks biasa untuk visi-misi, validasi tipe/ukuran gambar dan XLSX |
| CSRF / clickjacking | Mutasi POST, pemeriksaan Origin yang ketat dan proteksi CSRF, SameSite, CSP frame-ancestors, serta header yang sesuai |
| Serangan brute force / spam | Rate limiting bersama di database, pembatasan akun/perangkat/endpoint dan IP secara proporsional; manfaatkan proteksi Vercel yang termasuk paket |
| Kebocoran lewat cache / tab sebelumnya | No-store data privat, pembersihan state, pemeriksaan sesi saat restore/back, tanpa PII di URL atau analytics |
| Perolehan kandidat diketahui saat berjalan | Tidak ada subscription/endpoint hasil sebelum closed, termasuk melalui export dan halaman proyektor |

Ketentuan tambahan:

- Secret/service-role key dapat melewati RLS. Semua jalur server privileged harus memvalidasi hak sendiri; keberadaan RLS saja tidak cukup.
- Surat suara mentah tidak dapat dibaca langsung oleh akun aplikasi, termasuk admin. Endpoint admin mengembalikan agregat setelah penutupan; akses pemilik infrastruktur merupakan batas kepercayaan yang berbeda.
- Admin/operator memakai Supabase Auth resmi; identitas token diverifikasi di server. Data sesi yang sekadar dibaca dari cookie bukan bukti autentikasi.
- Supabase SSR memakai pola cookie/client resminya untuk auth dan realtime. Credential khusus perangkat/sesi bilik memakai cookie HttpOnly, Secure pada HTTPS produksi, dan SameSite yang sesuai.
- Credential bilik disimpan sebagai hash, dapat dicabut setelah acara, dan tidak memakai password admin yang dibagikan.
- Pairing usulan: kode acak sekali pakai berlaku lima menit, dengan batas percobaan. Tidak perlu login panitia di tiap pergantian mahasiswa.
- Gunakan HMAC ber-secret server untuk lookup UID jika menyimpan representasi terlindungi; jangan menganggap hash polos UID berentropi rendah sebagai anonimisasi. Format UID diverifikasi pada reader asli.
- Pembatasan request tidak hanya menggunakan memori instance serverless; hindari satu batas IP yang membuat semua bilik di Wi-Fi venue terblokir sekaligus.
- Body pengiriman suara, token, UID mentah, dan hubungan pemilih–kandidat tidak dicatat dalam telemetry/error log.
- Audit administratif meliputi import, perubahan konfigurasi, pairing/revoke, penugasan/pembatalan, buka/jeda/tutup, publikasi, export, dan pengelolaan akun. Audit tidak menyimpan pilihan individu.
- Audit append-only bagi peran aplikasi. Jangan menyebutnya kebal manipulasi terhadap pemilik database.
- Fullscreen memperjelas UX; penguncian OS/browser kiosk oleh petugas diperlukan bila ingin membatasi akses desktop fisik.

### 12.2 Batas kerahasiaan yang harus dipahami

- Admin dan operator aplikasi tidak memperoleh daftar “mahasiswa A memilih calon B”.
- Pemisahan surat suara, tanpa timestamp presisi dan tanpa siaran vote, mengurangi kemungkinan menghubungkan identitas dengan pilihan.
- Ini tidak memberikan anonimitas kriptografis terhadap pemilik database, server, WAL/log infrastruktur, perangkat yang terinfeksi, atau pengamat fisik. Transaksi dan waktu operasi masih dapat dikorelasikan oleh pihak dengan akses cukup kuat.
- Pembobotan 3:1 bersama publikasi jumlah orang dan total bobot memungkinkan komposisi kelompok suara disimpulkan secara agregat. Karena itu tidak disediakan rincian pilihan per kelas/angkatan/kelompok kecil.
- Bukti yang dibawa mahasiswa hanya status sukses di layar; tidak menerbitkan receipt yang membuktikan kandidat pilihannya.

## 13. Biaya, kapasitas, dan batas layanan gratis

Pemeriksaan sumber resmi dilakukan pada 4 November 2026. Batas dan ketentuan harus diperiksa kembali sebelum deployment acara.

| Komponen | Batas/fakta yang relevan | Rencana |
| --- | --- | --- |
| Vercel Hobby | Penggunaan personal nonkomersial; 1 juta function invocations dan 100 GB fast data transfer per bulan pada dokumentasi yang diperiksa | Satu deployment aplikasi, aset teroptimasi, request kecil; proyek dinyatakan sukarela oleh pengguna |
| Supabase Free database | 500 MB, maksimum dua project aktif; free project dapat dijeda setelah satu minggu tidak aktif | Satu produksi, satu simulasi; aktifkan/periksa sebelum acara |
| Supabase Free storage/egress | 1 GB file storage; 5 GB egress dan 5 GB cached egress | Foto terkompresi; ikon/audio statis ditempatkan di deployment |
| Supabase Free Realtime | 200 peak connections; 2 juta pesan per bulan | Koneksi hanya dashboard panitia; tidak membuat satu akun/koneksi per mahasiswa |
| Backup Supabase Free | Tidak mencakup backup otomatis/PITR | Backup logis manual terproteksi dan uji pemulihan sebelum acara |
| Domain | Subdomain vercel.app | Tanpa pembelian domain |
| Aset | Koleksi awal 3dicons CC0; audio/font berlisensi gratis | Simpan sumber/lisensi; tidak menggunakan item Pro berbayar |

### 13.1 Anggaran teknis awal

- Maksimal 1.000 pemilih resmi; 2–3 bilik; asumsi pengujian hingga 10 dashboard panitia bersamaan.
- Target database satu acara di bawah 25 MB termasuk indeks/audit; ukur setelah simulasi, bersihkan data teknis kedaluwarsa.
- Target foto kandidat maksimal 200 KB per foto; maksimal empat kandidat.
- Target ikon dekoratif sekitar 120 KB per aset dan total SFX sekitar 200 KB.
- Target muatan welcome di bawah 1 MB dan seluruh aset jalur bilik di bawah 2,5 MB terkompresi.
- Target JavaScript awal bilik sekitar 250 KB gzip atau lebih kecil; ukur build nyata sebelum menyatakan tercapai.
- Contoh biaya polling: tiga bilik setiap lima detik selama delapan jam menghasilkan 17.280 request. Jangan polling endpoint yang tidak diperlukan di semua tab.
- Sekitar 2.000 perubahan check-in/commit untuk 1.000 pemilih × 10 subscriber menghasilkan sekitar 20.000 pengiriman pesan, sebelum overhead/reconnect. Sediakan margin dan ukur penggunaan sebenarnya.
- Budget internal usulan: kurang dari 100.000 request dan 100.000 pesan realtime per acara; bukan pengganti batas platform yang sesungguhnya.

Target Rp0 mencakup software, hosting, dan database selama memenuhi ketentuan dan kuota. PC, reader, speaker, listrik, koneksi cadangan, serta penyimpanan backup merupakan sarana acara yang perlu tersedia. Status sukarela menghindari beberapa kategori penggunaan komersial, tetapi kelayakan Hobby untuk konteks personal/organisasi tetap perlu dipastikan; PRD tidak menganggap seluruh kegiatan non-profit otomatis memenuhi syarat.

Free tier tidak memberikan jaminan uptime khusus acara. Jika kuota atau layanan terganggu, prosedurnya jeda; jangan mencatat suara offline yang belum memiliki mekanisme rekonsiliasi.

## 14. Operasional dan pemulihan

### 14.1 Sebelum acara

- Peroleh izin data pemilih/RFID, finalisasi tata tertib, dan pastikan semua kandidat/pemilih memakai data periode yang benar.
- Periksa Supabase tidak sedang paused; cek kuota, status layanan, region terdekat yang tersedia, dan latensi venue.
- Siapkan hotspot/internet cadangan, perangkat cadangan bila tersedia, dan penanggung jawab insiden.
- Pastikan semua reader mengirim format UID yang konsisten, termasuk nol di depan, huruf besar/kecil, urutan byte, serta suffix Enter.
- Test keyboard, touchscreen, speaker/mute, fullscreen, clock server, dan pembatasan perangkat.
- Pair semua bilik, pastikan tidak ada sesi login admin tertinggal di browser bilik.
- Lakukan backup konsisten sebelum pembukaan, simpan terenkripsi dengan akses terbatas, dan uji restore ke lingkungan terpisah.
- Bekukan deployment selama acara setelah versi uji disetujui.

### 14.2 Saat acara

- Operator memeriksa identitas, menandai hadir, dan menugaskan bilik; admin memantau status/koneksi dan partisipasi.
- Bilik bermasalah ditandai tidak tersedia dan sesi diperiksa sebelum dialihkan.
- Gangguan internet: jeda, aktifkan koneksi cadangan, tunggu server dapat dijangkau, lalu rekonsiliasi sesi belum pasti.
- Catat insiden operasional tanpa mencatat kandidat pilihan mahasiswa.
- Jika perlu backup tengah acara, lakukan saat jeda terkoordinasi dan gunakan snapshot konsisten.

### 14.3 Sesudah acara

- Tutup dengan grace period, pastikan semua transaksi selesai, dan jalankan rekonsiliasi.
- Periksa hasil, export Excel, publikasikan, tampilkan di proyektor, lalu backup final.
- Cabut credential bilik dan akun operator yang tidak lagi bertugas.
- Simpan dokumentasi versi aplikasi, migrasi, total final, export, checksum berkas, dan catatan insiden untuk serah terima.

### 14.4 Pemulihan database

Export Excel bukan pengganti backup database. Backup harus mencakup data/schema/fungsi/policy yang diperlukan; aset Storage dan pemulihan Auth ditangani sesuai dukungan alat resmi, lalu dibuktikan melalui latihan restore.

Jangan memulihkan snapshot lama di tengah voting lalu membuka kembali begitu saja: snapshot dapat kehilangan surat suara yang sudah diterima. Jika database aktif tidak dapat dipulihkan tanpa kehilangan commit, pemilihan dijeda dan penyelesaiannya diputuskan panitia berdasarkan bukti yang tersedia. Tidak menjanjikan RPO nol atau pemulihan otomatis pada paket gratis.

## 15. Rencana pengujian dan penerimaan

Persyaratan di bawah adalah pengujian yang harus dilakukan saat aplikasi dibangun, bukan hasil tes yang sudah dilaksanakan.

| ID | Skenario | Hasil wajib |
| --- | --- | --- |
| AC-01 | Import 1.000 pemilih valid | Nama/NPM/bobot benar; preview dan hasil import cocok |
| AC-02 | NPM/UID ganda, nol hilang, formula, file terlalu besar | Kesalahan jelas; tidak ada perubahan parsial tersembunyi |
| AC-03 | Scan kartu berulang di meja | Kehadiran bertambah sekali |
| AC-04 | Penugasan mahasiswa/bilik yang sama secara paralel | Hanya satu sesi yang diizinkan |
| AC-05 | NPM/RFID milik mahasiswa lain di bilik aktif | Ditolak tanpa membuka identitas pemilik lain |
| AC-06 | Perangkat tidak terdaftar / credential dicabut | Tidak dapat memverifikasi atau mengirim suara |
| AC-07 | Pemilih biasa dan pengurus | Masing-masing menghasilkan satu surat suara berbobot 1 dan 3 |
| AC-08 | 20 request kirim paralel untuk satu hak suara | Tepat satu surat suara, satu penggunaan hak, counter tetap benar |
| AC-09 | Kandidat, election_id, bobot, atau session_id diubah | Ditolak atau nilai tak berwenang diabaikan; tidak ada suara lintas pemilihan |
| AC-10 | Kegagalan pada salah satu langkah transaksi | Seluruh perubahan rollback; hak tidak habis tanpa surat suara |
| AC-11 | Server commit tetapi respons hilang | Retry/status check menunjukkan sukses tanpa suara tambahan |
| AC-12 | Refresh/retry dengan kandidat berbeda setelah commit | Suara pertama tetap final; status tidak mengungkap pilihan |
| AC-13 | Operator membatalkan sementara request lama berjalan | Hasil serial konsisten: sudah commit atau sudah dibatalkan, tidak keduanya |
| AC-14 | Timeout 90 + 15 detik sebelum mengirim | Peringatan bisa diperpanjang; jika habis tidak ada suara dan identitas bersih |
| AC-15 | Sukses, reset, Back, tab restore | Sesi berikutnya tidak memperoleh data/konfirmasi pemilih lama |
| AC-16 | Audio diblokir, mute, reduced motion | Voting tetap dapat diselesaikan; feedback visual lengkap |
| AC-17 | Scan menghasilkan Enter pada layar kandidat/konfirmasi | Tidak mengirim suara otomatis |
| AC-18 | Pengiriman bersamaan dengan pause/closing/deadline | Keputusan mengikuti urutan transaksi dan waktu server; tidak ada penerimaan setelah tenggat |
| AC-19 | Dashboard mati saat tenggat closing | Server tetap menolak kirim terlambat; finalisasi kemudian tidak mengubah hasil |
| AC-20 | Subscribe/query/export hasil sebelum closed | Tidak ada perolehan kandidat atau surat suara mentah yang bocor |
| AC-21 | Akun operator memanggil endpoint admin langsung | Ditolak server; perubahan tidak terjadi |
| AC-22 | Supabase anon / authenticated tanpa role mencoba tabel/RPC sensitif | Ditolak oleh grant/RLS/otorisasi fungsi yang sesuai |
| AC-23 | Dashboard reconnect dan event duplikat | Snapshot dan counter kembali akurat; indikator stale terlihat saat terputus |
| AC-24 | Login spam, akses lintas origin, input XSS | Batas request dan proteksi bekerja tanpa memblokir seluruh venue secara tidak perlu |
| AC-25 | Hasil final dan export | Jumlah orang, bobot, persentase, dan total sesuai; tidak ada pilihan individu |
| AC-26 | Nol suara, hasil seri, kandidat kurang dari dua | Tidak ada pemenang palsu; pembukaan calon tunggal ditolak |
| AC-27 | Pemilihan tahun baru dan purge tahun lama | Arsip/snapshot tetap benar; hak suara tiap pemilihan terpisah |
| AC-28 | Simulasi dan preview | Tidak membaca/menulis data produksi |
| AC-29 | Restore backup di lingkungan terpisah | Data, fungsi, policy, dan aset yang diperlukan dapat dipulihkan; langkah dicatat |
| AC-30 | UAT 5–10 mahasiswa + panitia, semua perangkat | Target kemudahan, waktu tugas, aksesibilitas, dan kesiapan operator terpenuhi |

Uji otomatis diprioritaskan untuk transaksi, concurrency, otorisasi, status pemilihan, dan pemulihan. Pengujian browser/manual menilai reader asli, privasi layar, audio, touchscreen, serta pemahaman mahasiswa.

### 15.1 Definition of done versi pertama

- Semua FR dan pengujian kritis integritas/otorisasi/pemulihan lulus dengan bukti.
- UAT selesai dan temuan penghambat memilih dituntaskan.
- Tidak ada kerentanan kritis/tinggi yang diketahui dan relevan pada jalur produksi yang belum ditangani.
- Aturan operasional yang memblokir acara, izin data, dan aset final telah dikonfirmasi.
- Penggunaan layanan gratis diukur; recovery dan backup telah dicoba.
- Runbook serta serah terima dapat dijalankan pemilik teknis awal dan panitia.

## 16. Rencana dua minggu

| Waktu | Fokus | Hasil |
| --- | --- | --- |
| Hari 1 | Finalisasi aturan, izin data, uji reader, setup layanan | Kontrak data dan kelayakan deployment jelas |
| Hari 2 | Referensi Pinterest, warna, prototipe bilik dan registrasi | Alur dan desain inti disetujui |
| Hari 3–5 | Schema, auth/role, import, kandidat, pairing, transaksi | Jalur inti benar secara data dan otorisasi |
| Hari 6–7 | Registrasi, penugasan, UI bilik, konfirmasi, recovery | Satu alur end-to-end di perangkat asli |
| Hari 8 | Animasi, audio, timeout, responsive dashboard | Interaksi konsisten dan aksesibel |
| Hari 9 | Realtime, closing, hasil, export, proyektor | Operasional acara dan laporan lengkap |
| Hari 10–11 | Tes race, jaringan, keamanan, beban, backup/restore | Bukti integritas dan pemulihan |
| Hari 12 | UAT 5–10 mahasiswa dan panitia | Temuan lapangan terprioritaskan |
| Hari 13 | Perbaikan dan verifikasi terarah | Versi kandidat rilis |
| Hari 14 | Persiapan final, pairing, backup, pembekuan rilis | Siap acara jika seluruh kriteria terpenuhi |

Jadwal ini merupakan target dengan asumsi data, perangkat, keputusan, dan referensi tersedia sejak awal. Jika waktu menyempit, dekorasi/penyempurnaan visual diturunkan lebih dahulu; integritas suara, privasi, recovery, dan simulasi tetap menjadi syarat rilis.

## 17. Keputusan terbuka dan dependensi

| ID | Hal yang perlu dipastikan | Pemilik | Perlakuan sementara |
| --- | --- | --- | --- |
| D-01 | Penambahan pemilih saat acara | Panitia pemilihan | Daftar dikunci sejak pembukaan; tidak ada fitur pemilih susulan sampai aturan disahkan |
| D-02 | Penyelesaian hasil seri | Panitia / forum resmi | Tampilkan seri; tidak menetapkan pemenang atau putaran baru otomatis |
| D-03 | Izin, ketersediaan, dan struktur pemetaan RFID | Pemilik data + Rifqy | Gunakan dummy saat pembangunan; NPM terverifikasi tetap didukung |
| D-04 | Model reader, format UID, dan jumlah reader | Pengelola perangkat | Uji beberapa kartu pada semua reader; perkiraan minimum satu reader registrasi + satu per bilik |
| D-05 | Warna resmi dari situs Unpak | Rifqy | Minta screenshot/akses; HEX belum ditetapkan karena fetch 403 |
| D-06 | Referensi Pinterest | Rifqy | UX terang/minimalis sudah ditetapkan; komposisi final menunggu referensi |
| D-07 | Logo, foto, nomor urut, dan visi-misi periode baru | Panitia | Aset folder lama belum otomatis dianggap final |
| D-08 | Tanggal, durasi acara, dan petugas per meja | Panitia | Target pengembangan dua minggu; cocokkan kapasitas 2–3 bilik dengan jam acara |
| D-09 | Tanggal purge tahun berikutnya dan masa sengketa | Panitia + pengelola data | Pertahankan sampai siklus berikutnya; tetapkan tanggal operasional dan penanganan salinan |
| D-10 | Kelayakan Vercel Hobby untuk pola kepemilikan proyek | Rifqy | Proyek dinyatakan sukarela; verifikasi ketentuan personal/nonkomersial sebelum produksi |
| D-11 | File sound effect gratis dan lisensinya | Pelaksana desain | Howler telah dipilih; aset SFX final belum dipilih |

## 18. Sumber

Sumber daring berikut diperiksa pada 4 November 2026; implementasi harus memeriksa perubahan ketentuan dan versi kembali.

1. `Alur-Pengguna-E-Voting-HIMABISDI.pdf`, satu halaman, alur plugin OXS E-Voting v0.1.3 dan data backup 30 Juni 2026.
2. Jawaban pengguna untuk 52 pertanyaan: [catatan interview](docs/KEPUTUSAN-INTERVIEW.md).
3. Vercel Hobby: https://vercel.com/docs/plans/hobby.
4. Vercel fair use: https://vercel.com/docs/limits/fair-use-guidelines.
5. Supabase pricing/Free: https://supabase.com/pricing.
6. Supabase server-side auth: https://supabase.com/docs/guides/auth/server-side/nextjs.
7. Supabase API security: https://supabase.com/docs/guides/api/securing-your-api.
8. Supabase database functions: https://supabase.com/docs/guides/database/functions.
9. Supabase Realtime Postgres Changes: https://supabase.com/docs/guides/realtime/postgres-changes.
10. Next.js authentication/DAL: https://nextjs.org/docs/app/guides/authentication.
11. Next.js data security: https://nextjs.org/docs/app/guides/data-security.
12. Howler.js: https://github.com/goldfire/howler.js.
13. ExcelJS: https://github.com/exceljs/exceljs.
14. Lisensi koleksi awal 3dicons: https://3dicons.co/about.
15. Acuan warna: https://bisnisdigital.unpak.ac.id/ — konten/warna belum berhasil diverifikasi pada tahap ini.
