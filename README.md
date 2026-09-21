# Cyber Hero — WebAR (ZapWorks / Zappar for A-Frame)

Satu halaman AR ini memuat **4 target sekaligus** (Zappar menyebutnya "image target").
Peserta cukup memindai QR **satu kali**, lalu mengarahkan kamera ke salah satu dari
4 ikon misi di flyer — kontennya berganti otomatis, tanpa scan ulang.

File `.zpt` (target Zappar) **sudah tersedia dan siap pakai** — tidak perlu training ulang,
tidak perlu akun ZapWorks, tidak perlu CLI. Tinggal host lalu jalan.

## Isi folder
```
arproj_zappar/
├── index.html            ← halaman AR (sudah lengkap, tidak perlu diedit kalau tidak mau)
└── targets/
    ├── misi1.png / misi1.zpt   ← Misi 1 · Password Guardian (biru)
    ├── misi2.png / misi2.zpt   ← Misi 2 · Phishing Hunter (kuning)
    ├── misi3.png / misi3.zpt   ← Misi 3 · Privacy Defender (hijau)
    └── misi4.png / misi4.zpt   ← Misi 4 · Malware Escape (merah)
```
File `.png` adalah gambar yang dicetak di flyer sebagai "ikon pemicu AR".
File `.zpt` adalah hasil training dari gambar itu, dibaca oleh `index.html`.

## Langkah 1 — Host lewat HTTPS
Kamera hanya bisa diakses lewat HTTPS. Cara termudah dan gratis: **GitHub Pages**.
1. Buat repository baru di GitHub, misalnya `cyber-hero-ar`.
2. Unggah seluruh isi folder `arproj_zappar` (index.html + folder `targets/` lengkap).
3. Buka **Settings → Pages** → pilih branch `main`, folder `/ (root)` → **Save**.
4. Tunggu 1–2 menit, link akan muncul, contoh:
   `https://namamu.github.io/cyber-hero-ar/`

*(Alternatif: ZapWorks CLI `zapworks serve .` untuk uji cepat di jaringan lokal — lihat
dokumentasi di docs.zap.works/universal-ar/zapworks-cli — tapi untuk publikasi ke publik
tetap perlu hosting HTTPS seperti GitHub Pages.)*

## Langkah 2 — Uji di HP
Buka link tadi di **Chrome/Safari**, izinkan akses kamera, lalu arahkan ke salah satu
gambar `misi1.png`–`misi4.png` (di layar lain atau hasil cetak). Objek 3D dan teks tips
akan muncul mengambang di atas gambar tersebut.

## Langkah 3 — Kirim link ke flyer
Kirim link GitHub Pages kamu — flyer akan diperbarui: **satu QR Code** mengarah ke link
itu, dan ke-4 gambar `misi1–4.png` dicetak sebagai ikon pemicu di tiap kartu misi
(gambar yang sama persis dengan yang sudah dipakai di flyer sebelumnya).

---

## Kalau ingin ganti konten 3D
Tiap misi ada di blok:
```html
<a-entity zappar-image="target: #target1"> ... </a-entity>
```
`target1`–`target4` sesuai `<a-asset-item>` di bagian `<a-assets>`. Saat ini isinya bentuk
3D primitif (torus, kerucut, dsb) + teks — sudah langsung bisa dipakai. Untuk model 3D
sendiri (`.glb`), tambahkan `<a-asset-item id="model" src="model.glb">` di `<a-assets>`
lalu pakai `<a-gltf-model src="#model">` di dalam blok misi terkait.

## Kalau ingin ganti gambar target
1. Ganti file PNG di `targets/` dengan gambar barumu (disarankan: detail, kontras
   tinggi, tidak pola berulang, minimal 300×300 px).
2. Latih ulang jadi `.zpt` — dua cara:
   - **ZapWorks CLI:** `zapworks train gambar-baru.png -o targets/misi1.zpt`
     (perlu login akun ZapWorks gratis: `zapworks login`)
   - **Tanpa akun**, pakai Node + paket `@zappar/imagetraining`:
     ```js
     import { train } from "@zappar/imagetraining";
     import { promises as fs } from "fs";
     const png = await fs.readFile("gambar-baru.png");
     const zpt = await train(png);
     await fs.writeFile("targets/misi1.zpt", Buffer.from(zpt));
     ```

## Troubleshooting singkat
- **Layar putih / kamera tidak nyala:** pastikan dibuka lewat HTTPS, bukan `file://`.
- **Muncul pesan "Browser tidak didukung":** itu `zappar-compatibility-ui` bawaan —
  gunakan Chrome (Android) atau Safari (iOS) versi terbaru.
- **Target tidak terbaca:** cetak gambar target minimal 6×6 cm, cahaya cukup, kertas
  tidak mengilap/silau.
