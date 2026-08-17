## chess Tap Mode

## Tentang Game
chess tap mode adalah game catur online berbasis web yang dirancang ringan dan cepat. fokus utama: pengalaman bermain catur dengan sistem tap-to-move, tanpa perlu drag & drop. cocok dimainkan di hp atau desktop dengan sentuhan.

## Fitur Utama
- autentikasi: login pakai email/password, google, atau sebagai tamu.
- profil pengguna: ganti nama dan foto profil dari galeri (anime, manhwa, meme).
- ruangan: buat ruangan publik atau privat (pakai password). atur durasi ruangan 1, 3, atau 5 jam.
- realtime multiplayer: 2 pemain per ruangan, langsung sinkron via firebase.
- gerakan: tap bidak sendiri, lalu tap kotak tujuan. kalo gerakan valid, langsung jalan.
- info visual: sorot hijau di kotak tujuan, sorot kuning di bidak yang dipilih.
- notifikasi: muncul nama bidak di layar saat di-tap, ada suara gerakan & tangkapan.
- obrolan: chat realtime di dalam ruangan, badge notifikasi kalo ada pesan baru.
- graveyard: lihat daftar bidak lawan yang sudah dimakan.
- tema gelap/terang: toggle di pojok kanan atas.
- responsive: papan catur menyesuaikan ukuran layar.

## Teknologi & Library yang Dipakai
- firebase (auth + realtime database) versi 9 compat: buat auth user dan sinkronisasi data permainan.
- chess.js versi 0.10.3: engine catur buat validasi gerakan, deteksi skak, skakmat, dan remis.
- chessboardjs versi 1.0.0: render papan catur di web.
- jquery 3.6.0: buat manipulasi dom.
- font awesome 6.4.0: ikon.
- sweetalert2 11: popup modern.
- google fonts (poppins): font utama.

## Cara Kerja Game
1. user login atau daftar. kalo baru pertama kali, diminta isi nama & pilih foto.
2. masuk ke lobby, liat daftar ruangan aktif yang masih ada slot.
3. host buat ruangan baru. ruangan otomatis terhapus kalo durasi habis atau semua pemain keluar.
4. pemain kedua gabung ke ruangan, otomatis dapet warna hitam.
5. setelah 2 pemain terisi, permainan dimulai. papan catur di-sync ke kedua pemain via firebase.
6. pemain bergiliran tap bidak dan tap kotak tujuan. kalo langkah valid, game update fen dan turn di database.
7. semua perubahan realtime langsung tampil di layar pemain lain.
8. chat berjalan paralel, disimpan di node chat per ruangan.
9. kalo skakmat atau remis, muncul popup dan status berubah.

## Setup Firebase Realtime Database
### 1. Dapatkan konfigurasi firebase
- buat project di firebase console.
- aktifkan authentication (email/password & google sign-in).
- aktifkan realtime database (buka aturan, set ke yang sesuai).
- ambil konfigurasi firebase (apiKey, authDomain, databaseURL, projectId, dll).

### 2. Ganti config di file index.html
cari block `firebaseConfig` di dalam script, isi dengan konfigurasi punya lo.

### 3. Aturan Keamanan Firebase RTDB
rules yang direkomendasikan (biar sesuai kode):

```plaintext
{
  "rules": {
    "rooms": {
      "$roomId": {
        ".read": true,
        ".write": true,
        "chat": {
          ".read": true,
          ".write": true
        }
      }
    }
  }
}
```

penjelasan:

· rooms/*: semua ruangan bisa dibaca dan ditulis siapa aja (karena kode ga pake validasi uid di rules, validasi dilakukan di aplikasi).
· chat: anak dari rooms juga bisa dibaca/tulis bebas, karena obrolan perlu disinkronkan antar pemain.

catatan: kalo mau lebih aman, lo bisa tambah validasi berdasarkan uid pemain yang terdaftar di white/black, tapi kode ini ga ngatur di sisi rules, jadi bebas aja.

Struktur Data RTDB

· rooms
  · {roomId}
    · name: string
    · password: string (null kalo publik)
    · expireAt: timestamp
    · white: { uid, name, photo }
    · black: { uid, name, photo } (null kalo belum ada)
    · fen: string (posisi papan, default 'start')
    · turn: 'w' atau 'b'
    · chat
      · {pushId}
        · uid: string
        · name: string
        · photo: string
        · msg: string

Cara Menjalankan

· cukup buka file index.html di browser (chrome/firefox). ga perlu server khusus karena firebase yang handle backend.
· pastikan koneksi internet jalan untuk akses firebase dan library cdn.

Catatan Tambahan

· foto profil diambil dari url 'Photo/{kategori}/{file}.jpg'. kalo mau pake gambar sendiri, ganti sumbernya di dbPhotos.
· audio notifikasi pakai file dari chess.com, bisa berubah kalo url-nya mati.
· board orientation otomatis sesuai warna pemain (putih di bawah kalo pake putih, hitam di bawah kalo pake hitam).