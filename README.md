# SIMULATION AND IMPLEMENTATION OF MULTIPLEXER

## AIM
To design and simulate a 4:1 Multiplexer (MUX) using Verilog HDL in four different modeling styles—Gate-Level, Data Flow, Behavioral, and Structural—and to verify its functionality through a testbench using the Vivado 2023.1 simulation environment. The experiment aims to understand how different abstraction levels in Verilog can be used to describe the same digital logic circuit and analyze their performance.

## APPARATUS REQUIRED
- **Vivado 2023.1**

## Procedure
### Launch Vivado
- Open Vivado 2023.1 from the Start menu or desktop icon.

### Create a New Project
1. Click **"Create Project"**.
2. Enter the project name (e.g., `Mux4_to_1`) and choose a location. Click **Next**.
3. Select **RTL Project** and click **Next**.
4. Add Verilog files (e.g., `mux4_to_1_gate.v`, `mux4_to_1_dataflow.v`) and enable **"Copy sources into project"**. Click **Next**.
5. Skip the Constraints step and proceed.
6. Select a default FPGA part (e.g., `xc7a35ticsg324-1L`). Click **Next**, then **Finish**.

### Add Source Files (if needed)
- In the Sources window, right-click on **"Design Sources"** → **Add Sources**.
- Add Verilog files and the testbench (`mux4_to_1_tb.v`).

### Check Syntax
- In the Flow Navigator, under **Synthesis**, click **"Run Synthesis"**.
- Correct any errors or warnings, then re-run the synthesis.

### Simulate the Design
- In the Flow Navigator, under **Simulation**, click **"Run Behavioral Simulation"**.

### Analyze Simulation Results
- View waveform signals (e.g., `S1`, `S0`, `A`, `B`, `C`, `D`, etc.).
- Use the waveform controls to zoom, scroll, and analyze results.

### Adjust Simulation Time
- Click **"Simulation"** → **"Simulation Settings"** to modify the run time (e.g., 1000ns).

### Generate Report
- Right-click the simulation window → **"Export Simulation Results"**.

### Save and Document
- Click **File** → **Save Project**.
- Capture screenshots of the waveform window for your lab report.

### Close Simulation
- Go to **"Simulation"** → **"Close Simulation"** when finished.

---

## Logic Diagram
![image](https://github.com/user-attachments/assets/d4ab4bc3-12b0-44dc-8edb-9d586d8ba856)

---

## Truth Table
![image](https://github.com/user-attachments/assets/c850506c-3f6e-4d6b-8574-939a914b2a5f)

---

## Verilog Code

### 4:1 MUX Gate-Level Implementation
```verilog
module mux_24gl(s,a,b,c,d,y);
input [1:0]s;
input a,b,c,d;
output y;
wire [5:0]w;
not g1(w[0],s[0]);
not g2(w[1],s[1]);
and g3(w[2],w[0],w[1],a);
and g4(w[3],w[0],s[1],b);
and g5(w[4],s[0],w[1],c);
and g6(w[5],s[0],s[1],d);
or o1(y,w[2],w[3],w[4],w[5]);
endmodule
```
## Simulated Output Gate Level Modelling

![gate flow modelling](https://github.com/user-attachments/assets/e9631f3e-300e-4f97-b04d-4128f20786a3)



### 4:1 MUX Data Flow Implementation
```verilog
module mux_24dl(s,a,b,c,d,y);
input [1:0]s;
input a,b,c,d;
output y;
wire [3:0]w;
assign w[0]=~s[1]&~s[0]&a;
assign w[1]=~s[1]&s[0]&b;
assign w[2]=s[1]&~s[0]&c;
assign w[3]=s[1]&s[0]&d;
assign y=w[0]|w[1]|w[2]|w[3];
endmodule
```
## Simulated Output Data Flow Modelling

![data flow modelling](https://github.com/user-attachments/assets/6d894068-e5d9-4712-9e50-fafff337c615)



### 4:1 MUX Behavioral Implementation
```verilog
module mux24_beh(i,s,y);
input [4:1]i;
input [1:0]s;
output reg y;
always@(*)
begin
if(s[1]==0&s[0]==0)
y=i[1];
else if(s[1]==0&s[0]==1)
y=i[2];
else if(s[1]==1&s[0]==0)
y=i[3];
else if(s[1]==1&s[0]==1)
y=i[4];
end
endmodule
```
## Simulated Output Behavioral Modelling

![behavioural modelling](https://github.com/user-attachments/assets/bf211aac-3879-41df-a9d0-f1d0557dea8e)




### 4:1 MUX Structural Implementation

![image](https://github.com/user-attachments/assets/eea81c2c-7dfa-43aa-9cea-1ab4ed54db6c)


```verilog
module mux2_to_1 (
    input wire A,
    input wire B,
    input wire S,
    output wire Y
);
    assign Y = S ? B : A;
endmodule

module mux4_to_1_structural (
    input wire A,
    input wire B,
    input wire C,
    input wire D,
    input wire S0,
    input wire S1,
    output wire Y
);
    wire mux_low, mux_high;

    mux2_to_1 mux0 (.A(A), .B(B), .S(S0), .Y(mux_low));
    mux2_to_1 mux1 (.A(C), .B(D), .S(S0), .Y(mux_high));

    mux2_to_1 mux_final (.A(mux_low), .B(mux_high), .S(S1), .Y(Y));
endmodule
```
## Simulated Output Structural Modelling


### Testbench Implementation
```verilog
`timescale 1ns / 1ps

module mux4_to_1_tb;
    reg A, B, C, D, S0, S1;
    wire Y_gate, Y_dataflow, Y_behavioral, Y_structural;

    mux4_to_1_gate uut_gate (.A(A), .B(B), .C(C), .D(D), .S0(S0), .S1(S1), .Y(Y_gate));
    mux4_to_1_dataflow uut_dataflow (.A(A), .B(B), .C(C), .D(D), .S0(S0), .S1(S1), .Y(Y_dataflow));
    mux4_to_1_behavioral uut_behavioral (.A(A), .B(B), .C(C), .D(D), .S0(S0), .S1(S1), .Y(Y_behavioral));
    mux4_to_1_structural uut_structural (.A(A), .B(B), .C(C), .D(D), .S0(S0), .S1(S1), .Y(Y_structural));

    initial begin
        A = 0; B = 0; C = 0; D = 0; S0 = 0; S1 = 0;

        #10 {S1, S0, A, B, C, D} = 6'b00_0001;
        #10 {S1, S0, A, B, C, D} = 6'b01_0010;
        #10 {S1, S0, A, B, C, D} = 6'b10_0100;
        #10 {S1, S0, A, B, C, D} = 6'b11_1000;
        #10 $stop;
    end

    initial begin
        $monitor("Time=%0t | S1=%b S0=%b | Y_gate=%b | Y_dataflow=%b | Y_behavioral=%b | Y_structural=%b",
                 $time, S1, S0, Y_gate, Y_dataflow, Y_behavioral, Y_structural);
    end
endmodule
```
### SAMPLE OUTPUT
```verilog
Time=0 | S1=0 S0=0 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=10 | S1=0 S0=1 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=20 | S1=1 S0=0 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
```
**CONCLUSION**

In this experiment, a 4:1 Multiplexer was successfully designed and simulated using Verilog HDL across four different modeling styles: Gate-Level, Data Flow, Behavioral, and Structural.The simulation results verified the correct functionality of the MUX, with all implementations producing identical outputs for the given input conditions.

