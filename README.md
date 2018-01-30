#Compilers Independent Study

[TOC]

##Good resources

- [LLVM by Chris Lattner](http://www.aosabook.org/en/llvm.html)

##Questions to be answered

### How do we build and run LLVM?

 - [How to get started](https://llvm.org/docs/GettingStarted.html) link contains a list of source directories that have to be checked out prior building LLVM.
 - LLVM uses Cmake to build the project
 - LLVM is installed on Tesla/Hydra machines
 - There is a problem with CMake on Yosemite OSX
 - Installs on Sierra OSX

### How do we compile programs with LLVM?

This example is also taken form [How to get started](https://llvm.org/docs/GettingStarted.html) 
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

[Writing LLVM Pass](http://llvm.org/docs/WritingAnLLVMPass.html) link is available.

- Pass is an operation on a unit of IR in LLVM
	- Could mutate IR
	- Could compute something about IR
- Units of IR
 	- Modules
 	- Functions
 	- Basic Blocks
 	- Instructions
 

All LLVM passes are subclasses of the Pass class, which implement functionality by overriding virtual methods inherited from Pass. Depending on how your pass works, you should inherit from the ModulePass , CallGraphSCCPass, FunctionPass , or LoopPass, or RegionPass, or BasicBlockPass classes, which gives the system more information about what your pass does, and how it can be combined with other passes.



### Write a simple compiler pass to replace calls to printf with my_printf in LLVM.

Had no oportunity due to problems with installation.

### How do we use LLVM to emit the LLVM IR (-emit-llvm)?
	
	`clang -O3 -emit-llvm hello.c -c -o hello.bc`

### What other options for printing intermediate code does LLVM come with?

LLVM forms of IR: 

 1. A human-readable assembly format (.ll) (clang -Os -S -emit-llvm sample.c -o sample.ll)
```
define i32 @add1(i32 %a, i32 %b) {
entry:
  	%tmp1 = add i32 %a, %b
  	ret i32 %tmp1
}

define i32 @add2(i32 %a, i32 %b) {
entry:
  	%tmp1 = icmp eq i32 %a, 0
  	br i1 %tmp1, label %done, label %recurse

recurse:
  	%tmp2 = sub i32 %a, 1
  	%tmp3 = add i32 %b, 1
  	%tmp4 = call i32 @add2(i32 %tmp2, i32 %tmp3)
  	ret i32 %tmp4

done:
  	ret i32 %b
}
```

	That corresponds to:
```
unsigned add1(unsigned a, unsigned b) {
  return a+b;
}

// Perhaps not the most efficient way to add two numbers.
unsigned add2(unsigned a, unsigned b) {
  if (a == 0) return b;
  return add2(a-1, b+1);
}
```
 2. A C++ object format suitable for frontends 
 3. A dense bitcode format for serializing (.bc) 
```
 ; ModuleID = '<stdin>'
target datalayout = "e-p:64:64:64-i1:8:8-i8:8:8-i16:16:16-i32:32:32-i64:64:64-f32:32:32-f64:64:64-v64:64:64-v128:128:128-a0:0:64-s0:64:64-
target triple = "x86_64-redhat-linux-gnu"

@str = private unnamed_addr constant [12 x i8] c"hello world\00"

; Function Attrs: nounwind uwtable
define i32 @main() #0 {
  %puts = tail call i32 @puts(i8* getelementptr inbounds ([12 x i8]* @str, i64 0, i64 0))
  ret i32 0
}

; Function Attrs: nounwind
declare i32 @puts(i8* nocapture readonly) #1

attributes #0 = { nounwind uwtable "less-precise-fpmad"="false" "no-frame-pointer-elim"="false" "no-infs-fp-math"="false" "no-nans-fp-math
attributes #1 = { nounwind }

!llvm.ident = !{!0}

!0 = metadata !{metadata !"clang version 3.4.2 (tags/RELEASE_34/dot2-final)"}
```
That corresponds to:
```
#include <stdio.h>

int main() {
    printf("hello world\n");
    return 0;
}
```

### Can we use LLVM to present a 'stripped down' compiler to students?
 - That is, a compiler that only parses the input program into an abstract syntax tree.
 - Then we use the compiler pass system to generate intermediate code.