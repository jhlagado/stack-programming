# Stack programming and assembly language

## Introduction

It's unfortunate that there are so few standards for assemblers (or perhaps it's more appropriate to say there are too many, one for each assembler!) 

For example every assembler has a slightly different approach to declaring macros, a different but similar way of declaring literal blocks of data, of declaring strings or making sections of code conditional. 

Luckily though most assemblers have a way of achieving each of these goals in their own quirky way. I don't think this limitation should prevent us from talking about advanced assembly techniques especially as they relate to programming the Z80 and the TEC-1 in particular. 

No matter what assembler you use, TASM, MACRO80 or ASM80 etc. etc. http://www.z80.info/z80sdt.htm you are sitting on a more powerful programming system than you probably realise. Assembly language is raw and as close to the metal as most of us would ever want to go but they are the foundation systems of nearly all programming. You can therefore do a lot with them.

I thought I would run a series of posts on what I've been doing in assembly language and using it to write my way into a higher level programming language. 

Most of you already know that I have been interested in the Forth programming language for quite a while. Naturally therefore Forth techniques of one sort or another are going to feature heavily in this series. https://sites.google.com/view/forth-books/home/forth-books?authuser=0 That said, I think that these ways of solving problems are generally useful to assembly language programmers and may give others an alternative insight into what can be achieved with these tools. 

In this series I will be using https://www.asm80.com/ as my assembler and debugger. I may also discuss TASM as a point of comparison.