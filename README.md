# Overview RTL_design_workshop
This repo contain all of my files and documentation of the workshop "RTL design using Verilog with SKY130 Technology"

# Day 1
    
Before starting with the first lab is necessary to standardize some concepts.

- Simulator: will be the tool used for simulating the RTL designs. Trought this course, we will use iverilog

- Design: Can be one or more Verilog files that describe the functionality of some circuit based on a set of specifications 

- TestBench: Is a Verilog file that contains a set of stimulus (called test vectors) to check the functionality of my design


## How simulator works? (can be iverilog or other commercials simulators)

The simulator looks for the changes on the input signals. When one input (or more) change, the output is evaluated
- remember: if any change occurs in the input, will be any changes on the output.
*Every design has one or more Primary inputs and Primary outputs.*

A classical testbench look like the next figure:

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/test_bench.png?raw=true)

Where the "My design" is a verilog file that contains an digital circuit, and this circuit is instantiated in the test bench verilog file for aplicate stimulus to the inputs of My design and capture the changes on the outputs 

## Lab1 Day 1  (initial respositories and common files for the next labs)

To start the day one lab is necessary to clone some repositories. So on the terminal, write the following commands: 

    git clone https://github.com/kunalg123/vsdflow.git
    git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
    
This commands will create two directories.

On the directory ‌/sky130RTLDesignAndSynthesisWorkshop you will have two subdirectories. One is called my_lib and contains another two subdirectories /lib and /verilog_model

The subdir /lib contains the sky130 standar cell library for the synthesis process, and the name of this lib is sky130_fd_sc_hd__tt_025C_1v80.lib

The subdir /verilog_model contains all of the standar cell verilog model

The directory /sky130RTLDesignAndSynthesisWorkshop/verilog_files contains all of the verilog files for the labs experiments (verilog designs and test benchs)

## Lab2 Day 1 (iverilog and gtkwave)
Inside the directory /sky130RTLDesignAndSynthesisWorkshop/verilog_files we have a lot of .v files. This files are designs and test bench. 
The files that start whit "tb_.." are the test bench files. For example one of the design on this chapter is good_mux.v and the respective testbench is tb_good_mux.tb.
In the next picture it is possible to see all of the verilog files inside of the the directory /sky130RTLDesignAndSynthesisWorkshop/verilog_files

### Simulating using Iverilog

For do a simulation on iverilog will be use one of the design files of the /sky130RTLDesignAndSynthesisWorkshop/verilog_files and his respective tesbench file. 
The flow of simulation is graphically showed in the next figure

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/TB.png?raw=true)

The command and sintaxis for do this is:

 iverilog good_mux.v tb_good_mux.v

If everithing is good the last command will be create and executable binary file call a.out that you need to run doing
./a.out

You will see the next message: VCD info: dumpfile tb_good_mux.vcd opened for output
The past steps are shown in the next figure

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/3.jpg?raw=true)

Now we can use gtkwave for see the waveforms of the simulation. Run gtkwave tb_good_mux.vcd 
gtkwave gui will be open and you can do a drag and drop method for import the signals to the viewer.
An example of this is in the next figure:

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/gtk.jpg?raw=true)

## Lab3 Day 1 (inside to the verilog files)

If we look what contains the .v files used in the Lab2 we will see the structure of the design and the testbench of this design.
For see this run on terminal (in the same directory of the .v files) 
gvim good_mux.v -o tb_good_mux.v 
This will show you an editor and you can see what are writed inside of this files.

The file called good_mux.v describe a tipical 2-1 mux. I the folowing figure is the structure of this file is described
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/1.jpg?raw=true)
The file called tb_good_mux.v describe a testbench for the 2-1 mux. I the folowing figure is the structure of this file is described
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/2.jpg?raw=true)

## Introduction to yosys

yosys is a synthesizer. But what it is a synthesizer?
**Synthesizer is a tool that convert a RTL file to netlist** and this will be the synthesizer used in this workshop.

For convert RTL to netlist, the synthesizer requieres a library file (.lib file). 
- The netlist is a representation of the RTL design in standar cell of the .lib file 

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/yosys.png?raw=true)

### RTL Design
- Is a beahvioral representation of the requiered specifications in form of code (HDL representation)
an example of RTL code can be the next(register): 

```verilog
module REG(clk,rst,ce, D, Q);
	parameter n=16; // Parameter
  input wire [n-1:0] D;
	input wire rst,clk,ce;
  output reg [n-1:0] Q;

  always @(posedge clk)
	begin
      if(rst) Q<=0;
		else 
          if(ce)
			Q<=D;
	end

endmodule
```

but we need a digital circuit representation of the said code. So, how to do that?
**The process to convert an RTL code to digital circuit representation is called synthesis**

Synthesis process do:
- RTL to Gate level translation
- The design is converted into gates and the connections are made between the gates
- This is given out as a file called netlist

The process for check this netlist file (**that has exactly the same primary inputs and outputs**) is doing a test bench.
The test bench file for do this is the same test bench file used for verify the RTL code. Graphically:

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/yosysTB.png?raw=true)

### The .lib file
The .lib is file that the syntesizer requieres to do the synthesis process. This .lib contains:
- Collection of logical modules
- Includes basic logic gates like AND, OR, NOT, ETC...
- Different types of the same gate (2 input AND gate, 3 input AND gate, 4 input AND gate, etc)
- Different performance types of the same gate (slow, medium, fast)

#### Why we need fast or slow cells?
All depends of the specification of the design (like power consumption, maximum area, etc)
for example, a fast circuit requieres wide transistor and this will be implicate more area and more power consumption
for other side, a circuit that requieres low power consumption need to implement narrow transistor that will have a high capacitance, low area and less power consumption.
### Ilustration of the synthesis process
In the next figure it is possible to see a graphical representation of the RTL code mapped to standar cell of an specific .lib file
-- image --

## Laboratory (yosys basic flow)
- On the directory /sky130RTLDesignAndSynthesisWorkshop/verilog_files run:
 yosys

Now in yosys:

- Read the .lib file. Run:
  read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
- Read the verilog design file. Run:
  read_verilog good_mux.v
- Specify the top module of your design. Run:
  synth -top good_mux
- Generate the netlist. Run:
  abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
- It's possible to see a graphical representation of the netlist using the command view. So, run:
  show
  -- figure --
- Write the netlist file. Run:
  write_verilog -noattr good_mux_netlist.v
- For read the netlist run:
  !gvim good_mux_netlist.v
  -- image -- 
# Day 2

## Lab 1
The name of the .lib file contains important information for the designers. They are 3 paramaters that we can see on the .lib file called "sky130_fd_sc_hd__tt_025C_1v80.lib"

--image--

Also, the .lib files has a lot of information(like area, leakage power, power consumption, pin capacitance, etc) of every cell of the lib file. For example, in the next figures is possible to see the leakage power of one cell called sky130_fd_sc_hd__a2111o_1 for different inputs conditions, the capacitance of some pins

For different types of the same cells we can note that the parametrs of the cells are differents. For example, if we see the characteristics of different types of AND gates we will see that the parameters (like the total area, power, delay, etc) are differents. See the next figure 

-- images -- 

## Lab Hier synthesis vs flat synthesis
There are two types of synthesis: Hierachical and flat synthesis.

For example, consider the following RTL code:
```verilog
module sub_module2 (input a, input b, output y);
	assign y = a | b;
endmodule

module sub_module1 (input a, input b, output y);
	assign y = a&b;
endmodule


module multiple_modules (input a, input b, input c , output y);
	wire net1;
	sub_module1 u1(.a(a),.b(b),.y(net1));  //net1 = a&b
	sub_module2 u2(.a(net1),.b(c),.y(y));  //y = net1|c ,ie y = a&b + c;
endmodule
```
Note that said RTL code contains two sub modules (sub_module1 and sub_module2) and a thrid module called multiple_modules where sub_module1 and sub_module2 are interconnected.

If we synthesize this RTL code following the instruccion of the Day 1: lab Laboratory (yosys basic flow)
will be obtain a hierarchical model. See the next figure.

On the other hand it's possible to use a flag in the synthesis workflow for do a flatten sysnthesis. This flag is *flatten*
and the commands will be:

- Read the .lib file. Run:
  read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
- Read the verilog design file. Run:
  read_verilog multilple_modules.v
- Specify the top module of your design. Run:
  synth -top good_mux
- Generate the netlist. Run:
  abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
- Flatten flag. Run:
  flatten
-  To see a graphical representation of the netlist using the command view. So, run:
  show multilple_modules
  -- figure --
- Write the netlist file. Run:
  write_verilog -noattr multilple_modules_flatten.v
- For read the netlist run:
  !gvim multilple_modules_flatten.v
  
  A comparation between this two types of synthesis, and the resultant netlist file, is presented in the next figure
  
  --image--
  
 ### Sub module synthesis
 The file called multilple_modules.v contains two submodules (1 and 2) and it's possible to synthesized an specific submodule.
 This is very important in two cases:
  - When the topmodule has multiple instantiation of the same sub_module
  - In case when we are doing the classical *divide and conquer approach*

And example of how to do that is:

Suppose that we want to syntesized the sub_module1 of the RTL code called multilple_modules.v. The yosys command for do that are the following:

- Read the .lib file. Run:
  read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
- Read the verilog design file. Run:
  read_verilog multilple_modules.v
- Specify the top module of your design. Run:
  synth -top sub_module1
- Generate the netlist. Run:
  abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
-  To see a graphical representation of the netlist using the command view. So, run:
  show sub_module1
  -- figure --
- Write the netlist file. Run:
  write_verilog -noattr sub_module1.v
- For read the netlist run:
  !gvim sub_module1.v



