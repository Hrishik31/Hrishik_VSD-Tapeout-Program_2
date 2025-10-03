# BabySoC Fundamentals & Functional Modelling

[![RISC-V](https://img.shields.io/badge/RISC--V-Digital%20Design-blue?style=for-the-badge&logo=riscv)](https://riscv.org/)
[![Workshop](https://img.shields.io/badge/RTL-Workshop-orange?style=for-the-badge)](https://vsdiat.vlsisystemdesign.com/)
[![Week 0](https://img.shields.io/badge/Week%200-Part%201-green?style=for-the-badge)]()

## 📋 Table of Contents

- [About This Project](#about-this-project)
- [Understanding System-on-Chip (SoC)](#understanding-system-on-chip-soc)
  - [What Exactly is an SoC?](#what-exactly-is-an-soc)
  - [Key Components of a Typical SoC](#key-components-of-a-typical-soc)
- [Why VSDBabySoC?](#why-vsdbabysoс)
- [The Three Pillars of VSDBabySoC](#the-three-pillars-of-vsdbabysoс)
  - [1. RVMYTH - The Digital Brain](#1-rvmyth---the-digital-brain)
  - [2. PLL - The Clock Maestro](#2-pll---the-clock-maestro)
  - [3. DAC - The Digital-to-Analog Bridge](#3-dac---the-digital-to-analog-bridge)
- [The SoC Design Journey](#the-soc-design-journey)
- [How VSDBabySoC Works](#how-vsdbabysoс-works)
- [Types of SoCs in the Real World](#types-of-socs-in-the-real-world)
- [The Open-Source Advantage](#the-open-source-advantage)
- [Challenges in Mixed-Signal SoC Design](#challenges-in-mixed-signal-soc-design)
- [Key Takeaways](#key-takeaways)
- [Looking Ahead](#looking-ahead)
- [References](#references)

---

## 🎯 About This Project

This repository documents my journey through understanding fundamental concepts of **System-on-Chip (SoC)** design by exploring **VSDBabySoC** - a compact, educational RISC-V-based SoC that integrates three key components:

- **RVMYTH** - A RISC-V processor core
- **PLL** - Phase-Locked Loop for clock generation
- **DAC** - Digital-to-Analog Converter

---

## 🔍 Understanding System-on-Chip (SoC)

### What Exactly is an SoC?

Think of an SoC as a **complete computer system squeezed onto a single chip**. Instead of having separate components scattered across a circuit board, everything you need - the processor, memory, input/output interfaces, and specialized circuits - lives together on one piece of silicon.

#### Why does this matter?

- **Space efficiency**: Your smartphone wouldn't fit in your pocket if it needed a full-sized computer motherboard
- **Power savings**: When components are closer together, signals travel shorter distances, consuming less energy
- **Performance**: Shorter distances also mean faster communication between components
- **Cost**: Manufacturing one integrated chip is often cheaper than assembling multiple separate components

### Key Components of a Typical SoC

| Component | Function |
|-----------|----------|
| **CPU** | The decision-maker that executes instructions and coordinates operations |
| **Memory Subsystem** | RAM for temporary storage, ROM/Flash for permanent storage |
| **I/O Interfaces** | Bridges connecting the SoC to external devices |
| **Specialized Processors** | GPU for graphics, DSP for signal processing |
| **Power Management** | Controls voltage levels and power distribution |
| **Interconnect Fabric** | The "highway system" allowing different components to communicate |

---

## 🎓 Why VSDBabySoC?

VSDBabySoC serves as an ideal learning platform because it:

1. **Simplifies Complexity** - Focuses on three core components that teach fundamental principles
2. **Demonstrates Mixed-Signal Design** - Integrates both digital (processor) and analog (PLL, DAC) components
3. **Open-Source Foundation** - Built entirely on open-source tools and IP cores
4. **Complete Design Flow** - Takes learners through the entire journey from behavioral modeling to physical layout (GDSII)
5. **Real-World Application** - Shows how digital signals can drive analog outputs

---

## 🔧 The Three Pillars of VSDBabySoC

### 1. RVMYTH - The Digital Brain

**What is it?**  
RVMYTH is a RISC-V-based processor core designed specifically for educational purposes, created using Transaction-Level Verilog (TLV).

**Why RISC-V?**
- Open standard instruction set architecture
- No licensing fees or proprietary restrictions
- Growing ecosystem with industry adoption
- Excellent for learning computer architecture fundamentals

**Role in BabySoC:**  
RVMYTH executes instructions stored in its instruction memory (imem) and processes data, ultimately filling register `r17` with values that get passed to the DAC for analog conversion.

### 2. PLL - The Clock Maestro

**What is a Phase-Locked Loop?**  
A PLL is a control system that generates a stable, synchronized clock signal. It takes a reference frequency and produces an output that's locked in both frequency and phase.

**Why can't we just use an external clock?**  
Several practical challenges arise:
- Clock distribution delays
- Jitter variations
- Multiple frequency requirements
- Crystal inaccuracies

**PLL Components:**
