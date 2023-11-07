# Logbook for Instructions Architecture and Compiler

## Noam Weitzman 

## Lab 1: Counter

#### Task 1
The goal of Task 1 was to simulate a basic 8-bit binary counter. 

To do this, we followed basic steps, including copying the Lab 1 folder to our own github, running VS Code (DO NOT FORGET TO RUN IT WITH WSL), and writing some code for our counter. 

we created a system verilog file (counter.sv) where our function counts on the positive edge of clk if enable is 1.  It is synchronously reset to 0 if rst is asserted.

We also created a testbench file in C++ (counter_tb.cpp). This consist of various sections including: headers, introducing our variables, turning on signal tracing, setting initial signal levels, and a simple simulation that toggles the clock by changing rst and en during the simulation. 

##### Test yourself challenges

The first challenge asked us to modify the testbench so that we stop counting for 3 cycles once the counter reaches 0x9 and them resume counting. This is a snapshot of my code. 

```Pyhton
top->rst = (i<2);
top->en = (i>4);

if ((top->count == 9) && (steps < 3)){
    top->en = 0;
    steps++;
}
```

This is the waveform I obtained after running the simulation.

![Waveform simulation](/Images/Waveform1-lab1-task1.png)


The second challenge was to implement an asynchronous reset by changing the counter.sv file. This is a snapshot of my code.

```Python
always_ff @ (posedge clk, posedge rst)
    if (rst) count <= {WIDTH{1'b0}};
    else count <= count + {{WIDTH-1{1'b0}}, en};
```

#### Task 2
The goal of task 2 was to link our Verilator simulation with Vbudy.
After doing all the steps indicated on this webpage: it worked! 

![Vbuddy Connected](/Images/vbuddy%20connected.png)

see video of whats going on

I then copied the different functions and recompiled the code and it works, i get a counter displayed on my vbuddy. 

Every time, I reconnect I have to follow these steps: 
* run powershell as admin
* run these command: usbipd wsl attach --busid=2-1 and usbipd wsl list to check that it is attached
* run this command on vscode to see which port it is attached to:  dmesg | grep tty
* copy the number of the port to the vbuddy.cfg file and save it
* compile and run it using source ./doit.sh

I did the test challenge. do not forget tht enable is 0 so we need to substract !en (=1)

![Challenge](/Images/Test%20yourself%20challenge%20task2-lab1.png)

#### Task 3
We now introduce a new idea: we replace the enable signal with a load signal (ld). When ld == 1, the value v is loaded into the counter. We also use the vbdSetMode(1). IN this mode, whenever the switch is pressed on the vbussy, the flag register is set to 1, however everytime we read the function vbdFlag() (in every cycle), it resets it to 0. So by writing top->ld = vbdFlag(), the value of v is loaded in ld.

see video of whats going on

To single step, see code it works !
![Single Stepping](/Images/Single%20Stepping%20Lab1%20task3.png)

#### Task 4
For this task, we now write the digits only in binary coded digits so from 0 to 9, no A, B,..., F. 
To do this we have to have more files, so we also have to change the ./doit.sh to compile and execute all of them. CHECK THE NEW ./doit.sh, its important to see what we have changed. 
I also worked on two different versions: if in the testbench, we have vbdSetMode(0), we will have the same as in the beginning where the button on the vbuddy will act as a play/pause button, and it will go on automatically when it is playing. if we have the line vbdSetMode(1) instead, the button will allow us to have a step counter so it will increase one everytime we click it. 

see video on phone and code on wsl vscode

## Lab 2: Signal Generator

#### Task 1

For the first step, we look at the sinegen.py and sinerom.mem files. These files are used to initialise the ROM. The first one is python code used to create values of a sinewave and the second one is used to store them in hex format. 

We also create a component in the rom.sv file, using code written in lec4 slide20.

For the second step, we create the sinegen.sv top-level module, which includes two components: counter.sv and rom.sv. The sinegen.sv module included the two other componemts by writing this code:

![including modules](/Images/including%20modules%20in%20the%20toplevel%20module.png)

This is extremely important. We plug into the inner module th values of input that exit in te top-level module.

We also have to update the doit.sh file so that it compiles all the useful sv files and uses the testbench to execute the top level one. We also create the new files in the directory.

For writing the test bench, we have to adapt it to the sinegen.sv file. Look at how it is done, extremely important. 

For the challenge, we use the vbdValue() function to change the frequency of the sinewave generator. For this we implement the value of incr using the vbdValue()

#### Task 2

For the second task, we want to produce a dual sine and cosine wave.

For this we introduce an offset. We have to introduce it as an input in the sinegen.sv file and rom.sv. In the sinegen.sv we have to initalise the value of offset to an offset value, and also include the output value of dout2. In the testbench, we also have to change some code to set the value of offset to the value of the rotary encoder. Finally in the rom.sv file, when we change the value of our dout1 and dout2, we have to include a begin and end statement as it is longer than one line.

#### Task 3

All done, look at code I wrote. Fairly straightforward.


## Lab 3