---
title: 'FPGA Resource Visualization'
date: 2021-5-16
permalink: /posts/2021/4/FPGA_resource_visualization/
tags:
  - FPGA
  - Vivado
---

FPGAs consist of LUTs, Flip Flops, Block RAMs, DSPs, hard processors, interconnect, etc. 
In Xilinx Vivado, if you open up the device view window and click some _tile_, 
the separate window provides a detailed information about what the tile is about. 
Tile is a unit of physical structure for Xilinx FPGAs. 
A detailed information about tile, site, bel can be found [here](https://www.rapidwright.io/docs/Xilinx_Architecture.html).

While you can click each tile and find that the chip consists of a number of columns of different resources, 
Vivado doesn’t seem to support a high-level visualization of how different resources are located on a chip.

Extract Resource Information
------
You can easily extract a _tile table_ about a specific chip from Vivado. 
You have probably seen the device view above when you finished synthesis or implementation on Vivado. 
But if you create **I/O Planning Project**, you have access to the device view without a sample RTL design.

<p align="center"> <img src="https://dj-park.github.io/images/posts_img/step_1.png"> </p>
<p style="font-family: times, serif; font-size:11pt; font-style:italic; text-align:center; color:grey">
Select I/O Planning Project when creating Vivado project
</p>

Next, click **Device tab** to switch to the device view. Right click, and select **Find**. 

<p align="center"> <img src="https://dj-park.github.io/images/posts_img/step_2.png"> </p>
<p style="font-family: times, serif; font-size:11pt; font-style:italic; text-align:center; color:grey">
Open up the device view and select Find
</p>

Here, you want to output all tiles. Thus, the filter outputs tiles of which row value is greater than or equal to 0.

<p align="center"> <img src="https://dj-park.github.io/images/posts_img/step_3.png"> </p>
<p style="font-family: times, serif; font-size:11pt; font-style:italic; text-align:center; color:grey">
Outputs all tiles
</p>

Then, the find results are shown in a new tab. Right click, and select **Export to Spreadsheet**.
I extracted all tiles because I was interested in resource distribution view, 
but I believe you can tweak the find command to extract whatever necessary information you need.

<p align="center"> <img src="https://dj-park.github.io/images/posts_img/step_4.png"> </p>
<p style="font-family: times, serif; font-size:11pt; font-style:italic; text-align:center; color:grey">
Outputs spreadsheet
</p>


Visualization
 ------
The spreadsheet we just acquired is like a 2d array(grid). I wanted to color the grid, same resources with same color to see 
how the designated FPGA looks like. It may not be appropriate to map different resources to identical 2d array element, 
but I think it is good enough to provide some high-level view. 
It turns about that there are 105 different tile “Type.” The first trial is shown below and it hardly delivers any useful information. 

I excluded labels on the colorbar because there are too many labels on different types.

<p align="center"> <img src="https://dj-park.github.io/images/posts_img/verbose.png"> </p>
<p style="font-family: times, serif; font-size:11pt; font-style:italic; text-align:center; color:grey">
Detailed version
</p>


I created “concise” version, treating “INT_R”(Interconnect Right) and “INT_L” (Interconnect Left) types as just “INT” type. 
I simply split the under-bar and take the first part. Then, I ended up with 30 different types, which seems to be better than 105 types.

<p align="center"> <img src="https://dj-park.github.io/images/posts_img/concise.png"> </p>
<p style="font-family: times, serif; font-size:11pt; font-style:italic; text-align:center; color:grey">
Concise version
</p>

I created “main tile” version that if I define `mainTiles` list in the code like 
`mainTiles = ["BRAM", "CLB", "CLK", "DSP", "INT", "IO", "PS"]`, then the code will only draws map for tile types listed in `mainTiles`.

<p align="center"> <img src="https://dj-park.github.io/images/posts_img/mainTypes.png"> </p>
<p style="font-family: times, serif; font-size:11pt; font-style:italic; text-align:center; color:grey">
Only interesting tile types
</p>

Code is available in [here]( https://github.com/dj-park/FPGA-resource-visualization).
