## Hi there 👋 My name is k!M Olsen.
- 📫 How to reach me: kim.olsen@linux.com (email), kim@pizslacker.org (GTalk+GMeet)

---

## 🛠️ Tech Stack & Systems

- **Languages:** C, C++17, Rust, Go, Zig, Python, Bash, Lua, x86/ARM Assembly
- **Systems & Kernel:** Linux (Slackware, Debian, Fedora, Ubuntu, Mint), `x86_64` Long Mode, `PAE` Paging, POSIX Multithreading
- **Graphics & Audio:** Raylib, SDL2, Pygame, Custom GLSL Shaders, Procedural Audio
- **Security & Ops:** SIEM Monitoring, Incident Response & Automation

## 🚀 Featured Projects

### 🌟 [SectorZero-64](https://github.com/pizslacker/SectorZero-64)
*An open-source x86_64 bare-metal bootloader and C kernel (GPLv3).*
- Transitions from 16-bit real mode directly into 64-bit long mode.
- Implements custom PAE paging, PML4 table setup, and inline assembly CPUID feature checks.
- `Makefile` includes testing by emulation (`qemu`)
<details>
     <summary><b>🔍 View Bootloader Architecture & Mode Transition</b></summary>
     
     +-----------------------------------------------------------------------+
     |                       [16-BIT REAL MODE]                              |
     |  • BIOS loads Stage 1 MBR at physical address 0x7C00                  |
     |  • Enable A20 Line & load Stage 2 / Kernel via BIOS INT 0x13          |
     |  • Load 32-bit Global Descriptor Table (lgdt)                         |
     +-----------------------------------------------------------------------+
                                   |
                                   | Set CR0.PE = 1 (Protection Enable)
                                   | Far Jump (ljmp 0x08:protected_mode)
                                   v
     +-----------------------------------------------------------------------+
     |                     [32-BIT PROTECTED MODE]                           |
     |  • Reload data/stack segment registers (DS, SS, ES, FS, GS)           |
     |  • Check CPUID (0x80000001 -> EDX bit 29) for Long Mode support       |
     |  • Build 4-Level Page Tables (Identity Map):                          |
     |      [PML4] ---> [PDPT] ---> [PD] ---> [2MB Huge Pages / PT]          |
     +-----------------------------------------------------------------------+
                                   |
                                   | 1. Load PML4 base into CR3
                                   | 2. Set CR4.PAE = 1 (Physical Addr Ext)
                                   | 3. Set EFER.LME = 1 (MSR 0xC0000080)
                                   | 4. Set CR0.PG = 1 (Paging Enable)
                                   v
     +-----------------------------------------------------------------------+
     |                   [IA-32e COMPATIBILITY SUB-MODE]                     |
     |  • CPU is now in Long Mode, but executing 32-bit instructions         |
     |  • Load 64-bit GDT (Code Segment with L-bit = 1, D-bit = 0)           |
     +-----------------------------------------------------------------------+
                                   |
                                   | Far Jump (ljmp 0x08:long_mode_start)
                                   | Flushes instruction pipeline
                                   v
     +-----------------------------------------------------------------------+
     |                        [64-BIT LONG MODE]                             |
     |  • Setup 64-bit stack pointer (RSP) & clear segment registers         |
     |  • Jump to 64-bit C Kernel entry point (kmain)                        |
     +-----------------------------------------------------------------------+

</details>
<details>
     <summary><b>🔍 View Memory Map</b></summary>

     Physical Address                  Memory Region / Contents
     +------------------+---------------------------------------------------------+
     | 0x00000000       | Real Mode IVT (Interrupt Vector Table) & BIOS Data Area |
     |  ...             | (Reserved by BIOS during 16-bit initialization)         |
     +------------------+---------------------------------------------------------+
     | 0x00001000       | 64-Bit Page Tables (Identity Mapped, 4KB Aligned)       |
     |  ├─ 0x00001000   |  • PML4 (Page Map Level 4)            [CR3 Base]        |
     |  ├─ 0x00002000   |  • PDPT (Page Directory Pointer Table)                  |
     |  └─ 0x00003000   |  • PD   (Page Directory - 2MB Huge Pages)               |
     +------------------+---------------------------------------------------------+
     | 0x00007000       | Kernel / Bootloader Stack (Grows downward <-- )         |
     +------------------+---------------------------------------------------------+
     | 0x00007C00       | Stage 1 MBR Bootloader (512 Bytes, 0xAA55 Signature)    |
     +------------------+---------------------------------------------------------+
     | 0x00007E00       | Stage 2 Bootloader / GDT (32-bit & 64-bit Descriptors)  |
     |  ...             | (Or low-memory C Kernel entry at 0x8000 / 0x10000)      |
     +------------------+---------------------------------------------------------+
     | 0x000A0000       | Legacy Video RAM (VRAM)                                 |
     +------------------+---------------------------------------------------------+
     | 0x000B8000       | VGA Text Mode Buffer (80x25 chars, 2 bytes per cell)    |
     +------------------+---------------------------------------------------------+
     | 0x000C0000       | Video BIOS & Expansion ROMs                             |
     | 0x000F0000       | Motherboard BIOS ROM (64KB)                             |
     +==================+=========================================================+
     | 0x00100000 (1MB) | 64-Bit C Kernel (`kmain` ELF / Flat Binary Image)       |
     |  ...             | Free Extended Memory (Heap, Frame Allocator, Stack)     |
     +------------------+---------------------------------------------------------+

</details>

### 🌟 [SectorZero-ARM](https://github.com/pizslacker/SectorZero-ARM)
*A bare-metal ARM (AArch64) bootloader and freestanding 64-bit C kernel implementation built from scratch without external libraries or standard runtimes.*
- Instead of navigating real-mode to long-mode transitions, SectorZero-ARM boots directly into a flat 64-bit address space (AArch64), giving you immediate access to modern C development and Memory-Mapped I/O (MMIO).
<details>
     <summary><b>🔍 View Aarch644 Bootloader Pipeline</b></summary>

     +-----------------------------------------------------------------------+
     |                    [VIDEOCORE IV / VI GPU FIRMWARE]                   |
     |  • ROM Bootloader initializes SDRAM & reads FAT32 boot partition      |
     |  • Loads `kernel8.img` to physical address 0x00080000                 |
     |  • Releases ARM Cortex-A53 / A72 cores in 64-bit AArch64 state        |
     +-----------------------------------------------------------------------+
                                   |
                                   | All 4 Cores start at _start (0x80000)
                                   | Read MPIDR_EL1 (Aff0 bitmask 0x03)
                                   v
     +-----------------------------------------------------------------------+
     |                     [MULTI-CORE PARKING & EL CHECK]                   |
     |  • Cores 1, 2, 3 (Core ID != 0) ---> Enter low-power WFE loop         |
     |  • Core 0 (BSP) checks `CurrentEL` register:                          |
     |      - If EL3 (Secure Monitor): Configure SCR_EL3 & drop to EL2       |
     |      - If EL2 (Hypervisor):     Set HCR_EL2.RW = 1 (64-bit EL1)       |
     +-----------------------------------------------------------------------+
                                   |
                                   | 1. Disable EL1 traps (CPACR_EL1)
                                   | 2. Set SPSR_EL2 = 0x3C5 (EL1h, DAIF masked)
                                   | 3. Set ELR_EL2 = el1_entry
                                   | 4. Execute `eret` (Exception Return)
                                   v
     +-----------------------------------------------------------------------+
     |                      [EL1 KERNEL INITIALIZATION]                      |
     |  • Set 64-bit Stack Pointer (`SP_EL1`) below code base (0x00080000)   |
     |  • Zero-out `.bss` section via 64-bit `str xzr` loop                  |
     |  • Install Exception Vector Table (`VBAR_EL1` aligned to 2KB / 0x800) |
     |  • (Optional) Configure MMU: `MAIR_EL1`, `TCR_EL1`, `TTBR0_EL1`       |
     +-----------------------------------------------------------------------+
                                   |
                                   | Branch with Link (`bl kmain`)
                                   v
     +-----------------------------------------------------------------------+
     |                       [AArch64 C KERNEL (EL1)]                        |
     |  • Initialize PL011 / Mini-UART via MMIO for serial console           |
     |  • Query VideoCore Mailbox (Channel 8) for Board Rev & Framebuffer    |
     |  • Unmask IRQ (`msr daifclr, #2`) & start ARM Generic Timer           |
     +-----------------------------------------------------------------------+

</details>
<details>
     <summary><b>🔍 View Memory Map</b></summary>

     Physical Address                  Memory Region / Contents
     +------------------+---------------------------------------------------------+
     | 0x00000000       | ARM Stub / Spin Tables (Multicore release addresses)    |
     |  ├─ 0x000000D8   |  • Core 0–3 Mailbox Spin Addresses (0xD8, 0xE0, 0xE8..) |
     |  └─ 0x00000100   |  • Device Tree Blob (DTB) / ATAGs passed by GPU         |
     +------------------+---------------------------------------------------------+
     | 0x00001000       | Free Low Memory (Often used for Identity Page Tables)   |
     |  ...             |  • PGD -> PUD -> PMD (TTBR0_EL1 base if MMU enabled)    |
     +------------------+---------------------------------------------------------+
     | 0x0007FFFF       | EL1 Kernel Stack Top (`SP_EL1` grows downward <-- )     |
     +==================+=========================================================+
     | 0x00080000       | `kernel8.img` Load Address (`_start` Entry Point)       |
     |  ├─ .text.boot   |  • Core parking, EL2->EL1 drop, BSS clear               |
     |  ├─ .text / .ro  |  • AArch64 C Kernel (`kmain`), Vector Table (`VBAR_EL1`)|
     |  ├─ .data        |  • Initialized global variables                         |
     |  └─ .bss         |  • Zero-initialized data (Page-aligned end)             |
     +------------------+---------------------------------------------------------+
     | Kernel End       | Dynamic Memory / Kernel Heap & Page Frame Allocator     |
     +------------------+---------------------------------------------------------+
     | 0x3C000000+      | VideoCore GPU Shared RAM (Framebuffer allocated via     |
     | (Config-based)   | Mailbox Property Tag `0x00040001`)                      |
     +==================+=========================================================+
     | 0x3F000000 (Pi3) | BCM2837 / BCM2711 MMIO Peripherals Base                 |
     | 0xFE000000 (Pi4) |  ├─ +0x0000B880 : VideoCore Mailbox (Read/Write/Status) |
     |                  |  ├─ +0x00200000 : GPIO Controller (Alt-Func Pin Muxing) |
     |                  |  ├─ +0x00201000 : PL011 UART0 (TX/RX, Baud Rate Divs)   |
     |                  |  └─ +0x00215000 : Auxiliary Mini-UART (UART1)           |
     +------------------+---------------------------------------------------------+
     | 0x40000000 (Pi3) | ARM Local Peripherals (Core Timer Routing & IRQ Ctrl)   |
     | 0xFF840000 (Pi4) | GIC-400 (Generic Interrupt Controller - GICD / GICC)    |
     +------------------+---------------------------------------------------------+

</details>

---

## 📊 GitHub Stats

<p align="center">
  <img src="https://github-readme-stats.vercel.app/api?username=pizslacker&show_icons=true&theme=tokyonight&hide_border=true" width="50%" />
  <img src="https://github-readme-stats.vercel.app/api/top-langs/?username=pizslacker&layout=compact&theme=tokyonight&hide_border=true" width="38%" />
</p>

<!--
**pizslacker/pizslacker** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->
