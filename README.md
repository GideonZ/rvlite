# rvlite
Lightweight RiscV core, VHDL only

This repository contains the VHDL code of my own RiscV processor.
It implements rv32i_zicsr, thus with full integer support, barrel shifter, and a minimal CSR implementation for interrupt support (running machine mode only, thus supports MRET, ECALL, etc..)

The processor is a 4-stage pipeline: Fetch, Decode, Exec/Mem/CSR, and Writeback. The pipeline supports bubble collapsing. Since this was written for minimal size, it does not do data forwarding, nor branch prediction.

Actual metrics will follow soon in this readme.

**Why??**

Some people asked me why yet another RiscV implementation. Well:
* since I am VHDL oriented, I did not want to use any of the verilog implementations.
* second, I wanted to use this as a replacement for the Microblaze in an older Spartan 3A product, which is very resource limited. The Microblaze compilers from Xilinx are absolute horse shit. Every version of the SDK had different bugs in emitting microblaze code, on which I spent literally hundreds of hours, more than on writing this entire RiscV core. At least the RiscV compilers are stable!
* There is an excellent RiscV implementation in VHDL called NeoRV32 (https://github.com/stnolting/neorv32), which I initially used for a slightly bigger FPGA. However, migration to the older Spartan 3A showed that this processor is too large in terms of LUTs. While the NeoRV32 is absolutely superior in many ways, this simple implementation is smaller and at least twice as fast when it comes to code execution. NeoRV32, however, offers a hardware debugger and a lot of peripherals, great documentation and a very complete framework. This "rvlite" CPU doesn't have all that, but... it was used and extensively tested running full blown FreeRTOS applications.
* And maybe the foremost reason: It was so much fun to make! I was amazed how easy the RiscV really is. After studying the specification for some time, it was a matter of days to draft a working core...

**Bus**

The bus definition that is used is Wishbone. I must admit that I dislike Wishbone, because it comes in so many incompatible flavors and the signal definition is just horrific. But since it had to be a drop in replacement for the open source Microblaze core that I used before, it had to have the same interface.

The flavor of Wishbone used here is the semi-pipelined version. The address (and write data) are accepted by the target when 'ena_i' is '1'. The core (initiator) will then proceed to the next address.
When the core outputs 'ena_o' for a read, it expects the read data to be presented one cycle later, with 'ena_i' set. When 'ena_i' is '0', the core waits for the data.
So basically the bus is a standard blockram interface, with one cycle latency. By deasserting 'ena_i' waitstates can be introduced. This sounds simple, but in practice it is annoying that the 'ena_i' both controls the address output from the core, as well as the data valid into the core. And for God's sake... who invented the _i and _o postfixes? Do they mean in and out? And for whom?! What's output for the initiator is input for the target. Let's never make this mistake again to name signals like this!

