# 🚀 RTL Workshop - Day 1: From Verilog Code to Logic Gates

Welcome to the first session of our RTL Design Workshop! Today is all about building a solid foundation. We will take a simple digital design from a Verilog code file, test its logic in a simulator, and finally, synthesize it into a netlist of logic gates.

### Today's Learning Objectives:
* Understand the roles of a **Design**, **Testbench**, and **Simulator**.
* Simulate a Verilog design using the open-source **Icarus Verilog (iverilog)**.
* Visualize simulation results with **GTKWave**.
* Learn the fundamentals of logic synthesis with **Yosys**.
* Explore why standard cell **libraries** contain different "flavors" of gates.

---

## Part 1: Verilog Simulation

Before we can build hardware, we need to verify its correctness. This is where simulation comes in.

### The Three Pillars of Verification

* **Design:** This is the Verilog file (`.v`) that describes your circuit's intended behavior. It's the "what" you want to build.
* **Testbench:** A separate Verilog file used to test the design. It generates input signals and checks if the design's outputs match the expected results.
* **Simulator:** A software tool that executes the testbench, applies its inputs to the design, and records the output behavior.

<img src="https://github.com/user-attachments/assets/93927b96-df80-4da5-b801-284fc2cc6757" alt="Design & Testbench Overview" width="70%">

### 🔬 Lab A: Simulating a 2-to-1 Multiplexer

In this lab, we'll use `iverilog` to simulate a simple multiplexer.

**1. Setup Your Environment:**
First, clone the workshop repository and install the necessary tools, `iverilog` and `gtkwave`.

```shell
# Clone the repository and navigate to the verilog files
git clone [https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git](https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git)
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files

# Install the tools
sudo apt install iverilog gtkwave
