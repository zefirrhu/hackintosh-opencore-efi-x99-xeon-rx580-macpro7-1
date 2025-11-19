# OpenCore EFI — Amentmen X99-B4 (macOS Tahoe 26.x)

This repository contains a customized and fully adapted OpenCore EFI for running **macOS Tahoe 26.x** on an Intel® Xeon® workstation built around the **Amentmen X99-B4** motherboard. The distributed EFI is optimized for the hardware profile listed below and includes recommended ACPI patches, drivers and kexts. Sensitive Apple identifiers have been removed — see the sections **“Removed / Sanitized fields”** and **“Fields you MUST fill manually”**.

**Tested SMBIOS:** `MacPro7,1`

---

## Table of contents

* [Target hardware](#target-hardware)
* [Repository contents](#repository-contents)
* [Removed / sanitized fields](#removed--sanitized-fields)
* [Fields you MUST fill manually before first boot](#fields-you-must-fill-manually-before-first-boot)
* [Boot notes and known requirements](#boot-notes-and-known-requirements)
* [Recommended deployment workflow](#recommended-deployment-workflow)
* [FAQ](#faq)
* [License & legal notice](#license--legal-notice)

---

## Target hardware

> This is the hardware profile used during development and verification. If you deploy the EFI on a different motherboard or configuration, manual adjustments will likely be required.

* **Motherboard:** Amentmen X99-B4 (LGA 2011-3; Intel® X99 / Intel® C612)
* **CPU:** Intel® Xeon® Processor E5-2697A v4 — Broadwell-EP family (16 cores / 32 threads, base 2.60 GHz, Turbo up to 3.60 GHz, 40 MB L3 Smart Cache, TDP 145 W)
* **Memory:** Micron DDR4 ECC — 8 × 4 GB (32 GB total)
* **GPU:** AMD Radeon™ RX 580 — 8 GB GDDR5 (PCIe 3.0 x16, 2304SP, Video BIOS: 113-BE366EU-Z46, 015.050.002.001.000000)
* **Primary storage:** Samsung 980 M.2 NVMe SSD
* **External NIC:** TP-Link TX201 (Realtek® RTL8125-based, 2.5 Gbps)
* **Onboard NIC:** Realtek RTL8111 series (Gigabit)
* **TPM:** No TPM 2.0 module present

---

## Repository contents

The repository layout below describes the files included inside the distributed EFI bundle. Personal Apple identifiers and other sensitive data have been sanitized and are **not** included.

```
.
├── EFI
│   ├── BOOT
│   └── OC
│       ├── ACPI
│       ├── Drivers
│       ├── Kexts
│       ├── OpenCore.efi
│       ├── Resources
│       ├── Tools
│       └── config.plist
├── LICENSE
└── README.md
```

**Important:** This repository intentionally does not contain Apple identifiers, iMessage tokens, serials, or other private material. Descriptive placeholders are used where personal values must be supplied.

---

## Removed / sanitized fields

To avoid accidental disclosure of personal Apple identifiers, the following fields were removed or replaced with placeholders in `EFI/OC/config.plist`:

* `SystemSerialNumber`
* `MLB` (Main Logic Board)
* `SystemUUID`
* `ROM` (MAC/ROM)

All removed entries are replaced with the placeholder `<REMOVED>` in `config.plist`.

---

## Fields you MUST fill manually before first boot

**Warning:** Do not reuse serials, MLB values, or other Apple identifiers that belong to other machines or people. Using shared or publicly available identifiers may cause Apple services to fail and can lead to account lockouts.

Before first boot, open `EFI/OC/config.plist` in ProperTree (or another plist editor) and fill the following fields under `PlatformInfo -> Generic`:

* `SystemSerialNumber` — **REQUIRED** if you intend to use Apple services (iMessage, FaceTime, iCloud).
* `MLB` — **REQUIRED** for Apple services.
* `SystemUUID` — **REQUIRED** for Apple services.
* `ROM` — set to the appropriate Spoofed ROM / MAC if needed.

These fields are intentionally left as placeholders in the distributed EFI.

---

## Boot notes and known requirements

* **SMBIOS used for testing:** `MacPro7,1`
* **Audio:** `AppleALC` kext is included and configured for the target board.
* **Boot argument:** During development the system booted reliably only with the kernel argument:

```text
npci=0x2000
```

This boot-arg resolves PCI enumeration / IRQ assignment issues observed on some X99 implementations. It is included as a recommended boot-arg in the provided `config.plist`. If you experience different behavior on your hardware, adjust boot-args as needed.

---

## Recommended deployment workflow

1. Clone or download this repository.
2. Copy the contents of the `EFI/` directory to the target machine's EFI System Partition (FAT32) at the standard mount point `EFI/`.
3. Open `EFI/OC/config.plist` with ProperTree and set the required fields listed in **“Fields you MUST fill manually”**.
4. Validate the configuration:

```bash
ocvalidate EFI/OC/config.plist
```

5. Boot the target machine from USB or from the internal disk and use the OpenCore picker to select the installer or boot volume.
6. If the boot process stalls, boot with `-v` (verbose) and review the log messages. Adjust ACPI patches, kext selection, or boot-args as required for your hardware.

---

## FAQ

**Q: Can I use this EFI on another motherboard?**
A: Not recommended. ACPI patches, SSDTs, and kext tuning are specific to the Amentmen X99-B4 platform. A different motherboard will typically require rework of ACPI and retuning of kexts and drivers.

**Q: Why is `npci=0x2000` necessary?**
A: On certain X99 platform implementations, `npci=0x2000` resolves PCI initialization and IRQ assignment problems during early boot. It was required for stable boot during development on the target system and is provided as a recommended boot-arg.

---

## License & legal notice

* **License:** MIT. See the `LICENSE` file for full terms.
* **Legal disclaimer:** Running macOS on non-Apple hardware may violate Apple’s macOS Software License Agreement. This repository provides configuration files and educational artifacts only. You are responsible for ensuring compliance with any applicable license terms and laws in your jurisdiction.
