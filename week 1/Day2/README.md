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

Example Image:
![Hierarchinal](https://github.com/user-attachments/assets/0472a2b3-d7b2-46ab-8c6a-fc272a6d7f9f)


### The Flattened Approach

This approach dissolves all module boundaries, merging the entire design into a single, large netlist before optimization.

* **Benefits**:
    * Enables powerful, global optimizations across the entire logic of the design.
    * Can potentially lead to better area and timing results.
* **Drawbacks**:
    * Significantly increases synthesis runtime and memory usage.
    * The link to the original RTL structure is lost, making debugging very difficult.
Example Image:

![flat](https://github.com/user-attachments/assets/240596fd-aabb-4ad6-a0cd-9064bd08eb85)

### Comparison at a Glance

| Characteristic      | Hierarchical Synthesis              | Flattened Synthesis                 |
| ------------------- | ----------------------------------- | ----------------------------------- |
| **Design Structure**| Preserved from RTL        | Collapsed into one netlist|
| **Optimization** | Local (within modules)    | Global (across the full design)|
| **Compile Time** | Faster                    | Slower                    |
| **Debugging Ease** | High (easy to trace)      | Low (difficult to trace)  |

If we have same multiple modules or if we have a massive design where we want to divide and conquer then we synthesize an individual sub module:
```verilog
synth -top sub_module1
```
Example image:
<img width="1226" height="642" alt="image" src="https://github.com/user-attachments/assets/f431f822-4b82-42f9-812c-1abe33faf1c7" />
Of course. Here is the full paraphrased text formatted for your README file. You can copy the entire block of text below.
Markdown

# Day 2: A Study of Timing Libraries, Synthesis Methods, and Flip-Flop Design

This document covers the essential topics for Day 2 of the RTL Workshop, focusing on three core areas: the structure and use of `.lib` timing libraries with the open-source SKY130 PDK, a comparison of hierarchical and flat synthesis techniques, and best practices for coding various flip-flop circuits in Verilog.

---

# Contents

- [Timing Libraries](#timing-libraries)
  - [SKY130 PDK Overview](#sky130-pdk-overview)
  - [Decoding tt_025C_1v80 in the SKY130 PDK](#decoding-tt_025c_1v80-in-the-sky130-pdk)
  - [Opening and Exploring the .lib File](#opening-and-exploring-the-lib-file)
- [Hierarchical vs. Flattened Synthesis](#hierarchical-vs-flattened-synthesis)
  - [Hierarchical Synthesis](#hierarchical-synthesis)
  - [Flattened Synthesis](#flattened-synthesis)
  - [Key Differences](#key-differences)
- [Flip-Flop Coding Styles](#flip-flop-coding-styles)
  - [Asynchronous Reset D Flip-Flop](#asynchronous-reset-d-flip-flop)
  - [Asynchronous Set D Flip-Flop](#asynchronous-set-d-flip-flop)
  - [Synchronous Reset D Flip-Flop](#synchronous-reset-d-flip-flop)
- [Simulation and Synthesis Workflow](#simulation-and-synthesis-workflow)
  - [Icarus Verilog Simulation](#icarus-verilog-simulation)
  - [Synthesis with Yosys](#synthesis-with-yosys)

---

## Timing Libraries

### SKY130 PDK Overview

The SKY130 PDK is an open-source Process Design Kit for SkyWater Technology's 130nm CMOS manufacturing process. It equips designers with the critical models and libraries required for integrated circuit (IC) design, detailing information on timing, power consumption, and process variations.

### Decoding tt_025C_1v80 in the SKY130 PDK

The naming convention of the library file provides insight into the conditions it models:
* **tt**: Signifies the **Typical-Typical** process corner, representing average manufacturing conditions.
* **025C**: Denotes a standard operating **temperature of 25°C**.
* **1v80**: Specifies a nominal **core voltage of 1.8V**.

### Opening and Exploring the .lib File

To inspect the contents of the `sky130_fd_sc_hd__tt_025C_1v80.lib` file, you can use a text editor.

1.  **Install a text editor like `gedit`:**
    ```shell
    sudo apt install gedit
    ```
2.  **Use it to open the file:**
    ```shell
    gedit sky130_fd_sc_hd__tt_025C_1v80.lib
    ```

---

## Hierarchical vs. Flattened Synthesis

### Hierarchical Synthesis

* **Concept**: This method preserves the original modular structure of the RTL code during synthesis, processing each module as a separate entity.
* **Process**: Tools like Yosys analyze the design's structure using commands like `hierarchy` while keeping the module boundaries intact.

**Pros:**
* Leads to quicker synthesis runs for complex designs.
* Simplifies debugging by maintaining a clear link to the original RTL modules.
* Supports a modular workflow, which helps with integration.

**Cons:**
* Optimization across different modules is restricted.
* Generating comprehensive reports might require extra setup.

### Flattened Synthesis

* **Concept**: This approach collapses the entire design hierarchy into a single, unified netlist.
* **Process**: The `flatten` command in Yosys is used to merge all modules, enabling optimization across the entire design.

**Pros:**
* Allows for more aggressive, design-wide optimizations.
* Creates a single netlist, which can streamline certain post-synthesis steps.

**Cons:**
* Increases synthesis time, especially for large designs.
* Makes debugging more difficult due to the loss of the original hierarchy.
* Can lead to higher memory consumption and a more complex netlist.

> **Note:** The main distinction is that hierarchical synthesis works with sub-modules, whereas flattening builds a single netlist from scratch.

### Key Differences

| Feature               | Hierarchical Synthesis             | Flattened Synthesis           |
| --------------------- | ---------------------------------- | ----------------------------- |
| **Hierarchy** | Maintained                         | Removed                       |
| **Optimization** | Limited to individual modules      | Global (across entire design) |
| **Performance** | Faster on large designs            | Slower on large designs       |
| **Debugging** | Simpler, maps to RTL               | More difficult                |
| **Output** | Retains modular form               | Single, monolithic netlist    |
| **Best For** | Modularity and clear analysis      | Achieving maximum optimization|

---

## Flip-Flop Coding Styles

Flip-flops are the core storage elements in sequential logic. Below are efficient Verilog coding examples for common flip-flop variations.

### Asynchronous Reset D Flip-Flop

```verilog
module dff_asyncres (input clk, input async_reset, input d, output reg q);
  always @ (posedge clk, posedge async_reset)
    if (async_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```
* The **asynchronous reset** signal acts independently of the clock, forcing the output `q` to zero instantly upon assertion.
* The circuit is **edge-triggered**, capturing the `d` input on the rising clock edge when the reset is inactive.

### Asynchronous Set D Flip-Flop

```verilog
module dff_async_set (input clk, input async_set, input d, output reg q);
  always @ (posedge clk, posedge async_set)
    if (async_set)
      q <= 1'b1;
    else
      q <= d;
endmodule
```
* The **asynchronous set** overrides the clock to immediately force the output `q` to one.

### Synchronous Reset D Flip-Flop

```verilog
module dff_syncres (input clk, input async_reset, input sync_reset, input d, output reg q);
  always @ (posedge clk)
    if (sync_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```
* The **synchronous reset** is only applied on the active edge of the clock.

---

## Simulation and Synthesis Workflow

### Icarus Verilog Simulation

1.  **Compile the design:**
    ```shell
    iverilog dff_asyncres.v tb_dff_asyncres.v
    ```
2.  **Execute the simulation:**
    ```shell
    ./a.out
    ```
3.  **Open the waveform:**
    ```shell
    gtkwave tb_dff_asyncres.vcd
    ```

    <img width="1718" height="947" alt="image" src="https://github.com/user-attachments/assets/360693a5-5963-45d7-843e-761fa1ba9753" />


### Synthesis with Yosys

1.  Launch Yosys:
    ```shell
    yosys
    ```
2.  Load the standard cell library:
    ```shell
    read_liberty -lib /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
    ```
3.  Import the Verilog source file:
    ```shell
    read_verilog /path/to/dff_asyncres.v
    ```
4.  Run synthesis on the top module:
    ```shell
    synth -top dff_asyncres
    ```
5.  Map flip-flops to the library's cells:
    ```shell
    dfflibmap -liberty /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
    ```
6.  Perform technology mapping for logic gates:
    ```shell
    abc -liberty /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
    ```
7.  Display the resulting netlist:
    ```shell
    show
    ```
    <img width="1714" height="874" alt="image" src="https://github.com/user-attachments/assets/0880acef-4c21-40f1-a629-125d824ad9e9" />

    INTERESTING OPTIMIZATIONS:
    Mult_2.v example:
    <img width="1725" height="841" alt="image" src="https://github.com/user-attachments/assets/c41f9c56-2352-456f-81ea-bdd5e7356ea5" />

   Mult_8.v Example:
   <img width="1714" height="833" alt="image" src="https://github.com/user-attachments/assets/aaab2f32-0332-4ad2-8397-ebc9c29004b3" />





