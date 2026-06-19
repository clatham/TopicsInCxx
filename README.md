# Topics in C/C++

## Overview

My goal is to explain sometimes complex topics in C/C++ in the simplest terms possible, so newcomers
can understand them.  The easier it is to find your footing in a language, the quicker you will
discover your love for it.

I'm not a professional teacher, so please be understanding of any errors you find.  Either post a
comment, or put up a pull request with a correction.


## Contents

[Truly understanding pointers](#truly-understanding-pointers)


## Truly understanding pointers

So many people struggle with pointers, and I'm convinced that many working programmers only vaguely
understand them.  When I was in college, half of the class dropped the week we learned pointers.  Lets try
to gain a clear understanding of what pointers are, and in the process, we'll clear up what references are
too.

You've read that a pointer is a variable that stores the address of another variable.  That is true, but
it can be difficult to really visualize what that means.  Instead of using metaphors and descriptions, lets
try to understand how the compiler sees variables, including pointers and references.

The compiler builds a "symbol table" that holds the name and location of every variable and function it encounters.
When code references one of the symbols, it consults the symbol table, so it can substitute the actual location
into the instruction that it generates.  For our purposes, we don't need to do *exactly* what the compiler does.
We'll ignore functions and any other fields that won't help our understanding.  Let's store the memory address,
name, type, length, and value for each variable in our symbol table as we go through a small piece of code.  I'll
present the updated table to you after each section of code.

First, let's add a couple of typical variables: an integer, and a double.  We'll arbitrarily decide that the storage
for our variables begins at address 0xC0000000.
```
int a = 3;
double b;
```
Our table should now look like this:
| Address | Name | Type | Length | Value |
| :--- | :--- | :--- | :--- | :--- |
| C0000000 | a | int | 4 | 3 |
| C0000004 | b | double | 8 | ? |
Note that "b" is stored four bytes after "a", because that is the length of an integer in the LLP64 data model that
we're using.  Because "b" was not assigned a value at construction, we don't know what value it has.  It will contain
whatever was in memory at that location when the program runs.

