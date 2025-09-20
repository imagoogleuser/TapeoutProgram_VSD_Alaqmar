# Week 0: Environment Setup and Tool Installation

This document details the complete setup of the development environment, including the virtual machine configuration and the installation of the essential EDA (Electronic Design Automation) tools required for the RTL-to-GDSII flow.

---

## Part 1: Virtual Machine Configuration

To ensure a consistent and stable environment, a virtual machine was configured with the following specifications using VMware/VirtualBox:

* **Operating System:** Ubuntu 24.04 LTS
* **Allocated Memory (RAM):** 6 GB
* **Processor Cores:** 4 vCPUs
* **Storage:** 50 GB Virtual Hard Disk

Here is a snapshot of the running Ubuntu desktop environment after a successful installation.



---

## Part 2: EDA Toolchain Installation

The following open-source tools were compiled and installed from source or using the `apt` package manager.

### **Yosys - Synthesis Tool**

Yosys is used for Verilog RTL synthesis, transforming the hardware description into a gate-level netlist.

**Installation Steps:**
```bash
# Clone the official Yosys repository
git clone [https://github.com/YosysHQ/yosys.git](https://github.com/YosysHQ/yosys.git)
cd yosys

# Install all required dependencies for building from source
sudo apt-get install build-essential clang bison flex libreadline-dev gawk tcl-dev libffi-dev git graphviz xdot pkg-config python3 libboost-system-dev libboost-python-dev libboost-filesystem-dev zlib1g-dev

# Compile and install the tool
make
sudo make install
```

**Verification:**
A screenshot confirming the successful installation by checking the tool's version.

*(Drag and drop your screenshot of the `yosys --version` command here)*

---

### **Icarus Verilog & GTKWave - Simulation and Waveform Viewing**

`iverilog` is the Verilog compiler and simulator, while `gtkwave` is used for visualizing the resulting waveforms.

**Installation Steps:**
```bash
# Install both tools using the apt package manager
sudo apt-get update
sudo apt-get install iverilog
sudo apt-get install gtkwave
```

**Verification:**
A snapshot showing the installed version of Icarus Verilog.

*(Drag and drop your screenshot of the `iverilog -V` command here)*

---

### **Magic - VLSI Layout Editor**

Magic is a powerful tool for creating and editing VLSI circuit layouts.

**Installation Steps:**
```bash
# Install prerequisite libraries and tools for Magic
sudo apt-get install m4 tcsh csh libx11-dev tcl-dev tk-dev libcairo2-dev mesa-common-dev libglu1-mesa-dev libncurses-dev

# Clone, configure, and compile the Magic source code
git clone [https://github.com/RTimothyEdwards/magic.git](https://github.com/RTimothyEdwards/magic.git)
cd magic
./configure
make
sudo make install
```

**Verification:**
The terminal output after a successful `make install` process.

*(Drag and drop your screenshot of the final installation message here)*

---

### **OpenLane - Automated RTL to GDSII Flow**

OpenLane is a complete automated flow that integrates various tools to convert RTL code into a final GDSII layout file. It relies heavily on Docker for environment management.

**Installation Steps:**
```bash
# Install Docker prerequisites
sudo apt install apt-transport-https ca-certificates curl software-properties-common

# Add Docker's official GPG key and set up the repository
curl -fsSL [https://download.docker.com/linux/ubuntu/gpg](https://download.docker.com/linux/ubuntu/gpg) | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] [https://download.docker.com/linux/ubuntu](https://download.docker.com/linux/ubuntu) $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io

# Clone the OpenLane repository
cd $HOME
git clone [https://github.com/The-OpenROAD-Project/OpenLane.git](https://github.com/The-OpenROAD-Project/OpenLane.git)

# Build the OpenLane environment
cd OpenLane
make
```

**Verification:**
A snapshot showing the successful completion of the `make test` command, which validates the entire flow.

*(Drag and drop your screenshot of the `make test` results here)*

---
