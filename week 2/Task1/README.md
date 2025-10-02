# Design of a RISC-V Based System on Chip (SoC) with Integrated PLL and DAC

## Problem Statement
This project focuses on the design and implementation of an open-source **System on Chip (SoC)** built around the **RVMYTH** RISC-V processor core. The primary goal is to create a compact, educational platform that integrates key analog and digital components on a single chip using Sky130 technology. The SoC includes a **Phase-Locked Loop (PLL)** for high-precision clock generation and a **10-bit Digital-to-Analog Converter (DAC)** to interface with external analog devices. By converting digital data processed by the CPU into analog signals, the DAC enables the SoC to output audio or video to devices like televisions and mobile phones, providing a hands-on learning tool for digital-analog system design.

<details>
<summary>1. What is a System on a Chip (SoC)?</summary>

A **System on a Chip (SoC)** is an integrated circuit that combines all major components of a computer or electronic system onto a single chip. This consolidation makes SoCs ideal for compact, power-sensitive devices like smartphones, smartwatches, and IoT gadgets.

### Core Components of an SoC

1.  **Central Processing Unit (CPU)**: The primary processing unit that executes instructions and performs calculations. It acts as the "brain" of the SoC.
2.  **Memory**: Includes **RAM** for temporary data storage and **ROM** or **Flash** for permanent storage of the operating system and user data.
3.  **Graphics Processing Unit (GPU)**: A specialized processor designed to handle graphics rendering for displaying images, videos, and animations.
4.  **Digital Signal Processor (DSP)**: Optimized for processing real-world signals, such as audio from a microphone or video from a camera.
5.  **I/O Ports (Input/Output)**: Interfaces like USB, HDMI, and GPIO that connect the SoC to external devices and peripherals.
6.  **Power Management Unit**: Manages power consumption across the chip to optimize battery life and thermal performance.
7.  **Connectivity Modules**: Often includes built-in modules for Wi-Fi, Bluetooth, and cellular communication.

### Advantages of SoCs

* **Compact Size**: Integrating all components on one chip saves significant space, enabling smaller and thinner devices.
* **Power Efficiency**: Shorter communication paths between components reduce power consumption, which is crucial for battery-powered devices.
* **Higher Performance**: On-chip communication is much faster than communication between separate chips, leading to improved overall performance.
* **Cost-Effectiveness**: Manufacturing a single chip is generally cheaper than producing and assembling multiple individual components.
* **Increased Reliability**: With fewer separate parts and connections, there are fewer potential points of failure.

<img width="1024" height="1024" alt="unnamed" src="https://github.com/user-attachments/assets/e2a11dbe-cdc5-4f98-b522-fd05b337caf1" />


</details>

<details>
<summary>2. Types of SoCs and Design Flow</summary>

### Types of SoCs

* **Microcontroller-based SoC**: Built around a microcontroller, these are designed for specific control-oriented tasks. They are low-power and commonly found in embedded systems like home appliances, automotive controls, and IoT sensors.
* **Microprocessor-based SoC**: Centered around a more powerful microprocessor capable of running a full operating system like Android or iOS. These are used in complex devices such as smartphones, tablets, and smart TVs.
* **Application-Specific SoC (ASIC-based)**: Custom-designed to perform a single, highly specialized function with maximum efficiency. Examples include SoCs for high-end graphics cards, network switches, and cryptocurrency mining hardware.

### SoC Design Flow

The process of designing an SoC is a complex, multi-stage workflow that transforms a high-level concept into a physical chip. It typically involves system-level design, RTL coding, verification, synthesis, and physical layout.

![img_61d89021d8d47 copy](https://github.com/user-attachments/assets/54b5e8f9-f03d-4b53-a535-859360589119)

</details>

<details>
<summary>3. VSDBabySoC: An Overview</summary>

The **VSDBabySoC** is a minimal SoC designed for educational purposes, integrating three open-source IP cores to test their interoperability and calibrate analog functionality. It features the **RVMYTH** RISC-V processor, an 8x **Phase-Locked Loop (PLL)**, and a 10-bit **Digital-to-Analog Converter (DAC)**.

### Operational Flow

1.  **Clock Generation**: The PLL receives an input clock and generates a stable, higher-frequency clock signal that synchronizes all operations within the SoC, ensuring reliable timing for both the processor and the DAC.
2.  **Data Processing**: The RVMYTH processor executes instructions to process data. For this project, it cycles through a set of digital values stored in its `r17` register, preparing them for conversion.
3.  **Analog Conversion**: The 10-bit DAC takes the digital values from the RVMYTH processor and converts them into a continuous analog signal. This analog output can then be used to drive external devices like speakers or displays.

![333622249-04238eab-4d48-4d57-9061-f8b660a83d6e](https://github.com/user-attachments/assets/38253bb7-b658-496d-a043-15402219e089)

### Key Components

* **RVMYTH**: A simple, customizable RISC-V based CPU core that serves as the processing unit of the SoC.
* **Phase-Locked Loop (PLL)**: A control system that generates a precise and stable output clock signal by locking its phase to a reference input signal. PLLs are essential for clock multiplication, jitter reduction, and frequency synthesis in modern electronics.
* **Digital-to-Analog Converter (DAC)**: A component that converts digital binary data into an analog voltage or current. In this project, a 10-bit DAC is used to interface with the analog world.

## In-Depth: Phase-Locked Loop (PLL)

A **Phase-Locked Loop (PLL)** is a feedback control system that generates an output signal with a phase that is locked to the phase of an input or "reference" signal.

#### PLL Block Diagram
A PLL is composed of three main parts:
* **Phase Detector**: Compares the phase of the input signal with the phase of the oscillator's output signal and produces an error voltage based on the difference.
* **Loop Filter**: A low-pass filter that smooths the error voltage from the phase detector to generate a stable control voltage.
* **Voltage-Controlled Oscillator (VCO)**: An oscillator whose output frequency is controlled by the voltage from the loop filter. The VCO adjusts its frequency to minimize the phase error.

![333810875-fd7730e9-a867-4ce3-bfc6-9453e3d8ad14](https://github.com/user-attachments/assets/217d602f-003d-4606-9bca-855a4832764c)

#### Why Use a PLL Instead of an Off-Chip Clock?
Using an external clock source for a complex chip is often impractical due to:
* **Clock Skew and Delay**: Distributing a clock signal across a large chip introduces delays, making it difficult to keep all components synchronized.
* **Jitter**: Off-chip clocks can have timing variations (jitter) that degrade performance and reliability.
* **Multiple Frequency Domains**: Different blocks on an SoC often require different clock frequencies (e.g., CPU at 200 MHz, peripherals at 100 MHz). A PLL can generate multiple frequencies from a single reference.
* **Crystal Inaccuracy**: External crystal oscillators have frequency errors (measured in parts per million or ppm) that can vary with temperature and age, affecting timing precision.

## In-Depth: Digital-to-Analog Converter (DAC)

A **Digital-to-Analog Converter (DAC)** translates digital data (a series of 0s and 1s) into a continuous analog signal. The VSDBabySoC uses a 10-bit DAC, meaning it accepts a 10-bit digital number and produces a corresponding analog voltage level.

#### Common DAC Architectures
* **Binary-Weighted Resistor DAC**: Uses a network of resistors with values weighted in powers of two. It is simple in concept but can be difficult to fabricate accurately for high-bit DACs.

![binary_weighted_resistors](https://github.com/user-attachments/assets/344e4ffd-7509-41e7-ac42-21a553b3db11)

* **R-2R Ladder DAC**: Employs a repeating ladder network of resistors with only two values (R and 2R). This design is easier to manufacture with high precision and is more scalable than the binary-weighted approach.

![comb99 gif copy](https://github.com/user-attachments/assets/5c15f424-1a94-4424-b019-a76c0ca0db43)

</details>

---

This document provides a comprehensive overview of the VSDBabySoC, its components, and the fundamental concepts of SoC design. Understanding these elements offers a strong foundation for exploring modern embedded systems and digital-analog interfacing.

---
