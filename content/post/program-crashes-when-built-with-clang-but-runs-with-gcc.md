---
title: "Program crashes when built with Clang but runs when built with GCC"
date: 2022-08-25T23:51:48+05:30
lastmod: 2022-08-25T23:51:48+05:30
draft: false 
keywords: [gcc clang "undefined behaviour" ub2]
description: ""
tags: [debugging]
categories: [linux]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true 
toc: true 
autoCollapseToc: false
postMetaInFooter: true 
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams: 
  enable: false
  options: ""

---
Just how wild can a simple GCD program get?
<!--more-->
I wrote a stupid gcd program that returns, well, GCD of two numbers. Funny enough, the program runs fine when compiled with gcc but crashes with `illegal hardware instruction` when compiled with clang.
## My tools
Here is the program: 
```C++
#include <iostream>

size_t gcd(size_t dividend, size_t divisor) {
  if (divisor > dividend) std::swap(divisor, dividend);
  auto remainder = dividend % divisor;
  if (remainder == 0) return divisor;
  else gcd(divisor,remainder);
}

int main(int argc, char** argv) {
  auto dividend {std::stoul(argv[1])};
  auto divisor {std::stoul(argv[2])};
  std::cout << gcd(dividend, divisor) << std::endl;
  return 0;
}
```
Some more relevant info:
```Bash
gcc --version                                                  
gcc (GCC) 12.2.0
[...]
```
```Bash
clang --version                                                        
clang version 14.0.6
Target: x86_64-pc-linux-gnu
Thread model: posix
InstalledDir: /usr/bin
```

```Bash
yay -Qi glibc          
Name            : glibc
Version         : 2.36-3
```

## Getting started
Compile it with gcc:
```
g++ -W -Wall -Wextra -pedantic -std=c++20 -g -o gcd gcd-gcc.cpp 
gcd-gcc.cpp: In function ‘int main(int, char**)’:
gcd-gcc.cpp:10:14: warning: unused parameter ‘argc’ [-Wunused-parameter]
   10 | int main(int argc, char** argv) {
      |          ~~~~^~~~
gcd-gcc.cpp: In function ‘size_t gcd(size_t, size_t)’:
gcd-gcc.cpp:8:1: warning: control reaches end of non-void function [-Wreturn-type]
    8 | }
      | ^
```

Compile it with clang:
```
clang++ -Weverything -Wno-c++98-compat -std=c++20 -g -o gcd gcd-new.cpp 
gcd-new.cpp:3:8: warning: no previous prototype for function 'gcd' [-Wmissing-prototypes]
size_t gcd(size_t dividend, size_t divisor) {
       ^
gcd-new.cpp:3:1: note: declare 'static' if the function is not intended to be used outside of this translation unit
size_t gcd(size_t dividend, size_t divisor) {
^
static 
gcd-new.cpp:8:1: warning: non-void function does not return a value in all control paths [-Wreturn-type]
}
^
gcd-new.cpp:10:14: warning: unused parameter 'argc' [-Wunused-parameter]
int main(int argc, char** argv) {
             ^
3 warnings generated.
```

So far everything seems good. 
Now running gcc's version gives
```
./gcd 70 60
10
```
...which is expected.

And clang's version:
```
./gcd 70 60
[1]    12974 illegal hardware instruction  ./gcd 70 60
```
There it is! Let's dig in.
```
lldb ./gcd
(lldb) target create "./gcd"
Current executable set to '/tmp/gcd' (x86_64).
(lldb) r 70 60
Process 13207 launched: '/tmp/gcd' (x86_64)
Process 13207 stopped
* thread #1, name = 'gcd', stop reason = signal SIGILL: illegal instruction operand
    frame #0: 0x0000555555556368 gcd`gcd(dividend=70, divisor=60) at gcd-new.cpp:6:20
   3    size_t gcd(size_t dividend, size_t divisor) {
   4      if (divisor > dividend) std::swap(divisor, dividend);
   5      auto remainder = dividend % divisor;
-> 6      if (remainder == 0) return divisor;
   7      else gcd(divisor,remainder);
   8    }
   9   
(lldb) 
```
Stop reason says `signal SIGILL: illegal instruction operand`.

Illegal instruction operand? `dividend` is of type `size_t`, `divisor` is of type `size_t`. The modulus operator, in this case, will always return an integer greater than 0. Considering the above cases, the compiler should assign type `size_t` to `remainder`. Let's verify this.
```
(lldb) frame variable remainder
(unsigned long) remainder = 10
(lldb) 
```
Turns out `remainder` is of type `unsigned long`, which should be fine in this case since comparison  between unsigned long and a literal 0 is perfectly legal. If my memory serves right a literal 0 will be treated as a decimal of type `int`. Comparing it with type `unsigned long` would promote it to `unsigned long` and result in potential data loss of the signed bit. I think I am able to see the problem. The compiler would definitely deny such implicit type conversions. 

Okay let's try changing it's data type to `long`.
```
diff gcd-gcc.cpp gcd-new.cpp 
5c5
<   auto remainder = dividend % divisor;
---
>   long remainder = dividend % divisor;
```
Now we get a message from the compiler:
```
gcd-new.cpp:5:29: warning: implicit conversion changes signedness: 'unsigned long' to 'long' [-Wsign-conversion]
  long remainder = dividend % divisor;
       ~~~~~~~~~   ~~~~~~~~~^~~~~~~~~

```
Now we can be sure `dividend` and `divisor` will be of type `long` in this line of code.

Let's try running.
```
./gcd 70 60
[1]    13887 illegal hardware instruction  ./gcd 70 60
```
It crashes again.
```
lldb ./gcd
(lldb) target create "./gcd"
Current executable set to '/tmp/gcd' (x86_64).
(lldb) r 70 60
Process 13927 launched: '/tmp/gcd' (x86_64)
Process 13927 stopped
* thread #1, name = 'gcd', stop reason = signal SIGILL: illegal instruction operand
    frame #0: 0x0000555555556368 gcd`gcd(dividend=70, divisor=60) at gcd-new.cpp:6:20
   3    size_t gcd(size_t dividend, size_t divisor) {
   4      if (divisor > dividend) std::swap(divisor, dividend);
   5      long remainder = dividend % divisor;
-> 6      if (remainder == 0) return divisor;
   7      else gcd(divisor,remainder);
   8    }
   9   
(lldb) 
```
And the reason remains the same. Let's revert the changes and debug again. 

Checking all variables reveal
```
(lldb) frame variable
(size_t) dividend = 70
(size_t) divisor = 60
(unsigned long) remainder = 10
(lldb) 
```
Everything is normal here. So what causes the comparison of `remainder` with 0 raise `SIGILL`?

## Going deeper
Let's examine the assembly file.
```
  118 .Ltmp6:
  119     .loc    0 6 17 is_stmt 1                # gcd-new.cpp:6:17
  120     cmpq    $0, -32(%rbp)
  121 .Ltmp7:
  122     .loc    0 6 7 is_stmt 0                 # gcd-new.cpp:6:7
  123     jne .LBB1_5
  124 # %bb.3:
  125 .Ltmp8:
  126     .loc    0 6 30                          # gcd-new.cpp:6:30
  127     movq    -24(%rbp), %rax
  128     movq    %rax, -40(%rbp)                 # 8-byte Spill
  129     movq    %fs:40, %rax
  130     movq    -8(%rbp), %rcx
  131     cmpq    %rcx, %rax
  132     jne .LBB1_7
  133 # %bb.4:
  134     .loc    0 0 30                          # gcd-new.cpp:0:30
  135     movq    -40(%rbp), %rax                 # 8-byte Reload
  136     .loc    0 6 23                          # gcd-new.cpp:6:23
  137     addq    $48, %rsp
  138     popq    %rbp
  139     .cfi_def_cfa %rsp, 8
  140     retq
```
This is the entire code for line 6 in the C++ source file.

Here in line 120 is the code for the equality check. Line 123 tells the compiler to jump to `else` part if the comparison is not equal (section `LBB1_5 ` contains code for `else` part). And a lot of stuff happening there like code to prevent buffer overflow (lines 129 - 132).

In short, I know what is happening but not enough to tell what is breaking. Maybe I'll just have lldb dump the assembly code of the function. It will probably be the same as what we already saw.
```
(lldb) disas -n gcd
gcd`gcd:
    0x5555555562e0 <+0>:   pushq  %rbp
    0x5555555562e1 <+1>:   movq   %rsp, %rbp
    0x5555555562e4 <+4>:   subq   $0x30, %rsp
    0x5555555562e8 <+8>:   movq   %fs:0x28, %rax
    0x5555555562f1 <+17>:  movq   %rax, -0x8(%rbp)
    0x5555555562f5 <+21>:  movq   %rdi, -0x10(%rbp)
    0x5555555562f9 <+25>:  movq   %rsi, -0x18(%rbp)
    0x5555555562fd <+29>:  movq   -0x18(%rbp), %rax
    0x555555556301 <+33>:  cmpq   -0x10(%rbp), %rax
    0x555555556305 <+37>:  jbe    0x555555556318            ; <+56> at gcd-new.cpp:5:20
    0x55555555630b <+43>:  leaq   -0x18(%rbp), %rdi
    0x55555555630f <+47>:  leaq   -0x10(%rbp), %rsi
    0x555555556313 <+51>:  callq  0x555555556550            ; std::swap<unsigned long> at move.h:199
    0x555555556318 <+56>:  movq   -0x10(%rbp), %rax
    0x55555555631c <+60>:  xorl   %ecx, %ecx
    0x55555555631e <+62>:  movl   %ecx, %edx
    0x555555556320 <+64>:  divq   -0x18(%rbp)
    0x555555556324 <+68>:  movq   %rdx, -0x20(%rbp)
    0x555555556328 <+72>:  cmpq   $0x0, -0x20(%rbp)
    0x55555555632d <+77>:  jne    0x55555555635b            ; <+123> at gcd-new.cpp:7:12
    0x555555556333 <+83>:  movq   -0x18(%rbp), %rax
    0x555555556337 <+87>:  movq   %rax, -0x28(%rbp)
    0x55555555633b <+91>:  movq   %fs:0x28, %rax
    0x555555556344 <+100>: movq   -0x8(%rbp), %rcx
    0x555555556348 <+104>: cmpq   %rcx, %rax
    0x55555555634b <+107>: jne    0x55555555636a            ; <+138> at gcd-new.cpp
    0x555555556351 <+113>: movq   -0x28(%rbp), %rax
    0x555555556355 <+117>: addq   $0x30, %rsp
    0x555555556359 <+121>: popq   %rbp
    0x55555555635a <+122>: retq   
    0x55555555635b <+123>: movq   -0x18(%rbp), %rdi
    0x55555555635f <+127>: movq   -0x20(%rbp), %rsi
    0x555555556363 <+131>: callq  0x5555555562e0            ; <+0> at gcd-new.cpp:3
->  0x555555556368 <+136>: ud2    
    0x55555555636a <+138>: callq  0x555555556140            ; symbol stub for: __stack_chk_fail
(lldb) 
```
...and there we go. It seems more or less the same except the second last line. The arrow tells that is where things fell apart. Seems like things got a little bit wilder. 

Apparently, line 6 in the C++ source is undefined behaviour and LLVM decided to put `ub2` to crash the program instead of doing anything it could have (because it is undefined behaviour). 

Now this brings me to my next question: why does it work on gcc's version? Let's check out.
```
(lldb) disas -n gcd
gcd`gcd:
0x555555556289 <+0>:   pushq  %rbp
0x55555555628a <+1>:   movq   %rsp, %rbp
0x55555555628d <+4>:   subq   $0x20, %rsp
0x555555556291 <+8>:   movq   %rdi, -0x18(%rbp)
0x555555556295 <+12>:  movq   %rsi, -0x20(%rbp)
0x555555556299 <+16>:  movq   -0x20(%rbp), %rax
0x55555555629d <+20>:  movq   -0x18(%rbp), %rdx
0x5555555562a1 <+24>:  cmpq   %rax, %rdx
0x5555555562a4 <+27>:  jae    0x22b9                    ; <+48> at gcd-gcc.cpp:5:29
0x5555555562a6 <+29>:  leaq   -0x18(%rbp), %rdx
0x5555555562aa <+33>:  leaq   -0x20(%rbp), %rax
0x5555555562ae <+37>:  movq   %rdx, %rsi
0x5555555562b1 <+40>:  movq   %rax, %rdi
0x5555555562b4 <+43>:  callq  0x2911                    ; _ZSt4swapImENSt9enable_ifIXsrSt6__and_IJSt6__not_ISt15__is_tuple_likeIT_EESt21is_move_constructibleIS4_ESt18is_move_assignableIS4_EEE5valueEvE4typeERS4_SE_ at move.h:196:5
0x5555555562b9 <+48>:  movq   -0x18(%rbp), %rax
0x5555555562bd <+52>:  movq   -0x20(%rbp), %rcx
0x5555555562c1 <+56>:  movl   $0x0, %edx
0x5555555562c6 <+61>:  divq   %rcx
0x5555555562c9 <+64>:  movq   %rdx, -0x8(%rbp)
0x5555555562cd <+68>:  cmpq   $0x0, -0x8(%rbp)
0x5555555562d2 <+73>:  jne    0x22da                    ; <+81> at gcd-gcc.cpp:7:11
0x5555555562d4 <+75>:  movq   -0x20(%rbp), %rax
0x5555555562d8 <+79>:  jmp    0x22ed                    ; <+100> at gcd-gcc.cpp:8:1
0x5555555562da <+81>:  movq   -0x20(%rbp), %rax
0x5555555562de <+85>:  movq   -0x8(%rbp), %rdx
0x5555555562e2 <+89>:  movq   %rdx, %rsi
0x5555555562e5 <+92>:  movq   %rax, %rdi
0x5555555562e8 <+95>:  callq  0x2289                    ; <+0> at gcd-gcc.cpp:3:45
0x5555555562ed <+100>: leave  
0x5555555562ee <+101>: retq   
(lldb) 
```
The instructions look a little bit smaller and it looks like there is no stack canary? I am not sure. A few more things are different. Apart from that everything else seems similar.

## Lessons learnt
In the end I couldn't figure out what broke. I'll probably ask someone smarter.

## Wrapping up
While I definitely was unable to tell what broke, I enjoyed every bit of it. I learned a lot about compiler's behaviour and what looks like seemingly harmless code may just be very unsafe waiting for a disaster to unfold.
