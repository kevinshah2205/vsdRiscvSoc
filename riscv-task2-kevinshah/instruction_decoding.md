# RISC-V Instruction Decoding

This document decodes RISC-V integer instructions found in the provided assembly files, showing their binary encoding and functionality.

## Instruction Format

RISC-V instructions follow specific formats. The most common formats are:
- **R-type**: Register-Register operations
- **I-type**: Immediate operations
- **S-type**: Store operations
- **B-type**: Branch operations
- **U-type**: Upper immediate operations
- **J-type**: Jump operations

## Decoded Instructions

| Instruction | Opcode | rd | rs1 | rs2 | funct3 | funct7 | Binary | Description |
|-------------|--------|----|----|----|----|----|----|-------------|
| add a5,a4,a5 | 0110011 | a5 | a4 | a5 | 000 | 0000000 | 0000000 01111 01110 000 01111 0110011 | a5 = a4 + a5 |
| addi sp,sp,-32 | 0010011 | sp | sp | - | 000 | - | 111111100000 00010 000 00010 0010011 | sp = sp + (-32) |
| lw a5,-20(s0) | 0000011 | a5 | s0 | - | 010 | - | 111111101100 01000 010 01111 0000011 | a5 = memory[s0-20] |
| sw a5,-20(s0) | 0100011 | - | s0 | a5 | 010 | - | 1111111 01111 01000 010 01100 0100011 | memory[s0-20] = a5 |
| bne a5,zero,.L3 | 1100011 | - | a5 | zero | 001 | - | imm 00000 01111 001 imm 1100011 | if (a5 != 0) branch |
| lui a5,0x1c | 0110111 | a5 | - | - | - | - | 00000000000000011100 01111 0110111 | a5 = 0x1c000 |
| jal ra,offset | 1101111 | ra | - | - | - | - | imm[20:1] 00001 1101111 | ra = PC+4; PC += offset |
| xor a5,a5,a4 | 0110011 | a5 | a5 | a4 | 100 | 0000000 | 0000000 01110 01111 100 01111 0110011 | a5 = a5 ^ a4 |
| slli a5,a5,40 | 0010011 | a5 | a5 | - | 001 | 0000000 | 0000000 101000 01111 001 01111 0010011 | a5 = a5 << 40 |
| mul a5,a4,a5 | 0110011 | a5 | a4 | a5 | 000 | 0000001 | 0000001 01111 01110 000 01111 0110011 | a5 = a4 * a5 |
| srliw a5,a5,2 | 0011011 | a5 | a5 | - | 101 | 0000000 | 0000000 000010 01111 101 01111 0011011 | a5 = a5[31:0] >> 2 |
| blt a4,a5,.L10 | 1100011 | - | a4 | a5 | 100 | - | imm 01111 01110 100 imm 1100011 | if (a4 < a5) branch |

## Register Mapping Reference

| ABI Name | Register | Number | Usage |
|----------|----------|---------|-------|
| zero     | x0       | 0       | Hardwired zero |
| ra       | x1       | 1       | Return address |
| sp       | x2       | 2       | Stack pointer |
| gp       | x3       | 3       | Global pointer |
| tp       | x4       | 4       | Thread pointer |
| t0-t2    | x5-x7    | 5-7     | Temporary registers |
| s0/fp    | x8       | 8       | Saved register/Frame pointer |
| s1       | x9       | 9       | Saved register |
| a0-a7    | x10-x17  | 10-17   | Function arguments/return values |
| s2-s11   | x18-x27  | 18-27   | Saved registers |
| t3-t6    | x28-x31  | 28-31   | Temporary registers |

## Instruction Type Summary

- **R-type**: Register-Register operations (add, sub, xor, mul, etc.)
- **I-type**: Immediate operations (addi, lw, slli, etc.)
- **S-type**: Store operations (sw, sd, etc.)
- **B-type**: Branch operations (beq, bne, blt, etc.)
- **U-type**: Upper immediate (lui, auipc)
- **J-type**: Jump operations (jal, jalr)

## Sources

Instructions decoded from the following assembly files:
- `bitops.s` - Bitwise operations program
- `bubble_sort.s` - Bubble sort implementation
- `factorial.s` - Factorial calculation
- `max_array.s` - Array maximum finding
- `*.objdump` - Corresponding object dump files

All instructions follow the RISC-V ISA specification and demonstrate various instruction formats and operations commonly used in C program compilation.