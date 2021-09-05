# Overview RTL_design_workshop
This repo contain all of my files and documentation of the workshop "RTL design using Verilog with SKY130 Technology"

-----------------------------------------------------------------------------------------------------------------------------------------------
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

On the directory ‌``` /sky130RTLDesignAndSynthesisWorkshop``` you will have two subdirectories. One is called my_lib and contains another two subdirectories ``` /lib ``` and ``` /verilog_model```

The subdir ``` /lib ``` contains the sky130 standar cell library for the synthesis process, and the name of this lib is sky130_fd_sc_hd__tt_025C_1v80.lib

The subdir ``` /verilog_model```  contains all of the standar cell verilog model

The directory ``` /sky130RTLDesignAndSynthesisWorkshop/verilog_files``` contains all of the verilog files for the labs experiments (verilog designs and test benchs)

## Lab2 Day 1 (iverilog and gtkwave)
Inside the directory /sky130RTLDesignAndSynthesisWorkshop/verilog_files we have a lot of .v files. This files are designs and test bench. 
The files that start whit "tb_.." are the test bench files. For example one of the design on this chapter is good_mux.v and the respective testbench is tb_good_mux.tb.
In the next picture it is possible to see all of the verilog files inside of the the directory ``` /sky130RTLDesignAndSynthesisWorkshop/verilog_files```

### Simulating using Iverilog

For do a simulation on iverilog will be use one of the design files of the /sky130RTLDesignAndSynthesisWorkshop/verilog_files and his respective tesbench file. 
The flow of simulation is graphically showed in the next figure

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/TB.png?raw=true)

The command and sintaxis for do this is:

 ```iverilog good_mux.v tb_good_mux.v``` 

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
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/1.png?raw=true)
The file called tb_good_mux.v describe a testbench for the 2-1 mux. I the folowing figure is the structure of this file is described
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/2.png?raw=true)

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
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/8.jpg?raw=true)

## Laboratory (yosys basic flow)
- On the directory ``` /sky130RTLDesignAndSynthesisWorkshop/verilog_files``` run:
 ```yosys ```

Now in yosys:

- Read the .lib file. Run:
  ``` read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib ```
- Read the verilog design file. Run:
  ``` read_verilog good_mux.v ```
- Specify the top module of your design. Run:
  ``` synth -top good_mux```
- Generate the netlist. Run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib ```
- It's possible to see a graphical representation of the netlist using the command show. So, run:
 ``` show ```
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/6.jpg?raw=true)
- Write the netlist file. Run:
  ``` write_verilog -noattr good_mux_netlist.v ```
- For read the netlist run:
  ``` !gvim good_mux_netlist.v ```
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/7.jpg?raw=true)
  
-----------------------------------------------------------------------------------------------------------------------------------------------
  
# Day 2. Timinig libs, hierarchical vs flat synthesis and efficient flop coding styles

## Lab 1
The name of the .lib file contains important information for the designers. They are 3 paramaters that we can see on the .lib file called "sky130_fd_sc_hd__tt_025C_1v80.lib"

  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/lib.png?raw=true)

Also, the .lib files has a lot of information(like area, leakage power, power consumption, pin capacitance, etc) of every cell of the lib file. For example, in the next figures is possible to see the leakage power of one cell called sky130_fd_sc_hd__a2111o_1 for different inputs conditions, the capacitance of some pins, and other parameters.
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/1.jpg?raw=true)
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/2.jpg?raw=true)

For different types of the same cells we can note that the parametrs of the cells are differents. For example, if we see the characteristics of different types of AND gates we will see that the parameters (like the total area, power, delay, etc) are differents. See the next figure 

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3.jpg?raw=true)

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
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/5.jpg?raw=true)

On the other hand it's possible to use a flag in the synthesis workflow for do a flatten sysnthesis. This flag is *flatten*
and the commands will be:

- Read the .lib file. Run:
  ``` read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  ```
- Read the verilog design file. Run:
  ``` read_verilog multilple_modules.v ```
- Specify the top module of your design. Run:
  ``` synth -top good_mux ```
- Generate the netlist. Run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib ```
- Flatten flag. Run:
  ``` flatten ```
-  To see a graphical representation of the netlist using the command view. So, run:
  ```  show multilple_modules ```
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/6.jpg?raw=true)
- Write the netlist file. Run:
  ``` write_verilog -noattr multilple_modules_flatten.v ```
- For read the netlist run:
  ``` !gvim multilple_modules_flatten.v ```
  
  A comparation between this two types of synthesis, and the resultant netlist file, is presented in the next figure
  
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/4.jpg?raw=true)
  
 ### Sub module synthesis
 The file called multilple_modules.v contains two submodules (1 and 2) and it's possible to synthesized an specific submodule.
 This is very important in two cases:
  - When the topmodule has multiple instantiation of the same sub_module
  - In case when we are doing the classical *divide and conquer approach*

And example of how to do that is:

Suppose that we want to syntesized the sub_module1 of the RTL code called multilple_modules.v. The yosys command for do that are the following:

- Read the .lib file. Run:
  ``` read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  ```
- Read the verilog design file. Run:
  ``` read_verilog multilple_modules.v ```
- Specify the top module of your design. Run:
 ```  synth -top sub_module1 ```
- Generate the netlist. Run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  ```
-  To see a graphical representation of the netlist using the command view. So, run:
  ``` show sub_module1 ```
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/7.jpg?raw=true)
- Write the netlist file. Run:
 ```  write_verilog -noattr sub_module1.v ```
- For read the netlist run:
  ``` !gvim sub_module1.v ```
  
# Day 3. Combinational and sequential optimizations

##Why Flops?

Consider a combinatinal circuit like the next figure. And remeber that every digital circuit has a propagation delay.
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/2.png?raw=true)

So if the propagation delay of the gate AND is 2ns and the propagation delay of the OR gate is 1ns, we will have a glitch condition because the 1ns of the propagation delay difference. In the next time diagram this situation is showed.
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/1.png?raw=true)

If we have more combinational circuits in series, we will have possible glitches, like the following image.
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/3.png?raw=true)


It's possible to avoid this situation using Flops between the circuit. The flo ps will retain the information and the out of this circuit only will change in function of the clock, this granted a stable out avoiding the glitches.
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/4.png?raw=true)


## Types of Flops
There are different types of flops and differents code styles for everyone. The output of the flops will change on the rising edge or in the negedge of the clock, also the output can change when a reset signal is applied. But the output change when the reset signal is applied can be synchronous, asynchronous, or both.
The differences in the code style will determine the behavior of the flop. 
The next time diagrams show two cases of flops, synchronous and asynchronous.

## Lab1: Flip Flops simulation

The next diagram shows two types of Flop, synchronous and asynchronous respectively.
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/5.png?raw=true)
  
    ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/6.png?raw=true)
    
Using iverilog and gtk wave we will simulate three types of flop. Asynchronous set and reset, synchronous reset.

for do the simulatio run:
```
$ iverilog dff_your_flop.v tb_dff_your_flop.v
$ ./a.out
$ gtkwave tb_dff_your_flop.vcd
```

the gtkwave simulations are the following

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/2/1.jpg?raw=true)
    
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/2/2.jpg?raw=true)
        
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/2/3.jpg?raw=true)
    


## Lab2: FLip Flops synthesis
The same flip flop model used in the past lab called "Lab1: Flip Flops simulation" will be synthetized using yosys. The steps for that and the results are presented in the following section.
#### Asynchronous reset Flop synthesys flow
  ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read verilog dff_asyncres.v
  $ synth -top dff_asyncres
  ```
  Now we need to force the tool yosys for use only DFF, so run:
  ``` $ dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  Now create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show dff_asyncres ```
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/5.jpg?raw=true)
  
#### Asynchronous set Flop synthesys flow
  ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read_verilog dff_asyncres_set.v
  $ synth -top dff_asyncres_set
  ```
  Now we need to force the tool yosys for use only DFF, so run:
  ``` $ dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  Now create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show dff_asyncres_set ```
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/6.jpg?raw=true)

#### Synchronous set Flop synthesys flow
 ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read_verilog dff_syncres.v
  $ synth -top dff_syncres
  ```
  Now we need to force the tool yosys for use only DFF, so run:
  ``` $ dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  Now create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show dff_syncres```
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/7.jpg?raw=true)

## Lab3: Optimization
It's not necessary to have extra harwware for do some multiplication (two base multiplication), beacuse the basic arimetic operation called shifter do it easily and efficientily (in therms of hardware).

For example, on the directory ```/verilog_files``` we have a file called mult_2.v
On yosys we can synthesized this file. So, run:

 ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read_verilog mult_2.v
  $ synth -top mul2
  ```
  Create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show mul2```
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/8.jpg?raw=true)
  Write the netlist, run:
  ```$ write_verilog -noattr mult_2_net.v```
  
  In case of the file called mult_8.v, the steps are the same.
 

## Day 3: Combiantional and Secuential optimization

**The key for an optimize a digitial circuit is focus on Area and Power saving**
We will se two techniques for optimization on digital circuits:
- Cosntant propagation(direct optimization)
- Boolean logic optimization (using k-map for example)
## Constant propagation optimization
Consider the circuit of the next figure. And remember that every logic gate has a propagation delay. So, it's possible design a circuit that the ouput behavioral will be equivalent to the another circuit but whit less propagation delay. The answer to this question is yes, and that is the key for this type of optimization.
In the case of the last figuere, this circuit is equivalent to a neg-buffer. See the next figure.
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/diagrams/7.png?raw=true)
**Note that the number of transistor for implement the circuit of the last figure will be different in both cases. A less number of transistors is equivalent to have a circuit that consume less power and use less area! **

## Boolean optimization
Consider the next boolean expresion * assign y = a?(b?c:(c?a:0)):(!c). Using boolean algebra is possible to reduce this original expresion. The reduction of this expresion is equivalent to obtain a new circuit (simplified) that will be optimized in terms of area and power consumption because the epresion of this new circuit is a simplification of the original expresion. See the next figure:
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/diagrams/8.jpg?raw=true)

## Cloning and Retiming optimization
Cloning optimization consist in cloning one part of the circuit that is far away of the other parts. See the next figure
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/diagrams/9.png?raw=true)

Retiming optimization consist in modificate some part of the logic of one cicuit for reduce the max propagation delay, and increment the max frecuency of the clock.
In the next figure, Logic1 and Logic2 has a propagation delay equal to 5ns and 2ns respectively, so the max clock frecuency is 200MHz.
Now we can modify part of the Logic1 and also modify part of the Logic2 and compensate the slow stage with the fast stage, and in consequence increment the max clk frecuency. See the next figure
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/diagrams/10.png?raw=true)
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/diagrams/11.png?raw=true)

# Lab: combinational logic optimization 

consider the next verilog code  ```opt_check.v ```
 ```verilog
module opt_check (input a , input b , output y);
        assign y = a?b:0;
endmodule
 ```
On yosys we can synthesized this file. So, run:

 ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read_verilog opt_check.v
  $ synth -top opt_check
  ```
  Optimize, run:
  ```opt_clean -purge```
  
  Create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show opt_check```
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/9.jpg?raw=true)

consier the another verilog code called  ```opt_check2.v

 ```verilog
module opt_check2 (input a , input b , output y);
        assign y = a?1:b;
endmodule
 ```
On yosys we can synthesized this file. So, run:

 ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read_verilog opt_check2.v
  $ synth -top opt_check2
  ```
  Optimize, run:
  ```opt_clean -purge```
  
  Create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show opt_check2```
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/diagrams/10.jpg?raw=true)
  
# Lab Sequential Logic Optimisations

Consider the next verilog code:

 ```verilog
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
        if(reset)
                q <= 1'b0;
        else
                q <= 1'b1;
end

endmodule ```

On yosys we can synthesized this file. So, run:

 ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read_verilog dff_const1.v
  $ synth -top dff_const1
  ```
  Now we need to force the tool yosys for use only DFF, so run:
  ``` $ dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  
  Create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show dff_const1```
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/11.jpg?raw=true)

 ```verilog
module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
        if(reset)
                q <= 1'b1;
        else
                q <= 1'b1;
end

endmodule ```

On yosys we can synthesized this file. So, run:

 ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read_verilog dff_const2.v
  $ synth -top dff_const2
  ```
  dfflib it's was included in the last module so is not necessary to import now.
  
  Create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show dff_const2```
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/12.jpg?raw=true)

 ```verilog
module dff_const3(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
        if(reset)
        begin
                q <= 1'b1;
                q1 <= 1'b0;
        end
        else
        begin
                q1 <= 1'b1;
                q <= q1;
        end
end

endmodule ```

On yosys we can synthesized this file. So, run:

 ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read_verilog dff_const3.v
  $ synth -top dff_const3
  ```
  dfflib it's was included in the last module so is not necessary to import now.
  
  Create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show dff_const3```
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/13.jpg?raw=true)



## Lab: Seq optimisation unused outputs part1
----------------------------------------------------------------------------------------------------------------------------

# Day 4

What is GLS?
GLS is Gate Level Simulation. In the past labs, when we do the simulation of the DUT (device under test) using verilog(test bench), we just simulate the RTL level following this diagram:

In case of GLS, we don't use the RTL code, we will use the netlist genarated by yosys for do the test bench following the next diagram:

Note that the test bench file used in RTL simulation is the same test bench file for do the GLS. 

Why GLS?
Its crucial to do this type of simulation becuase the simulation will run with the .lib standard cells and that is with real models(real capacitance, delay, power, etc)
. Some important points are:
- Verify the logical correctness of design after synthesis
- Ensuring the timimng of the design is met


## Synthesis simulation mismatches

There are some point to validate the if the netlist generated by the synthesis of an RTL code are both equivalent (reciprocation between RTL code an his netlist after the synthesis).
Typical mismatches between RTL and netlist are:
- Absence of Sensitivity List
- Blocking Vs Non Blocking Assignments
- Non Standard Verilog Coding

### Absence of Sensitivity List:
Consider the next RTL code of a mux:

```verilog
module mux(
input i0,input i1
input sel,
output reg y
);
always @ (sel)
begin
   if (sel)
            y = i1;
   else 
            y = i0;          
end
endmodule
```
note that the always block has a sensitivity list **that only consider the input sel**. This will be implicate that the output only can change when the sel input change. So, if the input iO or i1 change but sel don't change there will not be any change on the oupyt y.

If we analyze the result of the RTL simulation, the simulator will consider the past verilog code like an a Latch/Double edged Flop. But when we do the Synthesis, the logic will be a real Mux (of the .lib cells) and there is a synthesis simulation mismatch.

Now look the next RTL code:
```verilog
module mux(
input i0,input i1
input sel,
output reg y
);
always @ (*)
begin
   if (sel)
            y = i1;
   else 
            y = i0;        
end
endmodule
```
In the past code, the only difference is the sensityvity list, and in this case the model consider any change on the input signals will produce a change on the output deteminated by the logic inside of the always block. The symbol  "*" it's equivalent to cosider every inputs of the circuit in the sensitity list.

### Blocking and Non blocking statments

In verilog codes there are two types of statments inside the always blocks:
- 1. Blocking statments, represent by the symbol "="
- 2. Non blocking statments, represent by the symbol "<="
**The difference between this two types of statments is the form of execution. The Blocking statments has a serial excecution (like in software programing) and Non blocking statments are excecute in parallel.**


consider the next verilog code, and note that the statments are blocking.
```verilog
module code (input clk,input reset,
input d,
output reg q);
always @ (posedge clk,posedge reset)
begin
if(reset)
begin
        q0 = 1'b0;
        q = 1'b0;
end
else
        q = q0;
        q0 = d;    
end
endmodule
```
The objetive of said code is create 2 stage 1-bit shift-register. Note that the reset conection is common for the two flops, but when reset is low q0 is assigned to q and q0 is assigned to d, and that happens in this order because the statment is blocking (secuential execution!)

Now if we look the next verilog code, we can note that the statements on the always block are Non blocking.
```verilog
module code (input clk,input reset,
input d,
output reg q);
always @ (posedge clk,posedge reset)
begin
if(reset)
begin
        q0 <= 1'b0;
        q <= 1'b0;
end
else
        q0 <= d;
        q <= q0;   
end
endmodule
```
when reset is off, **the excecuition will be parallel and the order of the decaltation of the statments don't care and not affect the behavirioal of the logic.**



### Synthesis simulation mismatch with non blocking statments:
Consider the next verilog code, that want to implement the ecuation *Y = (A+B)C*.
```verilog
module code (input a,b,c
output reg y);
reg q0;
always @ (*)
begin
        y = q0 & c;
        q0 = a|b ;    
end 
endmodule
```
Inside the always block is possible to see the implementation of the past ecuation, and note that is necessary a intermediate register called q0. If we analyze the code, the excecution of the always block will be secuential, so the q0 register only will have a value after the first iteration (secuential excecution).

and what happend if in the pas code the order of the statements change? consider the next code:

```verilog
module code (input a,b,c
output reg y);
reg q0;
always @ (*)
begin
        q0 = a|b ;
        y = q0 & c;
end 
endmodule
```
In this case the implementation of the ecuation *Y = (A+B)C* is implemented after the calculation of q0, so in the syntesis netlist will be not necesseary to have a intermiediate flop.

## Lab GLS Synth Sim 
For this lab we used a verilog file called ternary_operator_mux.v, that implement a 2-1mux usign the ternary operation.
The code is:

```verilog
module ternary_operator_mux (input i0, input i1, input sel, output y);
        assign y = sel?i1:i0;
endmodule
```

Using iverilog for simulate the module and next gtkwave for see the results of the .vcd file. For do that, the commands were:
```
//read the files RTL
$ iverilog ternary_operator_mux.v tb_ternary_operator_mux.v
//run the binary output file
$ ./a.out
//import .vcd to gtkwave
$ gtkwave tb_ternary_operator_mux.vcd
```
--image|--
Now, using yosys we do the synthesis. The commans were:
```
//open yosys
$ yosys
//read the library 
$ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//read the mux verilog code
$ read_verilog ternary_operator_mux.v
//Synthesis process
$ synth -top ternary_operator_mux
//netlist generation
$ abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//show the graphical result of the netlist
$ show 
```
-- image --
```
//write the netlist file
$ write_verilog -noattr ternary_operator_mux_net.v
// close yosys for the next steps
$ exit
```

Now using the netlist verilog file created in the yosys flow synthesis, is possible to do the GLS. The steps were:
```
//use iverilog and read the verilog models, the netlis file and the same testbech that was used in the RTL sim.
$ iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/
sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v
//run the binary output file
$ ./a.out
//use gtkwave for see the waves!
$ gtkwave tb_ternary_operator_mux.vcd
```
---image---

## Lab Sensitivity list mismatch
Using the file called bad_mux.v we will note a mismatch between the RTL sim and the GLS. 
The verilog code used in this lab was:

```verilog
module bad_mux (input i0, input i1, input sel, output reg y);
always @(sel)
begin
	if(sel)
		y <= i1;
	else
		y <= i0;
end
endmodule
```
The steps follwed in the lab were:
- 1. Simulate the RTL code using iverilog

```
//read the files RTL
$ iverilog bad_mux.v tb_bad_mux.v
//run the binary output file
$ ./a.out
//import .vcd to gtkwave
$ gtkwave tb_bad_mux.vcd
```
--image--

note that the output only will change when the sel input change, so some changes in the other inputs don't produce a change in the output.

- 2. Do the synthesis of the bad_mux.v:
```
//open yosys
$ yosys
//read the library 
$ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//read the mux verilog code
$ read_verilog bad_mux.v
//Synthesis process
$ synth -top bad_mux
//netlist generation
$ abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//show the graphical result of the netlist
$ show 
```
-- image --
```
//write the netlist file
$ write_verilog -noattr bad_mux_net.v
// close yosys for the next steps
$ exit
```
- 3. Do the GLS, run:
```
//use iverilog and read the verilog models, the netlis file and the same testbech that was used in the RTL sim.
$ iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/
sky130_fd_sc_hd.v bad_mux_net.v tb_bad_mux.v
//run the binary output file
$ ./a.out
//use gtkwave for see the waves!
$ gtkwave tb_bad_mux.vcd
```
---image vs image---

## Lab Blocking assignments mismatch example

Using the file called blocking_caveat.v we will note a mismatch between the RTL sim and the GLS. 
The verilog code used in this lab was:

```verilog
module blocking_caveat (input a , input b , input  c, output reg d);
reg x;
always @ (*)
begin
        d = x & c;
        x = a | b;
end
endmodule

```
The steps follwed in the lab were:
- 1. Simulate the RTL code using iverilog

```
//read the files RTL
$ iverilog blocking_caveat.v tb_blocking_caveat.v
//run the binary output file
$ ./a.out
//import .vcd to gtkwave
$ gtkwave tb_blocking_caveat.vcd
```
--image--



- 2. Do the synthesis of the blocking_caveat.v:
```
//open yosys
$ yosys
//read the library 
$ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//read verilog code
$ read_verilog blocking_caveat.v
//Synthesis process
$ synth -top blocking_caveat
//netlist generation
$ abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//show the graphical result of the netlist
$ show 
```
-- image --
```
//write the netlist file
$ write_verilog -noattr blocking_caveat_net.v
// close yosys for the next steps
$ exit
```
- 3. Do the GLS, run:
```
//use iverilog and read the verilog models, the netlis file and the same testbech that was used in the RTL sim.
$ iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/
sky130_fd_sc_hd.v blocking_caveat_net.v tb_blocking_caveat.v
//run the binary output file
$ ./a.out
//use gtkwave for see the waves!
$ gtkwave tb_blocking_caveat.vcd
```
---image vs image---
--------------------------------------------------------------------------------------------------------------------------
#Day 5

## If statment
If statement is used for create **priority logic**, this statement are used **inside of the always blocks**. The sintax of this statemen in verilog is simething like the next code:

```verilog
if(<my_condition>) begin
...
...
end

else begin
...
...
end
```
Also, it's possible to has IF-ELSE-IF statement, and the sintaxis of this statement in verilog is:

```verilog
if(<my_condition_1>) begin

...
 //condition 1 excecution logic
...
else if(<my_condition_2>) begin

...
  //condition 2 excecution logic
...
end
else if(<my_condition_3>) begin

...
  //condition 2 excecution logic
...
end

else begin

...
  //condition else excecution logic
...
end
```
every condition in the last codes have special hardware for everyone, and a grpahycal implementation of this is showed in the next fifure

### Some cautions with If statement
Cnsider the next example code:
```verilog
if(<my_condition_1>) begin

...
 //condition 1 excecution logic
...
else if(<my_condition_2>) begin

...
  //condition 2 excecution logic
...
end
else if(<my_condition_3>) begin

...
  //condition 2 excecution logic
...
end
```
note that in the last code not exist a ELSE condition, so is an **incomplete if statement (inferred latches)**

## Case statement
Like the If statement, case statement are used inside the always block on a verilog code, and the variables modified inside of this block need to be a register type. A example of syntaxis of this type of statement is showed in the next code.

```verilog
reg my_reg

always @ (*) begin

	case(sel_signal)
		2'b00:begin
		      ...
		      end
		2'b01:begin
		      ..
		      end
		default: begin
		      ...
		      end
	endcase	
end
```
Note that in this case we don't have a priority of excecution. And remember that the every case statement need to be a **default condition for avoid inferred latches** .

##Lab: Incomplete IF1

consider the next verilog file called incomp_if.v and note that the if statement is incomplete!

module incomp_if (input i0 , input i1 , input i2 , output reg y);
always @ (*)
begin
        if(i0)
                y <= i1;
end
endmodule

Now we will do the simulation RTL, syntehsis and GLS. Steps and command are bellow:


//load design and tb
$ iverilog incomp_if.v tb_incomp_if.v
//run the binary output file
$ ./a.out
//use gtkwave for see the waveforms
$ gtkwave tb_incomp_if.vcd

--image ---

//run yosys
$ yosys
//import the library 
$ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//design read
$ read_verilog incomp_if.v 
//Synthesize the design 
$ synth -top incomp_if
//netlist generation
$ abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//show the result
$ show 

-- image--

//GLS stage
//add the necessary files
$ iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/
sky130_fd_sc_hd.v incomp_if.v  tb_incomp_if.v
//run the .a file
$ ./a.out
//use gtkwave
$ gtkwave tb_incomp_if.vcd

--image--

##Lab: Incomplete IF2

consider the next verilog file called incomp_if2.v and note that the if statement is incomplete! 


module incomp_if2 (input i0 , input i1 , input i2 , input i3, output reg y);
always @ (*)
begin
        if(i0)
                y <= i1;
        else if (i2)
                y <= i3;

end
endmodule
           

Now we will do the simulation RTL, syntehsis and GLS. Steps and command are bellow:


//load design and tb
$ iverilog incomp_if.v tb_incomp_if2.v
//run the binary output file
$ ./a.out
//use gtkwave for see the waveforms
$ gtkwave tb_incomp_if2.vcd

--image ---

//run yosys
$ yosys
//import the library 
$ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//design read
$ read_verilog incomp_if2.v 
//Synthesize the design 
$ synth -top incomp_if2
//netlist generation
$ abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//show the result
$ show 

-- image--

//GLS stage
//add the necessary files
$ iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/
sky130_fd_sc_hd.v incomp_if2.v  tb_incomp_if2.v
//run the .a file
$ ./a.out
//use gtkwave
$ gtkwave tb_incomp_if2.vcd

--image--

##Lab incomplete case statement

consider the next verilog file called incomp_case.v and note that the case statement is incomplete!


module incomp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
        case(sel)
                2'b00 : y = i0;
                2'b01 : y = i1;
        endcase
end
endmodule

**Note that the case statement don't have a default case!**
Now we will do the simulation RTL, syntehsis and GLS. Steps and command are bellow:


//load design and tb
$ iverilog incomp_case.v tb_incomp_case.v
//run the binary output file
$ ./a.out
//use gtkwave for see the waveforms
$ gtkwave tb_incomp_case.vcd

--image ---

//run yosys
$ yosys
//import the library 
$ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//design read
$ read_verilog incomp_case.v 
//Synthesize the design 
$ synth -top incomp_case
//netlist generation
$ abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//show the result
$ show 

-- image--

//GLS stage
//add the necessary files
$ iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/
sky130_fd_sc_hd.v incomp_if2.v  tb_incomp_case.v
//run the .a file
$ ./a.out
//use gtkwave
$ gtkwave tb_incomp_case.vcd

--image--
            
##Lab complete case statement

consider the next verilog file called comp_case.v and note that the case statement is complete! (default condition)


module comp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
        case(sel)
                2'b00 : y = i0;
                2'b01 : y = i1;
                default : y = i2;
        endcase
end
endmodule

Now we will do the simulation RTL, syntehsis and GLS. Steps and command are bellow:


//load design and tb
$ iverilog comp_case.v tb_comp_case.v
//run the binary output file
$ ./a.out
//use gtkwave for see the waveforms
$ gtkwave tb_comp_case.vcd

--image ---

//run yosys
$ yosys
//import the library 
$ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//design read
$ read_verilog comp_case.v 
//Synthesize the design 
$ synth -top comp_case
//netlist generation
$ abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//show the result
$ show 

-- image--

//GLS stage
//add the necessary files
$ iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/
sky130_fd_sc_hd.v comp_case.v  tb_comp_case.v
//run the .a file
$ ./a.out
//use gtkwave
$ gtkwave tb_comp_case.vcd

--image--
          
##Lab partial case statement

consider the next verilog file called partial_case_assign.v and note that the case statement for the condition 2b01, the output x is not defined!


module partial_case_assign (input i0 , input i1 , input i2 , input [1:0] sel, output reg y , output reg x);
always @ (*)
begin
        case(sel)
                2'b00 : begin
                        y = i0;
                        x = i2;
                        end
                2'b01 : y = i1;
                default : begin
                           x = i1;
                           y = i2;
                          end
        endcase
end
endmodule
             

Now we will do the simulation RTL, syntehsis and GLS. Steps and command are bellow:


//load design and tb
$ iverilog partial_case_assign.v mux_generate.v
//run the binary output file
$ ./a.out
//use gtkwave for see the waveforms
$ gtkwave mux_generate .vcd

--image ---

//run yosys
$ yosys
//import the library 
$ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//design read
$ read_verilog partial_case_assign.v 
//Synthesize the design 
$ synth -top partial_case_assign 
//netlist generation
$ abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//show the result
$ show 

-- image--

//GLS stage
//add the necessary files
$ iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/
sky130_fd_sc_hd.v ipartial_case_assign .v  mux_generate .v
//run the .a file
$ ./a.out
//use gtkwave
$ gtkwave mux_generate .vcd

--image--
          
## For and generate statement labs
For loops can be used in two modes: create a logic (is inside of the always blcok) and the second option is use the for loops for generate hardware (outside of the always block!)


consider the next verilog file called mux_generate.v. This code is an example of use for loops for create a the logic of a circuit. The circuit described in this case is a mux 4-1 mux.


The verilog code is:

module mux_generate (input i0 , input i1, input i2 , input i3 , input [1:0] sel  , output reg y);
wire [3:0] i_int;
assign i_int = {i3,i2,i1,i0};
integer k;
always @ (*)
begin
for(k = 0; k < 4; k=k+1) begin
        if(k == sel)
                y = i_int[k];
end
end
endmodule


Now we will do the simulation RTL, syntehsis and GLS. Steps and command are bellow:


//load design and tb
$ iverilog mux_generate .v tb_mux_generate .v
//run the binary output file
$ ./a.out
//use gtkwave for see the waveforms
$ gtkwave tb_mux_generate .vcd

--image ---

//run yosys
$ yosys
//import the library 
$ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//design read
$ read_verilog mux_generate .v 
//Synthesize the design 
$ synth -top mux_generate 
//netlist generation
$ abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//show the result
$ show 

-- image--

//GLS stage
//add the necessary files
$ iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/
sky130_fd_sc_hd.v imux_generate .v  tb_mux_generate .v
//run the .a file
$ ./a.out
//use gtkwave
$ gtkwave tb_mux_generate .vcd

--image--

Now consider an use example for use for-loops for **generate hardware**. See the next code called rca.v

module rca (input [7:0] num1 , input [7:0] num2 , output [8:0] sum);
wire [7:0] int_sum;
wire [7:0]int_co;

genvar i;
generate
        for (i = 1 ; i < 8; i=i+1) begin
                fa u_fa_1 (.a(num1[i]),.b(num2[i]),.c(int_co[i-1]),.co(int_co[i]),.sum(int_sum[i]));
        end

endgenerate
fa u_fa_0 (.a(num1[0]),.b(num2[0]),.c(1'b0),.co(int_co[0]),.sum(int_sum[0]));


assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule

note that design depends of the one external block called 'fa', this block is an adder. The fa,v code is:

module fa (input a , input b , input c, output co , output sum);
        assign {co,sum}  = a + b + c ;
endmodule

note that in the file called rca.v the module fa is replicate i times. So we are using for loop for generate hardware.

Now we will do the simulation RTL, syntehsis and GLS. Steps and command are bellow:


//load designs and tb
$ iverilog fa.v rca.v tb_rca.v
//run the binary output file
$ ./a.out
//use gtkwave for see the waveforms
$ gtkwave tb_rca.vcd

--image ---

//run yosys
$ yosys
//import the library 
$ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//design read
$ read_verilog rca.v 
//Synthesize the design 
$ synth -top rca 
//netlist generation
$ abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
//show the result
$ show 

-- image--

//GLS stage
//add the necessary files
$ iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/
sky130_fd_sc_hd.v rca.v  tb_rca.v
//run the .a file
$ ./a.out
//use gtkwave
$ gtkwave tb_rca .vcd

--image--
