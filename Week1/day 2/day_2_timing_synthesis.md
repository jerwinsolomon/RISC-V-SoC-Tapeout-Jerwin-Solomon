# Day 2: Timing Libraries, Synthesis Approaches, and Efficient Flip-Flop Coding

Welcome to **Day 2** of my RTL Workshop! 🚀  
Today, I explored three key topics:
- Understanding the `.lib` timing library (sky130_fd_sc_hd__tt_025C_1v80.lib) used in open-source PDKs.
- Comparing **hierarchical vs. flat synthesis** methods.
- Learning efficient RTL coding styles for flip-flops.

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

The **SKY130 PDK** is an open-source Process Design Kit based on SkyWater Technology's 130nm CMOS technology. It provides models and libraries for IC design, including **timing**, **power**, and **process variation** data.

### Decoding tt_025C_1v80 in the SKY130 PDK

- **tt** → Typical process corner.
- **025C** → Temperature = 25°C.
- **1v80** → Supply voltage = 1.8V.

This naming convention defines the conditions modeled by the library.

---

### Opening and Exploring the .lib File

Steps I followed:

1. **Install a text editor:**
   ```bash
   sudo apt install gedit
   ```
2. **Open the library file:**
   ```bash
   gedit sky130_fd_sc_hd__tt_025C_1v80.lib
   ```

📸 Screenshot of opened file:  
![Screenshot_2025-05-29_11-43-13](https://github.com/user-attachments/assets/0c31ddf8-8a95-44a4-acaa-e1c5f0518425)

---

## Hierarchical vs. Flattened Synthesis

### Hierarchical Synthesis

- **Definition:** Retains the RTL hierarchy and synthesizes each module separately.
- **Flow:** Uses `hierarchy` command in Yosys to analyze structure.

✅ **Advantages:**
- Faster synthesis time for large designs.
- Easier debugging (modules are preserved).
- Modular flow helps with reusability.

⚠️ **Disadvantages:**
- Limited cross-module optimization.
- Reporting may need more configuration.

📸 Example:  
![Screenshot_2025-05-29_19-04-48](https://github.com/user-attachments/assets/91f0244a-2c41-42ea-be6f-468880c3af33)

---

### Flattened Synthesis

- **Definition:** Collapses all modules into a single netlist.
- **Flow:** Uses `flatten` command in Yosys.

✅ **Advantages:**
- Enables whole-design optimization.
- Produces a single unified netlist.

⚠️ **Disadvantages:**
- Slower runtime for big designs.
- Debugging is harder (loss of hierarchy).
- Can increase memory usage.

📸 Example:  
![Screenshot_2025-05-29_19-20-47](https://github.com/user-attachments/assets/e1d94a5d-d3f7-41ee-8e69-ca0c05be81a3)

---

### Key Differences

| Aspect                | Hierarchical Synthesis             | Flattened Synthesis           |
|-----------------------|------------------------------------|-------------------------------|
| Hierarchy             | Preserved                          | Collapsed                     |
| Optimization Scope    | Module-level only                  | Whole-design                  |
| Runtime               | Faster for large designs           | Slower for large designs      |
| Debugging             | Easier (maps to RTL)               | Harder                        |
| Output Complexity     | Modular                            | Single flat netlist           |
| Use Case              | Modularity, analysis               | Maximum optimization          |

---

## Flip-Flop Coding Styles

Flip-flops are essential sequential elements in RTL. I explored different coding styles:

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

➡️ Reset is immediate (ignores clock).

---

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

➡️ Set forces output to 1 immediately.

---

### Synchronous Reset D Flip-Flop

```verilog
module dff_syncres (input clk, input sync_reset, input d, output reg q);
  always @ (posedge clk)
    if (sync_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```

➡️ Reset only happens on the clock edge.

---

## Simulation and Synthesis Workflow

### Icarus Verilog Simulation

1. **Compile:**
   ```bash
   iverilog dff_asyncres.v tb_dff_asyncres.v
   ```
2. **Run:**
   ```bash
   ./a.out
   ```
3. **View Waveform:**
   ```bash
   gtkwave tb_dff_asyncres.vcd
   ```

📸 Example GTKWave:  
![Screenshot_2025-05-30_10-45-13](https://github.com/user-attachments/assets/1176581e-fd6c-4b71-8af5-5d7d5f6dbcda)

---

### Synthesis with Yosys

1. Start Yosys:
   ```bash
   yosys
   ```
2. Read Liberty library:
   ```tcl
   read_liberty -lib /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```
3. Read Verilog code:
   ```tcl
   read_verilog dff_asyncres.v
   ```
4. Synthesize:
   ```tcl
   synth -top dff_asyncres
   ```
5. Map flip-flops:
   ```tcl
   dfflibmap -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```
6. Technology mapping:
   ```tcl
   abc -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```
7. Visualize netlist:
   ```tcl
   show
   ```

📸 Example Yosys netlist:  
![Screenshot_2025-05-30_11-03-00](https://github.com/user-attachments/assets/fa8337df-e0ec-4b01-9b18-5910768e4421)

---

## Summary

- Explored **timing libraries** in the SKY130 PDK and decoded conditions.
- Compared **hierarchical vs flattened synthesis**, with pros and cons.
- Implemented **three flip-flop coding styles** (async reset, async set, sync reset).
- Practiced **simulation with iverilog** and **synthesis with Yosys**.

### Next Steps 🔥
- Experiment with more flip-flop variations (e.g., enable signals).
- Try hierarchical vs flattened synthesis on larger designs.
- Measure synthesis runtime and optimization effects.

