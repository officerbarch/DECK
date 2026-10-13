# Dekken

Papan catatan tempel untuk kelas paralel dan dosen. Mahasiswa menempel kartu, membalas, menyambungkan kartu dengan garis, dan menaikkan suara untuk yang penting. Semua orang bisa membaca tanpa login; menulis dan memberi suara butuh akun Google yang terdaftar. Pendaftaran mandiri: masuk dengan Google, klik **Daftar** satu kali, tanpa mengisi apa pun.

> **Tumpuk, balas, dan naikkan!**

Dibuat oleh Nurfahmi Muchlis, Arsitektur ITS, 2026.

## Fitur

- **Kartu bentuk bebas:** persegi, lingkaran, segi enam, segi lima, tumpul, dan ledak, dengan lima warna kartu dan lima warna teks. Batas huruf berbeda tiap bentuk.
- **Balasan bertumpuk:** kartu yang dibalas tampil sebagai tumpukan; klik untuk membuka.
- **Suara ↑ ↓:** satu akun satu suara per kartu, urut menurut skor lalu yang terbaru. Bisa diurutkan juga menurut waktu atau nama.
- **Koneksi antar kartu:** pembuat kartu menyambungkan kartunya ke kartu lain (maksimal 5) dengan garis berwarna kartu asal.
- **Papan lebar:** mode Acak selebar minimal 6 kolom, bisa digeser ke samping dan di-zoom 40–160%, dibuka dari tengah. Mode Rapi mengikuti lebar layar.
- **Peta kartu:** miniatur seluruh papan di kiri bawah untuk berpindah dengan klik atau seret.
- **Polling (khusus dosen):** kartu oranye (di semua tema) berisi satu pertanyaan dan 2–5 pilihan, dengan hitung mundur sendiri (15 menit sampai 8 jam, karena papan di-reset tiap hari). Akun terdaftar memilih satu jawaban dan hasilnya langsung tampil. Satu polling berjalan pada satu waktu, selalu di posisi pertama, tidak bisa dibalas, tetapi bisa disambungkan.
- **Kartu timer (merah):** kartu persegi panjang merah berlabel TIMER, dibuat semua akun terdaftar lewat tombol berikon stopwatch (kotak) di deretan bawah. Pembuat memilih umur 5, 15, atau 30 menit; hitung mundur tampil di kartu, dan saat habis kartu beserta balasannya hilang otomatis. Pembuat atau dosen juga bisa menghapusnya kapan saja dengan ✕. Bisa dibalas, divote, dan disambungkan.
- **Kartu game:** pembuat kartu biasa (bukan polling atau timer) menekan tombol kontroler di dekat tombol Balas. Semua jawaban orang lain lalu selalu tersegel (bergembok, isinya hanya terlihat pengirimnya); satu jawaban per akun, pembuat tidak ikut menjawab. Pembuat menekan ikon mata untuk membuka semua jawaban, yang lalu menjadi balasan biasa.
- **Hapus kartu:** pembuat kartu menghapus kartunya sendiri (dosen bisa menghapus kartu siapa pun) dengan konfirmasi singkat. Menghapus kartu utama ikut menghapus semua balasannya, suara, pilihan polling, dan sambungannya. Salinan kartu yang dihapus disimpan di sheet `Arsip`.
- **Foto semua kartu:** tombol kamera menyalin foto seluruh papan (beserta garis koneksi) ke clipboard, atau mengunduhnya sebagai PNG.
- **Tampilan:** terang, gelap, monokrom terang, monokrom gelap, dan tombol untuk mematikan latar tanda ^.
- **Ringan dan sinkron:** perangkat lemah (inti 4 atau kurang, memori 4 GB atau kurang, atau hemat data) mulai dengan latar mati; latar juga dimatikan otomatis bila halaman patah-patah. Spektrum berjalan di resolusi rendah ~33 fps. Ikon tiga tanda ^ bergerak naik di kiri tombol Urut menandakan sinkronisasi dengan server.
- **Latar spektrum:** alternatif latar tanda ^ (tombol ≋): pendar warna pelangi yang mengalir pelan, tertarik ke pointer dan kartu yang bergerak. Abu-abu di mode monokrom, diam bila perangkat meminta kurangi gerakan.
- **Suara:** bunyi "dek dek" untuk kartu baru, nada klik, nada koneksi, dan desir saat Acak atau Rapi. Dibuat langsung oleh browser, tanpa file audio.
- **Pendaftaran mandiri:** akun yang belum terdaftar melihat tombol Daftar di bawah layar. Satu klik mencatat email (diambil dari login Google yang terverifikasi) ke sheet `Whitelist`. Admin cukup meninjau sheet dan mengisi `blokir` di kolom status untuk akun yang janggal. Dibatasi 20 pendaftaran baru per jam.
- **Tanda dosen:** centang biru untuk akun dosen yang terverifikasi.
- **Statistik dan timer:** pengguna aktif, jumlah kartu, balasan, dan koneksi, plus hitung mundur reset harian.
- **Penghapusan langsung terlihat di layar lain:** CSV publik Google di-cache beberapa menit, jadi id kartu yang dihapus (dan waktu reset terakhir) dicatat server selama 20 menit dan dibaca halaman lewat `?aksi=terhapus`; kartu itu langsung disembunyikan di semua layar pada pembaruan berikutnya (maks 30 detik). Kartu baru dari orang lain tetap bisa tertunda sesuai cache CSV; kosongkan `CSV_URL` bila ingin langsung.
- **Reset harian:** setiap hari sekitar 07.00 WIB papan dikosongkan dan isinya diarsipkan. Kartu timer dan game ikut dikosongkan.

Menu **?** di dalam aplikasi menjelaskan semua fitur untuk pengguna.

## Cara kerja

```
GitHub Pages (index.html)
   ├── membaca  → CSV publik dari sheet "Posts"      (cepat, tanpa login)
   └── menulis  → Google Apps Script (Web App)       (memverifikasi token Google)
                      └── menulis ke Google Sheet
```

Seluruh aplikasi adalah satu berkas `index.html` tanpa proses build dan tanpa server sendiri.

| Berkas | Fungsi |
| --- | --- |
| `index.html` | Seluruh tampilan dan logika di browser |
| `Kode.gs` | Google Apps Script: verifikasi login, tulis kartu, suara, koneksi, reset harian |
| `README.md` | Dokumen ini |

Sheet yang dipakai: `Posts` (publik, dibaca sebagai CSV), serta `Votes`, `Pilihan` (pilihan polling), `Notifikasi` (penerima email), `Segel` (isi kartu game yang masih tersegel), `Log`, `Whitelist` (pendaftar), `Dosen`, dan `Arsip` (privat). Email tidak pernah masuk ke sheet publik.

## Memasang sendiri

### 1. Google Sheet dan Apps Script
1. Buat Google Sheet baru.
2. Buka **Extensions > Apps Script**, hapus isi bawaan, lalu tempel seluruh isi `Kode.gs`.
3. Pilih fungsi `setup`, klik **Run**, dan setujui izin. Ini membuat semua sheet. Lalu jalankan fungsi `pasangTimer` sekali untuk mengaktifkan reset harian otomatis (pukul 07.00 WIB). Menjalankannya lagi juga menghapus jadwal reset mingguan yang lama.
4. Sheet `Whitelist` berisi kolom `email`, `waktu`, dan `status`. Akun baru mengisinya sendiri lewat tombol Daftar. Untuk memblokir, tulis `blokir` di kolom `status` baris akun itu. Email yang kamu ketik manual di kolom A tanpa status dianggap aktif (cocok untuk mendaftarkan dosen lebih dulu). Isi sheet `Dosen` dengan email dosen untuk mendapat centang biru.
5. Di `Kode.gs`, `DAFTAR_MANDIRI = true` membuka pendaftaran sendiri (bawaan) dan `DAFTAR_MAKS_JAM` membatasi pendaftar baru per jam. Ubah `DAFTAR_MANDIRI` menjadi `false` bila hanya email yang kamu isi sendiri di `Whitelist` yang boleh menulis (Whitelist kosong lalu berarti semua akun boleh).

> Pendaftaran mandiri tanpa kode atau batasan domain berarti siapa pun yang punya akun Google bisa mendaftar. Pantau sheet `Whitelist` dan blokir yang janggal.

### 2. Login Google (OAuth Client ID)
1. Di [Google Cloud Console](https://console.cloud.google.com), buat project, lalu atur **OAuth consent screen** (External) dan klik **Publish app**.
2. **Credentials > Create credentials > OAuth client ID > Web application**.
3. Pada **Authorized JavaScript origins**, tambahkan `https://USERNAME.github.io` (hanya alamat dasar, tanpa nama repo).
4. Salin Client ID, lalu tempel di `Kode.gs` (`CLIENT_ID`) dan di `index.html` (`CONFIG.GOOGLE_CLIENT_ID`).

### 3. Deploy Apps Script
1. **Deploy > New deployment > Web app**. *Execute as*: **Me**. *Who has access*: **Anyone**.
2. Salin URL berakhiran `/exec` ke `CONFIG.SCRIPT_URL` di `index.html`.
3. Setiap `Kode.gs` berubah: **Deploy > Manage deployments > Edit > New version > Deploy**. URL tetap sama.

### Notifikasi untuk admin (opsional)
1. Di Apps Script, pilih fungsi `pasangNotifikasi` lalu klik **Run** sekali dan setujui izin kirim email.
2. Setelah itu, tiap 5 menit skrip mengirim satu email ringkasan ke pemilik skrip bila ada kartu baru, balasan, polling baru, polling yang baru selesai, atau pendaftaran akun baru. Email muncul sebagai notifikasi di HP lewat aplikasi Gmail.
3. Daftar penerima diatur di sheet `Notifikasi` kolom A (mulai baris 2), satu email per baris, maksimal 20. Tiap penerima mendapat emailnya sendiri. Bila sheet kosong, email dikirim ke pemilik skrip (atau ke `EMAIL_ADMIN` bila diisi). Jangan publikasikan sheet ini. Untuk mencantumkan alamat situs di email, isi `SITUS_URL`.
4. Untuk menghentikan, jalankan `matikanNotifikasi`.
5. Karena ada izin baru, deploy ulang: **Deploy > Manage deployments > Edit > New version > Deploy**.

### Pembersih kartu timer
1. Jalankan fungsi `pasangPembersih` sekali dan setujui izin. Tiap 5 menit kartu timer yang habis dipindahkan ke `Arsip` dan dihapus dari `Posts`. Tampilan situs sudah menyembunyikan kartu itu tepat waktu; pembersih hanya merapikan sheet.
2. Untuk menghentikan, jalankan `matikanPembersih`.
3. Deploy ulang: **Deploy > Manage deployments > Edit > New version > Deploy**.
### Game
Jalankan `setup()` lagi sekali bila belum (menambah kolom `game` di `Posts` dan sheet privat `Segel`), lalu deploy ulang. Isi jawaban yang masih tersegel hanya ada di sheet `Segel`; siapa pun yang bisa membuka sheet itu dapat membacanya. Saat pembuat menekan ikon mata, server menulis isinya ke `Posts`.

### 4. Publikasikan sheet Posts sebagai CSV
1. **File > Share > Publish to web**.
2. Pilih **hanya sheet `Posts`**, format **CSV**, lalu Publish.
3. Salin tautannya ke `CONFIG.CSV_URL` di `index.html`.
4. Jangan publikasikan `Votes`, `Pilihan`, `Segel`, `Notifikasi`, `Log`, `Whitelist`, `Dosen`, atau `Arsip`.

### 5. GitHub Pages
1. Unggah `index.html` ke repo.
2. **Settings > Pages > Deploy from a branch > main / (root)**.
3. Buka `https://USERNAME.github.io/NAMA-REPO/`.

Mengganti nama repo mengubah alamat situs, tetapi login Google tidak terpengaruh karena yang didaftarkan hanya alamat dasar `https://USERNAME.github.io`.

## Konfigurasi

Di bagian `CONFIG` pada `index.html`:

| Kunci | Isi |
| --- | --- |
| `SCRIPT_URL` | URL Web App Apps Script (berakhiran `/exec`) |
| `CSV_URL` | Tautan CSV sheet `Posts`. Kosongkan (`''`) untuk membaca langsung lewat Apps Script, lebih cepat tetapi lebih berat bagi kuota |
| `GOOGLE_CLIENT_ID` | OAuth Client ID |
| `REFRESH_MS` | Jeda pembaruan otomatis (bawaan 30 detik) |
| `MAKS_USERNAME` | Batas huruf username |

Konstanta bentuk, warna, dan batas di `Kode.gs` (`BENTUK`, `KARTU`, `WARNA`, `MAKS_TAUTAN`) harus sama dengan yang ada di `index.html`.

## Catatan

- CSV "Publish to web" di-cache Google beberapa menit. Kartu dan suara milikmu sendiri langsung tampil, milik orang lain bisa terlambat.
- Isi sheet `Posts` bersifat publik.
- Foto semua kartu memuat pustaka [html-to-image](https://github.com/bubkoo/html-to-image) dari CDN saat dipakai pertama kali, jadi butuh internet. Foto tidak dikirim ke server mana pun.
- Di layar HP (≤ 640px) garis koneksi tidak digambar; sambungan tampil sebagai label `↔ @nama`, dan Peta kartu disembunyikan.
- Fitur zoom memakai properti CSS `zoom`; di Firefox butuh versi 126 ke atas.
- Kuota Apps Script akun Gmail gratis cukup untuk komunitas kecil. Untuk pengguna banyak, pertimbangkan backend lain.

## Lisensi

Belum ditentukan.

## Kuota Apps Script (akun gmail.com; Workspace lebih besar)
Per Google (bisa berubah tanpa pemberitahuan): 6 menit per eksekusi, 30 eksekusi bersamaan per pengguna, total jalan pemicu 90 menit per hari, 20 pemicu per skrip, 100 penerima email per hari, 50.000 baca/tulis Properties per hari. Tidak ada batas harian permintaan masuk yang dipublikasikan untuk web app; yang membatasi adalah eksekusi bersamaan dan waktu jalan. Rancangan ini hemat: pembaruan tiap 30 detik memakai CSV (tidak kena kuota skrip) ditambah satu permintaan kecil `?aksi=terhapus` yang hanya membaca cache. Bila `CSV_URL` dikosongkan, tiap pembaruan menjalankan skrip dan lebih cepat menghabiskan kuota eksekusi bersamaan saat banyak orang membuka halaman bersamaan.
