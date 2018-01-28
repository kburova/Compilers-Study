#Compilers Independent Study

[TOC]

##Questions to be answered
### How do we build and run LLVM?
 - [How to get started](https://llvm.org/docs/GettingStarted.html) link contains a list of source directories that have to be checked out prior building LLVM.
 - LLVM uses Cmake to build the project
 - MacOSX has LLVM/Clang installed by default

### How do we compile programs with LLVM?
- Compile the C file into a native executable:
	`clang hello.c -o hello`
- Compile the C file into an LLVM bitcode file:
	`clang -O3 -emit-llvm hello.c -c -o hello.bc`
- Run the program in both forms. To run the program, use:
	`./hello`
  and
  	`lli hello.bc`
- Use the llvm-dis utility to take a look at the LLVM assembly code:
	`llvm-dis < hello.bc | less`
- Compile the program to native assembly using the LLC code generator:
	`llc hello.bc -o hello.s` 
- Assemble the native assembly language file into a program:
 	`gcc hello.s -o hello.native`
- Execute the native code program:
	`./hello.native`
### How do we write a compiler pass with LLVM?
### Write a simple compiler pass to replace calls to printf with my_printf in LLVM.
### How do we use LLVM to emit the LLVM IR (-emit-llvm)?
### What other options for printing intermediate code does LLVM come with?
### Can we use LLVM to present a 'stripped down' compiler to students?
 - That is, a compiler that only parses the input program into an abstract syntax tree.
 - Then we use the compiler pass system to generate intermediate code.