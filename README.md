# Topics in C/C++

## Overview

My goal is to explain complex topics in C/C++ in the simplest terms possible, so newcomers
can understand them.  The easier it is to find your footing in a language, the quicker you will
discover your love for it.

I'm not a professional teacher, so please be understanding of any errors you find.  Either create an issue,
or put up a pull request with a correction.


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
| 0xC0000000 | a | int | 4 | 3 |
| 0xC0000004 | b | double | 8 | ? |

Note that "b" is stored four bytes after "a", because that is the length of an integer in the LLP64 data model that
we're using.  Because "b" was not assigned a value at construction, we don't know what value it has.  It will contain
whatever was in memory at that location when the program runs.

Next, let's add a pointer-to-integer to our code, and we'll assign it the address of "a" using the address-of operator (&).
```
int a = 3;
double b;
int *c = &a;
```
Our table should now look like this:
| Address | Name | Type | Length | Value |
| :--- | :--- | :--- | :--- | :--- |
| 0xC0000000 | a | int | 4 | 3 |
| 0xC0000004 | b | double | 8 | ? |
| 0xC000000C | c | int * | 8 | 0xC0000000 |

Note that "c" is 64-bits in length, because that's how long all pointers are in the LLP64 data model.  Its value is the
address of "a", because a pointer is a variable that stores the address of a variable.

What happens if we indirect "c" and assign something to it?  Let's find out by modifying our code.
```
int a = 3;
double b;
int *c = &a;
*c = 10;
```
Our table should now look like this:
| Address | Name | Type | Length | Value |
| :--- | :--- | :--- | :--- | :--- |
| 0xC0000000 | a | int | 4 | 10 |
| 0xC0000004 | b | double | 8 | ? |
| 0xC000000C | c | int * | 8 | 0xC0000000 |

Note that only the value of "a" changed.  Instead of storing the value at the address of "c", the computer used the value
it holds as the actual destination address.  This is the a-ha moment, so read this last step again and again until you
get it.

Lets add a pointer-to-double to our code, and we'll make it point at "b".  Try to figure out the value for each field of
this symbol before looking ahead.
```
int a = 3;
double b;
int *c = &a;
*c = 10;
double *d = &b;
```
Our table should now look like this:
| Address | Name | Type | Length | Value |
| :--- | :--- | :--- | :--- | :--- |
| 0xC0000000 | a | int | 4 | 10 |
| 0xC0000004 | b | double | 8 | ? |
| 0xC000000C | c | int * | 8 | 0xC0000000 |
| 0xC0000014 | d | double * | 8 | 0xC0000004 |

Hopefully, you already knew what would be added to the table.  Note that the size of a pointer is always 64-bits regardless
of the size of the variable to which it points.

Finally, let's add a reference to our code.  Remember that a reference is basically another name for an existing variable.
The changes to the symbol table should make this exceedingly clear.
```
int a = 3;
double b;
int *c = &a;
*c = 10;
double *d = &b;
int& e = a;
```
Our table should now look like this:
| Address | Name | Type | Length | Value |
| :--- | :--- | :--- | :--- | :--- |
| 0xC0000000 | a | int | 4 | 10 |
| 0xC0000004 | b | double | 8 | ? |
| 0xC000000C | c | int * | 8 | 0xC0000000 |
| 0xC0000014 | d | double * | 8 | 0xC0000004 |
| 0xC0000000 | e | int | 4 | 10 |

Note that "e" has the same address as "a".  That's because "e" is *not* a new variable.  It is simply a new symbol for the
"a" variable.  This explains why a reference must *always* be assigned at construction, while a pointer does not need to be.
In reality, the symbol table would not contain the values, as I have depicted it here.  The computer would simply look at
the associated address in memory to find the value it contains.  This was added to help illustrate the value contained in a
pointer.

Hopefully, you now understand how pointers and references work in C/C++.
