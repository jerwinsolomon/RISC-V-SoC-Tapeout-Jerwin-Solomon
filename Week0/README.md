RISC-V Reference SoC Tapeout Program VSD
Tools Installation Week 0
All the required tools are installed:
Proof of Installation of Yosys

TOOL CHECK
Yosys
$ sudo apt-get update
$ git clone https://github.com/YosysHQ/yosys.git
$ cd yosys
$ sudo apt install make               # If make is not installed
$ sudo apt-get install build-essential clang bison flex \
    libreadline-dev gawk tcl-dev libffi-dev git \
    graphviz xdot pkg-config python3 libboost-system-dev \
    libboost-python-dev libboost-filesystem-dev zlib1g-dev
$ make config-gcc
# Yosys build depends on a Git submodule called abc, which hasn't been initialized yet. You need to run the following command before running make
$ git submodule update --init --recursive
$ make 
$ sudo make install
Alt Text

Iverilog
$ sudo apt-get update
$ sudo apt-get install iverilog
Alt Text

gtkwave         
