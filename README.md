# UART PMOD 1553 FUSESOC REPO
### Contains all fusesoc cores to build UART pmod1553 systems.

---
Respository of open source 1553 designs for the pmod1553 device.

author: Jay Convertino

date: 2023.09.19

details: FPGA devices used via a PMOD mil-std-1553 electrical interface device to decode and encode 1553 transmissions.

license: MIT
---

see https://github.com/johnathan-convertino-afrl/open1553 for more info on the project.

![logo_img](img/logo.png)

## RELEASE VERSIONS
### Current
  - release_1553_v3 (releases are tags)
    * Changed to UART only fusesoc build setup.

### Past (see old repo)
  - release_1553_v2 (releases are tags)
    * added analog switch for proper 1553 multiplexing
    * added en_diff for switch toggling
  - release_1553_v1 (releases are tags)
    * first public release

## Requirements
### HDL
  - Vivado 2022.2.2
  - fusesoc
  - gtkwave
  - Icarus

## Quick Start
0. Clonse this repo, and run 'git submodule update --init --recursive' in the root to pull all sub repos.
1. Install Vivado 2022.2.2
2. Install build essentials package for Ubuntu.
3. From the root of the HDL directory you may run the following build commands.
    - fusesoc run --target cmod-s7-25 --build --tool vivado AFRL:project:uart_open1553:1.0.0
    - fusesoc run --target arty-a7-35 --build --tool vivado AFRL:project:uart_open1553:1.0.0
    - fusesoc run --target nexys-a7-100t --build --tool vivado AFRL:project:uart_open1553:1.0.0
4. Once completed you may use Vivado to open the project, this is located in the build folder (build/AFRL_project_uart_open1553_X.X.X/TARGET_NAME). To program your development board via JTAG.
    - You will need to add a memory configuration device to write the bitstream to the flash of the cmod/arty.
    - cmod example at: https://reference.digilentinc.com/learn/programmable-logic/tutorials/cmod-a7-programming-guide/start?_ga=2.262285964.1885041324.1628539712-1298016575.1609856716
    - Again, for the fifo/fmc projects you oned to build the linux system. See the docs folder for instructions.

## USAGE
### General Usage

For xilinx fifo and uart the format is the same.
Data is received in the following ASCII string format:
>CMDS;D1;P1;I0;Hx5555\r
>DATA;D0;P1;I0;HxAAAA\r

The fields are seperated by ; .

  - The first is the sync type, Command/Status = CMDS, Data = DATA
  - The second is if there is a 4us delay, 1 = delay over 4us, 0 = no delay or less then 4us.
  - The third is parity, 1 = parity good, 0 = parity bad
  - The fourth is invert, 1 = core is inverting data, 0 = core is not inverting data
  - The fifth is the data in hex format, Hx???? where ? = 4 bits of data (16 bits in total).
  - The carrige return is the string terminator. This works well with serial consoles with local newline addition enabled.
      - tr '\r' '\n' is your friend for xilinx fifo applications.

Data is sent in the following ASCII string format:
>CMDS;D1;P1;I0;Hx5555\r
>DATA;D0;P1;I0;HxAAAA\r

The fields are seperated by ; .

  - The first is the sync type, Command/Status = CMDS, Data = DATA
  - The second is to enable a 4us delay, 1 = delay of at least 4us, 0 = no delay or less then 4us.
  - The third is parity, 1 = parity odd (default), 0 = parity even
  - The fourth is invert, 1 = invert data, 0 = don't invert data
  - The fifth is the data in hex format, Hx???? where ? = 4 bits of data (16 bits in total).
  - The carrige return is the string terminator.
      - echo -ne "DATA;D1;P1;I0;Hx5555\r" is your friend for xilinx fifo applications.

