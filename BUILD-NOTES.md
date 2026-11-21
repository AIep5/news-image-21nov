# BUILD-NOTES — news-image-20261121

Entri make.fun: halaman depan koran "THE WORLD TODAY" untuk
Sabtu 21 November 2026. Slug: `news-image-20261121`.
Daftar berita dipakai tepat dari `STORIES.md` (1 lead + 6 grid +
strip + 6 In Brief), termasuk aturan "reportedly" untuk B3 dan
pembingkaian A2 hanya sebagai konferensi pers 21 Nov.

## Cara kode ditulis

Semua kode ditulis baru di sesi ini, dengan kata-kata sendiri,
tanpa membaca atau menyalin `main.py`, data font, atau generator
font edisi 17/18/19 Nov, dan tanpa menyentuh direktori kit.
Struktur program ini berbeda dari edisi sebelumnya: satu class
`Sheet` yang memegang buffer RGB dan primitifnya sendiri
(`fill`, `frame`, `line`, `disc`, `ring`, `ellipse`, `poly`,
`star`), mesin teks bitmap dengan pembungkus kata berbasis lebar
advance, penulis PNG sendiri (zlib + struct), dan delapan fungsi
ilustrasi terpisah yang masing-masing digambar di dalam kotak
gambarnya sendiri dengan clip aktif — tidak ada gambar yang
bocor keluar dari kotaknya.

## Font

Font tertanam di `main.py` sebagai tabel bitmap yang diraster
sendiri di sesi ini oleh skrip pembantu di `/tmp` memakai PIL
lokal dari font sistem DejaVu (Serif Bold dan Sans/Sans Bold).
Sembilan tabel yang dipakai:
Serif Bold 54 (masthead), 38 (lead), 26 (strip), 22 (grid);
Sans Bold 17 (pita tanggal) dan 14 (label, brief); Sans 15
(deck lead), 14 (deck grid/strip), 12 (deck brief). Setiap glif
ASCII 32–126 menyimpan advance, ukuran, offset, dan bit tinta
terkompresi base64. `main.py` tidak membaca file apa pun saat
berjalan: tabelnya ada di dalam berkas itu sendiri, dan skrip
pembantu bukan bagian dari entri.

## Layout (1024×1024)

- Bar atas hitam (tanggal, sighting 1) → masthead serif besar
  → pita tanggal merah (sighting 2) → lead satu baris headline
  penuh + deck 3 baris di kiri dan ilustrasi langit malam di kanan.
- Grid 3 kolom × 2 baris: label, headline 1–2 baris, strip
  ilustrasi 44 px, deck 3 baris.
- Strip sains: ilustrasi nyamuk di kiri, headline + deck 2 baris
  di kanan.
- In Brief: 3 kolom × 2 baris, kotak nomor merah + headline
  satu baris + deck 2 baris.
- Footer hitam (tanggal, sighting 3). Tepi bawah footer tepat
  di 1024.

Sebelum render, semua deck diukur dengan lebar font yang sama
yang dipakai program, dan teks dipangkas sampai bungkusnya
muat: lead 3 baris, tiap grid 3 baris, strip 2 baris, tiap
brief 2 baris. Dua headline brief dipendekkan agar muat di
kolomnya ("KRYVYI RIH: BOY, MEDIC KILLED",
"FOUR KILLED IN BASRA FIRE"); detail lengkapnya tetap ada di
deck masing-masing.

## Pengujian (kode final, setelah perbaikan visual)

1. **Determinisme run normal:** dua run berurutan menghasilkan
   byte identik, sha256
   `d47b702ee7c6fd4f7c1f370a8690f1a8098e3ae502133ac395b8488530499130`.
2. **Gaya grader:** salinan `main.py` saja di direktori bersih,
   `env -i ... python3 -P -s main.py` dengan
   `MAKEFUN_OUTPUT_DIR` ke direktori tulis terpisah → sha256
   sama persis dengan run normal
   (`d47b702e…0499130`).
3. **Ukuran:** 1024×1024, 42.546 byte (≤ 1 MiB).
4. **Perilaku default:** tanpa argumen dan tanpa env, program
   menulis ke `/output/entry.png` (membuat direktorinya bila
   perlu); argumen baris perintah mengesampingkan env.

## Inspeksi visual (yang diperiksa, dan yang diperbaiki)

PNG hasil render dibaca kembali dan diperiksa penuh, lalu
diperiksa lagi per potongan yang diperbesar (grid baris 1,
bagian In Brief) plus pindai piksel pada baris headline brief:

- Semua teks terbaca penuh: tidak ada headline/deck yang
  terpotong di tepi kanan atau bawah, tidak ada deck yang
  menimpa ilustrasi atau baris lain, dan semua 14 cerita
  hadir dengan headline + deck + ilustrasi (brief tanpa
  ilustrasi, sesuai desain).
- Tanggal terlihat tiga kali dan terbaca: bar atas, pita
  merah, footer.
- Cacat yang ditemukan dan diperbaiki: (a) teks "DOWN" pada
  ilustrasi saham tertutup batang diagram — teks dihapus dan
  batang diskalakan ulang agar turunnya terbaca; (b) figur
  pada ilustrasi KTT tertutup bentuk meja — meja diganti
  pita tipis di bawah dan figur digambar utuh di atasnya;
  (c) garis diagonal merah pada ilustrasi tanker melintasi
  langit — dihapus, superstruktur kini duduk di atas lambung;
  (d) headline brief 5 yang terlalu panjang pada draf awal
  meluber ke kolom sebelah — dipendekkan dan lebarnya
  diukur ulang (251 px dari 289 px yang tersedia).
- Sisa yang diketahui, diterima apa adanya: ilustrasi
  sengaja datar dan skematis (nyamuk, globe iklim, kerumunan
  pawai) — bentuknya sederhana, bukan gambar realistis.
  Pada tampilan thumbnail yang diperkecil, headline brief 6
  tampak menyentuh kotak nomornya; pindai piksel pada resolusi
  penuh membuktikan ada jeda 7 px antara kotak nomor (berakhir
  x=701) dan tinta headline pertama (x=709), dan headline
  brief 5 berakhir di x=635, jauh sebelum kolom 6 (x=684) —
  tidak ada tumpang tindih.

## File

- `main.py` — program entri (root repo, self-contained)
- `LICENSE` (MIT), `README.md`
- `STORIES.md` — daftar berita + sumber (bukan bagian repo)
- `BUILD-NOTES.md` — file ini (bukan bagian repo)
- `out/entry.png` — hasil render lokal final (42.546 byte)
