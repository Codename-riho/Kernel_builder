<div align="center">

<img src="assets/cryo-kernel.jpg" alt="Cryo Kernel - Reborn from a Dark Destiny" width="100%">

# ❄️ Cryo Kernel Builder

**Reborn from a Dark Destiny**

GitHub Actions pipelines that build, patch and package **Cryo Kernel**.

[![License: GPL-3.0](https://img.shields.io/badge/license-GPL--3.0-blue.svg)](LICENCE)
[![Built with GitHub Actions](https://img.shields.io/badge/built%20with-GitHub%20Actions-2088FF.svg)](https://github.com/Codename-riho/Kernel_builder/actions)

🇬🇧 English · [🇮🇩 Bahasa Indonesia](README.id.md)

</div>

---

## 📖 About

Kernel_builder is the build system behind **Cryo Kernel**. Everything runs on GitHub Actions: no local toolchain is needed. Pick a workflow, run it, and download a flashable **AnyKernel3** zip from the run artifacts.

## ✨ Features

- **GKI / CLO build matrix**, each with **KernelSU (KSU)** or **No-KSU** variants
- **KernelSU-Next** with a custom **multi-manager patch** (see below)
- **SUSFS** integration through susfs4ksu
- **AnyKernel3** packaging with a Cryo installer banner
- Toolchain based on `android-tools` (`build/build.sh`)

## ⚙️ Workflows

Workflows live in [`.github/workflows`](.github/workflows):

| Workflow | Purpose |
| --- | --- |
| `Build Cryo.yml` | Main kernel build: GKI / CLO × KSU / No-KSU matrix |
| `Analyze susfs.yml` | Compare pinned vs latest SUSFS commits for GKI / CLO |
| `Mirror-SUSFS.yml` | Scheduled mirror sync of susfs4ksu into this repo |
| `Upstream_kernel_source.yml` | Sync upstream kernel source (merge / mirror / fresh) |

> Workflow file names can change over time. The **Actions** tab always shows the current list.

## 🧩 Multi-manager support (KernelSU-Next)

Cryo Kernel ships a patch that lets the kernel trust more than one KernelSU manager signature, including **spoofed** manager builds that use a random package name.

- Several signatures are trusted, but **at most 2 managers are active at the same time**.
- Only real app UIDs (10000-19999) can become managers.
- By default only the **owner (user 0)** can use a manager. Copies in secondary users or dual apps are rejected. Build with `KSU_MANAGER_ALL_USERS=1` to allow every user.
- Driver calls that are reserved for managers also reject kernel threads and processes whose euid differs from their uid.
- Override the trusted list with `KSU_NEXT_MANAGER_LIST` (format: `<cert size>:<sha256 of cert>`, comma-separated).

**Important:** use only **one manager app at a time** in practice. All managers share a single `/data/adb/ksud`, and a manager that uses a different ioctl protocol than the KernelSU-Next driver will not work. Every active manager has full root control, so only install managers you trust.

## 🚀 Usage

1. Open the **Actions** tab of this repository.
2. Choose a workflow and press **Run workflow**.
3. Set the inputs (variant, KSU / No-KSU, SUSFS, and so on).
4. When the run finishes, download the **AnyKernel3 zip** from the artifacts.
5. Flash it with a custom recovery or a kernel flasher of your choice.

## ⚠️ Disclaimer

Flashing a custom kernel can cause boot loops or data loss. Back up your data and your original `boot.img` first. You do this at your own risk; the project is provided without warranty.

## 📜 License

Released under the [GNU General Public License v3.0](LICENCE).

## 🙏 Credits

- [KernelSU-Next](https://github.com/KernelSU-Next/KernelSU-Next) and the KernelSU project
- [susfs4ksu](https://gitlab.com/simonpunk/susfs4ksu)
- [AnyKernel3](https://github.com/osm0sis/AnyKernel3)
- Banner artwork belongs to its original owner.
- [yougotme101](https://github.com/yougotme101/kernel_msm-5.15) For the incredibly solid MSM-5.15 base tree and the workflow framework
- [topnotchfreaks](https://github.com/topnotchfreaks/kernel_msm-5.15) For the brilliant Codelinaro (CLO) branch synchronization and the essential patches that push this kernel's performance to its absolute peak.

