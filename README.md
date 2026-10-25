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
     
     [16-Bit Real Mode]         [32-Bit Protected]          [64-Bit Long Mode]
     +--------------------+     +--------------------+     +----------------------+
     | • BIOS @ 0x7C00    |     | • Verify CPUID     |     | • 64-bit GDT (L=1)   |
     | • Enable A20 Gate  | --> | • Setup PML4 Table | --> | • Init 64-bit Stack  |
     | • Load 32-bit GDT  |     | • Enable CR4.PAE   |     | • Call C Kernel      |
     +--------------------+     +--------------------+     +----------------------+
          (CR0.PE = 1)             (EFER.LME = 1 &              (Far Jump to
                                     CR0.PG = 1)                 kmain() entry)

</details>
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
