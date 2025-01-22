# All-Digital Phase Locked Loop (ADPLL)
Design an All-Digital Phase-Locked Loop (ADPLL) circuit through a common architecture, as shown in the following figure. First, the Phase and Frequency Detector checks the phase and frequency difference between the reference clock and the divided clock. Then, it delivers the lead or lag signal to the controller, which adjusts the DCO control code based on the input conditions. Once the Digital-Controlled Oscillator (DCO) receives the control code, it oscillates at the corresponding frequency. Finally, the oscillated signal is sent to the frequency divider, which divides the signal according to the input value M. Once the ADPLL is locked, it transitions into maintenance mode and continues to track the phase and frequency of the reference clock.

<p align="center">
  <img src="https://github.com/RexJian/ADPLL/blob/main/img/architecture.png" width="800" height="300" alt="Architecture">
</p> 

## Table of Content
- [Specification](#specification)
- [Bang-Bang PFD](#bang-bang-pfd)
- [DCO](#dco)


  
## Specification

| Signal Name | I/O | Width | Sample Description |
| :----: | :----: | :----: | :----|
| CLK_IN | I | 1 | Clock Signal |
| M | I | 3 | Frequency divider ratio, rating from 3'd1 to 3'd7 |
| RESET | I | 1 | Reset signal |
| CLK_OUT | O | 1 | The output clock of the PLL|
| freq_lock | O | 1 | Whether the PLL is locked|

## Bang-Bang PFD

The figure below shows the PFD architecture. Bang-bang means that only the lead/lag information is used.
<p align="center">
  <img src="https://github.com/RexJian/ADPLL/blob/main/img/pfd.png" width="800" height="300" alt="Architecture">
</p> 
The digital pulse amplifier uses a cascaded two-input AND architecture to increase the low pulse width of OUTU and OUTD. The architecture is shown in the figure below.
<p align="center">
  <img src="https://github.com/RexJian/ADPLL/blob/main/img/digital_pulse_amplifier.png" width="800" height="200" alt="Architecture">
</p>

## DCO
The DCO is composed of a tri-state inverter matrix to construct a delay cell, as shown in the figure below.
<p align="center">
  <img src="https://github.com/RexJian/ADPLL/blob/main/img/dco.png" width="800" height="450" alt="Architecture">
</p>
