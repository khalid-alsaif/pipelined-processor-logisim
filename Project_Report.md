# Project_Report

Project COE301

Team members:

KHALID ALI ALSAIF (ID:[student ID removed])

Mohammed Al Elaiw   (ID:[student ID removed])

Turkey Alanazi (ID:[student ID removed])



Design and implementation:

Register file: our design contain 7 writable registers and one additional register which always contain zero.

 the inputs to the register file are:

RA(3-bits) which specify one of the registers to be the first operand.

 RB(3-bits) which specify one of the registers to be second operand.

 RW(3-bits) which specify one of the registers to be destination.

RegWr signal that  allow writing the registers.

Clock signal.

BusW that contain the data that will be written to the registers.

The outputs to the register file are:

BusA that contain the value of the register specified by RA.

BusB that contain the value of the register specified by RB.



ALU: is the arithmetic logic unit that do some arithmetic and logic  operations 

The inputs to the ALU are:

A which is the first operand.

B which is the second operand.

OpCode that specifies which operation to carry out.

Func that specifies which operation to carry out when the OpCode is the same.

The outputs from the ALU are:

ALU result.

Some signals that will be use in other places like PC control (BGT,BGE,BLT,BLE,Zero,MOVZ,MOVN)

ALU control: we designed our ALU so that we don’t need ALU control.

Main control: there is 8 control signals:

RegRd: which is the selection of the MUX that output to Rd port of the register file.

RegRb: which is the selection of the MUX that output to RB port of the register file.

RegWrite: the signal that decide whether a register will be written or not.

ExtOp: decide whether the immediate will be zero or sign extended.

ALUSrc: the selection of the MUX that output to an input to ALU.

MemRd: to allow reading from memory.

MemWr: to allow writing to the memory.

WBdata: selection of the MUX that output to BusW in the register file.

PC control: the selection of the MUX that will update the PC counter, also it contain a logic to kill one or two instructions when we have J or branch instruction.

PC counter: special purpose register with 12bit that contain the location of the next instruction in Memory Instruction.

Instruction and data Memory.

Hazard Detect & Forward: the circuit that will solve data hazard when we have RAW situation also it will stall the CPU when we have an instruction immediately after Lw and trying to read the destination register of Lw instruction.







Simulate and testing: 

Below are some test cases to check the correctness of the design.

Initializing Registers (Testing I-Type, ALU):



Instruction

Hex.

Expected Result

1

lui 0x78f

d78f

r1 = 0X78F0 (shifted 4 bits)

2

ori r2, r1, 4

3444

r2 = 0X78F4

3

addi r3, r0, -2

463e

r3 = -2 = 0XFFFE (sign extension)

4

movz r7, r1, r1

1e4c

r7 = r1 = 0X78F0

5

movz r7, r1, r0

1e44

r7 = 0X78F0

6

movn r7, r1, r0

1e45

r7 = r1 = 0X78F0

7

movn r7, r0, r3

1e1d

r7 = 0X0000



Testing R-Type ALU Instructions (No RAW hazards, No Forwarding)



Instruction

Hex.

Expected Result

8

add r3, r1, r1

064c

r3 = 0xf1e0 (-3608)(carry is ignored)

9

sub r4, r1, r2

0855

r4 = 0xfffc

A

slt r7, r4, r2

0f16

r7 = 0x0001 (true), r4 < 0

B

sltu r7, r4, r2

0f17

r7 = 0x0000 (false), r4 > r2

C

and r1, r3, r4

02e0

r1 = 0xf1e0

D

orr1, r1, r2

0251

r1 = 0xf9f4

E

xor r1, r1, r3

025a

r1 = 0x014

F

nor r2, r1, r2

0453

r2 = 0x870b

10

sll r3, r4, r2

1712

r3 = 0xe000

11

srl r4, r1, r2

1853

r4 = 0x0001

12

sra r5, r1, r2

1a54

r5 = 0x0001

13

ror r6, r3, r2

1cd5

r6 = 0x001c













Testing RAW hazards and Forwarding



Instruction

Hex.

Expected Result

14

add r5, r1, r1

0a4c

r5 = 0x1028

15

sub r6, r5, r4

0d65

r6 = 0x1027, (depends on add)

16

and r7, r5, r6

0f70

r7 = 0x1020, (depends on add/sub)

17

ori r5, r5, 0xf

3b4f

r5 = 0x102f, (depends on add)



Testing SW and LW



Instruction

Hex.

Expected Result

18

swr1, 0(r0)

5200

MEM[0] = 0x0814

19

swr4, 1(r0)

5801

MEM[1] = 0x0001

1A

lwr5, 0(r0)

6a00

r5 = MEM[0] = 0x0814













































Testing Load delay, stalling pipelining, and forwarding after LW



Instruction

Hex.

Expected Result

1B

lwr6, 1(r0)

6c01

r6 = MEM[1] = 0x0001

1C

Andi r7, r6,0xb

2f8b

r7 = 0x0001



(stall 1 cycle)



1D

swr7, 2(r0)

5e02

MEM[2] = 0x0001

(forward)

1E

lwr5, 0(r0)

6a00

r5 = MEM[0] = 0x0814



1F

swr5, 3(r0)

5a03

MEM[3] = 0x0814

(forward)





Testing Branch and Jump Instructions



Instruction

Hex.

Expected Result

20

beq r1, r1, +2

8242

branch forward 2 (to bne)

21

add r5, r2, r2

0a94

should be skipped (r5 not modified)

22

bne r0, r1, +3

7043

branch forward 3 (to j)

23

add r6, r2, r2

0c94

should be skipped (r6 not modified)

24

add r7, r4, r4

0f24

should be skipped (r7 not modified)

25

j0x30

e030

jump to address 0x30 (ori)

26

add r5, r2, r2

0a94

should be skipped (r5 not modified)

27

add r6, r4, r4

0d24

should be skipped (r6 not modified)

28

and r0, r0, r0

0000

NO Operation



Fibonacci Example: Testing JAL and JR



Instruction

Hex.

Expected Result

29

address 0x30:



Address of ori = 0x30

2A

ori r1, r0, 5

3025

r1 = 5 (5th Fibonacci element)

2B

jal fib

#(0x40)

f040

call Fib (r7 = address of or)

2C

orr5, r2, r0

0a81

move r5 = r2 (Fib result) = 0x0008

2D

beq r0, r0, 0

8000

branch to self (stop program)

2E

and r0, r0, r0

0000

No operation

2F

j here

e02d

Infinite loop. Program ends at address

0x02d



. . .

xxxx

put any values between addresses xxxx



(any instructions)

. .

.

and yyyy. The instructions in this



. . .

yyyy

range are not executed.

40

address 0x40:

----

---

Fib starts here:

41

ori r2, r0, 1

3401

r2 = 1

42

ori r3, r0, 1

3601

r3 = 1

43

add r3, r2, r3

069c

loop starts here: r3 = r2 + r3

44

sub r2, r3, r2

04d5

r2 = r3 – r2

45

addi r1, r1, -1

427f

r1 = r1 – 1

46

bne r1, r0, -3

723d

branch backward to add if (r1 != 0)

47

jrr7

11c6

return to caller

48

sub r2, r2, r2

0495

be skipped, and r2 not modified



Teamwork:

Below are the subtasks of the project and the percentage of contribution of each group member in the task.

Task

KHALID ALSAIF

Mohammed Al Elaiw

Turkey Alanazi



Create the register file 

100%

0%

0%

Create ALU&ALU control

60%

30%

20%

Create the main control 

60%

20%

30%

Create the PC control 

50%

25%

25%

Add the component together and complete the data path

100%

0%

0%

Add required registers for pipelined CPU

70%

20%

10%

Solve structural hazard

80%

10%

10%

Solve data hazard

100%

0%

0%

Solve control hazard

70%

10%

20%

Report

60%

20%

20%























Snapshots:

















Register fileRegister file

Register file

Register file





























ALU ALU 

ALU 

ALU 





























PC controlPC control

PC control

PC control



































main controlmain control

main control

main control































Hazard detect & forwardHazard detect & forward

Hazard detect & forward

Hazard detect & forward