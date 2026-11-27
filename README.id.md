<div align="center">

<img src="https://raw.githubusercontent.com/Codename-riho/Kernel_builder/Builders/assets/cryo-kernel.jpg" alt="Cryo Kernel - Reborn from a Dark Destiny" width="100%">

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

- Beberapa tanda tangan dipercaya, tetapi **maksimal 1 manager aktif bersamaan**.
- Hanya UID aplikasi asli (10000-19999) yang bisa menjadi manager.
- Secara bawaan hanya **pemilik (user 0)** yang bisa memakai manager. Salinan di user sekunder atau dual apps ditolak. Build dengan `KSU_MANAGER_ALL_USERS=1` untuk mengizinkan semua user.
- Panggilan driver yang khusus manager juga menolak kernel thread dan proses yang euid-nya berbeda dari uid.
- Daftar tanda tangan bisa diganti lewat `KSU_NEXT_MANAGER_LIST` (format: `<ukuran sertifikat>:<sha256 sertifikat>`, dipisah koma).
- Tanda tangan bisa **ditambahkan** ke daftar bawaan lewat `KSU_NEXT_MANAGER_EXTRA` (format sama). Jika manager menampilkan "tidak ada driver", jalankan `dmesg | grep "manager candidate"`: setiap APK yang dipindai dicatat bersama `sha256` sertifikatnya, dan hash itulah yang dimasukkan ke `KSU_NEXT_MANAGER_EXTRA`.

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

Kode dan patch pihak ketiga tetap memakai lisensi aslinya (misalnya kernel Linux GPL-2.0, dan TCP BBRv3 dual BSD/GPL). Lihat kredit di bawah untuk sumbernya.

## 🙏 Kredit

Proyek ini berdiri di atas karya banyak pihak. Terima kasih kepada semua yang tercantum di bawah.

**Source kernel**
- [Google AOSP `common` (android13-5.15)](https://android.googlesource.com/kernel/common/) source dasar GKI
- CodeLinaro (CLO) msm-5.15 source dasar vendor Qualcomm
- [MiCode/Xiaomi_Kernel_OpenSource](https://github.com/MiCode/Xiaomi_Kernel_OpenSource) source kernel Xiaomi
- [yougotme101](https://github.com/yougotme101/kernel_msm-5.15) Untuk pohon dasar MSM-5.15 yang luar biasa solid dan kerangka workflow-nya
- [topnotchfreaks](https://github.com/topnotchfreaks/kernel_msm-5.15) Untuk sinkronisasi cabang Codelinaro (CLO) yang brilian, serta [kernel_patches](https://github.com/topnotchfreaks/kernel_patches) (patch gki-fixup dan localversion) yang mendorong performa kernel ini ke puncak maksimalnya

**Root dan manager**
- [KernelSU](https://github.com/tiann/KernelSU) (tiann) dan [KernelSU-Next](https://github.com/KernelSU-Next/KernelSU-Next), dasar proyek ini, yang juga memiliki banyak sekali varian
- [pershoot/KernelSU-Next](https://github.com/pershoot/KernelSU-Next) fork KernelSU-Next yang dipakai sebagai varian manager
- [Baka-SU/BakaSU](https://github.com/Baka-SU/BakaSU) varian manager (default)
- [SukiSU-Ultra](https://github.com/SukiSU-Ultra/SukiSU-Ultra) varian manager, termasuk KPM
- [susfs4ksu](https://gitlab.com/simonpunk/susfs4ksu) (simonpunk) fitur SUSFS

**Fitur dan patch kernel**
- **TCP BBRv3 + PLB:** tim Google BBR (Neal Cardwell, Yuchung Cheng, Soheil Hassas Yeganeh, Van Jacobson, dan lainnya) [google/bbr](https://github.com/google/bbr); di-backport lewat [WildKernels/kernel_patches](https://github.com/WildKernels/kernel_patches), dengan port oleh yougotme101 dan topnotchfreaks
- [dakkshesh07/Kprofiles](https://github.com/dakkshesh07/Kprofiles) Kprofiles
- [vc-teahouse/Baseband-guard](https://github.com/vc-teahouse/Baseband-guard) Baseband-guard
- [maxsteeel/nomount](https://github.com/maxsteeel/nomount) nomount
- [ravindu644/Droidspaces-OSS](https://github.com/ravindu644/Droidspaces-OSS) patch kernel GKI untuk Droidspaces
- [RapliVx](https://github.com/RapliVx) Untuk basis patch KSUN Multi Manager
- [NotPichayut](https://github.com/NotPichayut) Untuk basis script Kernel Version Spoof
- [kusonekoworld-max](https://github.com/kusonekoworld-max) –  Base SUSFS Script, dan implementasi Reflex + Vorpal Governor (untuk build gemink)

**Toolchain dan alat build**
- Google AOSP Clang (`android-tools`, `build/build.sh`)
- [Neutron-Toolchains/antman](https://github.com/Neutron-Toolchains/antman), [XSans0/WeebX-Clang](https://github.com/XSans0/WeebX-Clang), [greenforce-project/greenforce_clang](https://github.com/greenforce-project/greenforce_clang), [ZyCromerZ/Clang](https://github.com/ZyCromerZ/Clang) toolchain Clang kustom
- [AnyKernel3](https://github.com/osm0sis/AnyKernel3) (osm0sis) kerangka kerja yang memungkinkan kernel ini di-flash secara optimal
- [pierotofy/set-swap-space](https://github.com/pierotofy/set-swap-space) GitHub Action untuk swap

**Lainnya**
- Karya seni spanduk (banner) adalah milik seniman aslinya.
