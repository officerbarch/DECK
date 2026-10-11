# Dekken

Papan catatan tempel untuk kelas paralel dan dosen. Mahasiswa menempel kartu, membalas, menyambungkan kartu dengan garis, dan menaikkan suara untuk yang penting. Semua orang bisa membaca tanpa login; menulis dan memberi suara butuh akun Google yang terdaftar.

> **Tumpuk, balas, dan naikkan!**

Dibuat oleh Nurfahmi Muchlis, Arsitektur ITS, 2026.

## Fitur

- **Kartu bentuk bebas:** persegi, lingkaran, segi enam, segi lima, tumpul, dan ledak, dengan lima warna kartu dan lima warna teks. Batas huruf berbeda tiap bentuk.
- **Balasan bertumpuk:** kartu yang dibalas tampil sebagai tumpukan; klik untuk membuka.
- **Suara ↑ ↓:** satu akun satu suara per kartu, urut menurut skor lalu yang terbaru. Bisa diurutkan juga menurut waktu atau nama.
- **Koneksi antar kartu:** pembuat kartu menyambungkan kartunya ke kartu lain (maksimal 5) dengan garis berwarna kartu asal.
- **Papan lebar:** mode Acak selebar minimal 6 kolom, bisa digeser ke samping dan di-zoom 40–160%, dibuka dari tengah. Mode Rapi mengikuti lebar layar.
- **Peta kartu:** miniatur seluruh papan di kiri bawah untuk berpindah dengan klik atau seret.
- **Polling (khusus dosen):** kartu merah (di semua tema) berisi satu pertanyaan dan 2–5 pilihan, dengan hitung mundur sendiri (1 jam sampai 7 hari). Akun terdaftar memilih satu jawaban dan hasilnya langsung tampil. Satu polling berjalan pada satu waktu, selalu di posisi pertama, tidak bisa dibalas, tetapi bisa disambungkan.
- **Foto semua kartu:** tombol kamera menyalin foto seluruh papan (beserta garis koneksi) ke clipboard, atau mengunduhnya sebagai PNG.
- **Tampilan:** terang, gelap, monokrom terang, monokrom gelap, dan tombol untuk mematikan latar tanda ^.
- **Suara:** bunyi "dek dek" untuk kartu baru, nada klik, nada koneksi, dan desir saat Acak atau Rapi. Dibuat langsung oleh browser, tanpa file audio.
- **Tanda dosen:** centang biru untuk akun dosen yang terverifikasi.
- **Statistik dan timer:** pengguna aktif, jumlah kartu, balasan, dan koneksi, plus hitung mundur reset mingguan.
- **Reset mingguan:** setiap Senin 00.00 WIB papan dikosongkan dan isinya diarsipkan.

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
| `Kode.gs` | Google Apps Script: verifikasi login, tulis kartu, suara, koneksi, reset mingguan |
| `README.md` | Dokumen ini |

Sheet yang dipakai: `Posts` (publik, dibaca sebagai CSV), serta `Votes`, `Pilihan` (pilihan polling), `Log`, `Whitelist`, `Dosen`, dan `Arsip` (privat). Email tidak pernah masuk ke sheet publik.

## Memasang sendiri

### 1. Google Sheet dan Apps Script
1. Buat Google Sheet baru.
2. Buka **Extensions > Apps Script**, hapus isi bawaan, lalu tempel seluruh isi `Kode.gs`.
3. Pilih fungsi `setup`, klik **Run**, dan setujui izin. Ini membuat semua sheet. Lalu jalankan fungsi `pasangTimer` sekali untuk mengaktifkan reset mingguan otomatis.
4. Isi sheet `Whitelist` kolom A (mulai baris 2) dengan email yang boleh menulis, satu per baris, huruf kecil. Kosong berarti semua akun Google boleh. Isi sheet `Dosen` dengan email dosen untuk mendapat centang biru.

### 2. Login Google (OAuth Client ID)
1. Di [Google Cloud Console](https://console.cloud.google.com), buat project, lalu atur **OAuth consent screen** (External) dan klik **Publish app**.
2. **Credentials > Create credentials > OAuth client ID > Web application**.
3. Pada **Authorized JavaScript origins**, tambahkan `https://USERNAME.github.io` (hanya alamat dasar, tanpa nama repo).
4. Salin Client ID, lalu tempel di `Kode.gs` (`CLIENT_ID`) dan di `index.html` (`CONFIG.GOOGLE_CLIENT_ID`).

### 3. Deploy Apps Script
1. **Deploy > New deployment > Web app**. *Execute as*: **Me**. *Who has access*: **Anyone**.
2. Salin URL berakhiran `/exec` ke `CONFIG.SCRIPT_URL` di `index.html`.
3. Setiap `Kode.gs` berubah: **Deploy > Manage deployments > Edit > New version > Deploy**. URL tetap sama.

### 4. Publikasikan sheet Posts sebagai CSV
1. **File > Share > Publish to web**.
2. Pilih **hanya sheet `Posts`**, format **CSV**, lalu Publish.
3. Salin tautannya ke `CONFIG.CSV_URL` di `index.html`.
4. Jangan publikasikan `Votes`, `Pilihan`, `Log`, `Whitelist`, `Dosen`, atau `Arsip`.

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
