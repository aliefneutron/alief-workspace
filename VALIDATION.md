# Validasi 18 Oktober 2026

- Build release AGP 9.1.1 / Gradle 9.3.1 / GeckoView 158.
- Uji contentscript Prompt Dock dengan mock native messaging di Chromium: textarea dan contenteditable terisi, input password tidak diisi, tidak otomatis submit. Ini bukan pengujian runtime GeckoView.
- Tidak ada perangkat pada `adb devices` saat pengerjaan. Tampilan aktual, isolasi login, upload, download, dan pemutaran media pada ponsel belum diuji.
- APK harus diverifikasi menggunakan apksigner sebelum dibagikan; hasil akhir dicatat di file verifikasi pendamping APK.

Uji perangkat selanjutnya: buka tiap tab, pilih layanan dan profil berbeda, login, ganti tab dan restart; mode penuh dan putar layar; tambah layanan manual; impor gambar/video, cari/filter, pratinjau, upload dari dua sumber; download dari situs; Prompt Dock pada kolom layanan.

## Pembaruan Dola 2.2

- Unit test Java DolaUrlPolicy lulus: origin khusus Dola, whitelist CDN, upgrade HTTP mempertahankan query bertanda tangan, penolakan host asing/userinfo/port tidak standar.
- Fixture Chromium dengan native messaging mock: interceptor fetch -> resolver -> tombol Download -> permintaan native, nama berkas, penghitung sukses, respons gagal, dan penonaktifan pada Gemini/Doubao/domain tiruan. Tidak membuktikan transfer jaringan nyata atau runtime GeckoView.
- Native downloader: timeout, batas 200 MB, verifikasi header MP4, file sementara dibersihkan, pesan gagal ditampilkan. Belum diuji dengan login dan tautan Dola aktif di perangkat Android.

## Toolbar 2.3

Build release dan verifikasi tanda tangan dijalankan ulang. Toolbar native Android belum diuji langsung karena adb tidak mendeteksi perangkat. Tidak ada perubahan pada integrasi Dola.

## Dola 2.4

Uji fixture qa-android-dola-clean.cjs lulus: resolver melalui sesi halaman, clean 720p diprioritaskan atas marked 1080p, query bertanda tangan dipertahankan, sumber marked-only ditolak, panel tidak keluar viewport 390x540, 320x360, 640x280. Screenshot work/android-dola-compact.png ditinjau. Semua jaringan uji menggunakan fixture; belum ada tautan video aktif untuk verifikasi visual tanpa watermark, dan adb belum mendeteksi ponsel. Pengguna mengonfirmasi ekstensi versi PC berhasil.

## Dola 2.5

qa-android-dola-pc-parity.cjs PASS: blok resolver sama persis dengan runtime preload PC (normalisasi baris), interceptor identik, fixture URL Base64 dan qAAB terenkripsi menghasilkan URL master yang sama, parameter API PC sama, header akun terpisah per tab dan tidak dikirim silang host, panel kecil tetap muat. Versi 2.4 ditimpa dan bukan alur aktif. Perangkat adb tidak tersedia; hasil video nyata belum diverifikasi.

## Dola 2.6

qa-android-dola-actions.cjs PASS: touch/tap pada tiga tombol, preview tidak menavigasi halaman, copy merespons ketika download mock belum selesai, node tombol tetap terhubung setelah 3,6 detik, sumber eksplisit watermarked diblokir dan pesan tampil. qa-android-dola-pc-parity.cjs tetap PASS, termasuk encrypted qAAB. Native Android compiled; clipboard/media player riil dan video watermark riil belum diuji karena perangkat adb kosong. Pengguna melaporkan aplikasi tetap terbuka tetapi aksi tidak merespons; tidak ada bukti crash proses.

## Dola 2.7

qa-dola-capture-recovery.cjs PASS: JSON nested/escaped/bootstrap dipindai; respons jaringan terbelah diteruskan identik byte demi byte; chat yang sudah dimuat sebelum hook dipulihkan; percobaan URL yang semula HTTP 503 berhasil pada percobaan berikutnya tanpa refresh; copy/preview/download/download-all tersedia. Uji tindakan sentuh 2.6 dan uji decoder PC/qAAB tetap lulus setelah penyesuaian mock listener. Tidak ada perangkat adb yang terhubung; capture jaringan GeckoView nyata dan watermark visual pada chat pengguna belum diverifikasi.

## Dola 2.8

qa-dola-timeout-retry.cjs PASS dengan waktu nyata: polling tidak menjawab dan native RPC tidak menjawab berakhir dengan timeout, tombol keluar dari loading dan klik yang sama bisa sukses setelah koneksi pulih. Uji payload PC diperluas untuk data.video_info.data dan JSON-string bertingkat, termasuk qAAB terenkripsi, PASS. Hasil video asli pengguna belum diverifikasi di perangkat.

## Dola 2.9

Uji PC decoder dengan native fetch reply berupa string JSON PASS untuk Base64, qAAB terenkripsi, nested data.video_info.data, nested JSON string. Build/signature diperiksa ulang. Tidak ada validasi native GeckoView perangkat atau watermark visual; laporan baru perlu menunjukkan native_fetch_received/payload_type untuk membuktikan kelanjutan respons di perangkat.

## Dola 3.0

DolaDecoderTest Java PASS: token AES-CBC qAAB terenkripsi diturunkan dengan SHA-512, URL Base64, query signed identik, seed kosong/salah ditolak. Native code compile release. Hasil video nyata di perangkat pengguna belum diuji.
