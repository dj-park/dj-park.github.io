---
title: 'PYNQ, Partial Reconfiguration Tutorial Part 1'
date: 2022-1-17
permalink: /posts/2021/12/PYNQ_PR_TUTORIAL_1/
tags:
  - FPGA
  - PYNQ
  - Partial Reconfiguration
  - Dynamic Function Exchange
---

## Environment
PYNQ v2.7, Z1 board, Xilinx 2020.2 tools 

## Tutorial
I've noticed that [this tutorial](https://youtu.be/sgjPbaguWCg)
goes over Partial Reconfiguration(PR) on Pynq environment and verified that it's working in PYNQ v2.7.
The related code is in the video's description.

### .tcl Files
One unclear thing was that if we do `make dynamic`, it calls `create_pr.tcl`, and in `create_pr.tcl`
it requires `led_0.tcl`, `led_5.tcl`, and `led_a.tcl`. The tutorial above covers how to generate
these files starting [20:42](https://youtu.be/sgjPbaguWCg?t=1242), but I think it needs a bit more elaboration.

First, double click the hierarchical block in Vivado's block diagram view, 
and it will display components inside the hierarchical block.
Right click and select all. Then, ***Create Block Design*** to create a seperate led_0 block diagram.
Right click and paste them all. Do the same for led_5 and led_a.
You can either ***File***&#8594;***Export***&#8594;***Export Block Design*** or
select ***Generate Block Design*** to get led_\*.tcl files.

### .hwh Files
PR in PYNQ requires not only .hwh file for the full bitstream but also .hwh file for each
partial bitstreams. Previously, it was either .tcl or .hwh, but in v2.7 I think only .hwh works.

.hwh file is auto-generated when we do ***Generate Block Design***.
(or rightclick the block diagram and ***generate output products***. They 
are the same things. FYI, this is automatically done when we do synthesis with `launch_runs synth_1`.)
You can check where .hwh files are located with `find . -name "*.hwh"`.

Therefore, to facilitate PR in PYNQ, we are kind of forced to create block diagram in Vivado.
However, we are not forced to use [Vivado Project Flow](https://www.xilinx.com/content/dam/xilinx/support/documentation/sw_manuals/xilinx2020_2/ug909-vivado-partial-reconfiguration.pdf#page=73)
to do PR. In Project flow, we create "child implementation runs" and `launch_runs impl_1` does
`update_design -black_box` and `lock_design -level routing`, etc under the hood.
The tutorial above also does it in this manner.
But explicitly generating static design with finer-grained commands mentioned above also works.
