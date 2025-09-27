# Day 5: Advanced Synthesis and Optimization Topics

Welcome to Day 5 of the RTL workshop. This session explores synthesis optimization in Verilog, with a focus on `if-else` and `case` statements, `for` loops, and `generate` blocks. We will also examine how improper coding can lead to the creation of unintended latches, with labs for practical application.

---
## Contents

- [1. If-Else Statements in Verilog](#1-if-else-statements-in-verilog)
- [2. Inferred Latches in Verilog](#2-inferred-latches-in-verilog)
- [3. Labs for If-Else and Case Statements](#3-labs-for-if-else-and-case-statements)
  - [Lab 1: Incomplete If Statement](#lab-1-incomplete-if-statement)
  - [Lab 2: Synthesis Result of Lab 1](#lab-2-synthesis-result-of-lab-1)
  - [Lab 3: Nested If-Else](#lab-3-nested-if-else)
  - [Lab 4: Synthesis Result of Lab 3](#lab-4-synthesis-result-of-lab-3)
  - [Lab 5: Complete Case Statement](#lab-5-complete-case-statement)
  - [Lab 6: Synthesis Result of Lab 5](#lab-6-synthesis-result-of-lab-5)
  - [Lab 7: Incomplete Case Handling](#lab-7-incomplete-case-handling)
  - [Lab 8: Partial Assignments in Case](#lab-8-partial-assignments-in-case)
- [4. For Loops in Verilog](#4-for-loops-in-verilog)
- [5. Generate Blocks in Verilog](#5-generate-blocks-in-verilog)
- [6. What is an RCA (Ripple Carry Adder)?](#6-what-is-an-rca-ripple-carry-adder)
- [7. Labs on Loops and Generate Blocks](#7-labs-on-loops-and-generate-blocks)
  - [Lab 9: 4-to-1 MUX Using For Loop](#lab-9-4-to-1-mux-using-for-loop)
  - [Lab 10: 8-to-1 Demux Using Case](#lab-10-8-to-1-demux-using-case)
  - [Lab 11: 8-to-1 Demux Using For Loop](#lab-11-8-to-1-demux-using-for-loop)
  - [Lab 12: 8-bit Ripple Carry Adder with Generate Block](#lab-12-8-bit-ripple-carry-adder-with-generate-block)
- [Summary](#summary)

---

## 1. If-Else Statements in Verilog

**If-else statements** provide a way to control the flow of execution based on specific conditions. They are used in behavioral modeling and are valid inside procedural blocks like `always` and `initial`.

### Syntax

```verilog
if (condition) begin
    // This block runs if the condition is true
end else begin
    // This block runs if the condition is false
end
```

- **condition**: Any expression that evaluates to true (non-zero, 'x', or 'z') or false (zero).
- **begin ... end**: These keywords are necessary to group multiple statements into a single block.
- The `else` clause is optional.

#### Nested If-Else
You can chain conditions for more complex logic.
```verilog
if (condition1) begin
    // Executes for condition1
end else if (condition2) begin
    // Executes for condition2
end else begin
    // Executes if no other conditions are met
end
```

---

## 2. Inferred Latches in Verilog

A synthesis tool creates an **unintended latch** when it encounters combinational logic where an output is not assigned a value under every possible condition. Since the output must hold its previous value, a latch is inferred to create this memory behavior.

### Example of Latch Inference

```verilog
module ex (input wire a, b, sel, output reg y);
    always @(a, b, sel) begin
        if (sel == 1'b1)
            y = a; // What happens if sel is 0? 'y' is not assigned.
    end
endmodule
```

**Issue**: A latch is inferred because the code doesn't specify what `y` should be when `sel` is 0.

#### Solution: Ensure All Paths Assign a Value

```verilog
module ex (input wire a, b, sel, output reg y);
    always @(a, b, sel) begin
        case(sel)
            1'b1    : y = a;
            default : y = 1'b0; // Default assignment prevents a latch
        endcase
    end
endmodule
```

---

## 3. Labs for Conditional Statements

### Lab 1: If Statement with Incomplete Logic
```verilog
module incomp_if (input i0, input i1, input i2, output reg y);
always @(*) begin
    if (i0)
        y <= i1;
end
endmodule
```
<img width="1574" height="759" alt="image" src="https://github.com/user-attachments/assets/f161f3aa-2677-40e0-8f97-10908dbded72" />


### Lab 2: Synthesis of Incomplete If Statement

---

### Lab 3: Incomplete Nested If-Else Logic
```verilog
module incomp_if2 (input i0, input i1, input i2, input i3, output reg y);
always @(*) begin
    if (i0)
        y <= i1;
    else if (i2)
        y <= i3;
end
endmodule
```
<img width="1669" height="834" alt="image" src="https://github.com/user-attachments/assets/90fd8185-f7de-4282-ac36-0d4fe6efbe0d" />

### Lab 4: Synthesis of Incomplete Nested If-Else

---

### Lab 5: Case Statement with Full Coverage
```verilog
module comp_case (input i0, input i1, input i2, input [1:0] sel, output reg y);
always @(*) begin
    case(sel)
        2'b00 : y = i0;
        2'b01 : y = i1;
        default : y = i2;
    endcase
end
endmodule
```
<img width="1511" height="705" alt="image" src="https://github.com/user-attachments/assets/1d8f8e66-61b8-4487-8afb-87c6eda57f16" />


### Lab 6: Synthesis of Complete Case Statement

---

### Lab 7: Case Statement with Incomplete Logic
```verilog
module bad_case (
    input i0, input i1, input i2, input i3,
    input [1:0] sel,
    output reg y
);
always @(*) begin
    case(sel)
        2'b00: y = i0;
        2'b01: y = i1;
        2'b10: y = i2;
        2'b1?: y = i3; // Wildcard '?' can lead to incomplete cases if not used carefully
    endcase
end
endmodule
```
<img width="1651" height="655" alt="image" src="https://github.com/user-attachments/assets/299a265f-a140-4570-9315-6ddfc2793640" />

### Lab 8: Case with Partial Signal Assignments
```verilog
module partial_case_assign (
    input i0, input i1, input i2,
    input [1:0] sel,
    output reg y, output reg x
);
always @(*) begin
    case(sel)
        2'b00: begin
            y = i0;
            x = i2;
        end
        2'b01: y = i1; // 'x' is not assigned here, inferring a latch for 'x'
        default: begin
            x = i1;
            y = i2;
        end
    endcase
end
endmodule
```
<img width="1645" height="681" alt="image" src="https://github.com/user-attachments/assets/947c65aa-08c9-4eae-9435-f7c6a42dd357" />

---

## 4. For Loops in Verilog

The **`for` loop** provides a mechanism for repeatedly executing a block of code within a procedural context (`initial`, `always`, tasks, or functions).

### Syntax
```verilog
for (initial_assignment; loop_condition; step_assignment) begin
    // Code to be repeated
end
```
- For a `for` loop to be synthesizable, the synthesis tool must be able to determine the total number of loop iterations during compilation (i.e., the loop bounds must be constant).

---

## 5. Generate Blocks in Verilog

The **`generate` construct** is a powerful Verilog feature that allows for the conditional or iterative creation of hardware structures during elaboration (compile time). It is commonly used with `for` loops, requiring a `genvar` loop variable.

#### Example
```verilog
genvar i;
generate
    for (i = 0; i < 4; i = i + 1) begin : gen_loop_name
        // Iteratively instantiate a module or logic
        and_gate instance_name (.a(in[i]), .b(in[i+1]), .y(out[i]));
    end
endgenerate
```

---

## 6. What is an RCA (Ripple Carry Adder)?

A **Ripple Carry Adder (RCA)** is a digital circuit for binary addition built by cascading multiple full adder blocks in series. For an `n`-bit addition, `n` full adders are required. The carry-out from each full adder "ripples" to become the carry-in of the next, more significant full adder.

---

## 7. Labs on Iterative Structures

### Lab 9: Implementing a 4-to-1 MUX with a For Loop
```verilog
module mux_generate (
    input i0, input i1, input i2, input i3,
    input [1:0] sel,
    output reg y
);
wire [3:0] i_int;
assign i_int = {i3, i2, i1, i0};
integer k;
always @(*) begin
    for (k = 0; k < 4; k = k + 1) begin
        if (k == sel)
            y = i_int[k];
    end
end
endmodule
```

### Lab 10: Implementing an 8-to-1 Demux with a Case Statement
```verilog
module demux_case (
    output o0, output o1, output o2, output o3,
    output o4, output o5, output o6, output o7,
    input [2:0] sel,
    input i
);
reg [7:0] y_int;
assign {o7, o6, o5, o4, o3, o2, o1, o0} = y_int;
always @(*) begin
    y_int = 8'b0; // Default assignment
    case(sel)
        3'b000 : y_int[0] = i;
        3'b001 : y_int[1] = i;
        3'b010 : y_int[2] = i;
        3'b011 : y_int[3] = i;
        3'b100 : y_int[4] = i;
        3'b101 : y_int[5] = i;
        3'b110 : y_int[6] = i;
        3'b111 : y_int[7] = i;
    endcase
end
endmodule
```

### Lab 11: Implementing an 8-to-1 Demux with a For Loop
```verilog
module demux_generate (
    output o0, output o1, output o2, output o3,
    output o4, output o5, output o6, output o7,
    input [2:0] sel,
    input i
);
reg [7:0] y_int;
assign {o7, o6, o5, o4, o3, o2, o1, o0} = y_int;
integer k;
always @(*) begin
    y_int = 8'b0; // Default assignment
    for (k = 0; k < 8; k = k + 1) begin
        if (k == sel)
            y_int[k] = i;
    end
end
endmodule
```

### Lab 12: Creating an 8-bit RCA with a Generate Block
```verilog
module rca (
    input [7:0] num1,
    input [7:0] num2,
    output [8:0] sum
);
wire [7:0] int_sum;
wire [7:0] int_co;

// Instantiate the first full adder for the LSB
fa u_fa_0 (.a(num1[0]), .b(num2[0]), .c(1'b0), .co(int_co[0]), .sum(int_sum[0]));

genvar i;
generate
    // Generate the remaining 7 full adders
    for (i = 1; i < 8; i = i + 1) begin
        fa u_fa_i (.a(num1[i]), .b(num2[i]), .c(int_co[i-1]), .co(int_co[i]), .sum(int_sum[i]));
    end
endgenerate

assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule

// Full Adder Behavioral Model
module fa (input a, input b, input c, output co, output sum);
    assign {co, sum} = a + b + c;
endmodule
```

---

## Summary

- To prevent unwanted latches, ensure all outputs are assigned a value in every branch of combinational logic.
- Leverage `for` loops and `generate` blocks to create scalable and parameterizable hardware.
- The provided labs offer practical demonstrations of these concepts and their impact on synthesis.
