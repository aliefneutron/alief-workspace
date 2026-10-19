# Alief Workspace Android 3.4

Versi Android dari konsep Alief Workspace Portable, menggunakan GeckoView 158. Android 9 atau lebih baru, ARM64/ARM32/x86_64.

## Penggunaan

- Home membuka panel layanan dan empat aplikasi Alief. Situs dibuka di workspace.
- Lima tab tetap tersedia dalam mode biasa maupun penuh. Mode penuh menyembunyikan judul dan alamat URL.
- Pilih profil akun melalui tombol di samping layanan. Default Akun 1–5 memakai penyimpanan terpisah. Profil yang sama di beberapa tab berbagi login. Profil dapat ditambah dan diganti namanya.
- Menu > Tambah layanan menyimpan nama dan URL HTTPS, tersedia di semua tab.
- Ikon Pustaka membuka panel kanan. Cari nama media atau filter Gambar/Video/Audio. Kelompok media dipisahkan dan diurutkan dari terbaru.
- Upload situs menawarkan penyimpanan ponsel atau Pustaka. Batas impor/unduhan per file 200 MB.
- Media Pustaka dapat dipratinjau dalam aplikasi, dibagikan atau dihapus.

Tidak ada Creative Studio, Toolkit, atau menu Pengaturan. Lima tab tetap aktif selama proses aplikasi hidup; setelah Android menutup proses, URL terakhir dimuat lagi. Nama dan profil dipertahankan, tetapi formulir situs yang belum disimpan tidak dijamin pulih.

## Batas kompatibilitas

Login Google dan fitur AI mengikuti kebijakan situs. GeckoView tidak menyediakan API Gemini gratis atau menjamin semua situs menerima login embedded. Ekstensi Dola disertakan khusus https://dola.com dan https://www.dola.com. Panel ekstensi muncul otomatis pada layanan itu. Download menyimpan video MP4 ke Pustaka, maksimal 200 MB. Tautan publik bertanda tangan diproses melalui downloader Android; URL yang membutuhkan cookie browser tambahan atau sudah kedaluwarsa mungkin ditolak dan perlu dimuat ulang. Pengaturan ekstensi disimpan per profil akun. Kamera/mikrofon langsung dari situs dan pemasangan ekstensi Chrome umum belum disediakan. Gunakan upload berkas dari ponsel untuk media.

APK ini menggunakan package dan sertifikat yang sama dengan Alief Studio Android sebelumnya, sehingga dapat dipasang sebagai pembaruan. Data yang hanya ada di PC tidak otomatis disalin ke Android.

## Build

Gunakan JDK 17, Android SDK platform 37/build-tools 36, Gradle 9.3.1. Atur sdk.dir pada local.properties, JAVA_HOME, ALIEF_KEYSTORE, ALIEF_SIGN_PASSWORD, dan opsional ALIEF_GECKO_BUILD. Jalankan `gradle assembleRelease`. Kunci penandatanganan tidak disertakan dalam source.

## Adaptasi ekstensi Dola

Interceptor situs dijalankan melalui content script MAIN dan bridge native berada di ISOLATED. Scope dan redirect CDN diperiksa kembali oleh aplikasi Android. Dokumentasi: https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/content_scripts

Pembaruan 2.2.1: tombol Prompt Dock dihapus dari toolbar Android.

## Pembaruan toolbar 2.3

- Teks status layanan / GeckoView / nomor tab dihapus dari bawah alamat.
- Tombol alamat menggunakan ikon pencarian; tombol Go pada keyboard juga membuka alamat.
- Ikon panel kiri berupa tiga garis.
- Preview penuh menyisakan satu baris: panel kiri, toggle preview, tab 1–5. Header, pemilih layanan/akun, navigasi/Pustaka/menu, dan alamat disembunyikan.
- Loading dimulai saat navigasi, bergerak maju, mencapai 100% pada pemuatan berhasil lalu hilang setelah 250 ms. Navigasi baru membatalkan penghilangan garis dari halaman sebelumnya.

## Dola Android 2.4

Panel Dola lebih kecil (lebar maksimal 280px), dibatasi tinggi viewport dan dapat digulir pada layar pendek. Resolver lebih dahulu memakai fetch dari halaman Dola yang aktif dengan credentials include; native HTTP menjadi cadangan jika permintaan halaman tidak tersedia. Kandidat sumber dibandingkan berdasarkan penanda lr/logo_type/unwatermarked_url dan metadata watermark sebelum kualitas. Preview DOM tidak lagi dipakai diam-diam sebagai sumber unduhan. Jika sumber tanpa watermark belum terdeteksi, tampil pesan untuk memuat ulang chat atau memutar video. Penanda URL/metadata merupakan petunjuk sumber, bukan bukti visual bahwa seluruh frame video bebas watermark.

## Dola 2.5: mengikuti runtime versi PC

Resolver dan decoder diambil langsung dari blok background dalam preload.js versi PC, termasuk retry unduhan gagal. Interceptor sama dengan page-interceptor.js PC. API memakai channel=no, codec_type=8 dan logo_type=unwatermarked; pilihan bitrate/main_url dan decoder Base64/qAAB sama dengan PC. Filter tambahan dan page-fetch relay eksperimen 2.4 dihapus. Header sesi (Cookie/User-Agent/Accept-Language) dari request GeckoView dipetakan per tab dan host melalui ekstensi background; native HTTP menerapkan header hanya pada host yang cocok, termasuk pada redirect. Header tidak ditulis ke log atau dikirim ke halaman. Java HttpURLConnection tetap merupakan transport Android, sehingga ini bukan engine jaringan Electron PC.

Menu kecil dari versi sebelumnya dipertahankan. Status sumber tanpa watermark tidak diverifikasi dengan pemrosesan visual frame. Uji aktual perlu chat Dola aktif pada perangkat.

Layanan Dola diminta dalam user-agent mode desktop GeckoView agar memakai varian situs desktop; layanan lain tetap mode mobile. Tampilan ekstensi tetap kecil dan responsif.

## Dola 2.6: aksi Android dan diagnostik

Salin tautan memakai ClipboardManager Android; preview memakai dialog VideoView dalam aplikasi. Keduanya tidak menunggu antrean unduhan. Worker Dola terpisah dari impor media. Daftar ekstensi tidak dibangun ulang jika data sumber tidak berubah, sehingga gesture sentuh dan status unduhan tidak hilang saat interval pemindaian.

Decoder dan resolver PC tetap sama. Media hasil resolver diberi provenance internal pada adapter; URL preview DOM tidak digunakan sebagai pengganti hasil resolver. URL yang secara eksplisit memuat lr=watermarked ditolak dengan pesan. Ini belum membuktikan tidak ada watermark yang tertanam dalam frame.

Untuk menelusuri hasil nyata: muat ulang Dola, putar video, coba tombol ekstensi; buka menu titik tiga > Diagnostik Dola > Salin laporan. Laporan lokal dibatasi 64 KB dan hanya memuat tahap, hostname, penanda lr/logo_type dan status HTTP. Cookie, URL lengkap, token query dan isi respons tidak dicatat.

## Dola 2.7: pemulihan penangkapan sumber

Penangkap Gecko webRequest.filterResponseData membaca salinan respons main-frame/XHR Dola sambil meneruskan setiap byte tanpa perubahan. source-scan.js memahami JSON bersarang, string JSON ter-escape, fallback_api dan fallbackApi. Kandidat awal disimpan sementara per tab sehingga content script yang terlambat tetap dapat mengambilnya. capture-bootstrap.js membaca script JSON awal chat. Tombol Periksa ulang sumber dan semua aksi video dapat menjalankan polling/pemulihan tanpa navigasi ulang.

Decoder dan parameter API versi PC dipertahankan; lifecycle cache diubah: status sedang diproses dibersihkan saat selesai/gagal, permintaan bersamaan berbagi promise, force retry membatalkan cache hasil lama. Download All kini mencoba memulihkan sumber dan memberi pesan jika belum ada URL. Panel menampilkan jumlah sumber, kandidat dan respons jaringan. URL video preview biasa tetap tidak digunakan sebagai master.

API Gecko: https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/webRequest/filterResponseData

## Dola 2.8: batas waktu dan payload bertingkat

Diagnostik pengguna 2.7 menunjukkan resolver_response HTTP 200 tetapi belum ada pc_resolved. Native bridge sekarang mengirim isi respons sebagai teks JSON, lalu parser JavaScript mencari token main_url/play_url pada objek atau JSON-string bertingkat dengan key_seed yang sesuai. Decoder PC dipertahankan. Diagnostik baru membedakan resolver_body_ready, payload_type, resolver_tokens, resolver_no_token, dan resolver_decode_failed.

RPC native, polling penangkap, callback ekstensi, dan pemeriksaan sumber diberi batas waktu. Header context maksimal 1,5 detik; polling 2 detik; resolver native 12 detik. Tiga kandidat diproses paralel per batch. Tombol selalu dipulihkan saat gagal atau timeout; kode DOLA_* ditampilkan. Native pembacaan resolver juga memiliki connect/read timeout 5 detik serta batas baca 10 detik. Transfer video tetap dibatasi 200 MB dan total 4 menit.

## Dola 2.9: native bridge reply

Laporan perangkat 2.8 menunjukkan resolver_body_ready (sekitar 2700 byte) berulang tetapi tanpa payload_type; respons selesai dibaca namun kelanjutan JavaScript belum terlihat. Balasan DolaBridge kini berupa string JSON, bukan JSONObject, dan penyelesaian hasil worker dikirim lewat UI thread. Ekstensi mem-parsing string sebelum membaca value. Penanda baru: native_reply_queued, native_reply_completed, native_fetch_received. Ini memperjelas transport respons sebelum decoder. Penyebab native yang pasti belum dibuktikan pada perangkat.

## Dola 3.0: native token decoder

Laporan 2.9 membuktikan native_fetch_received dan payload_type_object sudah berjalan, tetapi resolver_decode_failed setelah token ditemukan. Decoder Java baru mengikuti derivasi SHA-512 dan AES-CBC PC dengan percobaan payload/IV yang sama, lalu memvalidasi hasil sebagai URL ASCII. JS decoder tetap dicoba terlebih dahulu; jika gagal token+seed dikirim hanya ke proses aplikasi sendiri. Tidak ada token/seed yang ditulis ke diagnostik. Catatan decode_native_ready/decode_native_failed/decode_seed_missing menyertakan hanya panjang seed.

Akar pasti kegagalan browser (WebCrypto atau token/seed) belum terbukti sampai diagnostik perangkat baru tersedia. Decoder native tidak dapat memulihkan token terenkripsi bila seed hilang atau salah.

## Perbaikan 3.1
Sumber resolver tetap tersedia dalam menu saat tidak cocok dengan kartu halaman. Duplikat sumber digabung menurut identitas media; pencocokan berdasarkan posisi dihapus agar tidak memilih video yang salah. Sumber yang sudah siap digunakan kembali sebelum memanggil resolver. Seed query mempertahankan tanda +.
Pengujian fixture lulus; hasil chat Dola asli pada perangkat belum diverifikasi untuk versi ini. Log pengguna 3.0 membuktikan decode dan satu unduhan berhasil.

## Perbaikan 3.2
Daftar/unduhan Dola hanya menerima sumber dengan ID chat aktif atau kecocokan kartu di halaman. Sumber tanpa bukti kaitan chat disembunyikan. Menu > Tema menyediakan Hitam & Hijau dan Burgundy; ekstensi mengikuti tema tanpa reload. Uji fixture isolasi chat, aksi setelah navigasi, dan tema lulus. Perlu uji pada chat Dola asli di perangkat.

## Perbaikan 3.3
Metadata chat dipertahankan saat sumber datang kembali, cache resolver menerima metadata baru, parser mendukung SSE dan alias ID percakapan, pencocokan kartu memakai poster video serta ID pesan. Kandidat chat aktif diprioritaskan. Diagnostik menambahkan jumlah kandidat/media dengan ID chat yang cocok. Uji fixture lulus; hasil di perangkat belum diverifikasi.

## Perbaikan 3.4
Prioritas CSS tema diperbaiki agar tidak tertimpa stylesheet burgundy bawaan yang dimuat belakangan. Sinkronisasi tema diperiksa setiap 1,5 detik dan saat halaman aktif kembali. Uji computed style untuk warna hijau/burgundy lulus; belum diuji di perangkat Android.
