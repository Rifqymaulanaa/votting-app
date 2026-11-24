# Bisnis Digital Unpak — sistem visual feed

**Sumber:** [Salinan dari Tempelate Feeds Terbaru](https://canva.link/p6bts02e28v52xi), 468 halaman. **Kesimpulan:** identitas utamanya adalah bidang teal berlapis, identitas kampus di tepi atas, dan bilah kontak yang mengunci komposisi di bawah. Untuk situs pemilihan, kombinasi yang paling mudah dipindahkan adalah **header institusi + latar teal + teks informasi yang jelas**. Perlakukan emas dan huruf sambung sebagai aksen.

## Prinsip visual & mood

Teal memberi kesinambungan antara pengumuman, potret mahasiswa, dan dokumentasi acara; terang-gelapnya datang dari beberapa lapisan, bukan satu warna datar. Logo kecil yang konsisten dan ruang tengah yang lapang membuat template terasa institusional tanpa menyerupai surat resmi. Huruf sambung memberi nada seremonial pada ucapan, sementara angka, nama, dan rincian kegiatan mengambil hierarki yang lebih tegas. Untuk pemilihan, biarkan nama kandidat dan pilihan suara menjadi unsur yang paling cepat terbaca; tempatkan tekstur atau foto gedung di area pengantar.

### Cara membaca bukti

| Sumber | Yang diambil |
|---|---|
| Slide 1 · template teal | Susunan header, kontak, gradien dan sorot cahaya |
| Slide 2 · Color Palette | Tujuh label hex dan tujuh fill swatch |
| Slide 3 · Color Palette + Font | Konfirmasi fill; nama keluarga, varian font, ukuran dan spasi baris spesimen |
| Slide 4 · template kampus | Foto gedung, opacity, pola kelopak dan pita bawah |
| Slide 5 · template putih | Isolasi header, lambang, bilah dan bayangan tanpa latar poster |
| Slide 9 · pengecualian atas izin pengguna | **Hanya aset badge kapsul beserta logo penyusunnya**; tidak menyumbang token |
| 18 sampel: 9, 10, 16–18, 90, 150, 235, 313, 390, 451–453, 455, 457, 462, 463, 468 | Observasi komposisi, gaya foto, dan hierarki; bukan sumber angka atau warna |

Canva MCP membaca desain dan mengekspor halaman terpilih. Karena inspeksi elemen melalui transaksi tidak didukung untuk desain 468 halaman ini, properti dibaca dari **DrawingML dalam PPTX** dan diperiksa terhadap **PDF**: warna dari fill, font dari nama PostScript/tabel font, ukuran dari run teks, dan spasi baris dari paragraf. Tidak ada warna yang diambil dengan pipet screenshot. Nama token dan saran pemakaian web di bawah adalah penamaan dokumentasi, bukan nama variabel resmi kampus.

## Warna

Ketujuh kode **identik** antara label tertulis dan nilai `srgbClr` pada bentuk swatch di **kedua** slide 2 dan 3. Palet tidak memberi nama fungsi per warna; kolom terakhir adalah panduan penerapan.

| Token | Hex | Fungsi yang disarankan |
|---|---|---|
| `teal-utama` | `#075E55` | Identitas utama; bidang gelap dengan teks putih. Juga terukur sebagai warna subjudul panduan. |
| `teal-menengah` | `#287573` | Bidang sekunder untuk membedakan kelompok informasi. |
| `teal-terang` | `#2F8886` | Variasi bidang atau ornamen, ketika teal utama terlalu dominan. |
| `mint-aksen` | `#0EC29E` | Aksen detail atau penanda aktif yang juga diberi label. |
| `emas-utama` | `#FFB400` | Angka atau sorotan utama; gunakan teks gelap bila dijadikan permukaan. |
| `emas-terang` | `#FCBE0B` | Variasi aksen kuning; jangan pakai beda dua warna emas ini sebagai satu-satunya pembeda status. |
| `putih` | `#FFFFFF` | Kanvas terang, teks di atas teal gelap, dan ruang pemisah. |

**Warna elemen di luar tujuh swatch:** `#085B54` pada judul panduan dan nama font; `#04252A` pada moto serta dasar pita ornamen slide 4; `#000000` pada label hex dan outline swatch putih. Ketiganya punya bukti penggunaan spesifik, bukan tiga swatch tambahan.

**Kontras untuk situs** (perhitungan pada pasangan warna solid, sebelum tekstur): putih di `#075E55` **7,66:1**; putih di `#2F8886` **4,21:1**; putih di `#FFB400` **1,78:1**. Untuk label kecil di tombol emas, `#04252A` di `#FFB400` memberi **9,04:1**. Pada foto atau gradien, periksa area aktual di belakang teks.

### Gradien dan susunan background

Setiap sel berisi **hex / opacity**. Posisi adalah posisi stop **di dalam bentuk**, bukan persentase tinggi halaman. Bentuk melampaui tepi kanvas.

| Lapisan | 0% | 25% | 50% | 75% | 100% |
|---|---|---|---|---|---|
| `gradien-teal-bawah` · slide 1 & 4 | `#F1BA79` / 0% | `#175753` / 24,5% | `#175753` / 53% | `#1B6D69` / 83,5% | `#105250` / 100% |
| `gradien-teal-atas` · slide 1 & 4 | `#F1BA79` / 0% | `#1F5C56` / 24,5% | `#175753` / 53% | `#1B6D69` / 83,5% | `#105250` / 100% |
| `gradien-bilah-kontak` · slide 1, 4 & 5 | `#195854` / 100% | — | `#225E57` / 100% | — | `#418A84` / 100% |

- **Arah:** kedua lapisan latar memiliki sudut lokal DrawingML 90° (atas → bawah). Grup `gradien-teal-atas` diputar 180°, sehingga ujung pekatnya mengarah ke atas. Bilah kontak menggunakan sudut lokal 45° (kanan-bawah); ini bukan notasi sudut CSS.
- **Urutan slide 1:** dasar putih → gradien bawah → gradien atas terbalik → dua sorot cahaya → header → ulangan gradien bawah yang bergeser dan satu sorot lagi → footer. Tekstur cahaya dipasang pada opacity **52%**. Stop `#F1BA79` ber-alpha nol: bukan aksen jingga yang seharusnya tampak.
- **Slide 4:** foto gedung potong-latar disisipkan di antara gradien bawah dan gradien atas pada opacity **42%**. Dasar pita `#04252A` menopang empat pengulangan ornamen putih, masing-masing dengan nilai alpha DrawingML `19999/100000` (sekitar 20%). Bilah kontak berada di atas pita.
- Untuk mereproduksi tampilan, gunakan `assets/latar-teal.png` atau `assets/latar-teal-kampus.png`. Keduanya berisi komposit latar asli **tanpa logo, kontak, atau pita kaki**. SVG gradien adalah lapisan individual; satu SVG saja tidak setara dengan komposit.

## Tipografi

**Tiga keluarga di panduan:** Univers, Poppins, New Icon Script. **Pemetaan yang disarankan:** Univers untuk nama atau judul informasi, Poppins untuk penjelasan, dan New Icon Script hanya untuk ucapan atau aksen pendek. Slide 3 menunjukkan spesimen, bukan aturan bahwa setiap judul postingan memakai Univers. DM Sans pada footer dan dua font pada identitas universitas punya fungsi tersendiri.

**Ukuran berikut adalah pasangan ukuran / spasi baris eksplisit dalam pt ekspor**, pada kanvas sumber 1080 × 1350 px (PDF 810 × 1012,5 pt). Untuk membandingkan dengan piksel desain pada ukuran penuh, kalikan pt dengan 4/3. Ukuran ini bukan skala heading responsif.

| Level / bukti | Font dan weight aktual | Ukuran / line-height |
|---|---|---:|
| Judul lembar panduan, “Color Palette” / “Font” · 2–3 | Lovelace Regular · 400 | 56,77 / 79,47 pt |
| Subjudul lembar panduan · 2–3 | Poppins Light · 300 | 25,88 / 36,23 pt |
| Nama ketiga keluarga · 3 | Univers LT Pro 55 Roman, Poppins Regular, New Icon Script Regular · masing-masing 400 | 40,77 / 57,07 pt |
| Spesimen alfabet Univers dan Poppins · 3 | Univers LT Pro 55 Roman / Poppins Regular · 400 | 17,77 / 24,88 pt |
| Spesimen alfabet script, kecil / kapital · 3 | New Icon Script Regular · 400 | kecil **17,77 / 24,88**; kapital **11,77 / 16,48** pt |
| Label hex · 2–3 | Hero Light (`HeroLight-Light`); file menandai `usWeightClass=400` meski gayanya bernama Light | 19,00 / 22,80 pt |
| Nama “Universitas Pakuan” pada header · 1, 4, 5 | Times New Roman Regular · 400 | 21,72 / 26,93 pt |
| Moto universitas pada header · 1, 4, 5 | Great Vibes Regular · 400 | 14,59 / 18,10 pt |
| URL, akun Instagram, nomor telepon · 1, 4, 5 | DM Sans Regular · 400 | 15,85 / 22,19 pt |

**TIDAK TERVERIFIKASI:** ukuran H1–H6, body formulir, label pilihan suara, breakpoint, serta weight bold resmi untuk UI. Slide 1–5 tidak mendefinisikannya. Jangan mengubah ukuran spesimen menjadi ukuran antarmuka seolah-olah itu pedoman kampus.

### Google Fonts, lisensi dan substitusi

Ketersediaan dicocokkan dengan [katalog Google Fonts](https://fonts.google.com/metadata/fonts). Alternatif di bawah **bukan font sumber**. Tidak ada file font yang disertakan.

| Font sumber | Tersedia di Google Fonts? | Lisensi / keputusan untuk web |
|---|---|---|
| Univers LT Pro 55 Roman | Tidak | **Berbayar.** [Univers Pro 55 Roman, Linotype/Monotype](https://www.myfonts.com/products/pro-55-roman-univers-368526/licenses). Jika belum punya lisensi web, alternatif **Inter Regular**: sans yang terbaca baik untuk nama dan judul. |
| Poppins Regular & Light | Ya | [Poppins · SIL OFL 1.1](https://github.com/google/fonts/blob/main/ofl/poppins/OFL.txt). Gunakan keluarga asli untuk teks informasi. |
| New Icon Script Regular | Tidak | **Berbayar.** [Set Sail Studios — New Icon Font Duo](https://setsailstudios.com/downloads/new-icon/) menjual lisensi web tersendiri. Alternatif **Allura Regular** untuk aksen kaligrafis; proporsi dan swash berbeda. |
| Lovelace Regular | Tidak | EULA tertanam mengarahkan penggunaan web ke [lisensi Zetafonts](https://zetafonts.com/licensing). Alternatif **Cormorant Garamond Regular** untuk judul editorial, bukan untuk mengganti logo. |
| Hero Light | Tidak | [Fontfabric](https://www.fontfabric.com/fonts/hero/) menyatakan penggunaan personal/komersial gratis di bawah EULA mereka; hak redistribusi webfont dari file Canva **TIDAK TERVERIFIKASI**. Alternatif: Poppins Light. |
| Times New Roman Regular | Tidak | Proprietari Monotype; EULA file terkait produk asal. Izin self-host kampus **TIDAK TERVERIFIKASI**. Untuk header gunakan aset identitas asli; untuk teks serif terpisah, alternatif **Source Serif 4**. |
| Great Vibes Regular | Ya | [Great Vibes · SIL OFL 1.1](https://github.com/google/fonts/blob/main/ofl/greatvibes/OFL.txt). Fungsi terverifikasi: moto, bukan font utama UI. |
| DM Sans Regular | Ya | [DM Sans · SIL OFL 1.1](https://github.com/google/fonts/blob/main/ofl/dmsans/OFL.txt). Cocok untuk mempertahankan karakter informasi kontak. |

Lisensi alternatif Google Fonts: [Inter](https://github.com/google/fonts/blob/main/ofl/inter/OFL.txt), [Allura](https://github.com/google/fonts/blob/main/ofl/allura/OFL.txt), [Cormorant Garamond](https://github.com/google/fonts/blob/main/ofl/cormorantgaramond/OFL.txt), [Source Serif 4](https://github.com/google/fonts/blob/main/ofl/sourceserif4/OFL.txt) — SIL OFL 1.1. Bila mendistribusikan font tersebut, ambil dari rilis resmi dan sertakan lisensinya.

## Spacing, radius, shadow, border

Geometri di bawah adalah **piksel kanvas**, dibulatkan ke 0,1 px dari koordinat grup PPTX (9.525 EMU/px). Nilai posisi berbasis kotak elemen; bantalan transparan di dalam gambar dapat membuat jarak yang terlihat berbeda.

| Elemen | Ukuran/posisi terukur | Makna |
|---|---|---|
| Header dua sisi · 4–5 | kiri **79,6**, atas **83,2**, lebar **927,6**, tinggi **82,2** px | Identitas kampus di kiri dan afiliasi di kanan menempati satu zona atas. |
| Bilah kontak · 1 & 5 | kiri/kanan **76,2**, atas **1250,3**, bawah **38,1** px; ukuran **927,6 × 61,6** px | Bilah lebih dekat ke tepi bawah daripada ke tepi samping. |
| Sudut bilah · 1 & 5 | radius geometri sekitar **4,2 px** dari kurva path setelah skala grup | Persegi panjang bersudut lunak, bukan kapsul penuh. |
| Swatch putih · 2–3 | Stroke solid `#000000`, lebar lokal DrawingML **9.525 EMU** sebelum transformasi grup | Garis membedakan sampel putih dari kertas putih; bukan border kartu. |
| Bayangan kontak · 1, 4, 5 | Terlihat turun ke kanan. PDF menyimpan tampilan bilah dan bayangan sebagai satu gambar bermasker. | Offset, blur, spread, dan alpha editor **TIDAK TERVERIFIKASI**. `footer-bilah-kosong.png` mempertahankan bayangan tersebut. |

**TIDAK TERVERIFIKASI:** skala spacing global, radius kartu, border tombol, dan token elevasi bertingkat. Tidak ada spesifikasi komponen interaktif pada halaman token.

## Pola komponen dan gaya foto

- **Header institusi.** Pada 1/4/5, lambang, nama, dan moto di kiri diimbangi FEB Unpak, Bisnis Digital, dan Diktisaintek Berdampak di kanan. Pada 9/10 terdapat kapsul putih terpusat dengan **tiga identitas berbeda dari susunan kanan template**: lambang Unpak, FEB, dan Bisnis Digital berteks gelap. Gunakan varian berteks gelap di atas putih; pada slide 5, nama universitas berwarna putih memang kehilangan kontras.
- **Kartu informasi.** Pada 455, judul acara ditempatkan di bidang kontras lalu waktu dan lokasi dipasangkan dengan ikon. Pada 463, nomor urut besar, judul ringkas dan kelompok informasi kecil membuat isi dapat dipindai. Untuk kandidat, pola yang dapat dipinjam ialah urutan baca *nomor → nama → rincian*, dengan perlakuan visual yang sama antar kandidat.
- **Potret dan dokumentasi.** Slide 452 memberi ruang untuk teks di kiri dan menempatkan potret potong-latar di kanan. Pada 150, 235 dan 457, foto acara disusun sebagai pita atau kolase; transisi teal dekat kaki menyatukannya dengan kontak. Slide 451 memakai bingkai foto untuk narasi dokumenter. Tidak ada satu preset filter fotografi yang terverifikasi.
- **Footer dan ornamen.** Bilah kontak berurutan **situs → Instagram → telepon**. Versi kampus memakai motif kelopak berulang di bawah bilah, bukan di belakang paragraf. Pada situs, pertahankan urutan dan gunakan teks hidup untuk kontak yang harus dapat dibaca atau diklik.
- **Aksen kampanye.** Script dipakai sebagai aksen ucapan (452) atau pengantar topik (455); sans membawa informasi utama. Poster trofi ungu/emas pada 453 menunjukkan variasi kampanye, bukan alasan menambah ungu ke dalam palet brand.

## Aset

Path relatif terhadap `DESIGN.md`. **Media asli** berarti berkas gambar tertanam di ekspor Canva; **isolasi** berarti hanya operasi gambar/teks elemen itu yang dirender dari PDF, dengan alpha di luar bentuk. Dua background penuh memang opak. SVG kapsul dan gradien diturunkan dari path/stop dalam ekspor; logo raster tidak ditrace menjadi vektor semu.

| Path | Sumber dan kegunaan |
|---|---|
| `assets/logo-unpak-horizontal-asli.png` | Media sumber 1/4/5. Lambang + nama hitam + moto hijau, termasuk padding transparan asli; berbeda dari susunan yang tampak di template. |
| `assets/logo-unpak-horizontal-template.png` | Isolasi 5. Lambang + nama putih + moto gelap sesuai header; tampilkan di latar yang memberi kontras. |
| `assets/logo-unpak-lambang-template.png` | Isolasi lambang sebagaimana dipotong pada template. |
| `assets/logo-feb-unpak.png` | Media sumber 1/4/5. Identitas fakultas, versi 216 × 216 px. |
| `assets/logo-bisnis-digital-teks-putih.png` | Media sumber 1/4/5. Identitas prodi dengan lettering putih untuk permukaan gelap. |
| `assets/logo-diktisaintek-berdampak.png` | Media sumber 1/4/5. Emblem program di ujung kanan header. |
| `assets/header-template.png` | Isolasi 5. Susunan lengkap dua sisi dengan jarak relatif aslinya. |
| `assets/badge-header-tiga-logo.svg` | Khusus 9. Badge lengkap: path kapsul vektor, tiga PNG asli tertanam, crop dan susunan dipertahankan. |
| `assets/badge-header-kapsul.svg` | Khusus 9. Path kapsul putih tanpa logo. |
| `assets/logo-unpak-lambang-badge.png` | Penyusun badge 9. Lambang kampus tanpa nama, 600 × 600 px. |
| `assets/logo-feb-unpak-badge.png` | Penyusun badge 9. Media FEB versi 324 × 324 px. |
| `assets/logo-bisnis-digital-teks-gelap.png` | Penyusun badge 9. Versi lettering gelap untuk permukaan terang. |
| `assets/gradien-teal-atas.svg` | Lapisan atas 1/4, arah grup terbalik sudah diterapkan. |
| `assets/gradien-teal-bawah.svg` | Lapisan bawah 1/4; file yang sama mewakili stop pada ulangan yang bergeser. |
| `assets/tekstur-cahaya.png` | Media asli transparan. Sorot putih lembut untuk melapisi gradien. |
| `assets/foto-gedung-kampus.png` | Media asli transparan 4. Foto bangunan sebelum opacity dan komposisi template diterapkan. |
| `assets/latar-teal.png` | Isolasi latar penuh 1, 1080 × 1350 px. Pilihan tanpa foto. |
| `assets/latar-teal-kampus.png` | Isolasi latar penuh 4, 1080 × 1350 px. Pilihan dengan gedung samar. |
| `assets/ornamen-kelopak.svg` | Vektor asli satu bidang motif putih dari 4; ulangi di pita bawah. |
| `assets/footer-kontak.png` | Isolasi 5. Bilah, ikon, kontak, dan bayangan lengkap untuk referensi identitas. |
| `assets/footer-bilah-kosong.png` | Isolasi 5. Bilah dengan gradien dan bayangan, tanpa teks atau ikon. |
| `assets/ikon-situs.svg` | Vektor asli. Globe dan penunjuk untuk alamat situs. |
| `assets/ikon-instagram.svg` | Vektor asli. Penanda akun Instagram pada bilah. |
| `assets/ikon-telepon.svg` | Vektor asli. Gagang telepon putih untuk nomor kontak. |

## Sebelum dipakai di situs pemilihan

1. **Konfirmasi header dan kewenangan pemakaian.** Prodi, fakultas, universitas, dan program Diktisaintek adalah identitas berbeda. Pemilik desain perlu memastikan logo mana yang tepat untuk pemilihan himpunan. Hak pakai ulang logo, foto dan ornamen di luar Canva **TIDAK TERVERIFIKASI** dari ekspor. [Ketentuan lisensi konten Canva](https://www.canva.com/policies/content-license-agreement/).
2. **Periksa moto dan kontak.** Teks template terbaca persis `Unggul , Mandiri & Berkatakter`. Aset dipertahankan sesuai sumber; cocokkan ejaan dengan identitas resmi dan nomor kontak dengan panitia sebelum dipublikasikan.
3. **Putuskan lisensi webfont.** Kepemilikan lisensi web Univers dan New Icon Script oleh organisasi **TIDAK TERVERIFIKASI**. Jika tidak tersedia, pakai alternatif terbuka untuk teks hidup. Untuk susunan logo yang tetap, gunakan aset identitas yang sudah diekspor.
4. **Tentukan token web secara terpisah.** Halaman sumber tidak menentukan ukuran teks seluler, kontras fokus keyboard, maupun status kandidat terpilih, konfirmasi, dan gagal. Khusus pilihan suara, bedakan status dengan label atau ikon selain warna.

**Hasil ekstraksi:** tujuh swatch, tiga resep gradien, tipografi hingga level varian, geometri template, serta 24 aset. **Batas:** master logo SVG, parameter bayangan editor, dan varian logo di luar halaman yang diizinkan **TIDAK TERVERIFIKASI**. Sampel 18 postingan bukan inventaris seluruh aset dari 468 halaman.
