#  Xilinx Vivado IP for Block RAM Implementation

The Xilinx® IP Block Memory Generator (BMG) core is an advanced memory constructor that generates area and performance-optimized memories using embedded block RAM resources in Xilinx FPGAs.

The BMG core supports both Native and AXI4 interfaces.

#### We will be configuring BMG core to support Native interface only

## Native Block Memory Generator Specific Features

- Generates Single-port RAM, Simple Dual-port RAM, True Dual-port RAM, Single-port ROM, and Dual-port ROM
  
- Supports memory sizes up to a maximum of 16 MBytes (byte size 8 or 9) (limited only by memory resources on selected part)
-  Configurable port aspect ratios for dual-port configurations and Read-to-Write aspect ratios
- Supports the built-in Hamming Error Correction Capability (ECC). Error injection pins allow insertion of single and double-bit errors
- Supports soft Hamming Error Correction (Soft ECC) for data widths less than 64 bits
- Option to pipeline DOUT bus for improved performance in specific configurations
- Choice of reset priority for output registers between priority of SR (Set Reset) or CE (Clock Enable)
- Performance up to 450 MHz

# True Dual Port Ram Configuration for 16-bit adress

### • Create a new project in vivado and click on IP Catalog option in Project Manager menu

![image](https://github.com/user-attachments/assets/3845a795-dc62-4ea7-a9f9-69005bff7041)

### • In IP Catalog type Block Memory in search bar and select Block Memory Generator from the menu

![image](https://github.com/user-attachments/assets/432d6925-76aa-4b8e-b472-0b7621f2c514)

### • Customize IP dialogue box appears

### In Basic Tab

- Select Interface Type Native

- Select True Dual Port Ram from Memory Type menu

- Select check box for the common clock option to drive both clock inputs with same clock buffer

 ![image](https://github.com/user-attachments/assets/a305bb16-b05a-4b3e-85cc-2f9ca5b8228b)

### In Port A Options Tab

#### Memory Size

- Set the bits of write and read width to 32 bits

- Set the value of write and read depth to 65536 which is equivalent to 16 bits (2^16 = 65536)

- There are 3 type of operating modes available:
    1. Write First mode: In WRITE_FIRST mode, the input data is simultaneously written into memory and driven on the data output. This transparent mode offers the flexibility of using 
                         the data output bus during a Write operation on the same port.
    2. Read First mode: In READ_FIRST mode, data previously stored at the Write address appears on the data output, while the input data is being stored in memory.
    3. No Change mode:  In NO_CHANGE mode, the output latches remain unchanged during a Write operation

- Set Port A operating mode to No Change mode

- Set Port A Enable Port Type to Always Enabled which set your memory to be enabled always. (There is another option to use ENA pin in which memory can be enabled and disabled according 
  to the given input)

#### Port A Optional Output Registers

- Select check box for primitives Output Register option

- There are 2 more option of output registers available for port A
    1. Core Output Register: In this option your core output of port A adds a register stage which results in delaying output by 1 clock cycle
    2. REGCEA Pin: In this option your memory have a register enable pin (REGCEA pin). If it is not selected as in our case only primitives output register is selected than the output                      register is enabled using the enabled signal itself.

#### Port A Output Reset Options

- Select check box for the RSTA Pin (Set/Reset pin) option

- Set Output Reset Value in Hex to 0 (It can also be set to other values)

- Reset Priority have 2 options available:
    1. CE (Clock Enable): When CE is the selected priority, then CE (regcea) has a priority over reset (rsta)
    2. SR (Set Reset): When SR is the selected reset priority, then reset has a priority over CE. We are selecting SR option.

![image](https://github.com/user-attachments/assets/e5e1aa17-0d85-4141-8cef-65e310046845)

### In Port B Options Tab

#### Memory Size

- It's read write width and depth are already configured with respect to port A

- Set Port B operating mode to Read First Mode

- Set Port B Enable Port Type to Always Enabled

#### Port B Optional Output Registers

- Select check box for primitives Output Register option

- There are 2 more option of output registers available for port B
    1. Core Output Register: In this option your core output of port A adds a register stage which results in delaying output by 1 clock cycle
    2. REGCEB Pin: In this option your memory have a register enable pin (REGCEB pin). If it is not selected as in our case only primitives output register is selected than the output                      register is enabled using the enabled signal itself.

#### Port B Output Reset Options

- Select check box for the RSTB Pin (Set/Reset pin) option

- Set Output Reset Value in Hex to 0 (It can also be set to other values)

- Reset Priority have 2 options available:
    1. CE (Clock Enable): When CE is the selected priority, then CE (regceb) has a priority over reset (rstb)
    2. SR (Set Reset): When SR is the selected reset priority, then reset has a priority over CE. We are selecting SR option.

![image](https://github.com/user-attachments/assets/1483a66d-3bff-480c-a8c0-1f09926bf7d5)

### In Other Options Tab

#### Memory Initialization

- Select check box for Load init file

- Click on edit to edit the already existing file or to make a new COE file

- In COE file editor there are 2 key values to be set
    1. memory_initialization_radix: Set 2 to get Binary, 10 to get Decimal and 16 to get Hexa
    2. memory_initialization_vector: write your memory values within your specified width

![image](https://github.com/user-attachments/assets/d9c71c32-c679-43ac-bfe6-5fb4da2faeae)

- Select check box to fill remaining memory locations with your desired value (0 in this case)

![image](https://github.com/user-attachments/assets/b51d6399-1ecd-4c52-aa73-d5ff06c7e9b3)

### In IP Symbol Tab

- Here your Block Ram block diagram is shown where you can see the input ports and their bit size

![image](https://github.com/user-attachments/assets/9a2be150-2f11-4269-a710-0a76e7e87008)

- Clicking Ok appears this dialogue box. Click on generate to generate your Block Ram IP

![image](https://github.com/user-attachments/assets/9edfb7ae-33ea-4558-8f1c-47d453a82e0f)

![image](https://github.com/user-attachments/assets/b1276de5-208b-47cc-b72a-9c6544e32e9c)

## Using block ram IP 

I am using Block Ram Ip which have following specifications:

### Port A and Port B

- 16 bit read and write address
- 32 bit read and write data width
- no write enable for port A
- write enable for port B
- no data input for port A
- data input for port B
- Common clock for port A and port B

### System Verilog code with a Block RAM IP instantiated in it

```sh
`timescale 1ns / 1ps

module block_ram(

    input logic clk,
    input logic rst_n,
    
    input logic [15:0]address_a,
    
    output logic [31:0] data_outa,
    
    input logic write_en,
    input logic [15:0]address_b,
    input logic [31:0]data_in,

    output logic [31:0] data_outb
);

blk_mem_gen_0 bram(
        .clka(clk),
        .rsta(rst_n),
        .wea(1'b0),
        .addra(address_a),
        .dina(32'b0),
        .douta(data_outa),
        .clkb(clk),
        .rstb(rst_n),
        .web(write_en),
        .addrb(address_b),
        .dinb(data_in),
        .doutb(data_outb)
        );

endmodule
```
### System Verilog Test Bench code to test Block RAM IP

```sh
`timescale 1ns / 1ps

module block_ram_tb();

logic clk,rst_n;
logic write_en;
logic [15:0] address_a;
logic [31:0] data_in;
logic [15:0] address_b;
logic [31:0] data_outa;
logic [31:0] data_outb;
    
block_ram dut(
    .clk(clk),
    .rst_n(rst_n),
    .write_en(write_en),
    .address_a(address_a),
    .data_in(data_in),
    .address_b(address_b),
    .data_outa(data_outa),
    .data_outb(data_outb)
);   

// Initial block to generate the clock signal
initial begin
    clk = 0;
    forever #5 clk = ~clk;
end
 
 initial begin
    rst_n       = 1'b1; 
    write_en    = 1'b0;
    data_in     = 32'h0;
    address_a   = 16'h0;
    address_b   = 16'h0;
    #20;
    rst_n       = 1'b0;
    #20; 
    // Write 24 on Address_b
    write_en    = 1'b1;
    data_in     = 32'h24;
    address_a   = 16'h1;
    address_b   = 16'h0;
    #20;
    // Read Address_b
    write_en    = 1'b0;
    data_in     = 32'h0;
    address_a   = 16'h1;
    address_b   = 16'h0;
    #20;
    // Write FA on Address_b
    write_en    = 1'b1;
    data_in     = 32'hFA;
    address_a   = 16'h3;
    address_b   = 16'h2;
    #20;
    // Read Address_b
    write_en    = 1'b0;
    data_in     = 32'h0;
    address_a   = 16'h3;
    address_b   = 16'h2;
    #20;
    // Write 8B on Address_b
    write_en    = 1'b1;
    data_in     = 32'h8B;
    address_a   = 16'h5;
    address_b   = 16'h4;
    #20;
    // Read Address_b
    write_en    = 1'b0;
    data_in     = 32'h0;
    address_a   = 16'h5;
    address_b   = 16'h4;
    #20;
    // Write F9 on Address_b
    write_en    = 1'b1;
    data_in     = 32'hF9;
    address_a   = 16'h7;
    address_b   = 16'h6;
    #20;
    // Read Address_b
    write_en    = 1'b0;
    data_in     = 32'h0;
    address_a   = 16'h7;
    address_b   = 16'h6;
    #20;
    rst_n       = 1'b1; 
    #20;
    rst_n       = 1'b0;
    $finish;
    
 end    
endmodule
```

### The resultant simulation will be like this

#### Port A
In this simulation, the address of port A is changing and displaying the data output stored in the COE file accordingly. For port A, the enable signal is set to 0, as it is in no-change mode, so it is simply designed to retrieve data at the corresponding address provided by the user.

#### Port B
In this simulation, the address of port B is chosen where data needs to be written. First, the write enable is turned on to write the data to the designated address provided by the user. Then, the write enable is turned off in the next cycle while the address remains the same to read the data that the user wrote in the previous cycle.

Further references to learn more about implementation of BMG in Xilinx Vivado

https://web.mit.edu/neboat/Public/6.111_final_project/code/blk_mem_gen_ds512.pdf

## The problem we are facing while using the Block RAM IP is with 32-bit read and write addresses.

While using a 32-bit read or write address, the output for port A gets latched and never changes. Once it is set to the value of address 0, it remains the same regardless of address changes in each cycle. For port B, the output data variable initially holds the value of some random address. When the write enable is turned on to write to a specific byte, only that byte gets changed. However, when the address is changed to write to another address, the output value does not change; it remains the previously modified value. It updates the previous stored value instead of the new one. On different addresses, it changes the previous stored value instead of the original value for that address.

These are the issues we are facing. If anyone knows the solution to resolve them, please let us know.

