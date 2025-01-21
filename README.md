# All-Digital Phase Locked Loop (ADPLL)
Design an All-Digital Phase-Locked Loop (ADPLL) circuit through a common architecture, as shown in the following figure. First, the Phase and Frequency Detector checks the phase and frequency difference between the reference clock and the divided clock. Then, it delivers the lead or lag signal to the controller, which adjusts the DCO control code based on the input conditions. Once the Digital-Controlled Oscillator (DCO) receives the control code, it oscillates at the corresponding frequency. Finally, the oscillated signal is sent to the frequency divider, which divides the signal according to the input value M. Once the ADPLL is locked, it transitions into maintenance mode and continues to track the phase and frequency of the reference clock.

<p align="center">
  <img src="https://github.com/RexJian/PipelineCPU/blob/main/Image/Architecture.jpg" width="800" height="450" alt="Architecture">
</p> 

## Table of Content
- [Specification](#specification)
- [Instructions In The Register](#instructions-in-the-register)
- [Hazard Solutions](#hazard-solutions)
  - [Data Hazard](#data-hazard)
  - [Control Hazard](#control-hazard)
  - [Load-use Data Hazard](#load-use-data-hazard)


## Specification

| Signal Name | I/O | Width | Sample Description |
| :----: | :----: | :----: | :----|
| CLK | I | 1 | Clock Signal |
| EN | I | 1 | Enable signal |
| CLR_N | I | 1 | The register data clean signal|
| Addr | O | 32 | Current instruction address|
| ALU_D1 | O | 32 | One of the ALU input|
| Y | O | 32 | One of the ALU input |
| Ex_Alu_R | O | 32 | The result of ALU|
| stall | O | 1 | When the signal is asserted, the data in the pipeline registers will remain unchanged.|
| condition_met | O | 1 |When the signal is asserted, the data in the pipeline register will be cleared, and the new address will be the input in the next cycle. |
| Rs | O | 5 | The address of source register |
| Rt | O | 5 | The address of targe register |
| Rd | O | 5 | The address of destination register |
| Write_D | O | 32 | The value written in the destination register |
| Fwd_A | O | 2 | When the signal is asserted, it indicates a data hazard in the rs register. |
| Fwd_B | O | 2 | When the signal is asserted, it indicates a data hazard in the rt register. |


## Instructions In The Register

| Register Address (Hex) | Instruction (Hex) | Instruction Meaning | Desciption |
| :----: | :----: | :----: | :----: |
| 0 | 20020005 | addi $2, $0, 5 | Initialize $2 = 5 |
| 4 | 2003000c | addi $3, $0, 12 | Initialize $3 = 12 |
| 8 | 20070003 | addi $7, $0, 3 | Initialize $7 = 3 |
| c | 00e22025 | or $4, $7, $2 | $4 = 3 or 5 = 7 |
| 10 | 00642824 | and $5, $3, $4 | $5 = 12 and 7 = 4 |
| 14 | 00a42820 | add $5, $5, $4 | $5 = 4 + 7 = 11 |
| 18 | 10a7000a | beq $5, $7, end | The condition is invalid |
| 1c | 0064202a | slt $4, $3, $4 | $4 = 12 < 7 = 0 |
| 20 | 10800001 | beq $4, $0, around | The condition is valid, so next instruction address is "around" address|
| 24 | 20050000 | addi $5, $0, 0 | CPU will not implement the instruction |
| 28 | 00e2202a | around: slt $4, $7, $2 | $4 = 3 < 5 = 1|
| 2c | 00853820 | add $7, $4, $5 | $7 = 1 + 11 = 12 |
| 30 | 00e23822 | sub $7, $7, $2 | $7 = 12 - 5 = 7 |
| 34 | ac670044 | sw $7, 68($3) | Store $7 value to the address 80 of data memory |
| 38 | 8c020050 | lw $2, 80($0) | Load the value of the address 80 of data memory to $2 |
| 3c | 00442824 | and $5, $2, $4 | $5 = 7 and 1 = 1 |
| 40 | ac020054 | sw $2, 84($0) | Store $2 value to the address of 80 of data memory |

## Hazard Solutions
### Data Hazard
When the source registers in the current instruction depend on the destination register (Rd) of a previous instruction, the Fwd_A or Fwd_B signals will be asserted. The CPU will then use data forwarding to resolve this dependency.
<p align="center">
  <img src="https://github.com/RexJian/PipelineCPU/blob/main/Image/DataHazard_Command.jpg" width="850" height="50" >
   <br> <strong> When the CPU implement to the command, it would cause a data hazard in $7 register. </strong>
</p>
<p align="center">
  <img src="https://github.com/RexJian/PipelineCPU/blob/main/Image/DataHazard_Waveform.jpg" width="850" height="350" >
   <br> <strong> The waveform corresponding to the data hazard </strong>
</p>

### Control Hazard
When this branch command is valid, the condition_met signal will be asserted. Subsequently, the data in the IF/ID and ID/EX pipeline registers will be cleared in the next cycle.
<p align="center">
  <img src="https://github.com/RexJian/PipelineCPU/blob/main/Image/ControlHazard_Command.jpg" width="850" height="130" >
   <br> <strong> When the CPU implement to the command, it would cause a control hazard because the branch condtion is valid. </strong>
</p>
<p align="center">
  <img src="https://github.com/RexJian/PipelineCPU/blob/main/Image/ControlHazard_Waveform.jpg" width="850" height="320" >
   <br> <strong> The waveform corresponding to the control hazard. </strong>
</p>

### Load-use Data Hazard
When an instruction depends on the result of a previous load instruction before it has been completed, the stall signal will be asserted. Subsequently, the IF/ID pipeline register will stall for a cycle, and the data in the ID/EX pipeline register will be cleared.
<p align="center">
  <img src="https://github.com/RexJian/PipelineCPU/blob/main/Image/LoadDataHazard_Command.jpg" width="850" height="75" >
   <br> <strong> When the CPU implement to the command, it would cause a load-use data hazard in $2 register</strong>
</p>
<p align="center">
  <img src="https://github.com/RexJian/PipelineCPU/blob/main/Image/LoadDataHazard_Waveform.jpg" width="850" height="320" >
   <br> <strong> The waveform corresponding to the load-use data hazard. </strong>
</p>
