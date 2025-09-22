Of course. Here is a paraphrased version of your `README.md` file. It covers all the same information but is rephrased to look unique for your GitHub profile.

-----

# 🚀 Day 1: A Practical Introduction to Verilog RTL and Synthesis

Welcome to the first day of the RTL Workshop\! We're diving straight into the world of digital logic design. Today, you'll learn the fundamentals of Verilog, simulate your first circuit with the open-source tool **Icarus Verilog (iverilog)**, and convert your design into a hardware blueprint using **Yosys**.

## Workshop Agenda

1.  [Core Concepts: Simulator, Design, and Testbench](https://www.google.com/search?q=%231-core-concepts-simulator-design-and-testbench)
2.  [Lab 1: Simulating a 2:1 MUX with iverilog](https://www.google.com/search?q=%232-lab-1-simulating-a-21-mux-with-iverilog)
3.  [A Closer Look at the Verilog Code](https://www.google.com/search?q=%233-a-closer-look-at-the-verilog-code)
4.  [Diving into Synthesis with Yosys](https://www.google.com/search?q=%234-diving-into-synthesis-with-yosys)
5.  [Lab 2: Synthesizing Our MUX](https://www.google.com/search?q=%235-lab-2-synthesizing-our-mux)
6.  [Day 1 Recap](https://www.google.com/search?q=%236-day-1-recap)

-----

## 1\. Core Concepts: Simulator, Design, and Testbench

In digital design, we use three key components to verify our logic:

  * **Design**: The Verilog file that describes how your circuit is supposed to function.
  * **Testbench**: A separate Verilog file created to test the design by providing a sequence of inputs.
  * **Simulator**: A software tool that runs the testbench, applies its inputs to the design, and records the output to verify correctness.
<img width="1515" height="852" alt="448241377-93927b96-df80-4da5-b801-284fc2cc6757" src="https://github.com/user-attachments/assets/20c8a133-e143-4efa-bcd2-24e5f9a00337" />


-----

## 2\. Lab 1: Simulating a 2:1 MUX with iverilog

Let's get hands-on by simulating a 2-to-1 multiplexer. We'll use **iverilog** to compile our code and **GTKWave** to visualize the output waveforms.

### **Step 1: Get the Project Files**

First, clone the workshop repository to your local machine.

```shell
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```

### **Step 2: Install the Tools**

You'll need `iverilog` and `gtkwave`. Install them using your package manager.

```shell
sudo apt install iverilog gtkwave
```

### **Step 3: Run the Simulation**

The simulation process involves compiling the design with its testbench, running the compiled output, and then viewing the results.

```shell
# Compile the Verilog files
iverilog good_mux.v tb_good_mux.v

# Execute the simulation
./a.out

# Open the waveform file in GTKWave
gtkwave tb_good_mux.vcd
```
![Screenshot 2025-09-21 124249](https://github.com/user-attachments/assets/65097ac7-c4af-4525-8856-3c2dedb67a84)



-----

## 3\. A Closer Look at the Verilog Code

Here is the Verilog implementation for our multiplexer (`good_mux.v`).

```verilog
module good_mux (input i0, input i1, input sel, output reg y);
// This block executes whenever any input changes
always @ (*)
begin
    if(sel)
        y <= i1; // If sel is high, output takes the value of i1
    else 
        y <= i0; // Otherwise, output takes the value of i0
end
endmodule
```

The logic is straightforward: the `sel` input determines whether to pass the `i0` or `i1` input to the output `y`.

-----

## 4\. Diving into Synthesis with Yosys

### **What is Yosys?**

**Yosys** is an open-source framework that performs logic synthesis. It takes the abstract Verilog description of your circuit and converts it into a gate-level netlist—a structural description using standard logic gates (AND, OR, NOT, etc.). This netlist is the blueprint for creating the physical chip.

### **Why Do Gate Libraries Have So Many Options?**

Technology libraries (`.lib` files) provide multiple versions of standard gates to give the synthesis tool options for optimization. Each "flavor" of a gate offers a different trade-off between:

  * **Speed:** High-performance gates for timing-critical parts of the circuit.
  * **Power:** Low-power gates to improve energy efficiency.
  * **Area:** Smaller gates to reduce the overall chip size.
  * **Drive Strength:** Gates capable of driving longer wires or more subsequent gates without signal degradation.

-----

## 5\. Lab 2: Synthesizing Our MUX

Now, let's use Yosys to synthesize our `good_mux` design.

1.  **Launch Yosys** in interactive mode.
    ```shell
    yosys
    ```
2.  **Load the Technology Library** containing the standard cell definitions.
    ```shell
    read_liberty -lib /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
    ```
3.  **Read Your Verilog Design** file.
    ```shell
    read_verilog /home/vsduser/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files/good_mux.v
    ```
4.  **Run Generic Synthesis** for the specified top-level module.
    ```shell
    synth -top good_mux
    ```
5.  **Map to the Library** to convert the generic logic into specific gates from your `.lib` file.
    ```shell
    abc -liberty /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
    ```
6.  **Display the Synthesized Netlist** to see a visual representation of the circuit.
    ```shell
    show
    ```

\<div align="center"\>
\<img src="[https://github.com/user-attachments/assets/4b3a9939-92d0-4efc-ad69-e96faf19e6c3](https://github.com/user-attachments/assets/4b3a9939-92d0-4efc-ad69-e96faf19e6c3)" alt="Yosys Gate-level Schematic" width="70%"\>
\</div\>

-----

## 6\. Day 1 Recap

Great work\! Today you successfully:

  * Learned the fundamental workflow of design and verification.
  * Simulated a Verilog module using iverilog and GTKWave.
  * Understood the basics of logic synthesis.
  * Converted a Verilog design into a gate-level netlist with Yosys.
