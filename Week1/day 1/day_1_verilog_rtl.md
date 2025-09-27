# Day 1: Introduction to Verilog RTL Design & Synthesis

Welcome to **Day 1** of my RTL Workshop! 🚀  
Today, I started my journey into digital design by exploring Verilog, open-source simulation with **Icarus Verilog (iverilog)**, and the basics of logic synthesis using **Yosys**. This note documents everything I tried, learned, and observed.

---

## Table of Contents

1. [What is a Simulator, Design, and Testbench?](#1-what-is-a-simulator-design-and-testbench)  
2. [Getting Started with iverilog](#2-getting-started-with-iverilog)  
3. [Lab: Simulating a 2-to-1 Multiplexer](#3-lab-simulating-a-2-to-1-multiplexer)  
4. [Verilog Code Analysis](#4-verilog-code-analysis)  
5. [Introduction to Yosys & Gate Libraries](#5-introduction-to-yosys--gate-libraries)  
6. [Synthesis Lab with Yosys](#6-synthesis-lab-with-yosys)  
7. [Summary & Next Steps](#7-summary--next-steps)

---

## 1. What is a Simulator, Design, and Testbench?

### Simulator
A **simulator** is software that lets me check the functionality of my digital circuit. I can apply test inputs, observe outputs, and verify correctness before moving to hardware.

### Design
The **design** is my Verilog code that describes the logic circuit.

### Testbench
A **testbench** is the environment that applies inputs to the design and checks the output. It doesn’t get synthesized; it’s only for simulation.

---

## 2. Getting Started with iverilog

**iverilog** is the open-source simulator I used. The flow is simple:

- Provide the **design** and **testbench** to iverilog.
- Run the compiled simulation.
- Generate a `.vcd` file to view in **GTKWave**.

---

## 3. Lab: Simulating a 2-to-1 Multiplexer

### Step 1: Clone the Workshop Repository
```bash
$ git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
$ cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```

### Step 2: Install Tools
```bash
$ sudo apt install iverilog
$ sudo apt install gtkwave
```

### Step 3: Compile & Run Simulation
```bash
$ iverilog good_mux.v tb_good_mux.v
$ ./a.out
```

### Step 4: Open Waveform
```bash
$ gtkwave tb_good_mux.vcd
```

✅ **Expected Result:** When `sel=0`, `y=i0`. When `sel=1`, `y=i1`. The waveform should confirm correct mux operation.

---

## 4. Verilog Code Analysis

`good_mux.v`:
```verilog
module good_mux (input i0, input i1, input sel, output reg y);
    always @ (*) begin
        if (sel)
            y <= i1;
        else
            y <= i0;
    end
endmodule
```

- **Inputs:** `i0`, `i1` (data), `sel` (selector)
- **Output:** `y`
- **Logic:** Simple conditional assignment → if `sel=1`, choose `i1`, else `i0`.

---

## 5. Introduction to Yosys & Gate Libraries

### What is Yosys?
**Yosys** is an open-source synthesis tool. It converts my Verilog RTL design into a **gate-level netlist** (actual hardware description).

### Why Libraries Have Gate "Flavors"?
A `.lib` file includes multiple versions of gates:
- **Performance** → faster but bigger/power-hungry gates
- **Power** → smaller, energy-efficient gates
- **Drive strength** → gates strong enough to drive large loads
- **Area** → compact cells for chip space optimization

The synthesis tool picks the best gates depending on design constraints.

---

## 6. Synthesis Lab with Yosys

Here’s how I synthesized `good_mux` using Yosys:

1. **Start Yosys**
```bash
$ yosys
```

2. **Read the Liberty File**
```tcl
read_liberty -lib /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
```

3. **Read Verilog Code**
```tcl
read_verilog good_mux.v
```

4. **Run Synthesis**
```tcl
synth -top good_mux
```

5. **Technology Mapping**
```tcl
abc -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
```

6. **View Schematic**
```tcl
show
```

✅ **Expected Result:** The mux should be mapped into basic gates (AND, OR, NOT).

⚡ **Troubleshooting Tips:**
- If Yosys complains about missing `.lib`, double-check the path.
- Use `write_verilog mux_netlist.v` to save the synthesized netlist.

---

## 7. Summary & Next Steps

- I learned the role of **design, testbench, and simulator**.
- Ran my first **simulation in iverilog** and viewed waveforms in GTKWave.
- Understood the **2-to-1 mux RTL code**.
- Performed my first **synthesis with Yosys** and saw how RTL becomes gates.

### Next Steps 🔥
- Modify this design into a **4-to-1 multiplexer** and simulate it.
- Explore Yosys commands like `stat` to see resource usage.
- Try synthesizing other designs in the repo.

---

