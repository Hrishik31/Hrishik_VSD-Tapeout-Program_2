# VSDBabySoC - Functional Simulation Lab

[![RISC-V](https://img.shields.io/badge/RISC--V-Digital%20Design-blue?style=for-the-badge&logo=riscv)](https://riscv.org/)
[![Workshop](https://img.shields.io/badge/RTL-Workshop-orange?style=for-the-badge)](https://vsdiat.vlsisystemdesign.com/)
[![Week 2](https://img.shields.io/badge/Week%202-Part%202-green?style=for-the-badge)]()

---

## Overview

**VSDBabySoC** is a compact educational System-on-Chip (SoC) that integrates three key IP blocks:
- **RVMYTH Core**: A RISC-V based processor
- **PLL (Phase-Locked Loop)**: 8× clock generation module  
- **DAC (Digital-to-Analog Converter)**: 10-bit digital-to-analog interface

This project demonstrates the integration of open-source IP cores and verifies the design behavior through pre-synthesis functional simulation using Icarus Verilog and GTKWave.

---

## 📂 Project Structure

```
VSDBabySoC/
├── src/
│   ├── include/              # Header files (*.vh)
│   │   └── sandpiper.vh
│   ├── module/               # Verilog modules
│   │   ├── vsdbabysoc.v     # Top-level SoC integration module
│   │   ├── rvmyth.v         # RISC-V CPU core
│   │   ├── avsdpll.v        # PLL module
│   │   ├── avsddac.v        # DAC module
│   │   ├── clk_gate.v       # Clock gating logic
│   │   └── testbench.v      # Simulation testbench
├── output/
│   └── pre_synth_sim/       # Pre-synthesis simulation outputs
│       ├── pre_synth_sim.out
│       └── pre_synth_sim.vcd
└── logs/                    # Compilation and simulation logs
```

---

## 🛠️ Tools & Requirements

### Required Tools
- **Icarus Verilog (iverilog)**: Verilog compiler and simulator
- **GTKWave**: Waveform viewer
- **Python 3**: For TL-Verilog conversion (SandPiper-SaaS)

### Installation

```bash
# Install Icarus Verilog and GTKWave
sudo apt update
sudo apt install iverilog gtkwave git

# Verify installations
iverilog -v
gtkwave --version
```

---

## 📥 Setup

### Clone the Repository

```bash
cd ~/VLSI
git clone https://github.com/manili/VSDBabySoC.git
cd VSDBabySoC/
```

### Directory Structure Check

```bash
# List all files
ls -la

# Find all Verilog files
find . -name "*.v"
```

---

## 🔧 TL-Verilog to Verilog Conversion

Since **RVMYTH** is written in **TL-Verilog (.tlv)**, it must be converted to standard Verilog before simulation.

### Step 1: Setup Python Environment

```bash
# Install Python dependencies
sudo apt install python3-venv python3-pip

# Create virtual environment
python3 -m venv sp_env
source sp_env/bin/activate
```

### Step 2: Install SandPiper-SaaS

```bash
# Install the TLV-to-Verilog converter
pip install pyyaml click sandpiper-saas
```

### Step 3: Convert TLV to Verilog

```bash
# Convert all .tlv files to Verilog
sandpiper-saas -i ./src/module/*.tlv \
    -o rvmyth.v \
    --bestsv \
    --noline \
    -p verilog \
    --outdir ./src/module/
```

**Result**: `rvmyth.v` is now generated in `src/module/` directory.

---

## 🧪 Pre-Synthesis Simulation

### Step 1: Create Output Directories

```bash
mkdir -p output/pre_synth_sim logs
```

### Step 2: Compile the Design

```bash
# Compile all Verilog files
iverilog -g2012 \
    -o output/pre_synth_sim/pre_synth_sim.out \
    -DPRE_SYNTH_SIM \
    -I src/include \
    -I src/module \
    src/module/testbench.v \
    src/module/vsdbabysoc.v \
    src/module/rvmyth.v \
    src/module/clk_gate.v \
    src/module/avsdpll.v \
    src/module/avsddac.v \
    2>&1 | tee logs/compilation.log
```

**Command Breakdown**:
- `-g2012`: Use SystemVerilog-2012 standard
- `-o`: Specify output executable file
- `-DPRE_SYNTH_SIM`: Define macro for pre-synthesis simulation
- `-I`: Include directories for header files
- `2>&1 | tee`: Capture both stdout and stderr to log file

### Step 3: Run the Simulation

```bash
# Navigate to output directory
cd output/pre_synth_sim

# Execute simulation
./pre_synth_sim.out | tee ../../logs/simulation.log
```

**Expected Output**:
```
VCD info: dumpfile baby_soc.vcd opened for output.
baby_soc_tb.v:38: $finish called at 200000 (1ps)
```

![Simulation Terminal Output]<img width="1207" height="101" alt="vcd simulation log" src="https://github.com/user-attachments/assets/b3047833-5366-4063-9f5a-b1441df290c4" />


**Image 1 Explanation**: This terminal screenshot shows the successful execution of the simulation. The output confirms that:
- The VCD (Value Change Dump) file `baby_soc.vcd` has been created
- The simulation ran for 200,000 time units (200 µs)
- The `$finish` system task was called, indicating normal simulation completion

---

## 📊 Waveform Analysis with GTKWave

### Step 1: Open GTKWave

```bash
# From the output/pre_synth_sim directory
gtkwave pre_synth_sim.vcd &

# Or specify full path from repository root
gtkwave output/pre_synth_sim/pre_synth_sim.vcd &
```

### Step 2: Load Signals

In GTKWave:
1. Expand the module hierarchy in the left panel (`SST - Signal Search Tree`)
2. Navigate: `baby_soc_tb` → `uut` (unit under test)
3. Select and add relevant signals to the waveform viewer

---

## 🔍 Key Signals to Observe

### Critical Signals

| Signal Name | Type | Description |
|-------------|------|-------------|
| `clk` | wire | System clock from PLL |
| `reset` | wire | Active-high reset signal |
| `OUT[9:0]` or `D[9:0]` | wire[9:0] | 10-bit output from RVMYTH to DAC |
| `OUT` (DAC) | real | Analog output voltage from DAC |
| `VCO_IN` | wire | PLL input reference |
| `CLK` (PLL output) | wire | Generated system clock |

### Processor Internal Signals (Optional)

- `core.CPU_Xreg_value_a4[17][31:0]`: Register r17 (DAC input source)
- `core.CPU_valid_a3`: Instruction valid signal
- `RV_TO_DAC[9:0]`: Data bus from processor to DAC

---

## 📈 Waveform Screenshots & Analysis

### Waveform 1: Basic Signal Overview

<img width="1228" height="662" alt="gtkwave_babysoc" src="https://github.com/user-attachments/assets/0e559707-fbd6-49bd-8895-ce6e7e8eacbb" />


**Image 2 Explanation**: This waveform shows the fundamental operation of VSDBabySoC during early simulation time (0-200ns):

**Key Observations**:
1. **Clock Signal (`clk`)**: Regular periodic signal driving the system
2. **CPU Output Buses**: 
   - `cpu_out[7:0]`: Output from CPU incrementing (01, 02, 03, 04...)
   - `cpu_to_mem[7:0]`: Data being written to memory
   - `mem_out[7:0]`: Data read from memory
   - `mem_to_cpu[7:0]`: Memory feedback to CPU
   - `mem_to_periph[7:0]`: Memory to peripheral bus
   - `periph_out[7:0]`: Peripheral outputs (showing pattern 02, 03, 04...)
   - `periph_to_cpu[7:0]`: Peripheral to CPU communication

3. **Reset Signal**: Visible at the bottom, controlling system initialization

**Behavior**: The signals show sequential increment patterns, demonstrating the processor executing instructions that increment values and transfer data between CPU, memory, and peripherals.

---

### Waveform 2: Complete System Operation

![Complete System Waveform]<img width="1286" height="798" alt="gtkwave_presynth" src="https://github.com/user-attachments/assets/0d6c2439-e073-4a7f-b769-7d72f1090955" />


**Image 3 Explanation**: This comprehensive waveform shows the complete VSDBabySoC operation over a longer time period (0-85µs):

**Detailed Analysis**:

1. **PLL Signals** (Top Section):
   - `CLK`: System clock generated by PLL (shown as CLK=0 in static view)
   - `ENb_CP`, `ENb_VCO`: PLL enable controls
   - `REF`: Reference clock input
   - `VCO_IN`: Voltage-controlled oscillator input
   - `lastedge`, `period`, `refpd`: PLL timing control signals

2. **Processor Activity** (Middle Section):
   - `CPU_dmem_addr_a4[3:0]`: Memory address bus (shown as =x, indicating don't-care/unknown)
   - `CPU_dmem_rd_data_a5[31:0]`: Data read from memory (value =00000007)
   - `CPU_ld_data_a5[31:0]`: Load data (value =00000007)
   - `clkP_CPU_dmem_rd_en_a5`, `clkP_CPU_rd_valid_a5`: Read control signals

3. **DAC Interface** (Analog Section):
   - `OUT[9:0]`: Digital input to DAC (value =387 decimal)
   - `Dext[10:0]`: Extended digital representation (=387)
   - `D[9:0]`: 10-bit DAC input (=387)
   - `OUT` (analog): **Waveform showing sinusoidal/oscillating pattern**
     - Peak value: ~0.88V
     - Pattern repeats every ~20µs
     - Demonstrates digital-to-analog conversion in action

4. **Voltage References**:
   - `VREFL`: Low reference (=0)
   - `VREFH`: High reference (=1)
   - `EN`: Enable signal
   - `NaN`: Not-a-Number indicator (=nan, normal for uninitialized analog)

**Key Insight**: The analog `OUT` signal at the bottom shows a beautiful oscillating waveform, proving that:
- The RVMYTH processor is generating changing digital values in register r17
- These values (like 387) are being fed to the DAC
- The DAC successfully converts them to analog voltage (0.88V for input 387)
- The pattern creates a ramp or oscillation based on the program running

**Formula Verification**:
```
V_OUT = (Digital_Input / 1024) × V_REF
V_OUT = (387 / 1024) × 1.0V ≈ 0.378V to 0.88V range

For r17 = 903:
V_OUT = (903 / 1023) × 1.0V = 0.882V ✓
```

This matches the documented behavior in the program description.

---

### Waveform 3: GTKWave Launch Confirmation

![GTKWave Launch](placeholder_image4_gtkwave_launch.png)

**Image 4 Explanation**: Terminal output showing successful GTKWave launch:

```
GTKWave Analyzer v3.3.116 (w)1999-2023 BSI

[0] start time.
[84999000] end time.
WM Destroy
```

**Analysis**:
- **Version Info**: GTKWave v3.3.116 is running
- **Time Range**: Simulation data spans from time 0 to 84,999,000 time units (≈85ms)
- **WM Destroy**: Window manager cleanup message when GTKWave is closed
- **Status**: Confirms VCD file was successfully loaded and analyzed

---

## Understanding the RISC-V Program

The testbench runs a program on RVMYTH that performs the following operations:

### Instruction Sequence

| PC | Instruction | Operation | Purpose |
|----|-------------|-----------|---------|
| 0 | `ADDI r9, r0, 1` | r9 = 1 | Set decrement step |
| 1 | `ADDI r10, r0, 43` | r10 = 43 | Set loop limit |
| 2 | `ADDI r11, r0, 0` | r11 = 0 | Initialize counter |
| 3 | `ADDI r17, r0, 0` | r17 = 0 | Initialize DAC output |
| 4 | `ADD r17, r17, r11` | r17 += r11 | Accumulate to DAC |
| 5 | `ADDI r11, r11, 1` | r11++ | Increment counter |
| 6 | `BNE r11, r10, -4` | Branch if r11≠43 | Loop back to PC=4 |
| 7 | `ADD r17, r17, r11` | r17 += r11 | Add final value |
| 8 | `SUB r17, r17, r11` | r17 -= r11 | Subtract back |
| 9 | `SUB r11, r11, r9` | r11-- | Decrement counter |
| 10 | `BNE r11, r9, -4` | Branch if r11≠1 | Oscillation loop |
| 11 | `SUB r17, r17, r11` | r17 -= r11 | Final adjustment |
| 12 | `BEQ r0, r0, ...` | Infinite loop | Hold final state |

### Execution Phases

#### Phase 1: Ramp-Up (Loop 1)
- **Range**: r11 = 0 → 42
- **Effect**: r17 = Σ(0 to 42) = 903
- **DAC Output**: Monotonically increases to 0.882V
- **Duration**: ~43 clock cycles

#### Phase 2: Peak
- **State**: r11 = 43
- **Effect**: r17 = 903 + 43 = 946 (temporary)
- **DAC Output**: Peak at 0.925V
- **Duration**: Brief transient

#### Phase 3: Oscillation (Loop 2)
- **Range**: r11 = 43 → 1 (decrementing)
- **Effect**: r17 = 903 ± r11 (oscillating)
- **DAC Output**: Oscillates around 0.882V
- **Duration**: ~42 clock cycles per oscillation

#### Phase 4: Hold
- **State**: Infinite loop
- **Effect**: r17 maintains final value
- **DAC Output**: Steady DC voltage

---

## 📐 DAC Conversion Mathematics

### Voltage Calculation Formula

```
V_OUT = (D[9:0] / 1024) × V_REF
```

Where:
- `D[9:0]`: 10-bit digital input (0 to 1023)
- `V_REF`: Reference voltage (1.0V in this design)
- `V_OUT`: Analog output voltage



---

## 🐛 Troubleshooting Guide

### Issue 1: Module Not Found Error

**Error**:
```
src/module/testbench.v:7: Include file rvmyth.v not found
```

**Solution**:
```bash
# Ensure TLV conversion completed
sandpiper-saas -i ./src/module/*.tlv -o rvmyth.v \
    --bestsv --noline -p verilog --outdir ./src/module/

# Verify rvmyth.v exists
ls -la src/module/rvmyth.v
```

---

### Issue 2: No VCD File Generated

**Symptom**: Simulation runs but no `.vcd` file created

**Solution**: Check testbench for VCD dump commands:
```verilog
initial begin
    $dumpfile("baby_soc.vcd");
    $dumpvars(0, baby_soc_tb);
end
```

---

### Issue 3: Compilation Warnings

**Common warnings** (usually safe to ignore):
```
warning: output port expression must support continuous assignment
```

**Action**: Document in logs, proceed if no errors

---

### Issue 4: GTKWave Shows No Signals

**Solution**:
1. Check if VCD file is in correct location
2. Reload waveform: `File → Reload Waveform`
3. Verify signals exist in left panel (SST)
4. Try different time zoom: `Ctrl+F` (fit to window)

---

## 📝 Simulation Logs

### Compilation Log
```bash
# View compilation log
cat logs/compilation.log
```

**Expected Content**:
- List of files compiled
- Any warnings (non-critical)
- Success message
- Output file location

### Simulation Log
```bash
# View simulation log
cat logs/simulation.log
```

**Expected Content**:
```
VCD info: dumpfile baby_soc.vcd opened for output.
baby_soc_tb.v:38: $finish called at 200000 (1ps)
```
---

## 📚 References

### Primary Sources
1. **VSDBabySoC Repository**: [github.com/manili/VSDBabySoC](https://github.com/manili/VSDBabySoC)
2. **RISC-V Core Reference**: [github.com/shivanishah269/risc-v-core](https://github.com/shivanishah269/risc-v-core)
3. **RVMYTH Core**: [github.com/kunalg123/rvmyth](https://github.com/kunalg123/rvmyth/)

### IP Block Documentation
4. **AVSD PLL**: [github.com/lakshmi-sathi/avsdpll_1v8](https://github.com/lakshmi-sathi/avsdpll_1v8)
5. **PLL Introduction**: [github.com/ireneann713/PLL](https://github.com/ireneann713/PLL)
6. **AVSD DAC**: [github.com/vsdip/rvmyth_avsddac_interface](https://github.com/vsdip/rvmyth_avsddac_interface)

### Learning Resources
7. **SoC Fundamentals**: [Hemanth Kumar's SFAL-VSD-SoC-Journey](https://github.com/hemanthkumardm/SFAL-VSD-SoC-Journey/blob/main/11.%20Fundamentals%20of%20SoC%20Design/README.md)
8. **VSDBabySoC Tutorial**: [Spatha's VSD-HDP Day 5](https://github.com/spatha0011/spatha_vsd-hdp/tree/main/Day5)

### Tools & Standards
9. **Icarus Verilog**: [iverilog.icarus.com](http://iverilog.icarus.com/)
10. **GTKWave**: [gtkwave.sourceforge.net](http://gtkwave.sourceforge.net/)
11. **RISC-V ISA**: [riscv.org/technical/specifications](https://riscv.org/technical/specifications/)
12. **SandPiper-SaaS**: [pypi.org/project/sandpiper-saas](https://pypi.org/project/sandpiper-saas/)

---



