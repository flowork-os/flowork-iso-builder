# 📀 FLOWORK OS — PRODUCTION LIVE ISO MASTERING PIPELINE

[![Flowork OS](https://img.shields.io/badge/Flowork%20OS-Linux%207.3.0--Rust-00ff66?style=for-the-badge&logo=linux&logoColor=black)](https://floworkos.com)
[![License](https://img.shields.io/badge/License-GPLv3%2FProprietary-cyan?style=for-the-badge)](LICENSE)
[![Release](https://img.shields.io/badge/Release-v7.3.0-00f2fe?style=for-the-badge)](https://github.com/flowork-os/flowork-os/releases)

Sovereign, reproducible, and automated hybrid UEFI / Legacy BIOS Live ISO mastering toolkit for **Flowork OS**. Compresses the live running system into a multi-threaded XZ SquashFS image paired with Calamares Sovereign Optical Glass Installer.

---

## 🌟 Key Architecture & Capabilities

1. **Hybrid Dual-Boot Mastering (`xorriso` + `isolinux` + `grub2-efi`)**:
   - Produces a single hybrid `.iso` bootable across both modern UEFI (64-bit with shimless GRUB) and legacy BIOS systems.
   - Embeds signed EFI boot structures (`BOOTX64.EFI`, `grubx64.efi`, `mmx64.efi`) and isolinux fallback.

2. **Calamares Sovereign Optical Glass Installer**:
   - Pre-configured automated installer with disk encryption, BTRFS/EXT4 partition layouts, and zero-leak configuration scrubbing.
   - Custom optical glass HUD theme matching the Flowork OS desktop aesthetic.

3. **Autonomous On-Demand Subsystem Decoupling**:
   - Large runtime binaries (~3.7 GB total: Android Micro-VM SDK, Wine-Staging, OpenScreen, Audacity AI, Social Auto Upload) are decoupled into GitHub release tarballs.
   - Live ISO remains ultra-lean (5.6 GB), while installed systems hydrate subsystems automatically via `flowork-hydrate`.

4. **Universal GPU & Low-End PC Resilience**:
   - Automatically detects GPUs: Enforces Google SwiftShader CPU software rendering on Intel Core i3 / systems without discrete GPUs to eliminate `gfxstream` crashes.
   - Dynamic memory scaling preventing Linux Out-of-Memory (OOM) killer terminations.

5. **Sovereign Anti-Leak & Sanitization Shield**:
   - Strips transient tokens, personal credentials, private keys, logs, shell history, and user artifacts.
   - Replaces credentials with sanitized default templates (`vault.clean.json` chmod 0600, default user `flowork` / password `flowork`).

---

## 📁 Repository Structure

```text
flowork-iso-builder/
├── boot-skeleton/             # Bootloader skeletons (EFI, ISOLINUX, GRUB)
│   ├── boot/grub/efi.img      # FAT16 EFI boot partition image
│   ├── EFI/BOOT/              # UEFI x86_64 bootloader binaries
│   └── isolinux/              # BIOS syslinux/isolinux binaries & configs
├── build-live-iso.sh          # Master Live ISO compilation script
├── package-assets.sh          # Decoupled on-demand subsystem packager
├── release-iso.sh            # GitHub Releases atomic split-publisher (<2GB chunks)
├── .gitignore                 # Strict zero-leak security shield
└── README.md                  # This documentation
```

---

## 🚀 Quick Start Guide

### 1. Pre-flight Verification
Run a non-destructive audit of system tools, kernel configurations, and Calamares setup:
```bash
sudo ./build-live-iso.sh check
```

### 2. Build Production Live ISO
Compile the complete hybrid bootable Live ISO:
```bash
sudo ./build-live-iso.sh build
```
The output image is generated at:
```text
$HOME/Downloads/flowork-iso-build/FloworkOS-1.0-x86_64-Sovereign-Rust.iso
```

### 3. Flash to USB Drive (Bootable Live USB)
Identify your USB flash drive (e.g. `/dev/sda` or `/dev/sdb` — **verify with `lsblk` first!**):
```bash
# 1. Unmount existing partitions
sudo umount /dev/sdX* 2>/dev/null || true

# 2. Write image with direct sync
sudo dd if=$HOME/Downloads/flowork-iso-build/FloworkOS-1.0-x86_64-Sovereign-Rust.iso \
        of=/dev/sdX bs=4M status=progress conv=fsync oflag=direct
```

---

## 📦 Automated Release Publishing

To publish the ISO to GitHub Releases, the publisher splits the >5GB ISO into <1.8 GiB chunks complying with GitHub asset upload limits:
```bash
./release-iso.sh v7.3.0 \
  --iso "$HOME/Downloads/flowork-iso-build/FloworkOS-1.0-x86_64-Sovereign-Rust.iso" \
  --update
```

Reassembly on client machines is fully automated:
- **Linux / macOS**: `bash join-iso.sh`
- **Windows**: Double-click `join-iso.bat`

---

## 🔒 Security & Quality Gates
- **Zero Hardcoded Paths**: System assets dynamically resolve relative to `$FLOWORK_ROOT` (`/opt/floworkos`).
- **Cryptographic Integrity**: SHA-256 signatures generated and verified before and after split/join operations.
- **Empirical Proof**: Every pipeline stage verified with terminal Exit Code 0.

---
© 2026 Flowork OS Architecture Board. All rights reserved.
