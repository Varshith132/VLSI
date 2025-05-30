#CODTECHIT VLSI TASKS
*NAME*: A.VARSHITH
*INTERN ID* :CT4MPGN 
*DOMAIN*: VLSI
*DURATION*: 4 MONTHS
#TASK I*:-

# Arithmetic Logic Unit (ALU) Module Documentation

## 1. Introduction
The Arithmetic Logic Unit (ALU) is a fundamental component in digital systems, particularly in processors, responsible for performing arithmetic and logical operations. This document describes a 4-bit ALU implemented in Verilog, a hardware description language used for designing and modeling digital circuits. The ALU supports basic operations such as addition, subtraction, bitwise AND, OR, and NOT, selected via a 3-bit operation code.

## 2. Module Overview
The `alu` module is a combinational logic block that processes two 4-bit inputs and produces a 4-bit output based on the selected operation. It is designed for simplicity and efficiency, making it suitable for integration into larger digital systems like microprocessors or custom digital circuits.

### 2.1 Module Declaration
```verilog
module alu (
    input [3:0] A, B,    // 4-bit inputs
    input [2:0] op,      // 3-bit operation code
    output reg [3:0] Y   // 4-bit output
);
```

### 2.2 Port Description
- **Inputs**:
  - `A [3:0]`: A 4-bit operand (e.g., `4'b1010` for decimal 10).
  - `B [3:0]`: A second 4-bit operand (e.g., `4'b0011` for decimal 3).
  - `op [2:0]`: A 3-bit operation code that determines the operation to perform.
- **Output**:
  - `Y [3:0]`: A 4-bit result of the selected operation, implemented as a register (`reg`) for use in the procedural block.

## 3. Functional Description
The ALU performs one of five operations based on the 3-bit `op` input, with a default case for undefined operation codes. The operations are implemented in a combinational manner using an `always @(*)` block, ensuring the output updates immediately when inputs change.

### 3.1 Operation Table
The following table summarizes the operations supported by the ALU:

| Operation Code (`op`) | Operation       | Description                     |
|-----------------------|-----------------|---------------------------------|
| `3'b000`             | Addition        | `Y = A + B`                    |
| `3'b001`             | Subtraction     | `Y = A - B`                    |
| `3'b010`             | Bitwise AND     | `Y = A & B`                    |
| `3'b011`             | Bitwise OR      | `Y = A | B`                    |
| `3'b100`             | Bitwise NOT     | `Y = ~A` (ignores `B`)         |
| Others (`3'b101`, `3'b110`, `3'b111`) | Zero Output | `Y = 4'b0000`         |

### 3.2 Operation Details
- **Addition (`op = 3'b000`)**:
  - Computes the 4-bit sum of `A` and `B`.
  - Example: If `A = 4'b0011` (3) and `B = 4'b0100` (4), then `Y = 4'b0111` (7).
  - Note: Overflow is not detected (e.g., `4'b1111 + 4'b0001` wraps around to `4'b0000`).
- **Subtraction (`op = 3'b001`)**:
  - Computes `A - B`.
  - Example: If `A = 4'b0100` (4) and `B = 4'b0011` (3), then `Y = 4'b0001` (1).
  - Negative results may produce underflow (e.g., `4'b0000 - 4'b0001 = 4'b1111`, interpreted as unsigned).
- **Bitwise AND (`op = 3'b010`)**:
  - Performs a bitwise AND operation on `A` and `B`.
  - Example: If `A = 4'b1010` and `B = 4'b1100`, then `Y = 4'b1000`.
- **Bitwise OR (`op = 3'b011`)**:
  - Performs a bitwise OR operation on `A` and `B`.
  - Example: If `A = 4'b1010` and `B = 4'b1100`, then `Y = 4'b1110`.
- **Bitwise NOT (`op = 3'b100`)**:
  - Computes the bitwise NOT of `A`, ignoring `B`.
  - Example: If `A = 4'b1010`, then `Y = 4'b0101`.
- **Default Case**:
  - For undefined `op` codes (`3'b101`, `3'b110`, `3'b111`), the output is set to `4'b0000`.
  - This ensures predictable behavior for invalid inputs.

## 4. Implementation Details
The ALU is implemented using a single `always @(*)` block, which creates combinational logic. The output `Y` is declared as a `reg` type because it is assigned within a procedural block, but the logic is purely combinational (no sequential elements like flip-flops).

### 4.1 Verilog Code
```verilog
always @(*) begin
    if (op == 3'b000) Y = A + B;  // Add A + B
    else if (op == 3'b001) Y = A - B;  // Subtract A - B
    else if (op == 3'b010) Y = A & B;  // AND A and B
    else if (op == 3'b011) Y = A | B;  // OR A or B
    else if (op == 3'b100) Y = ~A;     // NOT A
    else Y = 4'b0000;                  // Output 0 for other codes
end
endmodule
```

### 4.2 Design Considerations
- **Combinational Logic**: The use of `always @(*)` ensures the ALU responds immediately to input changes, suitable for real-time processing in a processor’s datapath.
- **Compact Operation Set**: The ALU supports a minimal set of operations to keep the design simple, reducing resource usage in FPGA or ASIC implementations.
- **No Status Flags**: The design omits flags (e.g., carry, overflow, zero) to maintain simplicity, but this limits its use in applications requiring condition codes.
- **Unsigned Arithmetic**: The ALU treats inputs as unsigned 4-bit numbers, which simplifies the logic but restricts applicability for signed operations.

## 5. Usage
The ALU can be instantiated in a larger Verilog design, such as a CPU, microcontroller, or custom digital circuit. It is typically used in the execute stage of a processor pipeline to perform arithmetic or logical operations on register values.

### 5.1 Example Instantiation
```verilog
module top (
    input [3:0] input_a, input_b,
    input [2:0] operation,
    output [3:0] result
);
    alu my_alu (
        .A(input_a),
        .B(input_b),
        .op(operation),
        .Y(result)
    );
endmodule
```

In this example, the ALU is instantiated in a top-level module, connecting external inputs to `A`, `B`, and `op`, with the output `Y` driving the `result` wire.

## 6. Testing and Verification
To ensure the ALU functions correctly, a comprehensive testbench is recommended. The testbench should:
1. Test all operations (`op = 3'b000` to `3'b100`) with various input combinations for `A` and `B`.
2. Include edge cases, such as:
   - Maximum values (`A = 4'b1111`, `B = 4'b1111`).
   - Zero inputs (`A = 4'b0000`, `B = 4'b0000`).
   - Mixed values (e.g., `A = 4'b1010`, `B = 4'b0101`).
3. Verify invalid `op` codes produce `Y = 4'b0000`.
4. Check for correct handling of arithmetic wraparound (e.g., addition overflow).

### 6.1 Example Testbench
```verilog
module alu_tb;
    reg [3:0] A, B;
    reg [2:0] op;
    wire [3:0] Y;

    alu dut (.A(A), .B(B), .op(op), .Y(Y));

    initial begin
        $monitor("A=%b, B=%b, op=%b, Y=%b", A, B, op, Y);
        // Test addition
        A = 4'b0011; B = 4'b0100; op = 3'b000; #10;
        // Test subtraction
        A = 4'b0100; B = 4'b0011; op = 3'b001; #10;
        // Test AND
        A = 4'b1010; B = 4'b1100; op = 3'b010; #10;
        // Test OR
        A = 4'b1010; B = 4'b1100; op = 3'b011; #10;
        // Test NOT
        A = 4'b1010; B = 4'bxxxx; op = 3'b100; #10;
        // Test invalid op
        A = 4'b1010; B = 4'b1100; op = 3'b111; #10;
        $finish;
    end
endmodule
```

This testbench monitors inputs and outputs, applies test cases, and simulates the ALU’s behavior over time.

## 7. Limitations
The ALU has the following constraints:
- **Bit Width**: Limited to 4-bit operands and results, which restricts its use for larger data.
- **No Status Flags**: Lacks outputs for carry, overflow, or zero, which are common in processor ALUs.
- **Unsigned Operations**: Does not support signed arithmetic, limiting its use in applications requiring negative numbers.
- **Limited Operation Set**: Only supports five operations, excluding common operations like XOR, shift, or multiplication.
- **No Error Handling**: Invalid `op` codes default to `4'b0000` without signaling an error.

## 8. Potential Extensions
To enhance the ALU’s functionality, consider the following:
- **Add Status Flags**: Include outputs for carry (for addition), borrow (for subtraction), zero (when `Y = 0`), and overflow.
- **Support Signed Arithmetic**: Modify addition and subtraction to handle two’s complement numbers.
- **Expand Operations**: Add support for XOR (`A ^ B`), left/right shifts, or comparison operations.
- **Increase Bit Width**: Extend to 8-bit, 16-bit, or 32-bit operands for broader applicability.
- **Pipelining**: Add registers to make the ALU sequential for use in pipelined architectures.

## 9. Applications
The ALU can be used in:
- **Microprocessors**: As the core computational unit in a CPU’s datapath.
- **Digital Signal Processing**: For basic arithmetic operations in signal processing circuits.
- **Educational Projects**: To teach digital design and Verilog programming.
- **Custom Circuits**: In ASICs or FPGAs requiring simple arithmetic/logical operations.

## 10. Synthesis Considerations
When synthesizing the ALU for FPGA or ASIC:
- **Resource Usage**: The design is lightweight, using minimal logic gates due to its simple operation set.
- **Timing**: As combinational logic, the critical path depends on the longest operation (typically addition or subtraction).
- **Optimization**: Synthesis tools may optimize the `if-else` structure into a multiplexer-based design for efficiency.

## 11. Conclusion
This 4-bit ALU is a simple, efficient Verilog module suitable for basic arithmetic and logical operations. Its combinational design ensures fast response to input changes, and its minimal operation set makes it resource-efficient. While limited in scope, it serves as a foundation for more complex ALUs and can be extended with additional features as needed. Comprehensive testing is recommended to verify functionality across all input combinations and operation codes.
 ##OUTPUT
 ![Screenshot 2025-05-30 131208](https://github.com/user-attachments/assets/c2671efc-5e5d-42e4-86b9-ec9316d5ee9f)


 ##TASK II:-
 🧩 Module Name: RAM DESIGN 
✅ Description:
This module implements a synchronous 8-bit wide, 256-location RAM with the following features:

Synchronous reset: Initializes all memory contents to 0.

Synchronous write: Writes data to memory on the rising edge of the clock when wr is high.

Synchronous read: Reads data from memory on the rising edge of the clock when wr is low.

🧮 Ports:
Name	Direction	Width	Description
clk	input	1	System clock
rst	input	1	Synchronous reset (active high)
wr	input	1	Write enable
din	input	8	Data input to be written
addr	input	8	Memory address (0–255)
dout	output	8	Data output from the memory

📦 Internal Components:
🔸 reg [7:0] mem [0:255];
256 bytes of memory (each 8 bits wide).

Memory is addressed using the addr input.

🔁 Behavior (in always @(posedge clk) block):
🔹 Reset (rst == 1):
On rising edge of clk, if rst is high:

All memory locations are set to 0 (cleared).

🔹 Write (wr == 1):
On rising edge of clk, if wr is high:

The value on din is stored in the memory at the address specified by addr.

🔹 Read (wr == 0):
On rising edge of clk, if wr is low:

The value stored at addr is loaded into dout.

🧪 Testbench – Module Name: tb_ram
✅ Purpose:
Verifies the functionality of the ram module by:

Resetting the memory.

Writing known values to specific addresses.

Reading back those values to check correctness.

🧾 Testbench Components
Signal	Type	Description
clk	reg	Clock (toggles every 5 units)
rst	reg	Resets memory (asserted at start)
wr	reg	Controls read/write
din	reg	Input data to be written
addr	reg	Address to read/write
dout	wire	Output data from RAM

🔄 Simulation Sequence
Time	Action	Notes
0	Reset asserted	Memory cleared to 0
10	Reset deasserted	Ready to write/read
20	Write 0xAA to address 10	
30	Write 0xBB to address 20	
40	Read from address 10	Expect 0xAA on dout
50	Read from address 20	Expect 0xBB on dout
70	Simulation ends	

📈 Expected Output (Console or Waveform)
verilog
Copy
Edit
dout = 0xAA (after reading address 10)
dout = 0xBB (after reading address 20)
📌 Notes
All memory operations (read/write/reset) happen synchronously with the clock's rising edge.

This design is suitable for small FPGA RAM simulations, digital design labs, and learning purposes.

Can be expanded to 16-bit/32-bit RAM by changing reg [15:0] or reg [31:0] and adjusting din, dout.
    ##OUTPUT:--![Screenshot 2025-05-30 134514](https://github.com/user-attachments/assets/0c0ffe47-d0d0-4c34-afcc-8f39871c42f9)

