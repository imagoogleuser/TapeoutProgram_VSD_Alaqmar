# Day 4: Gate-Level Simulation, Verilog Assignment Types, and Synthesis Mismatches

Welcome to the fourth day of the RTL Workshop. Today's session explores three vital topics in the digital design workflow:

- **Understanding Gate-Level Simulation (GLS)**
- **Differentiating Blocking vs. Non-Blocking Assignments in Verilog**
- **Identifying and Preventing Synthesis-Simulation Mismatches**

This guide provides both theoretical background and practical labs to help you master these concepts.

---

## Table of Contents

- [1. Gate-Level Simulation (GLS)](#1-gate-level-simulation-gls)
- [2. Synthesis-Simulation Mismatch](#2-synthesis-simulation-mismatch)
- [3. Blocking vs. Non-Blocking Assignments in Verilog](#3-blocking-vs-non-blocking-assignments-in-verilog)
  - [3.1 Blocking Statements](#31-blocking-statements)
  - [3.2 Non-Blocking Statements](#32-non-blocking-statements)
  - [3.3 Comparison Table](#33-comparison-table)
- [4. Labs](#4-labs)
- [5. Summary](#5-summary)

---

## 1. Gate-Level Simulation (GLS)

**GLS** is an essential verification stage where a digital circuit's synthesized gate-level netlist is simulated. This process validates several key aspects of the design:

- **Functional Correctness:** Confirms the logic operates as intended after synthesis.
- **Timing Behavior:** Checks for timing violations using realistic gate and wire delays.
- **Power Analysis:** Helps in obtaining more accurate power estimates.
- **Testability Features:** Verifies structures like scan chains used for Design for Test (DFT).

### Why is GLS Necessary?

- **To Verify Synthesis:** Ensures the synthesis tool correctly translated the RTL to a gate-level implementation.
- **To Check Timing:** Uses Standard Delay Format (SDF) files to simulate with realistic delays and catch setup/hold time violations.
- **To Validate Test Structures:** Confirms that DFT features are functional in the post-synthesis netlist.

### When to Perform GLS

- **Post-Synthesis:** After converting the RTL code into a gate-level netlist.
- **Pre-Layout:** To identify and fix functional or timing bugs before the time-consuming physical design stage.

### Categories of GLS

- **Functional Simulation:** Checks logical correctness, typically using zero or unit delays.
- **Timing Simulation:** Incorporates annotated delay information to verify the design against real-world timing constraints.

---

## 2. Synthesis-Simulation Mismatch

A **synthesis-simulation mismatch** is a discrepancy where the behavior of the RTL code in simulation differs from the behavior of the synthesized gate-level netlist. This can happen for several reasons:

- **Use of Non-Synthesizable Code:** Employing Verilog constructs that cannot be turned into hardware, such as `#delay`, `initial` blocks, or `fork-join` statements.
- **Ambiguous RTL Coding:** Writing code with incomplete sensitivity lists or `if`/`case` statements that don't cover all possibilities, which can create unintended latches.
- **Tool Discrepancies:** Different interpretations of ambiguous or poorly written RTL by simulation and synthesis tools.

**Best Practice:** To avoid mismatches, write clear, synthesizable RTL code and adhere to established coding guidelines.

---

## 3. Blocking vs. Non-Blocking Assignments in Verilog

Verilog provides two assignment operators for use within procedural blocks.

### 3.1 Blocking Statements (`=`)

- **Syntax:** Uses the `=` symbol.
- **Behavior:** Assignments are executed sequentially in the order they appear. The next statement is "blocked" until the current one is complete.
- **Best Use Case:** Modeling combinational logic inside an `always @(*)` block.
- **Example:**
  ```verilog
  always @(*) y = a & b;
  ```

### 3.2 Non-Blocking Statements (`<=`)

- **Syntax:** Uses the `<=` symbol.
- **Behavior:** Assignments are scheduled to occur at the end of the current simulation time step. All non-blocking assignments in a block execute in parallel.
- **Best Use Case:** Modeling sequential logic (registers and flip-flops) inside a clocked `always @(posedge clk)` block.
- **Example:**
  ```verilog
  always @(posedge clk) q <= d;
  ```

### 3.3 Comparison Table

| **Blocking (`=`)** | **Non-Blocking (`<=`)** |
|-------------------------------------------|--------------------------------------------|
| Syntax is the `=` symbol                  | Syntax is the `<=` symbol                  |
| Executes sequentially, in order           | Executes in parallel at the end of a time step |
| Variable is updated immediately           | All updates are scheduled to happen together |
| Model combinational logic or temp variables | Model registers and flip-flops             |
| Synthesizes to combinational gates        | Synthesizes to sequential elements (flops)   |

---

## 4. Labs

### Lab 1: Ternary Operator MUX

A 2-to-1 multiplexer implemented with a Verilog conditional (ternary) operator.

```verilog
module ternary_operator_mux (input i0, input i1, input sel, output y);
  assign y = sel ? i1 : i0;
endmodule
```
- **Function:** If `sel` is `1`, `y` is assigned `i1`; otherwise, `y` is assigned `i0`.

---

### Lab 2: Synthesis Using Yosys

Synthesize the multiplexer from Lab 1 using the standard Yosys workflow.

---

### Lab 3: Gate-Level Simulation (GLS) of MUX

Perform a gate-level simulation on the synthesized MUX netlist. This requires including the primitive cell definitions and the standard cell library Verilog models in the compilation.

```shell
iverilog /path/to/primitives.v /path/to/sky130_fd_sc_hd.v ternary_operator_mux.v testbench.v
```

---

### Lab 4: Bad MUX Example (Common Pitfalls)

An example of a poorly coded MUX to demonstrate common mistakes.

```verilog
module bad_mux (input i0, input i1, input sel, output reg y);
  always @ (sel) begin
    if (sel)
      y <= i1;
    else 
      y <= i0;
  end
endmodule
```

#### What's Wrong?
- **Incomplete Sensitivity List:** The `always` block's sensitivity list is missing `i0` and `i1`. It should be `@(*)`.
- **Incorrect Assignment Type:** It uses non-blocking assignments (`<=`) to model combinational logic, which is not recommended and can lead to simulation mismatches.

**Corrected Code:**
```verilog
always @ (*) begin
  if (sel)
    y = i1;
  else
    y = i0;
end
```

---

### Lab 5: GLS of Bad MUX

Conduct a gate-level simulation on the `bad_mux` module. Due to the coding issues, you may observe mismatches between its RTL and gate-level behavior.

---

### Lab 6: Blocking Assignment Caveat

An example demonstrating an ordering issue with blocking assignments.

```verilog
module blocking_caveat (input a, input b, input c, output reg d);
  reg x;
  always @ (*) begin
    d = x & c;
    x = a | b;
  end
endmodule
```

#### The Problem:
Because blocking assignments execute in order, the line `d = x & c;` uses the value of `x` from the *previous* time the block was triggered, not the new value of `x` calculated on the next line. This creates a functional difference from what might be intended.

**Corrected Order:**
```verilog
always @ (*) begin
  x = a | b;
  d = x & c;
end
```

---

### Lab 7: Synthesis of the Blocking Caveat Module

Synthesize the correctly ordered version of the `blocking_caveat` module to see the resulting logic.

---

## 5. Summary

- **Gate-Level Simulation (GLS):** A critical post-synthesis step to verify the functionality, timing, and testability of the gate-level netlist.
- **Synthesis-Simulation Mismatch:** Often caused by non-synthesizable or ambiguous code. Sticking to good RTL coding practices is key to avoiding it.
- **Blocking vs. Non-Blocking:** A fundamental rule is to use blocking (`=`) for combinational logic and non-blocking (`<=`) for sequential logic.
- **Practical Labs:** The hands-on labs illustrate these principles and highlight common pitfalls in RTL design.

---

> [!TIP]
>  Make it a habit to simulate both your RTL and your gate-level netlist. Pay close attention to any warnings issued by your synthesis and simulation tools!
