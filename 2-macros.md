# Stack Programming and Assembly Language

[back](./)

## Tools of the trade: Macros

An early purchase in my programming career was a manual for the Intel 8080 Assembler https://altairclone.com/downloads/manuals/8080%20Programmers%20Manual.pdf which I think I bought at my local Tandy shop. I couldn't afford the actual software let alone the machine to run it on so assembly language for a long time remained a goal rather than a reality. I had to hand assemble all of my first programs but that was OK because they would only exist on paper for many years. I would not own a computer until I built my own in 1981 or so which Ken Stone reminds me I called the "Onion" computer (if that's so then maybe the TEC-1 could be considered the Onion ][ ;-)

Anyway, one of the most mysterious chapters of this book for me was the one on macros. The word "macro" up to that point had been associated in my mind with "macro photography". I had no idea what macro meant in this context but gradually I came to understand that a macro was kind of like a subroutine except that it wasn't a subroutine that ran on the microprocessor but inside the assembler itself. Any repeated block of code could be put into a macro but instead of it being called like a normal subroutine, it would be expanded in place. Using macros unlike subroutines wasn't going to make my programs shorter. A careless use of them would make my program longer! So what use were they? That's a good question.

Another thing is that macros allow you to do with them is parameterise them. Parameters change the code which gets expanded inline. This means that macros effectively enable you to add new commands to your assembly language and to express your code in a more structured way. Macros let you write code about your code. It's a kind of meta-programming which is really as cool and esoteric as it sounds.

When should you use macros? It's a trade off. You should use them when you want the speed of inline execution in preference to the overhead of making a subroutine call. This overhead is certainly not zero on a Z80, a call takes 17 t cycles while a return takes 10 t cycles (a software interrupt is faster and takes 11 t cycles but you can't choose the address). A subroutine call consumes 3 bytes when assembled while a macro expansion can be anything from zero upwards. If the expanded macro ends up being a lot more than 3 bytes and you use it all over your code then you might still prefer to use a subroutine.

Here's a simple example of a useful macro: a 16-bit comparison with zero operation. Using ASM80

---

.macro isZero, hireg, loreg  
 ld A, loreg  
 or hireg  
.endm

---

in TASM I believe it would be

---

isZero MACRO hireg, loreg
ld A, loreg  
 or hireg  
ENDM

---

The macro's name is isZero and it sets the zero flag if the two registers it checks are zero. For example if you wanted to know if DE contained zero you could just do something like:

---

ld DE,1
dec DE
isZero D,E
jr z, exit

---

This expands to

---

ld DE,1
dec DE
;\*Macro unroll: isZero
ld A, E  
or D  
jr z, exit

---

Macros can call other macros so you could create a specialised version of isZero e.g.

---

.macro isHLZero  
 isZero H,L
.endm

---

## Using isHLZero in your code will always expand to

;*Macro unroll: isHLZero
;*Macro unroll: isZero
ld A, L ; 4t
or H ; 4t

---

Because macros work at the source level you need to be aware of some gotchas. If you have parameters, the names you use for them get substituted wherever they appear in you macro body. For example:

---

.macro swapper, x, y
ld DE,x
ld HL,y
ex DE,HL
.endm

---

if you used it like this
swapper 1,2
would expand to

---

;\*Macro unroll: swapper
ld DE,1
ld HL,1
e1 DE,HL

---

In ASM80 you will get an error:
Unrecognized instruction E1 Line: 3

So be careful naming your parameters! That said you can see that this is an extremely powerful feature for rewriting your code. Just don't forget that you are not writing functions here. Macros are a source level expansion.

## If that's too foot-gunny for you in ASM80 you can skip formal parameters and use numbered parameters e.g.

.macro swapper
ld DE,%%1
ld HL,%%2
ex DE,HL
.endm

---

A related issue has to do with jumps inside macros. Obviously you need jumps to perform any kind of loop or conditional code. However jumps in assembly need labels and labels need to be unique. Every macro system has a way of making names which are local to each use of your macro. In ASM80 you use %%M. I'm just going to borrow this example straight from the ASM80 manual:

---

.macro xyz
loop%%M:
inc a
dec b
jr nz,loop%%M
.endm

---

## If you use the macro a few times

xyz
xyz
xyz

---

## it will expand as:

;*Macro unroll: xyz
LOOPM_1576S96:  
INC a  
DEC b  
JR nz,loopM_1576S96  
;*Macro unroll: xyz
LOOPM_1577S97:  
INC a  
DEC b  
JR nz,loopM_1577S97  
;\*Macro unroll: xyz
LOOPM_1578S98:  
INC a  
DEC b  
JR nz,loopM_1578S98

---

Note that while all these expansions are kind of ugly, you don't have to look at them except in your .LST output file. Understanding how they work though will certainly aid in using them effectively.

As I have tried to convey. I think macros are pretty awesome and you can use them to save a lot of work. They can also help you write your way out of low-level programming into something more structured and high level. That's a direction I'm hoping to build to with this series of articles.
