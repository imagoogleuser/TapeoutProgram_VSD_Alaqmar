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

