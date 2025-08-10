# vsdRiscvSoc - RISC-V Learning Journey

This repository documents my progression through RISC-V toolchain setup, programming, and SoC development tasks as part of the VSD (VLSI System Design) course.

## Repository Structure

```
vsdRiscvSoc/
├── README.md                    # This file - project overview and navigation
├── riscv-task1-kevinshah/      # Task 1: RISC-V Toolchain Setup
├── riscv-task2-kevinshah/      # Task 2: RISC-V Programming and ISA Decoding
├── Task 1 Screenshots/         # Visual documentation for Task 1
└── Task 2 Screenshots/         # Visual documentation for Task 2
```

## Tasks Overview

### Task 1: RISC-V Toolchain Setup & Uniqueness Test

**Objective**: Set up a complete RISC-V development environment including toolchain, simulator, and proxy kernel, then verify the setup with a unique identification test.

**Location**: `riscv-task1-kevinshah/`

**Key Components Installed**:
- RISC-V GCC Cross-compiler (`riscv64-unknown-elf-gcc`)
- Spike RISC-V ISA Simulator
- RISC-V Proxy Kernel (pk)
- Icarus Verilog (for digital design)
- Development tools and dependencies

**Deliverables**:
- `unique_test.c` - C program that generates machine-specific unique ID
- Compilation commands and execution results
- Screenshots of successful toolchain installation
- Verification of `spike pk ./unique_test` execution

**Unique Features**:
- Machine-dependent output using FNV-1a hash of `USERNAME@HOSTNAME`
- Compile-time injection of user identity for verification
- Cross-compilation targeting `rv64imac` architecture

### Task 2: RISC-V Programming and ISA Decoding

**Objective**: Implement multiple RISC-V C programs with unique identification, generate assembly code, and manually decode RISC-V instructions.

**Location**: `riscv-task2-kevinshah/`

**Programs Implemented**:
1. **factorial.c** - Recursive factorial calculation
2. **max_array.c** - Find maximum element in integer array
3. **bitops.c** - Bitwise operations demonstration
4. **bubble_sort.c** - Bubble sort algorithm implementation

**Key Features**:
- **Uniqueness System**: Each program includes machine-specific headers with:
  - Username, hostname, machine ID
  - Build and run timestamps
  - Unique ProofID and RunID generation
- **Assembly Generation**: `.s` files for each program
- **Disassembly Analysis**: `objdump` output focusing on `main:` sections
- **ISA Decoding**: Manual decoding of RISC-V integer instructions

**Technical Specifications**:
- **Architecture**: RV64IMAC (64-bit with Integer, Multiplication, Atomic, Compressed)
- **ABI**: lp64 (Long and Pointer 64-bit)
- **Optimization**: -O0 (no optimization for clear assembly)
- **Execution Environment**: Spike simulator with proxy kernel

## Folder Contents

### `riscv-task1-kevinshah/`
Contains all files related to RISC-V toolchain setup:
- `unique_test.c` - Source code for uniqueness verification program
- `unique_test` - Compiled RISC-V executable (152KB)
- `README.md` - Detailed documentation of Task 1 setup process

📖 **[View Task 1 Detailed README](./riscv-task1-kevinshah/README.md)**

### `riscv-task2-kevinshah/`
Complete implementation of RISC-V programming task:
- `unique.h` - Common header with uniqueness mechanisms (1.7KB)
- **Program Sources**:
  - `factorial.c` - Recursive factorial implementation (249B)
  - `max_array.c` - Array maximum finder (313B) 
  - `bitops.c` - Bitwise operations demo (313B)
  - `bubble_sort.c` - Bubble sort algorithm (540B)
- **Compiled Executables** (all ~156KB):
  - `factorial`, `max_array`, `bitops`, `bubble_sort`
- **Assembly Files**:
  - `factorial.s` (4.7KB), `max_array.s` (4.9KB)
  - `bitops.s` (5.0KB), `bubble_sort.s` (6.1KB)
- **Disassembly Analysis**:
  - `factorial_main_objdump.txt` - Main function disassembly
  - `max_array_main_objdump.txt` - Main function disassembly
  - `bitops_main_objdump.txt` - Main function disassembly
  - `bubble_sort_main_objdump.txt` - Main function disassembly
- `instruction_decoding.md` - Manual RISC-V ISA instruction analysis (3.5KB)
- `README.md` - Comprehensive Task 2 documentation (9.2KB)

📖 **[View Task 2 Detailed README](./riscv-task2-kevinshah/README.md)**

### `Task 1 Screenshots/`
Visual documentation of Task 1 toolchain setup (8 screenshots):
- `Task1.1.png` - Initial setup and dependencies installation
- `Task1.2.png` - RISC-V GCC toolchain extraction and setup
- `Task1.3.png` - PATH configuration verification
- `Task1.4.png` - Spike simulator build process
- `Task1.5.png` - Proxy kernel (pk) installation
- `Task1.6.png` - Final PATH setup and verification
- `Task1.7.png` - Toolchain sanity checks
- `Task1.8.png` - Unique test compilation and execution

### `Task 2 Screenshots/`
Visual documentation of Task 2 programming work (7 screenshots):
- `Task2.1.png` - Environment setup and factorial program
- `Task2.2.png` - Max array program compilation and execution
- `Task2.3.png` - Bitwise operations program results
- `Task2.4.png` - Bubble sort implementation and output
- `Task2.5.png` - Assembly generation process
- `Task2.6.png` - Objdump disassembly analysis
- `Task2.7.png` - Final verification and unique headers display

## Technical Environment

**Target Architecture**: RISC-V 64-bit (RV64IMAC)
- **I**: Base integer instruction set
- **M**: Multiplication and division
- **A**: Atomic instructions
- **C**: Compressed instructions

**Development Tools**:
- Cross-compiler: `riscv64-unknown-elf-gcc 8.3.0`
- Simulator: Spike RISC-V ISA Simulator
- Runtime: RISC-V Proxy Kernel (pk)
- Host Platform: Ubuntu 22.04 LTS

**Compilation Pattern**:
```bash
riscv64-unknown-elf-gcc -O0 -g -march=rv64imac -mabi=lp64 \
  -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
  -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
  program.c -o program
```

**Execution Pattern**:
```bash
spike pk ./program
```

## Learning Outcomes

Through these tasks, I have gained experience in:
1. **Cross-compilation** for RISC-V architecture
2. **ISA simulation** using Spike
3. **Assembly language analysis** and instruction decoding
4. **Build system integration** with unique identification
5. **RISC-V instruction set architecture** fundamentals
6. **Digital design toolchain** setup and verification

## File Statistics Summary

**Task 1 Implementation**:
- 3 files total (1 source, 1 executable, 1 documentation)
- Complete toolchain setup verification
- 8 screenshot documentation files

**Task 2 Implementation**:
- 18 files total across 4 programs
- All required deliverables present:
  - 4 C source files with unique headers
  - 4 compiled RISC-V executables  
  - 4 assembly (.s) files generated
  - 4 objdump disassembly files
  - 1 common header file (unique.h)
  - 1 manual ISA instruction decoding file
  - 1 comprehensive README
- 7 screenshot documentation files
- **Status**: Complete implementation with all requirements fulfilled

Each program output includes cryptographically unique identifiers ensuring:
- **ProofID**: Unique per user+host+machine+build combination
- **RunID**: Additional per-execution randomness
- **Machine traceability**: Full environment identification
- **Timestamp verification**: Build and execution time tracking

This ensures all deliverables are authentically generated on my development environment and cannot be duplicated by other participants.

---

**Author**: Kevin Shah  
**Course**: VSD RISC-V SoC Design  
**Repository**: https://github.com/kevinshah2205/vsdRiscvSoc