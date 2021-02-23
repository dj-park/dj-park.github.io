---
title: 'MIMO System Prototyping with Commercially Available Boards'
date: 2021-2-24
permalink: /posts/2021/2/blog-post-0/

tags:
  - MIMO
  - DAC
  - ADC
  - RFSoC
  - FPGA
  - FMC
---

In the previous workplace, I was given a mission to create **32x32 [MIMO]( https://en.wikipedia.org/wiki/MIMO)** prototype under limited budget. 
I didn’t have to deal with RF components, so my goal was to create a transmitter that consists of a FPGA and DACs and to create a receiver that consists of a FPGA and ADCs.

The system does not require high resolution (in fact, it can be less than 4~5 bits) nor high sampling rate. But the system requires 
at least **1)32 channels of DACs and 32 channels of ADCs**, and it has to be **2)scalable to 100+x100+ MIMO systems**. 
Also, I **3)couldn’t spend $100,000** to buy the components.

Xilinx RFSoC
======
The simplest solution was to purchase RFSoC evaluation board and just be done with it. 
At that time, [Xilinx RFSoC ZCU111](https://www.xilinx.com/products/boards-and-kits/zcu111.html) evaluation kit, 
equipped with eight 14-bit DACs and eight 12-bit ADCs, was available. The main reason we didn’t purchase ZCU111 was that eight DACs and ADCs were not enough. 

But even if it has sixteen DACs and ADCs like a recently released [Xilinx RFSoC ZCU216](https://www.xilinx.com/products/boards-and-kits/zcu216.html), 
**you need custom daughter board to create 16x16 MIMO system**. Based on my research, both ZCU111 and ZCU216 need “Balun Add-on Cards” to utilize DACs and ADCs, 
and the included add-on card is designed to support a wide rage of frequency rather than to focus on specific frequency range(Note that these are _evaluation kits_).

Below threads on Xilinx Forum show that you need custom add-on card to build 8x8 MIMO system with ZCU111 or 16x16 MIMO system with ZCU216.
- [ZCU111 DAC/ADC how to use?](https://forums.xilinx.com/t5/Xilinx-Evaluation-Boards/ZCU111-DAC-ADC-how-to-use/m-p/960343)
- [ZCU216 ADC/DAC spec](https://forums.xilinx.com/t5/Versal-and-UltraScale/ZCU216-ADC-DAC-spec/m-p/1207140#M16220)

DAC/ADC FPGA Mezzanine Card(FMC)
======
In this [page]( https://www.xilinx.com/products/boards-and-kits/fmc-cards.html), you can find a variety of DAC/ADC FMC daughter cards. 
The one we are looking for is [FMC116]( https://www.abaco.com/products/fmc116-fpga-mezzanine-card) from Abaco Systems, that has 16-channel ADC. 
The DAC counterpart is [FMC216](https://www.abaco.com/products/fmc216-fpga-mezzanine-card).

| Module Names | ADC Channels | ADC resolution(bits) | ADC Sample(MSPS) | DAC Channels | DAC Resolution(bits) | DAC Sample(MSPS) |
| :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| FMC116 | 16 | 14 | 125 | X | X | X |
| FMC216 | X | X | X | 16 | 16 | 312.5 |
<p style="font-family: times, serif; font-size:10pt; font-style:italic; text-align:center; color:grey">
    Different Analog FMC modules from Abaco Systems
</p>

FMC116 and FMC216 only work with **HPC**(high-pin count) FMC connector. HPC type FMC provides 160 user-defined, 
single-ended signals while **LPC**(low-pin count) type FMC provides 68 user-defined, single-ended signals. 
So, I needed evaluation board that has at least two HPC FMC connectors available. 
You can learn more about the difference between HPC FMC and LPC FMC [here]( https://fmchub.github.io/appendix/VITA57_FMC_HPC_LPC_SIGNALS_AND_PINOUT.html).

Two years ago when I did research about the board, 
I ended up [Xilinx ZCU102]( https://www.xilinx.com/products/boards-and-kits/ek-u1-zcu102-g.html) 
and [Xilinx VC707]( https://www.xilinx.com/products/boards-and-kits/ek-v7-vc707-g.html). 
I checked with Abaco Systems, and I confirmed that FMC116 is compatible with Xilinx ZCU102, 
but FMC216 is **NOT** compatible with ZCU102 because ZCU102’s HPC FMCs are not fully compliant to the VITA 57.1 FMC standard. 
Therefore, I went for VC707, which was vendor-confirmed to be compatible with both FMC116 and FMC216.

If you decrease the DAC/ADC resolution to 3~4bit and sampling rate to <100MSPS, FMC daughter board with 16<channels seems to be possible. 
At the moment, the demand for such FMC daughter board is not high, so it’s not commercially available.

Xilinx’s VC707 and Abaco’s FMC216
------
<p align="center"> <img src="https://dj-park.github.io/images/posts_img/VC7070_FMC216.jpg"> </p>
<p style="font-family: times, serif; font-size:10pt; font-style:italic; text-align:center; color:grey">
Two Analog outputs from FMC216 are connected to oscilloscope for analysis, one is DC signal and another is step signal
</p>

You can also purchase BSP that consists of software code to create data and send through ethernet to FPGA and FPGA netlist 
that receives data over ethernet and sends them to FMC216’s DAC cores. 
Even with all these sources, I did struggle at some point because the codes are outdated and there’s no active forum on the web. 
But Abaco System’s support was quite nice and I managed to manipulate signals(both on software side and hardware side) as shown in the picture above. 

The system has 16 DAC channels, and if you mount two FMC216s, it becomes 32 DACs system. You can create the ADC counter part in the exactly 
same manner with Xilinx VC707 and Abaco’s FMC216.

<p align="center"> <img src="https://dj-park.github.io/images/posts_img/mimo_system.png"> </p>
<p style="font-family: times, serif; font-size:10pt; font-style:italic; text-align:center; color:grey">
16x16 MIMO system diagram with DAC/ADC FMC daughter boards, RF components to be added
</p>

**Giga-bit Transceivers** support very high serial output from the FPGA. 
A variety of different transceivers on Xilinx FPGA are shown [here](https://www.xilinx.com/products/technology/high-speed-serial.html#overview). 
“GT” stands for Giga-bit Transceiver and the last letters, GT”X”, GT”Y”, or GT”H” are just the names… don’t mean much. 
The power analysis for FPGA design to support FMC216(and some extra logic I added) is shown below. As expected, GTXs consume majority of the power.

<p align="center"> <img src="https://dj-park.github.io/images/posts_img/power_analysis_tx_fpga.JPG"> </p>
<p style="font-family: times, serif; font-size:10pt; font-style:italic; text-align:center; color:grey">
Vivado Power Analysis on TX FPGA’s logic
</p>

Other options
======
[Xilinx ZCU1275]( https://www.xilinx.com/products/boards-and-kits/zcu1275.html#overview)  characterization kit supports 
x16 MIMO system. _Characterization kit_ concentrates on specific usage, in this case DAC and ADC, and shows off its feasibility on the use. 
Previously mentioned Xilinx ZCU102, VC707, and ZCU216 are all _evaluation kits_ that focus on broader usage with all the peripherals of the board. 
Xilinx Korea told me that ZCU1275 is more like a Xilinx internal demonstration and recommended me evaluation kits. 

You may also want to check out Abaco Systems’ SRS6000 and Nutaq’s PicoDigitizer for a complete system(easily out of budget for a small-sized lab though).
