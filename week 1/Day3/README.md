# Day 3: Optimizing Combinational and Sequential Logic

Welcome to the third day of the workshop. This session focuses on the optimization of both combinational and sequential circuits, exploring various techniques designed to improve the efficiency and performance of digital designs.

---

## Table of Contents

- [1. Constant Propagation](#1-constant-propagation)
- [2. State Optimization](#2-state-optimization)
- [3. Cloning](#3-cloning)
- [4. Retiming](#4-retiming)
- [5. Labs on Optimization](#5-labs-on-optimization)
  - [Lab 1](#lab-1)
  - [Lab 2](#lab-2)
  - [Lab 3](#lab-3)
  - [Lab 4](#lab-4)
  - [Lab 5](#lab-5)
  - [Lab 6](#lab-6)

---

## 1. Constant Propagation

Constant propagation is an optimization strategy used by synthesis tools where variables with a known, fixed value are substituted with that value. This process can significantly simplify a design and boost its performance.

**How it works:** The synthesis tool analyzes the RTL code to find any variables that are assigned a constant value. These variables are then replaced with the constants themselves, which allows the tool to remove unnecessary logic and shrink the overall circuit.

**Benefits:**
- **Simplified Logic:** Results in a smaller, less complex circuit.
- **Enhanced Performance:** Can lead to faster logic and reduced path delays.
- **Efficient Resource Use:** Often reduces the number of gates or flip-flops needed.

---

## 2. State Optimization

State optimization is a process for making Finite State Machines (FSMs) more efficient in an IC design. The primary goals are to decrease the number of states, select an optimal encoding scheme, and minimize the supporting logic.

**Methodology:**
- **Reducing States:** Equivalent states are identified and merged using specialized algorithms.
- **Optimizing State Assignments:** State codes are chosen to minimize the complexity of the combinational logic.
- **Minimizing Logic:** Boolean algebra and optimization tools are used to create more compact logic equations.
- **Optimizing for Power:** Techniques like clock gating are employed to lower dynamic power consumption.

---

## 3. Cloning

In VLSI, cloning refers to the practice of creating identical copies of a logic cell or module. This is done to improve performance, manage power, or resolve timing issues by balancing fan-out or shortening critical wire lengths.

**Process:**
- The design is analyzed to identify critical paths with timing violations.
- The target cell or module causing the bottleneck is duplicated.
- The connections (fan-out) are redistributed between the original and the cloned cell.
- The new cell is placed and routed optimally.
- The design is re-verified to confirm improvements in timing and power.

---

## 4. Retiming

Retiming is a sequential optimization technique that improves a circuit's performance by strategically moving flip-flops across combinational logic. This is done without altering the circuit's input/output behavior.

**Methodology:**
1.  **Modeling the Circuit:** The circuit is first represented as a directed graph where nodes are logic gates and edges are connections.
2.  **Shifting Registers:** Registers are repositioned across the graph to balance the delay between paths.
3.  **Verifying Constraints:** The process ensures that timing and functional behavior remain identical to the original design.
4.  **Final Optimization:** Register placement is fine-tuned to achieve the minimum possible clock period or to optimize for power.

---

## 5. Labs on Optimization

### Lab 1

Verilog for Lab 1:

```verilog
module opt_check (input a , input b , output y);
	assign y = a?b:0;
endmodule
```

**Functionality:**
This code describes a 2-to-1 multiplexer where `a` is the selector. If `a` is high, output `y` is driven by input `b`; otherwise, `y` is assigned the constant value `0`.

To run, follow the synthesis steps from Day 1 and insert this command between `abc -liberty` and `synth -top`:
```shell
opt_clean -purge
```
<img width="1509" height="768" alt="image" src="https://github.com/user-attachments/assets/1aee19bb-5468-41dd-ac50-f29f1b6bee69" />


---

### Lab 2

Verilog for Lab 2:

```verilog
module opt_check2 (input a , input b , output y);
	assign y = a?1:b;
endmodule
```

**Functionality:**
This module also functions as a 2-to-1 multiplexer.
- If `a` is true, the output `y` is assigned the constant value `1`.
- If `a` is false, `y` is driven by the input `b`.

---
<img width="1653" height="748" alt="image" src="https://github.com/user-attachments/assets/9bdbdf43-4ef7-4c9c-8c78-b8127dc06df9" />


### Lab 3

Verilog for Lab 3:

```verilog
module opt_check3 (input a , input b , input c, output y);
	assign y = a?(c?b:0):0;
endmodule
```

**Functionality**: This code describes a logic circuit where inputs a and c act as enable signals.

    If a is false (0), the output y is assigned 0.

    If a is true (1), the logic then checks c:

        If c is also true (1), the output y is assigned the value of b.

        If c is false (0), the output y is assigned 0.

In short, the output y is equal to b only when both a and c are true. Otherwise, y is always zero. This is logically equivalent to a 3-input AND gate: y = a & c & b.
<img width="1689" height="763" alt="image" src="https://github.com/user-attachments/assets/a4155c25-6ddd-4882-9cc4-90ceeffcc29a" />


---

### Lab 4

Verilog for Lab 4:

```verilog
module opt_check4 (input a , input b , input c , output y);
 assign y = a?(b?(a & c ):c):(!c);
 endmodule
```

**Functionality:**
- The logic uses a nested conditional (ternary) assignment.
- A careful analysis shows the logic simplifies:
  - If `a = 1`, the expression becomes `y = (b?(1 & c):c)`, which simplifies to `y = (b?c:c)`, so `y = c`.
  - If `a = 0`, the expression becomes `y = !c`.
- Therefore, the entire statement is equivalent to `y = a ? c : !c`.
<img width="1709" height="766" alt="image" src="https://github.com/user-attachments/assets/d446036f-f5f6-4c0c-a163-72ff21c75ed1" />

---


### Lab 5

Verilog for Lab 5:

```verilog
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
end
endmodule
```

**Functionality:**
- This describes a D-type flip-flop.
- It features an asynchronous reset that forces the output `q` to `0`.
- When not in reset, the flip-flop is always loaded with a constant `1` on every positive clock edge.
**Simulation**
<img width="1651" height="754" alt="image" src="https://github.com/user-attachments/assets/0e73b052-451d-4588-b141-5e7a1110523a" />
**Synthesis**
<img width="1717" height="840" alt="image" src="https://github.com/user-attachments/assets/1c932ac6-b1aa-4504-abc4-56cede41f6e4" />


---

### Lab 6

Verilog for Lab 6:

```verilog
module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b1;
	else
		q <= 1'b1;
end
endmodule
```

**Functionality:**
- This circuit's output `q` is invariably driven to `1`. Both the asynchronous reset condition and the normal clocked behavior assign `q` to be `1`.
**Simulation**
<img width="1701" height="820" alt="image" src="https://github.com/user-attachments/assets/5b828f9d-a5e6-4d4d-861d-5f2f627af053" />
**Synthesis**
<img width="1645" height="804" alt="image" src="https://github.com/user-attachments/assets/a0ae4a5a-bdc7-41c1-ad9d-299ffa2bea8d" />

---
### Lab 7

Verilog for Lab 7:

```verilog
module dff_const3(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end

endmodule
```

**Functionality:**
- This circuit's output `q` is invariably driven to `1`. Both the asynchronous reset condition and the normal clocked behavior assign `q` to be `1`.
**Simulation**
<img width="1667" height="606" alt="image" src="https://github.com/user-attachments/assets/019f2143-5c05-4a67-8a07-cc7559cc9db8" />
**Synthesis**
<img width="1716" height="775" alt="image" src="https://github.com/user-attachments/assets/2632d4cf-de2e-47ce-935a-f4015e12e742" />

---

## Summary
- **Main Focus:** This session explored optimization strategies for digital circuits, demonstrated through a series of hands-on Verilog labs.
  
- **Topics Covered:**
  1.  **Constant Propagation:** How substituting known constants simplifies logic.
  2.  **State Optimization:** Methods for making FSMs more compact and power-efficient.
  3.  **Cloning:** The technique of duplicating cells to resolve timing and load issues.
  4.  **Retiming:** How repositioning registers can improve a circuit's overall performance.

- **Practical Labs:** The six labs provide concrete examples of these optimization principles in action, from combinational logic simplification to the behavior of D flip-flops with constant inputs.
