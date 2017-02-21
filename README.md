# [Glypho - The linguistically independent programming language](https://web.archive.org/web/20060621185740/http://www4.ncsu.edu/~bcthomp2/glypho.txt)

**The materials in this repository were not produced by the owner of the repository. Please click on the link in the header to go to the original web site these materials were copied from. The code archive comes from [here](https://web.archive.org/web/20060618011057/http://rune.krokodille.com/lang/Glypho0.2.zip). The purpose of this copy is to automate setup of a mirror for https://tryitonline.net. It's more straight forward to clone a repository than to parse a web page.**

Created & Described by: Brian Thompson (Wildhalcyon) September 18, 2005

Updated: September 20, 2005 - Revised tutorial on symbol enumeration, added appendix on stirling numbers
Updated: October 3, 2005 - Implemented new operator e(xecute).

## Introduction

Consider a programming language that is not only independent of the symbols
used, but also independent of the symbols used from one instruction to the next.
At each instruction, the language must decipher which instruction the symbols
are attempting to represent. If we restrict the length of an instruction symbol
to be some fixed integer, n, it is possible to enumerate all the possible
combinations of symbols for each instruction. It turns out that the number of
possible symbol permutations of length n is equal to B_n - or the nth Bell
Number.

Glypho is based on glyphs (or symbols), and therefore glyph-oriented, hence the name Glyph-O(riented), but really its just Glypho.

## Symbol Enumeration

Now, consider how those possible symbol permutations can be made without
considering the underlying symbols themselves. We'll number the symbols by the order in which we see them, so the first symbol we see will be denoted as '1'.
Obviously, since the first symbol we see is the first symbol, every string
will start with `1`:

```Text
1
```

Consider the second symbol. This can either be the same as the first, in
which case we'll call it another `1`, or it can be a new, unseen symbol, `2`. So
every combination of `2` symbols can either be

- `11` - two of the first symbol together, or
- `12` - one symbol followed by a second, different symbol.

Things start to get tricky when looking at the third symbol, because you need to consider two cases: the case that you've only ever seen 1 symbol, or the
case that you've seen two different symbols. If only one symbol has been seen,
the next symbol can be either that same symbol, or a different symbol:

```Text
111
112
```

If two symbols have been seen already, then the third symbol could be either
the first symbol seen, the second symbol seen, or a new, unique symbol, `3`:

```Text
121
122
123
```

So the number of possible 3-symbol permutations is `2 + 3 = 5`

For the rest of this document, rather than using `{1 2 3 4 ...}` to denote
the nth unique symbol, we'll use `{a b c d...}`, but the concept remains exactly
the same. Appendix C of this document contains the list of symbol combinations
for length n = {1 ... 5} for reference, however the Glypho programming
language only uses the 15 combinations for length 4.

All sets of length n strings generated in this manner are finite languages,
and all finite languages are regular. A curious property of these languages is
that the language produced by reversing every string in the language is the
same language.

## The Stack

The stack in Glypho can have any number of elements on it at a given time.
Stack elements are signed-integers, any implementation of Glypho should use a
minimum of 32-bit signed integers, but higher numbers are valid as well. There
are several stack manipulation instructions in Glypho:

- dup == duplicates the element on the top of the stack
- swap == swaps the top two elements of the stack
- rot == pops the top element off the stack and pushes it onto the bottom
- rrot == pops the bottom element off the stack and pushes it onto the top
- pop == pops an element off of the stack and discards it
- push == pushes a 1 onto the top of the stack

Any actions which pop or otherwise manipulate the stack, except pushing, while
it is empty have an undefined behavior and are frowned upon at all times.

## Instruction Set

Glypho uses length-4 symbol strings for the instruction set, but higher and
lower order versions would be possible. With a 4-symbol string, Glypho can
encode a total of 15 instructions. The first instruction is aaaa - four of the
same symbol - which is used as a no-operation (NOP) instruction meaning "do
nothing". For the other instructions, Glypho actually does something useful.

In the spirit of being horribly confusing, some shorthand notation is given
for each of the Glypho symbols:

- n = NOP: Doesn't do a thing
- i = input: inputs and pushes new stack element
- o = output: pops and outputs top stack element
- d = dup: duplicates top stack element
- \\ = swap: swaps top two stack elements
- \> = rot: rotates stack (top stack element popped and pushed to bottom of stack)
- < = rrot: reverse-rotates stack (bottom element "popped", pushed to top)
- ! = pop: pops and discards stack element
- 1 = push: pushes a 1 onto the stack (generate a new stack element)
- \+ = add: pops two values from the stack, pushes their sum
- \- = negate: pops value from stack, pushes -(value)
- \* = multiply: pops two values from stack, pushes their product
- e = execute: pops four values from the stack and interprets as an instruction
- [ = l-brace: skip to matching r-brace if top element of stack is 0
- ] = r-brace: skip back to matching l-brace

Glyph|Symbol|Operation
-----|------|---------
aaaa |n     | NOP
aaab |i     | input
aaba |>     | rot
aabb |\     | swap
aabc |1     | push
abaa |<     | rrot
abab |d     | dup
abac |[     | l-brace
abba |+     | add
abbb |o     | output
abbc |*     | multiply
abca |-     | negate
abcb |]     | r-brace
abcc |!     | pop
abcd |e     | execute



## Acknowledgements

GS30NG: For the creation of Udage, the primary inspiration for Glypho.
Jix, Grim & Kipple: For helping to make Glypho Turing-Complete.
Kipple (again): The execute instruction



## Appendix A - Sample code

### Outputs "Hello" in Glypho (shorthand-notation)

`1d+d*dddd**++d1d+d*d*1d+*111++-++d1d+dd**1-++dd111+++11-+<[o<]!`

### Fibonacci Number Generator

`1ddoo[>d<d>+<\do]`

### Terminating Fibonacci Number Generator

`11#[>do<1-+][>>d<d>+<\do<1-+]` Where # is the number of terms to generate

### cat (EOF returns 0)

`i[oi]`

### The normal Fibonacci Number Generator in full-Glypho (separated by spaces for
easier reading of each instruction)

```Glypho
aabc abab abab abbb
abbb abac aaba abab
abaa abab aaba abba
abaa aabb abab abbb
abcb
```

### Various Integers

Int|Glypho
---|------
-1| `1-`
0 | `11-+`
1 | `1`
2 | `11+ , 1d+`
3 | `111++`
4 | `11+d* , 1d+d+`
5 | `11+d*1+`
6 | `11+dd++`
7 | `11+dd**1-+ , 11+dd++1+`
8 | `11+dd** , 1d+dd**`
9 | `111++d*`
A | `11+dd*1+*`
F | `1d+d*d*1-+`


## Appendix B - The Mutant Rabbit Sequence

How to calculate the number of words of length n that use k symbols (where
k is always <= n):

A rabbit of order 'n' has (n+1) offspring, n of which are order n, 1 of
which is a mutant of order n+1:

```Text
rabbit 1 -> 1 2
rabbit 2 -> 2 2 3
rabbit 3 -> 3 3 3 4
.
.
.
rabbit n -> n n n ... n n n (n+1)
            |-- n terms --|
```

If you start with just 1 rabbit, you get the sequence:

```Text
n1:1
n2:1 2
n3:1 2 2 2 3
n4:1 2 2 2 3 2 2 3 2 2 3 3 3 3 4
n5:1 2 2 2 3 2 2 3 2 2 3 3 3 3 4 2 2 3 2 2 3 3 3 3 4 2 2 3 2 2 3 3 3 3 4 3 3 3 4
    3 3 3 4 3 3 3 4 4 4 4 4 5
.
.
.
```

To find out how many words of length n use k symbols, compute the sum:

```Text
k
--
> # of i's in string n
--
i=1
```

Alternatively (but using the exact same approach)

```Text
k
--
>  S(n,i) , where S(n,k) are the Stirling Numbers of the second kind
--
i=1
```

## Appendix C - Length n Glypho strings, for n = {1 .. 5}

### Possible symbol combinations

```Text
length 1 :   1
length 2 :   2
length 3 :   5
length 4 :  15
length 5 :  52
length 6 : 203
```

### Length 1

```Text
1
a
```


### Length 2

```Text
2
aa
ab
```


### Length 3

```Text
5
aaa
aab
aba
abb
abc
```


### Length 4

```Text
10
aaaa
aaab
aaba
aabc
abaa
abab
abac
abba
abbb
abbc

14
abca
abcb
abcc
abcd
```


### Length 5

```Text
10
aaaaa
aaaab
aaaba
aaabb
aaabc
aabaa
aabab
aabac
aabba
aabbb

20
aabbc
aabca
aabcb
aabcc
aabcd
abaaa
abaab
abaac
ababa
ababb

30
ababc
abaca
abacb
abacc
abacd
abbaa
abbab
abbac
abbba
abbbb

40
abbbc
abbca
abbcb
abbcc
abbcd
abcaa
abcab
abcac
abcad
abcba

50
abcbb
abcbc
abcbd
abcca
abccb
abccc
abccd
abcda
abcdb
abcdc

52
abcdd
abcde
```
