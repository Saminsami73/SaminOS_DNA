# SaminOS: 512-Byte Bare-Metal Operating System and 2,048 bp Synthetic DNA Genome

[![License: MIT](https://img.shields.io/badge/License-MIT-teal.svg)](LICENSE)
[![Architecture: x86 16-Bit Real Mode](https://img.shields.io/badge/Architecture-x86_16--Bit_Real_Mode-blue.svg)](samin_os.asm)
[![Binary Size: 512 Bytes](https://img.shields.io/badge/Binary_Size-512_Bytes-success.svg)](samin_os.bin)
[![DNA Length: 2,048 bp](https://img.shields.io/badge/DNA_Length-2%2C048_bp-emerald.svg)](SaminOS_v1_DNA.txt)
[![Location: Dhaka, Bangladesh](https://img.shields.io/badge/Origin-Dhaka%2C_Bangladesh-red.svg)](#creator-and-origin)

SaminOS is a self-contained operating system written in 16-bit x86 assembly that fits within a single 512-byte Master Boot Record sector. It boots directly on physical PC hardware or virtual machines without an underlying operating system or second-stage loader.

Through a deterministic 2-bit quaternary encoding standard (00=A, 01=C, 10=G, 11=T), the entire 512-byte operating system translates into an exact 2,048 base-pair DNA sequence. This length matches the size of a single natural bacterial gene, allowing SaminOS to exist as a single, unbroken molecule of synthetic DNA.

---

## Key Features

1. **Bare-Metal 512-Byte Footprint:** The compiled kernel, command dispatcher, hardware drivers, ASCII branding, and metadata occupy 508 bytes, followed by two null padding bytes and the 0x55AA boot signature.
2. **Interactive CLI Shell:** Features an active prompt (`samin> `) with keystroke buffering and destructive backspace editing (`\b \b`).
3. **Motherboard RTC Time Decoding:** Queries BIOS interrupt 0x1A, decodes packed BCD digits, applies single-cycle decimal adjustment (`DAA`), and displays local Bangladesh Standard Time (Dhaka GMT+6).
4. **Fast Two-Character Word Dispatcher:** Dispatches commands by evaluating 16-bit word prefixes (`he`, `in`, `ti`, `sa`, `cl`, `re`), executing in 28 bytes.
5. **Six Built-in Commands:**
   - `help`: Displays a vertical list of supported commands.
   - `info`: Displays author identity and build version.
   - `time`: Displays real-time hardware clock in Dhaka local time.
   - `say <text>`: Echoes user text arguments to the terminal teletype.
   - `cls`: Clears the screen and redraws the bold ASCII banner.
   - `reboot`: Invokes BIOS interrupt 0x19 for a warm software restart.
6. **Molecular Restriction Enzyme Surgery:** Machine code instructions create exact recognition sites for 19 commercial restriction enzymes:
   - *SmaI* and *XmaI* cut CCCGGG inside the 0x55AA boot signature (CCCCGGGG at bases 2042 and 2044), rendering the disk unbootable while preserving instructions.
   - *NsiI* cuts base 100 right before BIOS keyboard interrupt 0x16, disabling user input.
   - *AseI*, *PmlI*, and *MscI* slice through the real-time clock decoding math.
   - *AgeI* and *SalI* cut inside the ASCII letters of "Dhaka".
   - *StuI* cuts through the middle of the ASCII banner logo.
7. **Unbroken DNA Genome:** Because the operating system is only 2,048 base pairs long, it requires no fragmentation or assembly pools. The entire system exists on a single physical molecule.

---

## System Architecture

```text
Physical Memory Map (16-Bit Real Mode):
+--------------------+ 0000:0000h
| Interrupt Vectors  | (IVT: 1,024 Bytes, Vectors 0-255)
+--------------------+ 0000:0400h
| BIOS Data Area     | (BDA: 256 Bytes, Hardware State)
+--------------------+ 0000:0500h
| Free Low RAM       | Stack grows downward from 0x7C00
+--------------------+ 0000:7C00h
| SaminOS Kernel     | 512 Bytes (508B Code/Data + 2B Pad + 2B Sig)
+--------------------+ 0000:7E00h
| Input Line Buffer  | 24-Byte ephemeral scratchpad (CMD_BUF)
+--------------------+ 0000:7E20h
| Free Conventional  | Up to 640 KB RAM
+--------------------+ B800:0000h
| Video Color Text   | 80x25 16-Color Display Buffer
+--------------------+
```

---

## Repository Structure

```text
.
├── samin_os.asm                 # Raw x86 16-bit real-mode assembly source code
├── samin_os.bin                 # Compiled 512-byte Master Boot Record sector image
├── samin_os.img                 # 1.44MB bootable floppy / virtual machine disk image
├── samin_dna_tools.py           # Two-way binary and DNA conversion toolchain
├── SaminOS_v1_DNA.txt           # Complete 2,048 base-pair FASTA sequence file
├── EachDNAseqmeaning.txt        # Base-pair locus to RAM address translation table
├── 100 facts about SaminOS.txt  # Bio-digital dossier containing 151 scientific facts
├── LICENSE                      # MIT Open Source License
├── CITATION.cff                 # Citation metadata format
├── docs/                        # Complete technical documentation
│   ├── SaminOS_Complete_Guidebook.pdf      # 61-page comprehensive engineering manual
│   ├── SaminOS_Contra_Project_Description.md # Human story-based case study
│   └── SaminOS_Zenodo_Submission_Package.md  # Official Zenodo publication metadata
├── diagrams/                    # High-resolution architectural vector diagrams
│   ├── x86-architecture-light.png
│   ├── usb-boot-flow-light.png
│   └── dna-storage-pipeline-light.png
└── media/                       # Laboratory photography, animations, and videos
    ├── saminos_enzyme_showcase.jpg
    ├── saminos_in_a_tube.jpg
    ├── saminos_youtube_thumbnail.jpg
    ├── saminos_enzyme_showcase.mp4
    ├── saminos_enzyme_showcase.webp
    ├── saminos_in_a_tube.mp4
    └── saminos_in_a_tube.webp
```

---

## Quick Start Guide

### 1. Assembling the Source Code

Compile `samin_os.asm` into a raw 512-byte binary using the Netwide Assembler (NASM):

```bash
nasm -f bin samin_os.asm -o samin_os.bin
```

Verify that the output file is exactly 512 bytes:

```bash
ls -l samin_os.bin
# -rw-r--r-- 1 samin samin 512 samin_os.bin
```

### 2. Running in QEMU Emulator

To run SaminOS inside QEMU as a raw hard drive:

```bash
qemu-system-x86_64 -drive format=raw,file=samin_os.bin
```

To run SaminOS as a virtual floppy disk:

```bash
qemu-system-x86_64 -fda samin_os.bin
```

### 3. Flashing to a Physical USB Drive on Bare Metal

Write the 512-byte binary directly to Sector 0 of a USB flash drive. 

*Caution: Ensure you identify the correct USB drive letter to avoid overwriting host disks.*

On Linux or macOS:

```bash
sudo dd if=samin_os.bin of=/dev/sdX bs=512 count=1 conv=notrunc
sudo sync
```

Insert the USB drive into an x86 computer, restart, enter motherboard BIOS setup, enable Legacy BIOS / CSM boot mode, disable UEFI Secure Boot, select the USB drive as primary boot device, and restart.

---

## DNA Conversion Toolchain

Use `samin_dna_tools.py` to convert between raw binary bytes and DNA nucleotides:

```bash
# Convert 512-byte binary into 2,048 bp FASTA sequence
python samin_dna_tools.py --bin-to-dna samin_os.bin -o my_dna.fasta

# Convert FASTA sequence back into 512-byte binary
python samin_dna_tools.py --dna-to-bin my_dna.fasta -o restored_os.bin

# Run bidirectional round-trip verification with SHA-256 hash checks
python samin_dna_tools.py --verify
```

---

## Quaternary Encoding Specification

Each byte is split into four 2-bit dibits from MSB to LSB:

| 2-Bit Dibit | Nucleotide | Chemical Family | Molecular Weight |
| :--- | :--- | :--- | :--- |
| `00` | Adenine (A) | Purine | 135.13 g/mol |
| `01` | Cytosine (C) | Pyrimidine | 111.10 g/mol |
| `10` | Guanine (G) | Purine | 151.13 g/mol |
| `11` | Thymine (T) | Pyrimidine | 126.11 g/mol |

Example:
- Byte `0x31` (`00110001` in binary) encodes as `00` (A), `11` (T), `00` (A), `01` (C) -> **`ATAC`**.
- Sector signature `0x55AA` (`01010101 10101010`) encodes as **`CCCCGGGG`**.

---

## Creator and Origin

- **Creator:** Samin Sami (Full Name: Samin Yasar Al Sami)
- **Lab:** WeDevelop Systems Laboratory
- **Location:** Dhaka, Bangladesh
- **Contact:** samin.sami@10x.ai

---

## License and Citation

SaminOS software and source code are released under the [MIT License](LICENSE). Documentation, diagrams, and sequence specifications are licensed under the Creative Commons Attribution 4.0 International (CC-BY-4.0) License.
