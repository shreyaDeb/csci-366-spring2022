# Little Man Stack Machine

The year is 1970, and, for the last five years, the [Little Man Computer](https://en.wikipedia.org/wiki/Little_man_computer) 
has been the dominant system in the burgeoning computer revolution.  The chip that the computer uses, the x80 Trix, 
produced by Mintel, however, is starting to show its age.  Worse, a 
competitor, United Fruit Computing, has just released a new chip, the eM-Uno, which puts Mintel's Little Man
Computer industry dominance at risk.  Unacceptable.

Mintel has decided to create a new, more advanced chip, the hAPY 243, which:

* Supports a much larger set of instructions
* Includes support for the concept of a stack, rather than a simple accumulator

This new, advanced architecture will allow LMC programmers to write much more compact code and even use a new-fangled
technology called "function calls".

This new chip will support a machine called The Little Man Stack Machine(tm): A Revolution in Little Man Computing(c).

The only problem is, Mintel is having a tough time actually producing the chip.  They announced it last year in a bit
of a panic due to the em-Uno, but so far no one has seen this new chip.  Mintel needs help, and fast.

## The LMSM Achitecture

The LMSM architecture is backwards compatible with the LMC.  All instructions that work on LMC will work on LMSM:

| code | assembly | instruction | description |
|------|----------|-------------|-------------|
|1xx|ADD|Add| Add the value stored in location xx to the current value in the accumulator
|2xx|SUB|Subtract| Subtract the value stored in location xx to the current value in the accumulator
|3xx|STA|Store| Store the value in the accumulator to the location xx
|5xx|LDA|Load| Load the value from location xx into the accumulator
|6xx|BRA|Branch| Unconditionally branch to the instruction at the location xx
|7xx|BRZ|Branch if zero| Branch to the instruction at the location xx if the accumulators value is zero
|8xx|BRP|Branch if positive| Branch to the instruction at the location xx if the accumulators value is positive
|901|INP|Input| Get numeric input from the user
|902|OUT|Output| Print the current value of the accumulator
|000|HALT, COB|Halt| Stop the computer
|   |*DAT*|Data| Assembler instruction to simply write the raw value following this declaration to memory

As with the LMC, LMSM assembly may use labels to reference particular parts of memory.

Any valid LMC program should also be a valid LMSM program.

In addition to these standard LMC instructions, a new instruction has been added to the standard set:

| code | assembly | instruction | description |
|------|----------|-------------|-------------|
|4xx|LDI|Load Immediate| Loads the value xx into the accumulator directly

This instruction allows programs to load a value "immediately" into the accumulator.

### Capped Values

As with the original Little Man Computer, the values stored in memory locations and in the accumulator should be capped
between -999 and 999.

### The LMSM Accumulator

The biggest difference between the x80 Trix and the hAPY 243, is that the hAPY 243 chip supports a more advanced
accumulator, which can act as a stack.  This means, rather than storing a single value, the accumulator can store
a stack of values, with the value "on top" being the current value of the accumulator.

#### Stack Instructions

With this new accumulator architecture comes a slew of new instructions:

| code | assembly | instruction | description |
|------|----------|-------------|-------------|
|920|SPUSH|Stack push| Pushes a new `0` onto the top of the stack
|921|SPOP|Stack pop| Pops the current value off the stack
|922|SDUP|Stack duplicate| Duplicates the current value on the top of the stack
|923|SADD|Stack add| Pops the top two values on the stack, adds them and pushes the result onto the stack 
|924|SSUB|Stack subtract| Pops the top and second values on the stack, subtracts the top from the second, and pushes the result onto the stack 
|925|SMAX|Stack maxiumum| Pops the top two values of the stack and pushes the maximum value of the two back onto the stack
|926|SMIN|Stack minimum| Pops the top two values of the stack and pushes the minimum value of the two back onto the stack
|927|SMUL|Stack multiply| Pops the top two values of the stack,  multiplies them and pushes the result onto the stack
|928|SDIV|Stack divide| Pops the top and second values on the stack, divides second value by the top value, and pushes the result onto the stack 

In addition to these instructions, the following virtual instruction is available:

| code | assembly | instruction | description |
|------|----------|-------------|-------------|
| |*SPUSHI*|Stack push immediate| Pushes a new immediate onto the top of the stack

This instruction will compile down to the following to actual instructions in the following manner:

```
00:  SPUSHY 22
```

becomes

```
00:  SPUSH 
01:  LDI 22
```

The assembler will need to take this two-slot instruction into account when creating machine code.

Note this command is pronounced in such a way that it rhymes with "Sushi"

#### Function Call Instructions

In addition to the new stack-based accumulator, Mintel has jumped on the "function call" bandwagon and has added hardware
support for this new fangled idea.  To support function calls, Mintel has added another register, The Call Stack, that 
is used to call and return from functions.

This register is "hidden" from normal programmers, and can only be accessed by two special instructions:

| code | assembly | instruction | description |
|------|----------|-------------|-------------|
|910|CALL|Call| Jumps to the function address on the stack, pushing the next instruction onto The Call Stack
|911|RET|Return| Jumps to the memory location on the top of The Call Stack, consuming it

The calling conventions for LMSM are as follows:

* Arguments are passed to the function via the normal accumulator stack
* When a function returns, the value it produced will be on the top of the stack

Note that at the assembly level, these are all synthetic commands because you must push the address to jump to onto
the stack, then issue the machine command.

So the following assembly:
```
CALL EXIT
LDI 22
OUT
EXIT HLT
```
will compile to the following machine code:
```
SPUSH
LDI 5
CALL
LDI 22
OUT
HLT
```
with the `CALL EXIT` turning into both a `LDI 5` (the location of the `EXIT` tag) and `CALL` machine instruction

## Gallatin Computing Inc.

Your company, Gallatin Computing Inc., has been chosen by Mintel to write an *emulator* for the new LMSM chip.  Your
emulator will be used by developers who want to write programs for the new LMSM architecture before it is generally
available in silicon.

The emulator will have the following functionality:

* The ability to assemble the high level LMSM instructions into LMSM machine code
  * including label support
* The ability to load LMSM programs into an emulated system
* The ability to run and/or step through the execution of a program
* The ability to inspect the state of the system
* The ability to execute instructions directly
* The ability to manipulate the system directly

You have been provided with a test suite to assist you in your quest.

Good luck!