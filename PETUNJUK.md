# Ular Tangga Ziezaa — Multiplayer Tahap 1

Paket ini mempertahankan game lama di `index.html`, lalu menambahkan fondasi room multiplayer: buat room, QR code, controller HP, dan daftar pemain yang bergabung. Tahap stabilisasi ini menambahkan pembuatan kode room dengan transaksi (agar room yang sudah ada tidak tertimpa), validasi nama, batas maksimal 4 controller, pencegahan nama duplikat, pengecekan status room, dan penanganan koneksi dasar. **Ini belum tahap akhir**: tombol dadu/jawaban di HP belum mengendalikan game. Setelah lobby lolos pengujian di perangkat nyata, tahap berikutnya adalah menghubungkan aksi controller ke mesin permainan yang sudah ada.

## 1. Buat proyek Firebase
1. Buka https://console.firebase.google.com/ lalu buat project.
2. Tambahkan aplikasi Web (ikon `</>`), salin konfigurasi Web App.
3. Buka **Authentication → Sign-in method** dan aktifkan **Anonymous**.
4. Buka **Realtime Database → Create database**. Salin `databaseURL` yang ditampilkan.
5. Isi semua nilai placeholder di `firebase-config.js` dengan nilai project milikmu. Jangan mengirim konfigurasi privat/token server kepada siapa pun. Konfigurasi Web Firebase memang dipakai di sisi klien; keamanan ditentukan oleh Rules.

## 2. Aturan database untuk tes awal
Untuk pengujian pertama, jangan gunakan data pribadi. Di Realtime Database Rules, gunakan aturan sementara yang hanya mengizinkan pengguna yang sudah login anonim:

```json
{
  "rules": {
    "rooms": {
      "$roomCode": {
        ".read": "auth != null",
        ".write": "auth != null"
      }
    }
  }
}
```

**PENTING:** ini aturan prototipe, bukan aturan produksi. Pengguna anonim lain masih bisa membaca/mengubah room jika mengetahui kodenya. Sebelum membagikan ke publik, perlu aturan yang lebih ketat (atau backend tepercaya) dan validasi perintah.

## 3. Publikasikan
Upload `index.html`, `controller.html`, dan `firebase-config.js` ke folder yang sama di repository GitHub Pages. Pastikan `index.html` adalah game utama. Buka website melalui `https://...github.io/.../` (bukan `file://`).

## 4. Uji tahap 1
1. Di laptop/TV, buka website dan tekan **MAIN BARENG (SCAN QR)**.
2. Tekan **BUAT ROOM & QR**.
3. Scan QR dengan HP, isi nama, lalu tekan **GABUNG ROOM**.
4. Pastikan nama HP muncul pada daftar pemain di layar utama.
5. Tekan **LANJUT KE PENGATURAN** untuk mengisi nama pemain di game lama.

Jika QR dibuat tapi HP gagal masuk, cek Firebase config, Authentication Anonymous, Database URL, Rules, dan apakah kedua perangkat membuka alamat website yang sama.


## 5. Checklist uji stabilitas lobby
Lakukan pengujian dengan satu laptop sebagai host dan beberapa HP sebagai controller:

- [ ] Firebase config sudah diisi benar; buka website melalui HTTPS GitHub Pages.
- [ ] Host membuat room dan kode 6 karakter tampil.
- [ ] QR mengarah ke halaman `controller.html` pada website/repository yang sama.
- [ ] HP pertama berhasil bergabung dan namanya muncul di layar host.
- [ ] Nama kosong ditolak; nama yang sama di room ditolak.
- [ ] Pemain kelima ditolak dengan pesan room penuh.
- [ ] Kode room acak tidak menimpa room lain yang sudah ada.
- [ ] Kode yang tidak ada dan room yang sudah dimulai ditolak.
- [ ] Jika HP menutup halaman/terputus, entri pemain dibersihkan oleh Firebase `onDisconnect` (perilaku ini perlu diuji dengan koneksi nyata).
- [ ] Tombol lanjut hanya aktif setelah minimal satu pemain bergabung.

Catatan: pengecekan sintaks JavaScript sudah dilakukan, tetapi ini bukan pengganti pengujian browser dengan project Firebase yang telah dikonfigurasi. Jangan lanjut ke controller dadu/jawaban sebelum checklist lobby di atas lolos.
