# RISC-V Task 2 - Local Setup Verification

## Overview
This repository contains the implementation and verification of 4 RISC-V C programs compiled with the local RISC-V toolchain and executed using Spike simulator with proxy kernel (pk). Each program includes a uniqueness mechanism that embeds username, hostname, machine ID, and timestamps to ensure outputs are unique to this PC.

## System Information

### Spike Version
```bash
$ spike --version
# [ADD SPIKE VERSION OUTPUT HERE]
```

### GCC Toolchain Information
```bash
$ riscv64-unknown-elf-gcc -v
# [ADD GCC -v OUTPUT HERE]
```

## Step-by-Step Implementation

### A. Uniqueness Mechanism Setup
First, set identity variables in the Linux host shell:

```bash
export U=$(id -un)
export H=$(hostname -s)
export M=$(cat /etc/machine-id | head -c 16)
export T=$(date -u +%Y-%m-%dT%H:%M:%SZ)
export E=$(date +%s)
```

#### Verification of Environment Variables
```bash
echo "Username: $U"
echo "Hostname: $H" 
echo "Machine ID: $M"
echo "Build UTC: $T"
echo "Build Epoch: $E"
```

**Output:**
```
# [ADD ENVIRONMENT VARIABLE VERIFICATION OUTPUT HERE]
```

### B. Common Header (unique.h)
Created using EOF to avoid escape character issues:

```bash
cat > unique.h << 'EOF'
#ifndef UNIQUE_H
#define UNIQUE_H
#include <stdio.h>
#include <stdint.h>
#include <time.h>
#ifndef USERNAME
#define USERNAME "unknown_user"
#endif
#ifndef HOSTNAME
#define HOSTNAME "unknown_host"
#endif
#ifndef MACHINE_ID
#define MACHINE_ID "unknown_machine"
#endif
#ifndef BUILD_UTC
#define BUILD_UTC "unknown_time"
#endif
#ifndef BUILD_EPOCH
#define BUILD_EPOCH 0
#endif
static uint64_t fnv1a64(const char *s) {
const uint64_t OFF = 1469598103934665603ULL, PRIME = 1099511628211ULL;
uint64_t h = OFF;
for (const unsigned char *p=(const unsigned char*)s; *p; ++p) {
h ^= *p; h *= PRIME;
}
return h;
}
static void uniq_print_header(const char *program_name) {
time_t now = time(NULL);
char buf[512];
int n = snprintf(buf, sizeof(buf), "%s|%s|%s|%s|%ld|%s|%s",
USERNAME, HOSTNAME, MACHINE_ID, BUILD_UTC,
(long)BUILD_EPOCH, __VERSION__, program_name);
(void)n;
uint64_t proof = fnv1a64(buf);
char rbuf[600];
snprintf(rbuf, sizeof(rbuf), "%s|run_epoch=%ld", buf, (long)now);
uint64_t runid = fnv1a64(rbuf);
printf("=== RISC-V Proof Header ===\n");
printf("User : %s\n", USERNAME);
printf("Host : %s\n", HOSTNAME);
printf("MachineID : %s\n", MACHINE_ID);
printf("BuildUTC : %s\n", BUILD_UTC);
printf("BuildEpoch : %ld\n", (long)BUILD_EPOCH);
printf("GCC : %s\n", __VERSION__);
printf("PointerBits: %d\n", (int)(8*(int)sizeof(void*)));
printf("Program : %s\n", program_name);
printf("ProofID : 0x%016llx\n", (unsigned long long)proof);
printf("RunID : 0x%016llx\n", (unsigned long long)runid);
printf("===========================\n");
}
#endif
EOF
```

### C. Program Implementation

#### 1. factorial.c
```bash
cat > factorial.c << 'EOF'
#include "unique.h"
static unsigned long long fact(unsigned n){ return (n<2)?1ULL:n*fact(n-1); }
int main(void){
uniq_print_header("factorial");
unsigned n = 12;
printf("n=%u, n!=%llu\n", n, fact(n));
return 0;
}
EOF
```

#### 2. max_array.c
```bash
cat > max_array.c << 'EOF'
#include "unique.h"
int main(void){
uniq_print_header("max_array");
int a[] = {42,-7,19,88,3,88,5,-100,37};
int n = sizeof(a)/sizeof(a[0]), max=a[0];
for(int i=1;i<n;i++) if(a[i]>max) max=a[i];
printf("Array length=%d, Max=%d\n", n, max);
return 0;
}
EOF
```

#### 3. bitops.c
```bash
cat > bitops.c << 'EOF'
#include "unique.h"
int main(void){
uniq_print_header("bitops");
unsigned x=0xA5A5A5A5u, y=0x0F0F1234u;
printf("x&y=0x%08X\n", x&y);
printf("x|y=0x%08X\n", x|y);
printf("x^y=0x%08X\n", x^y);
printf("x<<3=0x%08X\n", x<<3);
printf("y>>2=0x%08X\n", y>>2);
return 0;
}
EOF
```

#### 4. bubble_sort.c
```bash
cat > bubble_sort.c << 'EOF'
#include "unique.h"
void bubble(int *a,int n){ for(int i=0;i<n-1;i++) for(int j=0;j<n-1-i;j++) if(a[j]>a[j+1]){int t=a[j];a[j]=a[j+1];a[j+1]=t;} }
int main(void){
uniq_print_header("bubble_sort");
int a[]={9,4,1,7,3,8,2,6,5}, n=sizeof(a)/sizeof(a[0]);
bubble(a,n);
printf("Sorted:"); for(int i=0;i<n;i++) printf(" %d",a[i]); puts("");
return 0;
}
EOF
```

### D. Build, Run, and Capture Evidence

#### Program 1: factorial
**Compile:**
```bash
riscv64-unknown-elf-gcc -O0 -g -march=rv64imac -mabi=lp64 \
    -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
    -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
    factorial.c -o factorial
```

**Run:**
```bash
spike pk ./factorial
```

**Output:**
```
# [ADD FACTORIAL EXECUTION OUTPUT HERE]
# ProofID: [ADD PROOF ID]
# RunID: [ADD RUN ID]
```

#### Program 2: max_array
**Compile:**
```bash
riscv64-unknown-elf-gcc -O0 -g -march=rv64imac -mabi=lp64 \
    -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
    -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
    max_array.c -o max_array
```

**Run:**
```bash
spike pk ./max_array
```

**Output:**
```
# [ADD MAX_ARRAY EXECUTION OUTPUT HERE]
# ProofID: [ADD PROOF ID]
# RunID: [ADD RUN ID]
```

#### Program 3: bitops
**Compile:**
```bash
riscv64-unknown-elf-gcc -O0 -g -march=rv64imac -mabi=lp64 \
    -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
    -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
    bitops.c -o bitops
```

**Run:**
```bash
spike pk ./bitops
```

**Output:**
```
# [ADD BITOPS EXECUTION OUTPUT HERE]
# ProofID: [ADD PROOF ID]
# RunID: [ADD RUN ID]
```

#### Program 4: bubble_sort
**Compile:**
```bash
riscv64-unknown-elf-gcc -O0 -g -march=rv64imac -mabi=lp64 \
    -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
    -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
    bubble_sort.c -o bubble_sort
```

**Run:**
```bash
spike pk ./bubble_sort
```

**Output:**
```
# [ADD BUBBLE_SORT EXECUTION OUTPUT HERE]
# ProofID: [ADD PROOF ID]
# RunID: [ADD RUN ID]
```

### E. Produce Assembly and Disassembly

#### factorial
**Generate Assembly:**
```bash
riscv64-unknown-elf-gcc -O0 -S -march=rv64imac -mabi=lp64 \
    -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
    -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
    factorial.c -o factorial.s
```

**Generate Disassembly:**
```bash
riscv64-unknown-elf-objdump -d ./factorial | sed -n '/<main>:/,/^$/p' > factorial_main_objdump.txt
```

#### max_array
**Generate Assembly:**
```bash
riscv64-unknown-elf-gcc -O0 -S -march=rv64imac -mabi=lp64 \
    -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
    -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
    max_array.c -o max_array.s
```

**Generate Disassembly:**
```bash
riscv64-unknown-elf-objdump -d ./max_array | sed -n '/<main>:/,/^$/p' > max_array_main_objdump.txt
```

#### bitops
**Generate Assembly:**
```bash
riscv64-unknown-elf-gcc -O0 -S -march=rv64imac -mabi=lp64 \
    -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
    -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
    bitops.c -o bitops.s
```

**Generate Disassembly:**
```bash
riscv64-unknown-elf-objdump -d ./bitops | sed -n '/<main>:/,/^$/p' > bitops_main_objdump.txt
```

#### bubble_sort
**Generate Assembly:**
```bash
riscv64-unknown-elf-gcc -O0 -S -march=rv64imac -mabi=lp64 \
    -DUSERNAME="\"$U\"" -DHOSTNAME="\"$H\"" -DMACHINE_ID="\"$M\"" \
    -DBUILD_UTC="\"$T\"" -DBUILD_EPOCH=$E \
    bubble_sort.c -o bubble_sort.s
```

**Generate Disassembly:**
```bash
riscv64-unknown-elf-objdump -d ./bubble_sort | sed -n '/<main>:/,/^$/p' > bubble_sort_main_objdump.txt
```

## Repository Structure
```
riscv-task2-<username>/
├── unique.h
├── factorial.c
├── factorial.s
├── factorial_main_objdump.txt
├── factorial_output.png
├── factorial_main_asm.png
├── max_array.c
├── max_array.s
├── max_array_main_objdump.txt
├── max_array_output.png
├── max_array_main_asm.png
├── bitops.c
├── bitops.s
├── bitops_main_objdump.txt
├── bitops_output.png
├── bitops_main_asm.png
├── bubble_sort.c
├── bubble_sort.s
├── bubble_sort_main_objdump.txt
├── bubble_sort_output.png
├── bubble_sort_main_asm.png
├── instruction_decoding.md
└── README.md
```

## Verification Notes
- ProofID is unique per user+host+machine+build combination
- RunID adds per-run randomness for execution verification
- All .s and .objdump files match the compiled code
- Screenshots contain visible ProofID and RunID for verification