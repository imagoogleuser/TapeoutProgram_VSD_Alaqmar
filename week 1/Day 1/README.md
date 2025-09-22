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

2. Run the Simulation:
Use iverilog to compile your design (good_mux.v) and testbench (tb_good_mux.v). Then, run the compiled output and open the waveform.
# Compile
iverilog good_mux.v tb_good_mux.v

# Execute
./a.out

# Visualize
gtkwave tb_good_mux.vcd
<img src="https://github.com/user-attachments/assets/701e8189-3101-4a82-8134-e799521b9a8b" alt="GTKWave Example" width="70%">
Of course. Here is the complete markdown code in a single block. You can click the "Copy" button in the top-right corner and paste it directly into your README.md file on GitHub.
Markdown

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

2. Run the Simulation:
Use iverilog to compile your design (good_mux.v) and testbench (tb_good_mux.v). Then, run the compiled output and open the waveform.
Shell

# Compile
iverilog good_mux.v tb_good_mux.v

# Execute
./a.out

# Visualize
gtkwave tb_good_mux.vcd

<img src="https://github.com/user-attachments/assets/701e8189-3101-4a82-8134-e799521b9a8b" alt="GTKWave Example" width="70%">

Part 2: Logic Synthesis

Simulation proves our logic is correct. Synthesis is the process of converting that abstract logic into an actual circuit made of standard logic gates.

Our Toolkit: Yosys

Yosys is an open-source synthesis tool that transforms our Verilog HDL into a gate-level netlist, which is essentially a blueprint for the physical chip. It handles optimization, technology mapping, and verification.

What's with all the gate "flavors" in a library?
A technology library (.lib file) doesn't just have one AND gate. It has many variations to help the synthesis tool make trade-offs between:

    Performance: Faster gates for critical timing paths.

    Power: Low-power gates for non-critical paths to save energy.

    Area: Smaller gates to make the chip more compact.

    Drive Strength: Stronger gates that can drive longer wires or more subsequent gates.

💡 Lab B: Synthesizing the Multiplexer

Let's use Yosys to turn our good_mux.v into a gate-level circuit.

1. Launch Yosys and Load Resources:
Start Yosys, then load the technology library and your Verilog design file.
# Start the Yosys interactive shell
yosys

# In the Yosys shell, load the library and design
read_liberty -lib /path/to/your/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog /path/to/your/good_mux.v
2. Synthesize and Map:
Run the synthesis command for your top module (good_mux), and then map it to the cells in your loaded library.
# Synthesize the design
synth -top good_mux

# Map the generic logic to the library cells
abc -liberty /path/to/your/sky130_fd_sc_hd__tt_025C_1v80.lib
3. Visualize the Result:
Use the show command to see a graphical representation of your synthesized circuit.
# Display the gate-level netlist
show
<img src="https://github.com/user-attachments/assets/4b3a9939-92d0-4efc-ad69-e96faf19e6c3" alt="Yosys Gate-level Schematic" width="70%">
