# 🛠️ Task 1: RISC-V Toolchain Setup and Verification Using WSL

[![RISC-V](https://img.shields.io/badge/Architecture-RISC--V-blue.svg)](https://riscv.org/)
[![Toolchain](https://img.shields.io/badge/Toolchain-RISC--V%20GCC-blueviolet.svg)]()
[![Ubuntu](https://img.shields.io/badge/Platform-Ubuntu-E95420.svg)](https://ubuntu.com/)
[![Status](https://img.shields.io/badge/Status-✅%20Complete-success.svg)]()

A comprehensive guide for setting up a complete RISC-V development toolchain with detailed troubleshooting and solutions for common issues.
## 📋 Table of Contents

- [Overview](https://github.com/kevinshah2205/vsdRiscvSoc/blob/main/README.md#-overview)
- [Prerequisites](https://github.com/kevinshah2205/vsdRiscvSoc/blob/main/README.md#-prerequisites)
- [Installation](https://github.com/kevinshah2205/vsdRiscvSoc/blob/main/README.md#installation)
- [Issues Encountered & Solutions](#issues-encountered--solutions)
- [Project Structure](#project-structure)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

This project documents the complete setup process for a RISC-V development toolchain, including:

- **RISC-V GCC Cross-Compiler** (riscv64-unknown-elf-gcc)
- **Spike RISC-V ISA Simulator** 
- **RISC-V Proxy Kernel (pk)**
- **Icarus Verilog** for HDL simulation
- **GTKWave** for waveform viewing

The setup includes comprehensive troubleshooting documentation for compatibility issues between different toolchain versions and solutions for cross-compiler interference problems.

## 🔧 Prerequisites

### System Requirements
- **OS**: Ubuntu 22.04 LTS (64-bit) - native or VirtualBox 
- **RAM**: 4-8 GB recommended
- **Storage**: 30 GB free space
- **CPU**: 2+ cores recommended

## 🚀 Installation
- Once you have completed the installation of the ubuntu distro, Open the terminal and run the base dependencies command shown below.
### Base Dependencies
```bash
sudo apt-get update
sudo apt-get install -y git vim autoconf automake autotools-dev curl \
libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex \
texinfo gperf libtool patchutils bc zlib1g-dev libexpat1-dev gtkwave \
device-tree-compiler
```
- Once that is done you will get some msg like this 

<img width="840" height="739" alt="image" src="https://github.com/user-attachments/assets/c63c1547-3bd9-4335-b7c8-101a4b28538a" />

- Here it is showing that everything is already installed as i already ran the command and all of my depepdies were installed.

## 1. Workspace Setup

- Then we need to create a workspace setpu where we will download and install all our files and tools. We begin by storing our home path which make installing, cleaning and removing files easy.

```bash
cd ~
pwd=$PWD
mkdir -p riscv_toolchain
cd riscv_toolchain
```
### 2. Install RISC-V GCC Toolchain

- Now we Download the prebuilt toolchain and extract it and verify that it is downloaded and extraced correctly. Also add it to our path and verify the path.

```bash
wget "https://static.dev.sifive.com/dev-tools/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14.tar.gz"
tar -xvzf riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14.tar.gz
ls -la
ls -la riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14.tar.gz

# Add to PATH
export PATH=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/bin:$PATH
echo 'export PATH=$HOME/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/bin:$PATH' >> ~/.bashrc
echo $PATH | grep riscv
```

<img width="1555" height="390" alt="image" src="https://github.com/user-attachments/assets/f10b79cd-45cc-4752-aa5c-5c351f1e8ab9" />

- After downloading and extracting the file and running the verification code for it you will get some results like shown above in the image.

<img width="1838" height="147" alt="image" src="https://github.com/user-attachments/assets/7f796aaa-0b36-4617-bb69-bdbfd5d0c2e5" />

- and after adding your path you will get some result like the one you are seeing right above.

- Verifying if we installed the toolchain correctly or not so for that we should run these command.

```
riscv64-unknown-elf-gcc --version
riscv64-unknown-elf-objdump --version
riscv64-unknown-elf-gdb --version
riscv64-unknown-elf-gcc -dumpmachine
ls -la ~/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/bin/ | grep riscv64
```
- But when i ran the code mentioned above i got error showing like (This totally depend on what version of ubuntu you are using)

```
riscv64-unknown-elf-gdb: error while loading shared libraries: libncurses.so.5: cannot open shared object file: No such file or directory
```

- So to resolve it i first understood the problem that my ubuntu system had libncurses.so.6 and libtinfo.so.6 installed instead of toolchain needing version 5 as it was older and the prebuilt toolchain was compiled against libncurses.so.5 and libtinfo.so.5 , but modern Ubuntu systems have libncurses.so.6 and libtinfo.so.6. The older version is missing. So solution for it was to first download the older version and then create symbolic links to point the gbd compiler to it.

```
# Install the legacy packages manually:
cd /tmp
wget http://archive.ubuntu.com/ubuntu/pool/universe/n/ncurses/libtinfo5_6.3-2ubuntu0.1_amd64.deb
wget http://archive.ubuntu.com/ubuntu/pool/universe/n/ncurses/libncurses5_6.3-2ubuntu0.1_amd64.deb
sudo dpkg -i libtinfo5_6.3-2ubuntu0.1_amd64.deb
sudo dpkg -i libncurses5_6.3-2ubuntu0.1_amd64.deb

# Find where libraries were installed:
find /usr -name "libncurses.so.5*" 2>/dev/null
find /usr -name "libtinfo.so.5*" 2>/dev/null
```

**Result:** 
- Libraries were installed in /lib/x86_64-linux-gnu/ but toolchain looks in /usr/lib/x86_64-linux-gnu/

```

# Create proper symbolic links:
sudo ln -s /lib/x86_64-linux-gnu/libncurses.so.5.9 /usr/lib/x86_64-linux-gnu/libncurses.so.5
sudo ln -s /lib/x86_64-linux-gnu/libtinfo.so.5.9 /usr/lib/x86_64-linux-gnu/libtinfo.so.5

# Update library cache:
sudo ldconfig
```

- So after that i again ran all the commands and got the result successfully.

<img width="1838" height="994" alt="image" src="https://github.com/user-attachments/assets/f7fe8ce4-b3bb-4138-9933-0ba557f741e1" />

### 3. Install Device Tree Compiler (DTC)

```
sudo apt-get install -y device-tree-compiler
```

### 4. Build Spike (RISC-V ISA Simulator)

```bash
git clone https://github.com/riscv/riscv-isa-sim.git
cd riscv-isa-sim
mkdir -p build && cd build
../configure --prefix=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14
make -j$(nproc)
sudo make install
cd $pwd/riscv_toolchain
```

### 5. Build RISC-V Proxy Kernel (Fixed Version)

```
cd $pwd/riscv_toolchain
git clone https://github.com/riscv/riscv-pk.git
cd riscv-pk
mkdir -p build && cd build
../configure --prefix=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc8.3.0-2019.08.0-x86_64-linux-ubuntu14 --host=riscv64-unknown-elf
make -j$(nproc)
sudo make install
```

- So when i ran the above code i got the error

```
../machine/minit.c:250: Error: unknown CSR `menvcfg'
../pk/pk.c:125: Error: unknown CSR `senvcfg'
../pk/pk.c:207: Error: unknown CSR `senvcfg'
../pk/pk.c:207: Error: unknown CSR `senvcfg'
../pk/pk.c:121: Error: unknown CSR `senvcfg'
../pk/pk.c:121: Error: unknown CSR `senvcfg'
make: *** [Makefile:336: minit.o] Error 1
make: *** Waiting for unfinished jobs....
make: *** [Makefile:336: pk.o] Error 1
```

**What was wrong:** 
- Newer riscv-pk uses CSRs (menvcfg, senvcfg) that were introduced after the GCC 8.3.0 toolchain was built.

**Fix:** 
- Use compatible older version (v1.0.0) of riscv-pk that matches the toolchain era.

- so then i ran the modified code for the older version and then i successfully installed it.

```
cd $pwd/riscv_toolchain
rm -rf riscv-pk  # Remove problematic version
git clone https://github.com/riscv/riscv-pk.git
cd riscv-pk
git checkout v1.0.0
mkdir -p build && cd build
../configure --prefix=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14 --host=riscv64-unknown-elf
make -j$(nproc)
sudo make install
```

### 6. Ensure cross bin in PATH

```
# Current shell
export PATH=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/riscv64-unknown-elf/bin:$PATH

# Persistent
echo 'export PATH=$HOME/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/riscv64-unknown-elf/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

### 7. Install Icarus Verilog

- first i ran the command that was send in the pdf.

```
cd $pwd/riscv_toolchain
git clone https://github.com/steveicarus/iverilog.git
cd iverilog
git checkout --track -b v10-branch origin/v10-branch
git pull
chmod +x autoconf.sh
./autoconf.sh
./configure 
make -j$(nproc)
sudo make install
```

- so after running the above code i got the error.

```
checking whether the C compiler works... no
configure: error: in `/home/kevinshah/riscv_toolchain/iverilog':
configure: error: C compiler cannot create executables
See `config.log' for more details
make: *** No targets specified and no makefile found.  Stop.
make: *** No rule to make target 'install'.  Stop.
```

- so after so many debugging attempts and searching internet, i ran the following command that clarify what the issue was.

```
echo 'int main(){return 0;}' > test.c
gcc test.c -o test  # ❌ FAILED
./test
echo $?
```

**Root Cause Discovered:** 
- The RISC-V cross-compiler's assembler (riscv64-unknown-elf-as) was being picked up instead of the system assembler (/usr/bin/as). The RISC-V assembler doesn't understand x86-64 specific options like --64.

```
cd $pwd/riscv_toolchain/iverilog
make distclean 2>/dev/null || true
git clean -fdx

# ✅ KEY FIX: Complete PATH isolation
echo $PATH > /tmp/saved_path  # Save current PATH with RISC-V tools
export PATH=/usr/bin:/bin:/usr/sbin:/sbin  # Clean PATH - NO RISC-V tools
export CC=/usr/bin/gcc
export CXX=/usr/bin/g++

# Verify we're using correct system tools
which gcc    # Should show /usr/bin/gcc
which as     # Should show /usr/bin/as (NOT riscv64-unknown-elf-as)
gcc --version

# Test that system compiler actually works now
echo 'int main(){return 0;}' > test.c
gcc test.c -o test  # ✅ Should work now
./test
echo $?  # Should print 0
rm -f test test.c  # Cleanup

# Build with clean environment
./autoconf.sh
./configure
make -j$(nproc)
sudo make install

# Restore full PATH with RISC-V tools
export PATH=$(cat /tmp/saved_path)
rm -f /tmp/saved_path
```

- So then i ran the command shown above that solved the icarus verilog installtion problem. But it introduced another problem, that was of path that icarus verilog's path was missing.

**What was wrong:**

**Primary Issue:** 
- RISC-V cross-compiler's assembler (riscv64-unknown-elf-as) was being used instead of system assembler (/usr/bin/as). RISC-V as doesn't understand x86-64 specific --64 option.

**Secondary Issue:** 
- After successful installation, /usr/local/bin wasn't in PATH so iverilog couldn't be found.

so then i ran the below code to define the path of the Icarus verilog and it worked.

```
# Add to current shell
export PATH=/usr/local/bin:$PATH

# Make it persistent
echo 'export PATH=/usr/local/bin:$PATH' >> ~/.bashrc
source ~/.bashrc

# Verify it works
which iverilog  # ✅ Now found
iverilog -V     # ✅ Shows version
```
  
**Fix:**

- Completely isolate the build environment using clean PATH with only system tools

- Add /usr/local/bin to PATH permanently

### 8. Installation Check

```
which riscv64-unknown-elf-gcc
riscv64-unknown-elf-gcc -v   
which spike                 
spike --version || spike -h   
which pk                   
which iverilog
```

Each Command ran successfully and got this output

<img width="1838" height="981" alt="image" src="https://github.com/user-attachments/assets/482ce7f4-4442-414b-8593-8914839a9d88" />

<img width="1838" height="981" alt="image" src="https://github.com/user-attachments/assets/a30d7499-060c-4526-b8c6-ce1b9b0bcd4c" />

***Note:*** 

- spike --version doesn't exist, but spike --help works and shows version info:

```
spike: unrecognized option --version
Try 'spike --help' for more information.
Spike RISC-V ISA Simulator 1.1.1-dev
```

### 9. A Unique C Test (Username & Machine Dependent)

- Creating the Unique C Test program.

```
cat > unique_test.c << 'EOF'
#include <stdint.h>
#include <stdio.h>

#ifndef USERNAME
#define USERNAME "unknown_user"
#endif

#ifndef HOSTNAME
#define HOSTNAME "unknown_host"
#endif

static uint64_t fnv1a64(const char *s) {
    const uint64_t FNV_OFFSET = 1469598103934665603ULL;
    const uint64_t FNV_PRIME = 1099511628211ULL;
    uint64_t h = FNV_OFFSET;
    for (const unsigned char *p = (const unsigned char*)s; *p; ++p) {
        h ^= (uint64_t)(*p);
        h *= FNV_PRIME;
    }
    return h;
}

int main(void) {
    const char *user = USERNAME;
    const char *host = HOSTNAME;
    char buf[256];
    int n = snprintf(buf, sizeof(buf), "%s@%s", user, host);
    if (n <= 0) return 1;
    uint64_t uid = fnv1a64(buf);
    printf("RISC-V Uniqueness Check\n");
    printf("User: %s\n", user);
    printf("Host: %s\n", host);
    printf("UniqueID: 0x%016llx\n", (unsigned long long)uid);
#ifdef __VERSION__
    unsigned long long vlen = (unsigned long long)sizeof(__VERSION__) - 1;
    printf("GCC_VLEN: %llu\n", vlen);
#endif
    return 0;
}
EOF
```
  
- Getting the username and Hostname from the machine.

```
echo "Username: '$(id -un)'"
echo "Hostname: '$(hostname -s)'"
```

- Got output as:

<img width="798" height="154" alt="image" src="https://github.com/user-attachments/assets/a2f9e56d-5bfb-4cf0-8584-c948c37d4acd" />

```
Username: 'kevinshah'
Hostname: 'kevinshah-Dell-G15-5520'
```

- Then the following code to test whether all tools were working correctly and whether will i get the output as desired from the c unique test.

```
riscv64-unknown-elf-gcc -O2 -Wall -march=rv64imac -mabi=lp64 \
-DUSERNAME='"kevinshah"' -DHOSTNAME='"kevinshah-Dell-G15-5520"' \
~/riscv_toolchain/unique_test.c -o ~/riscv_toolchain/unique_test

spike pk ~/riscv_toolchain/unique_test
```
<img width="1204" height="338" alt="image" src="https://github.com/user-attachments/assets/864a840a-2b84-4292-a267-56e7228f102f" />

- And finally got the desired output meaning the installation worked correctly and all tools and their dependencies are installed.

```
bbl loader
RISC-V Uniqueness Check
User: kevinshah
Host: kevinshah-Dell-G15-5520
UniqueID: 0x5ccf0ab8853aa91c
GCC_VLEN: 5
```

