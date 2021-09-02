# RTL_design_workshop
Overview
========
This repo contain all of my files and documentation of the workshop "RTL design using Verilog with SKY130 Technology"

Day 1
========
To start the workshop is necessary to clone some repositories. So on the terminal, write the following commands: 

    git clone https://github.com/kunalg123/vsdflow.git
    git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
    
    
Before starting with the first lab is necessary to standardize some concepts.

- Simulator: will be the tool used for simulating the RTL designs. Trought this course, we will use iverilog

- Design: Can be one or more Verilog files that describe the functionality of some circuit based on a set of specifications 

- TestBench: Is a Verilog file that contains a set of stimulus (called test vectors) to check the functionality of my design


## How simulator works? (can be iverilog or other commercials simulators)

The simulator looks for the changes on the input signals
When one input (or more) change, the output is evaluated
remember: if any change occurs in the input, will be any changes on the output.


Every design has one or more Primary inputs and Primary outputs.

A classical testbench look like the next figure:

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/Diagrams/day1/test_bench.png?raw=true)

Where the "My design" is a verilog file that contains an digital circuit, and this circuit is instantiated in the test bench verilog file for aplicate stimulus to the inputs of My design and capture the changes on the outputs 


