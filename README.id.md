<div align="center">

<img src="assets/cryo-kernel.jpg" alt="Cryo Kernel - Reborn from a Dark Destiny" width="100%">

# ❄️ Cryo Kernel Builder

**Reborn from a Dark Destiny**

Pipeline GitHub Actions untuk membangun, mem-patch, dan mengemas **Cryo Kernel**.

[![License: GPL-3.0](https://img.shields.io/badge/license-GPL--3.0-blue.svg)](LICENCE)
[![Built with GitHub Actions](https://img.shields.io/badge/built%20with-GitHub%20Actions-2088FF.svg)](https://github.com/Codename-riho/Kernel_builder/actions)

[🇬🇧 English](README.md) · 🇮🇩 Bahasa Indonesia

</div>

---

## 📖 Tentang

Kernel_builder adalah sistem build di balik **Cryo Kernel**. Semuanya berjalan di GitHub Actions, jadi tidak perlu toolchain lokal. Pilih workflow, jalankan, lalu unduh zip **AnyKernel3** siap flash dari artifact hasil run.

## ✨ Fitur

- **Matriks build GKI / CLO**, masing-masing dengan varian **KernelSU (KSU)** atau **Tanpa KSU**
- **KernelSU-Next** dengan **patch multi-manager** buatan sendiri (lihat di bawah)
- Integrasi **SUSFS** lewat susfs4ksu
- Pengemasan **AnyKernel3** dengan banner installer Cryo
- Toolchain berbasis `android-tools` (`build/build.sh`)

## ⚙️ Workflow

Workflow berada di [`.github/workflows`](.github/workflows):

| Workflow | Fungsi |
| --- | --- |
| `Build Cryo.yml` | Build kernel utama: matriks GKI / CLO × KSU / Tanpa KSU |
| `Analyze susfs.yml` | Membandingkan commit SUSFS pinned vs terbaru untuk GKI / CLO |
| `Mirror-SUSFS.yml` | Sinkronisasi mirror susfs4ksu ke repo ini (terjadwal) |
| `Upstream_kernel_source.yml` | Sinkronisasi source kernel upstream (merge / mirror / fresh) |

> Nama file workflow bisa berubah. Tab **Actions** selalu menampilkan daftar terbaru.

## 🧩 Dukungan multi-manager (KernelSU-Next)

Cryo Kernel membawa patch yang membuat kernel memercayai lebih dari satu tanda tangan manager KernelSU, termasuk manager versi **spoofed** yang memakai package name acak.

- Beberapa tanda tangan dipercaya, tetapi **maksimal 2 manager aktif bersamaan**.
- Hanya UID aplikasi asli (10000-19999) yang bisa menjadi manager.
- Secara bawaan hanya **pemilik (user 0)** yang bisa memakai manager. Salinan di user sekunder atau dual apps ditolak. Build dengan `KSU_MANAGER_ALL_USERS=1` untuk mengizinkan semua user.
- Panggilan driver yang khusus manager juga menolak kernel thread dan proses yang euid-nya berbeda dari uid.
- Daftar tanda tangan bisa diganti lewat `KSU_NEXT_MANAGER_LIST` (format: `<ukuran sertifikat>:<sha256 sertifikat>`, dipisah koma).

**Penting:** dalam praktiknya pakai **satu aplikasi manager saja dalam satu waktu**. Semua manager berbagi satu `/data/adb/ksud`, dan manager dengan protokol ioctl yang berbeda dari driver KernelSU-Next tidak akan berfungsi. Setiap manager aktif punya kendali root penuh, jadi pasang hanya manager yang Anda percaya.

## 🚀 Cara pakai

1. Buka tab **Actions** di repositori ini.
2. Pilih workflow lalu tekan **Run workflow**.
3. Isi input yang tersedia (varian, KSU / Tanpa KSU, SUSFS, dan sebagainya).
4. Setelah run selesai, unduh **zip AnyKernel3** dari artifact.
5. Flash lewat custom recovery atau aplikasi kernel flasher pilihan Anda.

## ⚠️ Peringatan

Memasang kernel kustom bisa menyebabkan bootloop atau kehilangan data. Cadangkan data dan `boot.img` asli Anda terlebih dahulu. Risiko ditanggung sendiri; proyek ini diberikan tanpa garansi.

## 📜 Lisensi

Dirilis di bawah [GNU General Public License v3.0](LICENCE).

## 🙏 Kredit

- [KernelSU-Next](https://github.com/KernelSU-Next/KernelSU-Next) dan proyek KernelSU, yang juga memiliki banyak sekali varian
- [susfs4ksu](https://gitlab.com/simonpunk/susfs4ksu) Fitur SUSFS
- [AnyKernel3](https://github.com/osm0sis/AnyKernel3) sebagai kerangka kerja yang memungkinkan kernel ini di-flash secara optimal
- Karya seni spanduk (banner) adalah milik pemilik aslinya.
- [yougotme101](https://github.com/yougotme101/kernel_msm-5.15) Untuk pohon dasar MSM-5.15 yang luar biasa solid dan kerangka kerja alurnya
- [topnotchfreaks](https://github.com/topnotchfreaks/kernel_msm-5.15) Untuk sinkronisasi cabang Codelinaro (CLO) yang brilian dan tambalan esensial yang mendorong performa kernel ini ke puncak maksimalnya.
