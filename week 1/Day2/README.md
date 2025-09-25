# RTL Synthesis and Sequential Logic Design

This guide delves into the essential concepts of digital design synthesis, focusing on timing libraries, synthesis methodologies, and best practices for coding sequential circuits like flip-flops.

---

## 📚 Table of Contents
- [Understanding Timing Libraries (`.lib`)](#-understanding-timing-libraries-lib)
  - [The SKY130 Open Source PDK](#the-sky130-open-source-pdk)
  - [Decoding the Library Naming Convention](#decoding-the-library-naming-convention)
- [Synthesis Strategies: Hierarchical vs. Flattened](#-synthesis-strategies-hierarchical-vs-flattened)
  - [The Hierarchical Approach](#the-hierarchical-approach)
  - [The Flattened Approach](#the-flattened-approach)
  - [Comparison at a Glance](#comparison-at-a-glance)
- [Best Practices for Flip-Flop Coding](#-best-practices-for-flip-flop-coding)
  - [Flip-Flop with Asynchronous Reset](#flip-flop-with-asynchronous-reset)
  - [Flip-Flop with Asynchronous Set](#flip-flop-with-asynchronous-set)
  - [Flip-Flop with Synchronous Reset](#flip-flop-with-synchronous-reset)
- [A Practical Workflow: Simulation & Synthesis](#-a-practical-workflow-simulation--synthesis)
  - [Step 1: Simulation with Icarus Verilog](#step-1-simulation-with-icarus-verilog)
  - [Step 2: Synthesis with Yosys](#step-2-synthesis-with-yosys)

---

## 🔬 Understanding Timing Libraries (`.lib`)

Timing libraries are critical files that characterize the performance (delay, power, etc.) of standard cells for a specific semiconductor process.

### The SKY130 Open Source PDK

The SKY130 Process Design Kit (PDK) is a comprehensive, open-source toolkit for SkyWater Technology's 130nm process. It provides all the necessary files, including the timing libraries, required for designing integrated circuits.

### Decoding the Library Naming Convention

The filename `sky130_fd_sc_hd__tt_025C_1v80.lib` contains information about the conditions under which the cells were characterized:
-   **`tt`**: Represents the **Typical-Typical** process corner, meaning nominal manufacturing conditions.
-   **`025C`**: Specifies an operating **temperature of 25°C**.
-   **`1v80`**: Denotes a **supply voltage of 1.8V**.

---

## ⚙️ Synthesis Strategies: Hierarchical vs. Flattened

Synthesis tools can interpret your RTL design in two primary ways.

### The Hierarchical Approach

This method preserves the design's modular structure as defined in your Verilog files. Each module is synthesized as a separate entity.

* **Benefits**:
    * Faster compilation times for complex designs.
    * Makes debugging easier since the final netlist maps clearly to the original RTL.
    * Promotes a modular and organized design flow.
* **Drawbacks**:
    * Optimizations are confined within module boundaries and cannot occur across them.

### The Flattened Approach

This approach dissolves all module boundaries, merging the entire design into a single, large netlist before optimization.

* **Benefits**:
    * Enables powerful, global optimizations across the entire logic of the design.
    * Can potentially lead to better area and timing results.
* **Drawbacks**:
    * Significantly increases synthesis runtime and memory usage.
    * The link to the original RTL structure is lost, making debugging very difficult.

### Comparison at a Glance

| Characteristic      | Hierarchical Synthesis              | Flattened Synthesis                 |
| ------------------- | ----------------------------------- | ----------------------------------- |
| **Design Structure**| Preserved from RTL        | Collapsed into one netlist|
| **Optimization** | Local (within modules)    | Global (across the full design)|
| **Compile Time** | Faster                    | Slower                    |
| **Debugging Ease** | High (easy to trace)      | Low (difficult to trace)  |

---

## 💾 Best Practices for Flip-Flop Coding

Flip-flops are the fundamental building blocks of sequential logic. Below are standard, synthesizable Verilog templates.

### Flip-Flop with Asynchronous Reset

The reset signal acts immediately, regardless of the clock signal.

```verilog
// DFF with an active-high asynchronous reset
module dff_async_reset(
    input clk,
    input async_reset,
    input d,
    output reg q
);
    always @(posedge clk or posedge async_reset) begin
        if (async_reset)
            q <= 1'b0;
        else
            q <= d;
    end
endmodule
```

### Flip-Flop with Asynchronous Set

The set signal immediately forces the output to '1', overriding the clock.

```verilog
// DFF with an active-high asynchronous set
module dff_async_set(
    input clk,
    input async_set,
    input d,
    output reg q
);
    always @(posedge clk or posedge async_set) begin
        if (async_set)
            q <= 1'b1;
        else
            q <= d;
    end
endmodule
```

### Flip-Flop with Synchronous Reset

The reset condition is only evaluated on the active clock edge.

```verilog
// DFF with an active-high synchronous reset
module dff_sync_reset(
    input clk,
    input sync_reset,
    input d,
    output reg q
);
    always @(posedge clk) begin
        if (sync_reset)
            q <= 1'b0;
        else
            q <= d;
    end
endmodule
```

---

## 🚀 A Practical Workflow: Simulation & Synthesis

### Step 1: Simulation with Icarus Verilog

Verify the functional correctness of your design before synthesis.

1.  **Compile the Verilog Files:**
    ```shell
    iverilog your_design.v your_testbench.v
    ```
2.  **Run the Compiled Simulation:**
    ```shell
    ./a.out
    ```
3.  **Visualize the Waveforms:**
    ```shell
    gtkwave your_dumpfile.vcd
    ```

### Step 2: Synthesis with Yosys

Convert your RTL description into a gate-level netlist using a standard cell library.

1.  **Launch Yosys:**
    ```shell
    yosys
    ```
2.  **Load the Liberty Library:**
    ```tcl
    read_liberty -lib /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
    ```
3.  **Read the Verilog Design:**
    ```tcl
    read_verilog your_design.v
    ```
4.  **Execute Synthesis:**
    ```tcl
    synth -top your_top_module_name
    ```
5.  **Map Flip-Flops to Library Cells:**
    ```tcl
    dfflibmap -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
    ```
6.  **Perform Technology Mapping with ABC:**
    ```tcl
    abc -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
    ```
7.  **Display the Synthesized Netlist:**
    ```tcl
    show your_top_module_name
    ```
