---
title: 'Partial Reconfiguration comparision, Intel vs AMD'
date: 2023-4-29
permalink: /posts/2023/4/PR_INTEL_AMD/

tags:
  - FPGA
  - Partial Reconfiguration
---
The comparison is based on Quartus 23.1 and Vivado 22.2. If there's incorrect info, please let me know.

### Both...
* **Static logic can route over the PR regions** \
  To the best of my knowledge, there's no vendor tool support to prevent static logic from routing over the PR regions. <em>Reserved Routing</em> 
  from Intel Quartus might be related, but in my experience, it is not functioning as expected. There are some academic works to
  block static routing on the PR regions.
* **Cannot perfectly remove the static design footprint while compiling reconfigurable partition (*persona* in Quartus term)** \
  Quartus's *Fast Preservation* and Vivado's *Abstract Shell* are related technology, but the saving in the compile time
  is not dramatic in *Fast Preservation* and *Abstract Shell* sometimes contains too much static logic.\
  Related discussion on Intel FPGA forum and academic paper:
  [Fast Preservation](https://community.intel.com/t5/Intel-Quartus-Prime-Software/Question-on-AN899-Reducing-Compile-Time-with-Fast-Preservation/m-p/1478780) and
  [Abstract Shell](https://ic.ese.upenn.edu/abstracts/nested_dfx_fpt2022.html)


### Intel Quartus
* **Can stack multiple PR regions within one clock region (*sector* in Quartus term)**
* **But can NOT have multiple PR regions in one row clock region (half width of the clock region)**
* **Route Region can be controlled by the user**
* **Fast Preservation flow can reduce compile time with large static design**
* **Cannot generate partial bitstream only, and bitstream generation takes a long time** \
  Quartus (at least versions up to 23.1) always generates a full bitstream first and then generates partial bitstreams
  masking corresponding bits. What's worse is that with PR mode on, the full bitstream generation time significantly increases.\
  Related discussion on Intel FPGA forum: 
  [thread 1](https://community.intel.com/t5/Programmable-Devices/Assembler-s-bitstream-generation-time-in-partial-reconfiguration/m-p/1458035)
  and
  [thread 2](https://community.intel.com/t5/Intel-Quartus-Prime-Software/Generate-only-partial-bitstream-not-the-full-bitstream/m-p/1453125#M77137)
* **Takes only O(log(N)) subdivisions for Hierarchical Partial Reconfiguration**\
  For example, if there exists 8 total grand children PR regions where two children have one parent PR region (total 4 parent PR regions) and 
  two parents have one grand parent PR region (total 2 grand parent PR regions), it takes only three subdivision to create final static design.

### AMD-Xilinx Vivado
* **Can NOT stack multiple PR regions within one clock region**
* **But does not have any restriction on horizontally placing PR regions**
* **Route Region is controlled by the tool**\
  This is called *Expanded Routing Region*, available for Ultrascale and Ultrascale+ devices.
  Users can also turn off the expansion with `set_param hd.routingContainmentAreaExpansion false`.
* **Abstract Shell can reduce compile time with large static desgin**
* **Can generate partial bitstream only**
* **Takes O(N) subdivisions for Hierarchical Partial Reconfiguration**\
  In the same scenario above that has 8 total grand children PR regions, it takes 7 subdivisions. This is because of Vivado's in-context 
  characteristic. Only single subdivision can be performed at a time, so it requires sequentialized top-level implementations to generate
  the final static design.