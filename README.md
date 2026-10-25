## Hi there 👋 My name is k!M Olsen.
- 📫 How to reach me: kim.olsen@linux.com (email), kim@pizslacker.org (GTalk+GMeet)

---

## 🛠️ Tech Stack & Systems

- **Languages:** C, C++17, Rust, Go, Zig, Python, Bash, Lua, x86/ARM Assembly
- **Systems & Kernel:** Linux (Slackware, Debian, Fedora, Ubuntu, Mint), x86 Long Mode, PAE Paging, POSIX Multithreading
- **Graphics & Audio:** Raylib, SDL2, Pygame, Custom GLSL Shaders, Procedural Audio
- **Security & Ops:** SIEM Monitoring, Incident Response & Automation

## 🚀 Featured Projects

### 🌟 [SectorZero](https://github.com/pizslacker/SectorZero)
*An open-source x86 bare-metal bootloader and C kernel (GPLv3).*
- Transitions from 16-bit real mode directly into 64-bit long mode.
- Implements custom PAE paging, PML4 table setup, and inline assembly CPUID feature checks.

<details>
<summary><b>🔍 View Bootloader Architecture & Mode Transition</b></summary>
```text
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
```

---

## 📊 GitHub Stats

<p align="center">
  <img src="https://github-readme-stats.vercel.app/api?username=pizslacker&show_icons=true&theme=tokyonight&hide_border=true" width="48%" />
  <img src="https://github-readme-stats.vercel.app/api/top-langs/?username=pizslacker&layout=compact&theme=tokyonight&hide_border=true" width="48%" />
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
