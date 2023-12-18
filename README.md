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

