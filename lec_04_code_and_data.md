---
title: "Code as Data, Data as Code"
filename: "lec_04_code_and_data"
chapternum: "5"
---

# Code as data, data as code { #codeanddatachap }



> # { .objectives }
* Understand one of the most important concepts in computing: duality between code and data. \
* Build up comfort in moving between different representations of programs. \
* Follow the construction of a "universal NAND-CIRC program" that can evaluate other NAND-CIRC programs given their representation. \
* See and understand the  proof of a major result that compliments the result of the last chapter: some functions require an _exponential_ number of gates to compute.
* Understand the _physical extended Church-Turing thesis_ that NAND-CIRC programs capture _all_ feasible computation in the physical world, and its physical and philosophical implications.




>_"The term code script is, of course, too narrow. The chromosomal structures are at the same time instrumental in bringing about the development they foreshadow. They are law-code and executive power - or, to use another simile, they are architect’s plan and builder’s craft - in one."_ ,  Erwin Schrödinger, 1944.


>_"A mathematician would hardly call a correspondence between the set of 64 triples of four units and a set of twenty other units, "universal", while such correspondence is, probably, the most fundamental general feature of life on Earth"_, Misha Gromov, 2013




A NAND-CIRC program can be thought of as simply a sequence of symbols, each of which can be encoded with zeros and ones using (for example) the ASCII standard.
Thus we can represent every NAND-CIRC program (and hence also every Boolean circuit) as a binary string.
This statement seems obvious but it is actually quite profound.
It means that we can treat circuits or  NAND-CIRC programs both as instructions to carrying computation and also as _data_ that could potentially be input to other computations.



::: { .bigidea #programisinput }
A _program_ is a piece of text, and so it can be fed as input to other programs.
:::

This correspondence between _code_ and _data_ is one of the most fundamental aspects of computing.
It  underlies  the notion of _general purpose_ computers, that are not pre-wired to compute only one task, and it is also the basis of our  hope for obtaining _general_ artificial intelligence.
This concept finds immense use in all areas of computing, from scripting languages to machine learning, but it is fair to say that we haven't yet fully mastered it.
Indeed many security exploits involve cases such as "buffer overflows" when attackers manage to inject code where the system expected only "passive" data (see [XKCDmomexploitsfig](){.ref}).
The idea of code as data reaches beyond the realm of electronic computers.
For example, DNA can be thought of as both a program and data (in the words of Schrödinger, who wrote  before DNA's discovery a book that inspired Watson and Crick, it is both "architect's plan and builder's craft").

![As illustrated in this xkcd cartoon, many exploits, including buffer overflow, SQL injections, and more, utilize the blurry line between "active programs" and "static strings".](../figure/exploits_of_a_mom.png){#XKCDmomexploitsfig .margin  }


## A NAND interpreter in NAND

Since we can represent programs as strings, we can also think of a program as an input to a function.
In particular, for every choice of a representation scheme for NAND-CIRC programs, the following is a well defined mathematical function:

$$
EVAL(P,x) = \begin{cases} P(x) & |x|= \text{no. of $P$'s inputs} \\ 0 & \text{otherwise} \end{cases}
$$
where $P$ and $x$ are strings in $\{0,1\}^*$, and we denote by $P(x)$ the output of the program represented by the string $P$ on the input $x$.

> # { .pause }
The above is one of those observations that are simultaneously both simple and profound. Please make sure that you understand __(1)__ how for every fixed choice of representing programs as strings, the function $EVAL$ above is well defined, and __(2)__ what this function actually does.

$EVAL$ takes strings of  _arbitrary length_, and hence cannot be computed by a NAND-CIRC program, since such programs take inputs of finite length.
However, for every fixed number of lines $s$, inputs $n$, and outputs $m$, we can define $EVAL_{s,n,m}$ to be the function that on input a string $P$ representing an $s$-line $n$-input $m$-output program, and a string $x\in \{0,1\}^n$, output the value $P(x)$.^[If $P \in \{0,1\}^S$ is a string that  does not describe a valid program then we don't care what $EVAL_{s,n,m}(P,x)$ is. For concreteness we can define $EVAL_{s,n,m}(P,x)$ to equal   $0^m$ in such a case.]
Specifically, we will fix some representation scheme for NAND-CIRC programs and  let $S(s)$ denote the number of bits needed to represent programs of $s$ lines.
Then $EVAL_{s,n,m}$ takes inputs of length $S+n$ (that is a string $P \in \{0,1\}^S$ representing the program and a string $x\in \{0,1\}^n$ which corresponds to the input) and outputs the string $P(x) \in \{0,1\}^m$.

One of the first examples of _self circularity_ we will see in this book is the following theorem:

::: {.theorem title="Bounded Universality of NAND-CIRC programs" #bounded-univ}
For every $s,n,m \in \N$ with $s\geq m$ there is a NAND-CIRC program $U_{s,n,m}$ that computes the  function $EVAL_{s,n,m}$.
:::

That is, the NAND-CIRC program $U_{s,n,m}$ takes the description of _any other NAND-CIRC program_ $P$ (of the right length and inputs/outputs) and  _any input_ $x$, and computes the result of evaluating the program $P$ on the input $x$.
Given the equivalence between NAND-CIRC programs and Boolean circuits, we can also think of $U_{s,n,m}$ as a circuit that takes as input the description of other circuits and their inputs, and returns their evaluation, see [universalcircfig](){.ref}.


We call this  NAND-CIRC program $U_{s,n,m}$ that computes $EVAL_{s,n,m}$ a _bounded universal program_ (or a _universal circuit_).
"Universal" stands for the fact that this is a _single program_ that can evaluate _arbitrary_ code,  where "bounded" stands for the fact that  $U_{s,n,m}$ only evaluates programs of bounded size.
Of course this limitation is inherent for the NAND-CIRC programming language, since  a program of $s$ lines (or, equivalently, a circuit of $s$ gates) can take at most $2s$  inputs.
Later, in [chaploops](){.ref}, we will  introduce the concept of _loops_, that  allows  to escape this limitation.



![A _universal circuit_ $U$ is a circuit that gets as input the description of an arbitrary (smaller) circuit $C$ as a binary string, and an input $x$, and outputs the string $C(x)$ which is the evaluation of $C$ on $x$. We can also think of $U$ as a straight-line program that gets as input the code of a straight-line program $C$ and an input $x$, and outputs $C(x)$.](../figure/universalcircuit.png){#universalcircfig .margin  }

Of course to fully specify $EVAL_{s,n,m}$, we need to fix a precise representation scheme  for NAND-CIRC programs as binary strings.
We can simply use the ASCII representation, though  below we will choose a  more convenient representation.
But regardless of the choice of representation,
[bounded-univ](){.ref} is an immediate corollary of [NAND-univ-thm](){.ref}, which states that _every_ finite function, and so in particular the function $EVAL_{S,n,m}$ above, can be computed by _some_ NAND-CIRC program.

> # { .pause }
Once again, [bounded-univ](){.ref}  is subtle but important. Make sure you understand what this theorem means, and why it is a corollary of [NAND-univ-thm](){.ref}.


### Efficient universal programs

It turns out that we don't even need to pay that much of an overhead for universality. Namely, the size of $U$ needs  only be  _polynomial_ in the size of the input program.

> # {.theorem title="Efficient bounded universality of NAND-CIRC programs" #eff-bounded-univ}
For every $s,n,m \in \N$ there is a NAND-CIRC program of at most $O(s^2 \log s)$ lines that computes the  function
$EVAL_{S,n,m}:\{0,1\}^{S+n} \rightarrow \{0,1\}^m$ defined above.

Unlike [bounded-univ](){.ref}, [eff-bounded-univ](){.ref} is not a trivial corollary of the fact that every finite function can be computed by some circuit.
Proving [bounded-univ](){.ref} requires us to present a concrete NAND-CIRC program for the $EVAL_{s,n,m}$ function.
We will do so in several stages.

1. First, we will spell out precisely how to  represent NAND-CIRC programs as strings.
We can prove  [eff-bounded-univ](){.ref}  using the ASCII representation, but a "cleaner" representation will turn out to be more convenient.

2. Then, we will show how we can write a  program to compute $EVAL_{s,n,m}$ in _Python_.^[We will not use much about Python, and a reader that has familiarity with programming in any language should be able to follow along.]

3. Finally, we will show how we can transform this Python program into a NAND-CIRC program.

### Concrete representation for NAND-CIRC programs

![In the Harvard Mark I computer, a program was represented as a list of triples of numbers, which were then encoded by perforating holes in a control card.](../figure/tapemarkI.png){#figureid .margin  }


A NAND-CIRC program is simply a sequence of lines of the form

```python
blah = NAND(baz,boo)
```

There is of course nothing special about the  particular names we use for variables.
Although they would be harder to read, we could write all our programs using only working variables such as `temp_0`, `temp_1` etc.
Therefore, our representation for NAND-CIRC programs will ignore the actual names of the variables, and just associate a _number_ with each variable.
Thus we will encode a _line_ of the program as just a triple of numbers.
If the line has the form `foo = NAND(bar,blah)` then we encode it with the triple $(i,j,k)$ where  $i$ is the number corresponding to the variable `foo` and $j$ and $k$ are the numbers corresponding to `bar` and `blah` respectively.

More concretely, we use the set $[t]= \{0,1,\ldots,t-1\}$ as our set of variables.
The first $n$ numbers $\{0,\ldots,n-1\}$ will correspond to the _input_ variables, the last $m$ numbers $\{t-m,\ldots,t-1\}$ will correspond to the _output_ variables, and the intermediate numbers $\{ n,\ldots, t-m-1\}$ will correspond to the remaining "workspace" variables.

This motivates the following definition:

::: {.definition title="List of tuples representation" #nandtuplesdef}
Let $P$ be a NAND-CIRC program of $n$ inputs, $m$ outputs, and $s$ lines, and let $t$ be the number of distinct variables used by $P$.
The _list of tuples representation of $P$_ is the triple $(n,m,L)$ where $L$ is a list of triples of the form $(i,j,k)$ for $i,j,k \in [t]$.

We assign a number  for  variable  of $P$ as follows:

* For every $i\in [n]$, the variable `X[`$i$`]` is assigned the number $i$.

* For every $j\in [m]$, the variable `Y[`$j$`]` is assigned the number $t-m+j$.

* Every other variable is assigned a number in $\{n,n+1,\ldots,t-m-1\}$ in the order of which it appears.
:::


The list of tuples representation will be our default choice for representing NAND-CIRC programs, and since "list of tuples representation" is a bit of a mouthful, we will often call this simply the  _representation_ for a program $P$.

::: {.example title="Representing the XOR program" #representXOR}
Our favorite NAND-CIRC program, the XOR program:

```python
u = NAND(X[0],X[1])
v = NAND(X[0],u)
w = NAND(X[1],u)
Y[0] = NAND(v,w)
```

is represented as the tuple $(2,1,L)$ where $L=((2, 0, 1), (3, 0, 2), (4, 1, 2), (5, 3, 4))$. That is, the variables `X[0]` and `X[1]` are given the indices  $0$ and $1$ respectively, the variables `u`,`v`,`w` are given the indices $2,3,4$ respectively, and the variable `Y[0]` is given the index $5$.
:::



Transforming a NAND-CIRC program from its representation as code to the representation as a list of tuples is a fairly straightforward programming exercise, and in particular can be done in a few lines of _Python_.^[If you're curious what these few lines are, see our [GitHub repository](https://github.com/boazbk/tcscode).]
Note that this representation loses information such as the particular names we used for the variables, but this is OK since these names do not make a difference to the functionality of the program.
To represent these $s$ triples numbers as a string requires $O(s \log s)$ bits, as each number in $[t]$ can be represented in the binary basis using $\ceil{\log t}$ bits, and $t \leq 3s$.^[The maximum value $t$ can take is $s+n$ and since every line touches at most two inputs, we can assume that $s \geq n/2$ or $n \leq 2s$ as otherwise there would be an input that is not used in any line of the program.]
For a fixed $s,n,m$, we can represent the list of triples of a program with $n$ inputs, $m$ outputs, and $s$ lines by simply concatenating the representation of the $3s$ numbers, with each represented as a string of length $\ceil{\log 3s}$ using the binary basis.
Hence we can think of $EVAL_{s,n,m}$ as mapping  $\{0,1\}^{3s\ceil{\log 3s} + n}$ to $\{0,1\}^m$.







### A NAND interpeter in "pseudocode"

To prove [eff-bounded-univ](){.ref} it suffices  to give a NAND-CIRC program of $O(s^2 \log s) \leq  O((s\log s)^2)$ lines that can evaluate NAND-CIRC programs of $s$ lines.
Let us start by thinking how we would evaluate such programs  if we weren't restricted to the NAND operations.
That is, let us describe informally an _algorithm_ that on input $n,m,s$, a list of triples $L$, and a string $x\in \{0,1\}^n$, evaluates the program represented by $(n,m,L)$ on the string $x$.

> # { .pause }
It would be highly worthwhile for you to stop here and try to solve this problem yourself.
For example, you can try thinking how you would write a program `NANDEVAL(n,m,s,L,x)` that computes this function in the programming language of your choice.

Here is a description of such an algorithm:


::: { .algorithm title="Eval NAND-CIRC programs" #evalnandcircalg }
__Input:__ Numbers $n,m$ and a list $L$ of $s$ triples  of numbers in $[t]$ for some $t\leq 3s$, as well as a string $x\in \{0,1\}^n$.

__Goal:__ Evaluate the program represented by $(n,m,L)$ on the input $x\in \{0,1\}^n$.

__Operation:__

1. We will create a _dictionary_ data structure `Vartable` that for every $i \in [t]$ stores a bit. We will assume we have the operations `GET(Vartable,i)` which restore the bit corresponding to `i`, and the operation `UPDATE(Vartable,i,b)` which update the bit corresponding to `i` with the value `b`. (More concretely, we will write this as `Vartable = UPDATE(Vartable,i,b)` to emphasize the fact that the state of the data structure changes, and to keep our convention of using functions free of "side effects".)

2. We will initialize the table by setting the $i$-th value of `Vartable`  to $x_i$ for every $i\in [n]$.

3. We will go over the list $L$ in order, and for every triple  $(i,j,k)$ in $L$, we let $a$ be `GET(Vartable,`$j$`)`, $b$ be `GET(Vartable,`$k$`)`, and then set the value corresponding to $i$ to the NAND of $a$ and $b$. That is, let `Vartable = UPDATE(Vartable,`$i$,`NAND(`$a$`,`$b$`))`.

4. Finally, we output the value `GET(Vartable,`$t-m+j$`)` for every $j\in [m]$.
:::


> # { .pause }
Please make sure you understand this algorithm and why it does produce the right value.

### A NAND interpreter in Python

To make things more concrete, let us see how we implement the above algorithm in the _Python_ programming language.
We will construct a function `NANDEVAL` that  on input $n,m,L,x$  will output the result of evaluating the program represented by $(n,m,L)$ on $x$.^[To keep things simple, we will not worry about the case that $L$ does not represent a valid program of $n$ inputs and $m$ outputs. Also, there is nothing special about Python. We could have easily presented a corresponding function in JavaScript, C, OCaml, or any other programming language.]

```python
def NANDEVAL(n,m,L,X):
    # Evaluate a NAND-CIRC program from its list of triple representation.
    s = len(L) # number of lines
    t = max(max(a,b,c) for (a,b,c) in L)+1 # maximum index appearing + 1

    Vartable = [0] * t # we'll simply use an array to store data

    def GET(V,i): return V[i]
    def UPDATE(V,i,b):
        V[i]=b
        return V

    # load input values to Vartable:
    for i in range(n): Vartable = UPDATE(Vartable,i,X[i])

    # Run the program
    for (i,j,k) in L:
        a = GET(Vartable,j)
        b = GET(Vartable,k)
        c = NAND(a,b)
        Vartable = UPDATE(Vartable,i,c)

    # Return outputs Vartable[t-m], Vartable[t-m+1],....,Vartable[t-1]
    return [GET(Vartable,t-m+j) for j in range(m)]

# Test on XOR (2 inputs, 1 output)
L = ((2, 0, 1), (3, 0, 2), (4, 1, 2), (5, 3, 4))
print(NANDEVAL(2,1,L,(0,1))) # XOR(0,1)
# [1]
print(NANDEVAL(2,1,L,(1,1))) # XOR(1,1)
# [0]
```


Accessing an  element  of the array `Vartable` at a given index takes a constant number of basic operations.
Hence (since $n,m \leq s$ and $t \leq 3s$),  the program above will use  $O(s)$ basic operations.^[Python does not distinguish between lists and arrays, but allows constant time random access to an indexed elements to both of them. One could argue that if we allowed programs of truly unbounded length (e.g., larger than $2^{64}$) then the price  would not be constant but logarithmic in the length of the array/lists, but the difference between $O(s)$ and $O(s \log s)$ will not be important for our discussions.]

### Constructing the  NAND interpreter in NAND

We now turn to describing the proof of  [eff-bounded-univ](){.ref}.
To do this, it is of course not enough to give a Python program.
Rather, we need to show  how we compute the function  $EVAL_{s,n,m}$  itself using a NAND-CIRC program.
In other words, our job is to transform, for every $s,n,m$, the Python code above to a NAND-CIRC program $U_{s,n,m}$ that  computes the function $EVAL_{s,n,m}$.

> # { .pause }
Before reading further, try to think how _you_ could give a "constructive proof" of [eff-bounded-univ](){.ref}.
That is, think of how you would write, in the programming language of your choice, a function `universal(s,n,m)` that on input $s,n,m$ outputs the code for the NAND-CIRC program $U_{s,n,m}$ such that $U_{s,n,m}$ computes $EVAL_{s,n,m}$.
Note that there is a subtle but crucial difference between this function and the Python `NANDEVAL` program described above.
Rather than actually evaluating a given program $P$ on some input $w$, the function `universal` should output the _code_ of a NAND-CIRC program that computes the map $(P,x) \mapsto P(x)$.

Our construction will follow very closely the Python implementation of `EVAL` above.
We will use variables `Vartable[`$0$`]`,$\ldots$,`Vartable[`$2^\ell-1$`]`, where $\ell = \ceil{\log 3s}$ to store our variables.
However, NAND doesn't have integer-valued variables, so we cannot write code such as
`Vartable[i]` for some variable `i`.
However, we _can_ implement the function `GET(Vartable,i)` that outputs the `i`-th bit of the array `Vartable`.
Indeed, this is nothing by the function `LOOKUP` that we have seen in [lookup-thm](){.ref}!

> # { .pause }
Please make sure that you understand why `GET` and `LOOKUP` are the same function.

We saw that we can compute `LOOKUP` on arrays of size $2^\ell$ in time $O(2^\ell)$, which will be $O(s)$ for our choice of $\ell$.

To compute the `update` function on input `V`,`i`,`b`, we need to scan the array `V`, and for  $j \in [2^\ell]$, have our $j$-th output be `V[`$j$`]` unless $j$ is equal to `i`, in which case the $j$-th output is `b`.
We can do this as follows:

1. For every $j\in [2^\ell]$, there is an $O(\ell)$ line NAND-CIRC program to compute the function $EQUALS_j: \{0,1\}^\ell \rightarrow \{0,1\}$ that on input $i$ outputs $1$ if and only if $i$ is equal to (the binary representation of) $j$. (We leave verifying this as [equals](){.ref} and [equalstwo](){.ref}.)

2. We have seen that we can compute the function $IF:\{0,1\}^3 \rightarrow \{0,1\}$ such that $IF(a,b,c)$ equals $b$ if $a=1$ and $c$ if $a=0$.

Together, this means that we can compute `UPDATE` as follows:

```python
def UPDATE(V,i,b):
    # update a 2**ell length array at location i to the value b
    for j in range(2**ell): # j = 0,1,2,....,2^ell -1
        a = EQUALS_j(i)
        Y[j] = IF(a,b,V[j])
    return Y
```


Once we can compute `GET` and `UPDATE`, the rest of the implementation amounts to "book keeping" that needs to be done carefully, but is not too insightful, and hence we omit the full details.

Since the loop over `j` in `UPDATE` is run $2^\ell$ times, and computing `EQUALS_j` takes $O(\ell)$ lines, the total number of lines to compute `UPDATE` is $O(2^\ell \cdot \ell) = O(s \log s)$. Since we run this function $s$ times, the total number of lines for computing $EVAL_{s,n,m}$ is $O(s^2 \log s)$.
This completes (up to the omitted details) the proof of [eff-bounded-univ](){.ref}.


::: {.remark title="Improving to quasilinear overhead (advanced optional note)" #quasilinearevalrem}
The NAND-CIRC program above is less efficient that its Python counterpart, since NAND does not offer arrays with efficient random access. Hence for example the `LOOKUP` operation on an array of $s$ bits takes $\Omega(s)$ lines in NAND even though it takes $O(1)$ steps (or maybe $O(\log s)$ steps, depending how we count) in _Python_.

It turns out that it is  possible to improve the bound of [eff-bounded-univ](){.ref}, and  evaluate $s$ line NAND-CIRC programs using a NAND-CIRC program of  $O(s \log s)$ lines.
The key is to consider the description of NAND-CIRC programs as circuits, and in particular as directed acyclic graphs (DAGs) of bounded in degree.
A universal NAND-CIRC program $U_s$ for $s$ line programs  will correspond to a _universal graph_ $H_s$ for such $s$ vertex DAGs.
We can think of such as graph $U_s$ as fixed "wiring" for communication network, that should be able to accommodate any arbitrary pattern of communication between $s$ vertices (where this pattern corresponds to an $s$ line NAND-CIRC program).
It turns out that there exist such efficient [routing networks](https://goo.gl/NnkkjM) exist  that allow embedding any $s$ vertex circuit inside a universal graph of size $O(s \log s)$, see the bibliographical notes [bibnotescodeasdata](){.ref} for more on this issue.
:::




##  A Python interpreter in NAND (discussion)

To prove [eff-bounded-univ](){.ref} we essentially translated every line of the Python program for `EVAL` into an equivalent NAND snippet.
It turns out that none of our reasoning  was specific to the  particular function $EVAL$.
It is possible to translate _every_ Python program into an equivalent `NAND` program of comparable efficiency.^[More concretely, if the Python program takes $T(n)$ operations on inputs of length at most $n$ then we can find a NAND-CIRC program of $O(T(n) \log T(n))$ lines that agrees with the Python program on inputs of length $n$.]
Actually doing so requires taking care of many details and is beyond the scope of this book, but let me try to convince you why you should believe it is possible in principle.

For starters, one can can use [CPython](https://en.wikipedia.org/wiki/CPython) (the reference implementation for Python), to evaluate every Python program using a `C` program.
We can combine this with a C compiler to transform a Python program to various flavors of "machine language".
So, to transform a Python program into an equivalent NAND-CIRC program, it is enough to show how to transform a _machine language_ program into an equivalent NAND-CIRC program.
One minimalistic (and hence convenient) family of machine languages is known as the _ARM architecture_ which powers many mobile devices including essentially all Android devices.^[ARM stands for "Advanced RISC Machine" where RISC in turn stands for "Reduced instruction set computer".]
There are even simpler machine languages, such as the [LEG acrhitecture](https://github.com/frasercrmck/llvm-leg) for which a  backend for the [LLVM compiler](http://llvm.org/) was implemented (and hence can be the target of compiling any of [large and growing list](https://en.wikipedia.org/wiki/LLVM#Front_ends) of languages that this compiler supports).
Other examples include the  [TinyRAM](http://www.scipr-lab.org/doc/TinyRAM-spec-0.991.pdf) architecture (motivated by  interactive proof systems that we will discuss in [chapproofs](){.ref}) and  the teaching-oriented [Ridiculously Simple Computer](https://www.ece.umd.edu/~blj/RiSC/) architecture.
Going one by one over the instruction sets of such computers and translating them to NAND snippets is no  fun, but it is a feasible thing to do.
In fact, ultimately this is very similar to the transformation that takes place in converting our high level code to actual silicon gates that are not so different from the operations of a NAND-CIRC program.
Indeed, tools such as [MyHDL](http://www.myhdl.org/) that transform "Python to Silicon" can be used to convert a Python program to a NAND-CIRC program.

The NAND-CIRC programming language is just a teaching tool, and by no means do I suggest that writing NAND-CIRC programs, or compilers to NAND, is a practical, useful, or  enjoyable activity.
What I do want is to make sure you understand why it _can_ be done, and to have the confidence that if your life (or at least your grade) depended on it, then you would be able to do this.
Understanding how programs in high level languages such as Python are eventually transformed into concrete low-level representation such as NAND is fundamental to computer science.

The astute reader might notice that the above paragraphs only outlined why it should be possible to find for every _particular_ Python-computable function $f$, a _particular_ comparably efficient NAND-CIRC program $P$ that computes $f$.
But this still seems to fall short of our goal of writing a "Python interpreter in NAND" which would mean  that for every parameter $n$, we come up with a _single_ NAND-CIRC program $UNIV_n$ such that given a description of a Python program $P$, a particular input $x$, and a bound $T$ on the number of operations (where the length of $P$, $x$ and the magnitude of $T$ are all at most $n$) would return the result of executing $P$ on $x$ for at most $T$ steps.
After all, the transformation above would transform every Python program into a different NAND-CIRC program, but would not yield "one NAND-CIRC program to rule them all" that can evaluate every Python program up to some given complexity.
However, it turns out that it is enough to show such a transformation for a single Python program.
The reason is that we can write a Python interpreter _in Python_: a Python program $U$ that takes a bit string, interprets it as Python code, and then runs that code.
Hence, we only need to show a NAND-CIRC program $U^*$ that computes the same function as the particular Python program $U$, and this will give us a way to evaluate _all_ Python programs.

What we are seeing time and again is the notion of _universality_ or _self reference_ of computation, which is the sense that all reasonably rich models of computation are expressive enough that they can "simulate themselves".
The importance of this phenomena to both the theory and practice of computing, as well as far beyond it, including the foundations of mathematics and basic questions in science, cannot be overstated.



## Counting programs, and lower bounds on the size of NAND-CIRC programs

One of the consequences of our representation is the following:


> # {.theorem title="Counting programs" #program-count}
For every $n,m,s$ with $s \geq m, s \geq n/2$,
$$|SIZE_{n,m}(s)| \leq 2^{O(s \log s)}.$$
That is, there are at most $2^{O(s\log s)}$ functions computed by  NAND-CIRC programs of at most $s$ lines.

Moreover, the implicit constant in the $O(\cdot)$ notation in [program-count](){.ref} is at most $10$.^[By this we mean that for all sufficiently large $s$, $|Size(s)|\leq 2^{10s\log s}$.]

> # {.proofidea data-ref="program-count"}
The idea behind the proof is that, as we've seen, we can represent every $s$ line program by a binary string of  $O(s \log s)$ bits.
Therefore the  number of functions  computed by $s$-line programs cannot be larger than the number of such strings, which is $2^{O(s \log s)}$.
In the actual proof, given below, we  count the number of representations a little more carefully, talking directly about triples rather than binary strings, although the idea remains the same.


::: {.proof data-ref="program-count"}
Every NAND-CIRC program $P$ with $s$ lines has at most $3s$ variables.
Hence, using our canonical representation, $P$ can be represented by the numbers $n,m$ of $P$'s inputs and outputs, as well as by the list $L$ of $s$ triples of natural numbers, each of which is smaller or equal to $3s$.

If two programs compute distinct functions then they have distinct representations.
(Make sure you understand why the above statement is true!)
This means that our representation scheme yields a _one to one function_ from $SIZE_{n,m}(s)$ to the set $[3s]^{3s}$ of all length-$s$ lists of triples of numbers in $[3s]$.^[Strictly speaking, the lists have length _at most_ $s$, but we can always add "dummy instructions" to a program with smaller than $s$ lines that would not modify its input/output behavior but ensure that its length is exactly $s$.]
Therefore

$$|SIZE_{n,m}(s)| \leq (3s)^{3s} = 2^{3s \log (3s)} = 2^{3s \log s + 3\log 3 s} \;.$$

Since $s = o(s \log s)$, for sufficiently large $n$, this bound will be smaller than $2^{4s\log s}$.
:::


::: {.remark title="Counting by ASCII representation" #countingfromascii}
We can also establish [program-count](){.ref} directly from the ASCII representation of the source code.  Since an $s$-line NAND-CIRC program has at most $3s$ distinct variables,  we can change all the non input/output variables of such a program to have the form `Temp[`$i$`]`  for $i$ between $0$ and $3s-1$ without changing the function that it computes. This means that  after removing  extra whitespaces, every line of such a program (which will  be of the form form `var = NAND(var',var'')` for variable identifiers which will be either `X[###]`,`Y[###]` or `Temp[###]` where `###` is some number smaller than $3s$) will require at most, say, $20 + 3\log_{10} (3s) \leq O(\log s)$ characters. Since each one of those characters can be encoded using seven bits in the ASCII representation, we see that the number of functions computed by $s$-line NAND-CIRC programs is at most $2^{O(s \log s)}$.
:::


A function mapping $\{0,1\}^2$ to $\{0,1\}$ can be identified with the table of its four values on the inputs $00,01,10,11$;
a function mapping $\{0,1\}^3$ to $\{0,1\}$ can be identified with the table of its eight values on the inputs $000,001,010,011,100,101,110,111$.
More generally, every function $F:\{0,1\}^n \rightarrow \{0,1\}$ can be identified with the table of its  $2^n$  values  on the inputs $\{0,1\}^n$.
Hence the number of functions mapping $\{0,1\}^n$ to $\{0,1\}$ is equal to the number of such tables which (since we can choose either $0$ or $1$ for every row) is exactly $2^{2^n}$. Note that this is _double exponential_ in $n$, and hence even for small values of $n$ (e.g., $n=10$) the number of functions from $\{0,1\}^n$ to $\{0,1\}$ is truly astronomical.^["Astronomical" here is an understatement: there are much fewer than $2^{2^{10}}$ stars, or even particles, in the observable universe.]
This has the following important corollary:

> # {.theorem title="Counting argument lower bound" #counting-lb}
There is a function $F:\{0,1\}^n\rightarrow \{0,1\}$ such that the  shortest NAND-CIRC program to compute $F$ requires $2^n/(100n)$ lines.

::: {.proof data-ref="counting-lb"}
Suppose, towards the sake of contradiction, that every function $F:\{0,1\}^n\rightarrow\{0,1\}$ can be computed by a NAND-CIRC program of at most $s=2^n/(100n)$ lines.
Then the by [program-count](){.ref} the total number of such functions would be at most $2^{10s\log s} \leq 2^{10 \log s \cdot 2^n/(100 n)}$.
Since $\log s = n - \log (100 n) \leq n$ this means that the total number of such functions would be at most $2^{2^n/10}$, contradicting the fact that there are $2^{2^n}$ of them.
:::

We have seen before that _every_ function mapping $\{0,1\}^n$ to $\{0,1\}$ can be computed by an  $O(2^n /n)$ line program.
We now see that this is  tight in the sense that some functions do require such an astronomical number of lines to compute.

::: { .bigidea #countinglb }
Some functions  $f:\{0,1\}^n \rightarrow \{0,1\}$   _cannot_ be  computed  by a Boolean circuit using a fewer than exponential number of gates.
:::

In fact, as we explore in the exercises below, this is the case for _most_ functions.
Hence  functions that can be computed in a small number of lines (such as addition, multiplication, finding short paths in graphs, or even the $EVAL$ function) are the exception, rather than the rule.


> # {.remark title="Advanced note: more efficient representation" #efficientrepresentation}
The list of triples is not the shortest representation for NAND-CIRC programs.
We have seen that every NAND-CIRC program of $s$ lines and $n$ inputs can be represented by a directed graph of $s+n$ vertices, of which $n$ have in-degree zero, and the $s$ others have in-degree at most two. Using the adjacency list representation, such a graph can be represented using roughly $2s\log(s+n) \leq 2s (\log s + O(1))$ bits.
Using this representation we can reduce the implicit constant in [program-count](){.ref} arbitrarily close to $2$.


## Size hierarchy theorem (advanced, optional)

By [NAND-univ-thm](){.ref} the class $SIZE_{n}(4 \cdot 2^n)$ contains _all_ functions from $\{0,1\}^n$ to $\{0,1\}$.
In fact, as discussed in [tight-upper-bound](){.ref} this can be improved to show that there is some constant $C$ such that $SIZE_n(C \cdot 2^n / n)$ contains all functions from $\{0,1\}^n$ to $\{0,1\}$ (in fact, $C=4$ will do).
On the other hand,  [counting-lb](){.ref} shows that if $c >0$ is small enough ($c = 1/4$ will do) then $SIZE_n(c 2^n / n)$ does _not_ contain all functions.
Thus, these two results together show that there exists some constants $C>c>0$ such that for every sufficiently large $n$,
$$
SIZE_n(c 2^n /n) \subsetneq  SIZE_n(C 2^n /n) \;.
$$
That is, the set of functions that can be computed using $c 2^n/n$ gates is a _strict subset_ of the set of functions that can be computed using $C 2^n / n$ gates.

In fact, we can use the same results to show a more general result: as a general rule, if we increase our "budget" of gates by a constant factor, then we can compute new functions:


> # {.theorem title="Size Hierarchy Theorem" #sizehiearchythm}
There exists some constant $C$ such that for _every_ $n \leq s \leq 2^n/(4n)$, there exists some function $f$ that _can not_ be computed using $s$ gates but _can_ be computed using $C\cdot s$ gates.


> # {.proofidea data-ref="sizehiearchythm"}
The idea is to "scale down" the result of [counting-lb](){.ref}.
We set $\ell$ to be such that $s$ is about exponential in $\ell$, and so that $Cs$ gates are enough to compute all functions on $\ell$ bits but $s$ gates are not enough to compute some function $g:\{0,1\}^\ell \rightarrow \{0,1\}$.
We can then let $f:\{0,1\}^n \rightarrow \{0,1\}$ be a function that ignores all but the first input $\ell$ bits, and returns the result of applying $g$  to these bits.


::: {.proof data-ref="sizehiearchythm,}
Let $s$ be as in the theorem statement, and let $\ell$ be the largest integer such that $10s \geq 2^\ell/(10\ell)$.
(By this choice $10s < 2^{\ell-1}/(10\ell-1)$ which means that $5s < 2^\ell/(10\ell)$.)
Let $g:\{0,1\}^\ell \rightarrow \{0,1\}$ be a function outside $SIZE_\ell(2^\ell/(10\ell)) \supseteq SIZE_\ell(5s)$ (the existence of such a function is guaranteed by [counting-lb](){.ref}).
We let $f:\{0,1\}^n \rightarrow \{0,1\}$ be the function defined as $f(x_0,\ldots,x_{n-1}) = g(x_0,\ldots,x_{\ell-1})$.
We claim that there is some constant $C$ such that:

* $f \in SIZE_n(C \cdot s)$.

* $f \not\in SIZE_n(s)$.

For the former, by the [NAND-univ-thm](){.ref} (more precisely its  strengthened variant discussed in [tight-upper-bound](){.ref}), if $C'$ is sufficiently large then $SIZE_\ell(C' \cdot 2^\ell / \ell)$ contains _all_ functions mapping $\{0,1\}^\ell$ to $\{0,1\}$. Therefore we can compute $g$, and hence $f$ as well, using a circuit of at most
$C' \cdot 2^\ell / \ell$ gates which is at most $C s$ for $C=10C'$.

For the latter, suppose towards the sake of contradiction that $f$ _can_ be computed by an $n$-input NAND-CIRC program $P$ of at most $s$ lines.
Now consider the following program $P'$ which is obtained by $P$ as follows:

1. We add three lines to ensure access to the variable `zero` that is always equal to $0$.

2. We replace every instance in which $P$ refers to a variable of the form `X[`$i$`]` for $i>\ell$ with the variable `zero`.

The new program $P'$ only takes $\ell$ inputs and has at most $s+3$ lines. Moreover, for every $x\in \{0,1\}^\ell$, $P'(x)=P(x0^{n-\ell})$.
This means that under our assumption that $P$ computes $f$, $P'(x)=f(x0^{n-\ell})=g(x)$ for every $x\in \{0,1\}^\ell$.
But this contradicts the assumption that $g \not\in SIZE_\ell(5s)$.
:::





![An illustration of some of what we know about the size complexity classes (not to scale!). This figure depicts  classes of the form $SIZE_{n,n}(s)$ but the state of affairs for other size complexity classes such as $SIZE_{n,1}(s)$ is similar. We know by [NAND-univ-thm](){.ref} (with the improvement of [tight-upper-bound](){.ref}) that all functions mapping $n$ bits to $n$ bits can be computed by a circuit of size $c \cdot 2^n$ for $c \leq 10$, while on the other hand the counting lower bound ([counting-lb](){.ref}, see also [countingmultibitex](){.ref}) shows that _some_ such functions will require $0.1 \cdot 2^n$, and the size hierarchy theorem ([sizehiearchythm](){.ref}) shows the existence of functions in $SIZE(S) \setminus SIZE(s)$ whenever $s=o(S)$, see also [sizehiearchyex](){.ref}.
We also consider some specific examples: addition of two $n/2$ bit numbers can be done in $O(n)$ lines, while we don't know of such a program for _multiplying_ two $n$ bit numbers, though we do know it can be done in $O(n^2)$ and in fact even better size. In the above  $FACTOR_n$ corresponds to the inverse problem of multiplying- finding the _prime factorization_ of a given number. At the moment  we do not know  of any circuit  a polynomial (or even sub-exponential) number of lines that can compute $FACTOR_n$. ](../figure/sizecomplexity.png){#sizeclassesfig .full   }

::: {.remark title="Explicit functions" #explicitfunc}
While the size hierarchy theorem guarantees that there exists _some_ function that _can_ be computed using, for example, $n^2$ gates, but not using $100n$ gates, we do not know of any explicit example of such a function.
While we suspect that integer multiplication is such an example, we do not have any proof that this is the case.
:::





## The physical extended Church-Turing thesis (discussion) { #PECTTsec }

We've seen that  NAND gates can be implemented using very different systems in the physical world.
What about the reverse direction?
Can NAND-CIRC programs simulate any physical computer?


We can take a leap of faith and stipulate that NAND-CIRC programs do actually encapsulate _every_ computation that we can think of.
Such a statement (in the realm of infinite functions, which we'll encounter in [chaploops](){.ref}) is typically attributed to Alonzo Church and Alan Turing, and in that context is  known as the _Church Turing Thesis_.
As we will discuss in future lectures, the Church-Turing Thesis is not a mathematical theorem or conjecture.
Rather, like theories in physics, the Church-Turing Thesis is about mathematically modelling the real world.
In the context of finite functions, we can make the following informal hypothesis or prediction:

>_If a function $F:\{0,1\}^n \rightarrow \{0,1\}^m$ can be computed in the physical world using $s$ amount of "physical resources" then it can be computed by a Boolean circuit program of roughly $s$ gates._

We call this hypothesis the **"Physical Extended Church-Turing Thesis"** or _PECTT_ for short.
A priori it might seem rather extreme to hypothesize that our meager NAND model captures all possible physical computation.
But yet, in more than a century of computing technologies, no one has yet built any  scalable computing device that challenges this hypothesis.

We now  discuss  the "fine print" of the PECTT in more detail, as well as the (so far unsuccessful) challenges that have been raised against it.
There is no single universally-agreed-upon formalization of "roughly $s$ physical resources",  but
we can approximate this notion by considering the size of any  physical computing device and the time it takes to compute the output, and ask that  any such device can be simulated by a Boolean circuit with a number of gates that is a polynomial (with not too large exponent) in the size of the system and the time it takes it to operate.


In other words, we can phrase the PECTT as stipulating that any function that can be computed by a device of that takes as a certain volume $V$ of space and requires $t$ time to complete the computation, must be computable by a Boolean circuit with a number of gates that is polynomial in $V$ and $t$.

The exact polynomial is not clear but it is  generally accepted that if $f:\{0,1\}^n \rightarrow \{0,1\}$ is an _exponentially hard_ function, in the sense that it has no NAND-CIRC program of fewer than, say, $2^{n/2}$ lines, then a demonstration of a physical device that can compute in the real world $f$ for moderate input lengths (e.g., $n=500$) would be a violation of the PECTT.


::: {.remark title="Advanced note: making PECTT concrete (advanced optional)" #concretepectt}
We can attempt at a more exact phrasing of the PECTT as follows.
Suppose that $Z$ is a physical system that accepts $n$ binary stimuli and has a binary output, and can be enclosed in a sphere of volume $V$.
We say that the system $Z$ _computes_ a function $f:\{0,1\}^n \rightarrow \{0,1\}$ within $t$ seconds if whenever we set the stimuli to some value  $x\in \{0,1\}^n$,  if we measure the output after $t$ seconds then we obtain $f(x)$.

One can then phrase the PECTT as stipulating  that if there exists such a system $Z$ that  computes $F$ within $t$ seconds, then  there exists  a NAND-CIRC program that computes $F$ and has at most $\alpha(Vt)^2$ lines, where $\alpha$ is some normalization constant.^[We can also consider variants where we use [surface area](https://goo.gl/ALgbVS) instead of volume, or take $(Vt)$ to a  different power than $2$.  However, none of these choices makes a qualitative difference  to the discussion below.]
In particular, suppose that $f:\{0,1\}^n \rightarrow \{0,1\}$ is a function that requires $2^n/(100n)>2^{0.8n}$ lines for any NAND-CIRC program (such a function exists by [counting-lb](){.ref}).
Then the PECTT would imply that either the volume or the time of a system that computes $F$ will have to be at least $2^{0.2 n}/\sqrt{\alpha}$.
Since this quantity grows _exponentially_ in $n$, it is not hard to set parameters so that even for moderately large values of $n$, such a system could not fit in our universe.

To fully make the PECTT  concrete, we need to decide on the units for measuring time and volume, and the normalization constant $\alpha$.
One  conservative choice is to assume that we could squeeze computation to the absolute physical limits (which are many orders of magnitude beyond current technology).
This corresponds to setting $\alpha=1$ and using the [Planck units](https://goo.gl/gkpmBF) for volume and time.
The _Planck length_ $\ell_P$ (which is, roughly speaking, the shortest distance that can theoretically be measured) is roughly $2^{-120}$ meters.
The _Planck time_ $t_P$ (which is the time it takes for light to travel one Planck length) is about $2^{-150}$ seconds.
In the above setting, if a function $F$ takes, say, 1KB of input (e.g., roughly $10^4$ bits, which can encode a $100$ by $100$ bitmap image), and requires at least $2^{0.8 n}= 2^{0.8 \cdot 10^4}$ NAND lines to compute, then any physical system that computes it would require either volume of $2^{0.2\cdot  10^4}$ Planck length cubed, which is more than $2^{1500}$ meters cubed or take at least $2^{0.2 \cdot 10^4}$ Planck Time units, which is larger than $2^{1500}$ seconds.
To get a sense of how big that number is, note that the universe is only about $2^{60}$ seconds old, and its observable radius is only roughly $2^{90}$ meters.
The above discussion suggests that it is possible to _empirically falsify_ the PECTT by presenting a smaller-than-universe-size system that computes such a function.^[There are of course several hurdles to refuting the PECTT in this way, one of which is that we can't actually test the system on all possible inputs. However,  it turns out that we can get around this issue using notions such as  _interactive proofs_ and _program checking_ that we might encounter later in this book. Another, perhaps more salient problem, is that while we know many hard functions exist, at the moment there is _no single explicit function_ $F:\{0,1\}^n \rightarrow \{0,1\}$ for which we can _prove_ an $\omega(n)$ (let alone  $\Omega(2^n/n)$) lower bound  on the number of lines that a NAND-CIRC program needs to compute it.]
:::


### Attempts at refuting  the PECTT

One of the admirable traits of mankind is the refusal to accept limitations.
In the best case this is manifested by people achieving longstanding "impossible" challenges such as heavier-than-air flight, putting a person on the moon, circumnavigating the globe, or even resolving [Fermat's Last Theorem](https://en.wikipedia.org/wiki/Fermat%27s_Last_Theorem).
In the worst case it is manifested by people continually following the footsteps of previous failures to try to do proven-impossible tasks such as build a [perpetual motion machine](https://en.wikipedia.org/wiki/Perpetual_motion), [trisect an angle](https://en.wikipedia.org/wiki/Angle_trisection) with a compass and straightedge, or refute [Bell's inequality](https://en.wikipedia.org/wiki/Bell%27s_theorem).
The Physical Extended Church Turing thesis (in its various forms) has attracted both types of people.
Here are some physical devices that have been speculated to  achieve computational tasks that cannot be done by not-too-large  NAND-CIRC programs:

* **Spaghetti sort:** One of the first lower bounds that Computer Science students encounter is that sorting $n$ numbers requires making $\Omega(n \log n)$ comparisons. The "spaghetti sort" is a description of a proposed "mechanical computer" that would do this faster. The idea is that to sort $n$ numbers $x_1,\ldots,x_n$, we could cut $n$ spaghetti noodles into lengths $x_1,\ldots,x_n$, and then if we simply hold them together in our hand and bring them down to a flat surface, they will emerge in sorted order. There are a great many reasons why this is not truly a challenge to the PECTT hypothesis, and I will not ruin the reader's fun in finding them out by her or himself.

* **Soap bubbles:** One function $F:\{0,1\}^n \rightarrow \{0,1\}$ that is conjectured to require a large number of NAND lines to solve is the _Euclidean Steiner Tree_ problem. This is the problem where one is given $m$ points in the plane $(x_1,y_1),\ldots,(x_m,y_m)$ (say with integer coordinates ranging from $1$ till $m$, and hence the list can be represented as a string of $n=O(m \log m)$ size) and some number $K$.  The goal is to figure out whether it is possible to connect all the points by line segments of total length at most $K$. This function is conjectured to be hard because it is _NP complete_ - a concept that we'll encounter later in this course - and it is in fact reasonable to conjecture that as $m$ grows, the number of NAND lines required to compute this function grows _exponentially_ in $m$, meaning that the PECTT would predict that if $m$ is sufficiently large (such as few hundreds or so) then no physical device could compute $F$.
Yet, some people claimed that there is in fact a very simple physical device that could solve this problem, that can be constructed using some wooden pegs and soap. The idea is that if we take two glass plates, and put $m$ wooden pegs between them in the locations $(x_1,y_1),\ldots,(x_m,y_m)$ then bubbles will form whose edges touch those pegs in the way that will minimize the total energy which turns out to be a function of the total length of the line segments.
The problem with this device of course is that nature, just like people, often gets stuck in "local optima". That is, the resulting configuration will not be one that achieves the  absolute minimum of the total energy but rather one that can't be improved with local changes.
[Aaronson](http://www.scottaaronson.com/papers/npcomplete.pdf) has carried out actual experiments (see [aaronsonsoapfig](){.ref}), and  saw that while this device often is successful for three or four pegs, it starts yielding suboptimal results once the number of pegs grows beyond that.

![Scott Aaronson [tests](http://www.scottaaronson.com/blog/?p=266) a candidate device for computing Steiner trees using soap bubbles.](../figure/aaronsonsoapbubble.jpg){#aaronsonsoapfig .margin  }

* **DNA computing.** People have suggested using the properties of DNA to do hard computational problems. The main advantage of DNA is the ability to potentially encode a lot of information in relatively small physical space, as well as compute on this information in a highly parallel manner. At the time of this writing, it was [demonstrated](http://science.sciencemag.org/content/337/6102/1628.full) that one can use DNA to store about $10^{16}$ bits of information in a region of radius about milimiter, as opposed to about $10^{10}$ bits with the best known hard disk technology. This does not posit a real challenge to the PECTT but does suggest that one should be conservative about the choice of constant and not assume that current hard disk + silicon technologies are the absolute best possible.^[We were extremely conservative in the suggested parameters for the PECTT, having assumed that as many as $\ell_P^{-2}10^{-6} \sim 10^{61}$ bits could potentially be stored in a milimeter radius region.]

* **Continuous/real computers.** The physical world is often described using continuous quantities such as time and space, and people have suggested that analog devices might have direct access to computing with real-valued quantities and would be inherently more powerful than discrete models such as NAND machines.
Whether the "true" physical world is continuous or discrete is an open question.
In fact, we do not even know how to precisely _phrase_ this question, let alone answer it. Yet, regardless of the answer, it seems clear that the effort to measure a continuous quantity grows with the level of accuracy desired, and so there is no "free lunch" or way to bypass the PECTT using such machines (see also [this paper](http://www.cs.princeton.edu/~ken/MCS86.pdf)). Related to that are proposals  known as "hypercomputing" or  "Zeno's computers" which attempt to use the continuity of time by doing the first operation in one second, the second one in half a second, the third operation in a quarter second and so on..  These fail for a  similar reason to the one guaranteeing that Achilles will eventually catch the tortoise despite the  original Zeno's paradox.

* **Relativity computer and time travel.** The formulation above assumed the notion of time, but under the theory of relativity time is in the eye of the observer. One approach to solve hard problems is to leave the computer to run for a lot of time from _his_ perspective, but to ensure that this is actually a short while from _our_ perspective. One approach to do so is for the user to start the computer and then go for a quick jog at close to the speed of light before checking on its status. Depending on how fast one goes, few seconds from the point of view of the user might correspond to centuries in computer time (it might even finish updating its Windows operating system!). Of course the catch here is that the energy required from  the user is proportional to how close one needs to get to the speed of light. A more interesting proposal is to use time travel via _closed timelike curves (CTCs)_. In this case we could run an arbitrarily long computation by doing some calculations, remembering  the current state, and the travelling back in time to continue where we left off. Indeed, if CTCs exist then we'd probably have to revise the PECTT (though in this case I will simply travel back in time and edit these notes, so I can claim I never conjectured it in the first place...)


* **Humans.** Another computing system that has been proposed as a counterexample to the PECTT is a 3 pound computer of  about 0.1m radius, namely the human brain. Humans can walk around, talk, feel, and do others things that are not commonly  done by NAND-CIRC programs, but can they compute partial functions that NAND-CIRC programs cannot?
There are certainly computational tasks that _at the moment_  humans do better than computers (e.g., play some [video games](http://www.theverge.com/2016/11/4/13518210/deepmind-starcraft-ai-google-blizzard), at the moment), but based on our current understanding of the brain, humans (or other animals) have no _inherent_ computational advantage over computers.
The brain has about $10^{11}$ neurons, each operating in a speed of about $1000$ operations per seconds. Hence a rough first approximation is that a Boolean circuit of about $10^{14}$ gates  could simulate one second of a brain's activity.^[This is a very rough approximation that could be wrong to a few orders of magnitude in either direction. For one, there are other structures in the brain apart from neurons that one might need to simulate, hence requiring higher overhead. On the other hand, it is by no mean clear that we need to fully clone the brain in order to achieve the same computational tasks that it does.]
Note that the fact that such a circuit (likely) exists does not mean it is easy to _find_ it.
After all, constructing this circuit took evolution billions of years.
Much of the recent efforts in artificial intelligence research is focused on finding programs that replicate some of the brain's capabilities and they take massive computational effort to discover, these programs often turn out to be much smaller than the pessimistic estimates above. For example, at the time of this writing, Google's [neural network for machine translation](https://arxiv.org/pdf/1609.08144.pdf) has about $10^4$ nodes (and can be simulated by a NAND-CIRC program of comparable size). Philosophers, priests and many others have since time immemorial argued that there is something about humans that cannot be captured by  mechanical devices such as computers; whether or not that is the case, the evidence is thin that humans can perform computational tasks that are inherently impossible to achieve by computers of similar complexity.^[There are some well known scientists that have [advocated](http://www.telegraph.co.uk/science/2017/03/14/can-solve-chess-problem-holds-key-human-consciousness/) that humans have inherent computational advantages over computers. See also [this](https://arxiv.org/abs/1508.05929).]


* **Quantum computation.** The most compelling attack on the Physical Extended Church Turing Thesis comes from the notion of _quantum computing_.
The idea was initiated by the observation that systems with strong quantum effects are very hard to simulate on a computer.
Turning this observation on its head, people have proposed using such systems to perform computations that we do not know how to do otherwise.
At the time of this writing, Scalable quantum computers have not yet been built, but it is a fascinating possibility, and one that does not seem to contradict any known law of nature.
We will discuss quantum computing in much more detail in [quantumchap](){.ref}.
Modeling quantum computation    involves extending the model of Boolean circuits into _Quantum circuits_ that hvae  one more (very special) gate.
However, the main take away is that while quantum computing does suggest we need to amend the PECTT, it does _not_ require a complete revision of our worldview. Indeed, almost all of the content of this course remains the same whether the underlying computational model is Boolean circuits or quantum circuits.


> # {.remark title="PECTT in practice" #PECTTpractice}
While even the precise phrasing of the PECTT, let alone understanding its correctness, is still a subject of research, some variant of it is already implicitly assumed in practice.
A statement such as "this cryptosystem provides 128 bits of security" really means that __(a)__ it is conjectured that there is no Boolean circuit (or, equivalently, a NAND gate) of size much smaller than $2^{128}$ that can break the system,^[We say "conjectured" and not "proved" because, while we can phrase such a  statement as a precise mathematical conjecture, at the moment we are unable to _prove_ such a statement for any cryptosystem. This is related to the $\mathbf{P}$ vs $\mathbf{NP}$ question we will discuss in future chapters.] and __(b)__ we assume that no other physical mechanism can do better, and hence it would take roughly a $2^{128}$ amount of "resources" to break the system.




> # { .recap }
* We can think of programs both as describing a _process_, as well as simply a list of symbols that can be considered as _data_ that can be fed as input to other programs.
* We can write a NAND-CIRC program that evaluates arbitrary NAND-CIRC programs (or equivalently a circuit that evaluates other circuits). Moreover, the efficiency loss in doing so is not too large.
* We can even write a NAND-CIRC program that evaluates programs in other programming languages such as Python, C, Lisp, Java, Go, etc.
* By a leap of faith, we could hypothesize that the number of gates in the smallest circuit that computes a function $f$ captures roughly the amount of physical resources required to compute $f$. This statement is known as the _Physical Extended Church-Turing Thesis (PECTT)_.
* Boolean circuits (or equivalently AON-CIRC or NAND-CIRC programs) capture a surprisingly wide array of computational models. The strongest currently known challenge to the PECTT comes from the potential for using quantum mechanical effects to speed-up computation, a model known as _quantum computers_.


## Finite computation recap

The main take-aways from [compchap](){.ref}, [finiteuniversalchap](){.ref}, and [codeanddatachap](){.ref} are:

* We can formally define the notion of a function $f:\{0,1\}^n \rightarrow \{0,1\}^m$ being computed using $s$ basic operations. Whether these operations are AND/OR/NOT, NAND, or some other universal basis does not make much difference. We can describe such a computation either using a _circuit_ or using a _straight-line program_.

* _Every_ function $f:\{0,1\}^n \rightarrow \{0,1\}^m$ can be computed using a circuit of _at most_ $O(m \cdot 2^n / n)$ gates. _Some_ functions require _at least_ $\Omega(m \cdot 2^n /n)$ gates. We define $SIZE_{n,m}(s)$ to be the set of functions from $\{0,1\}^n$ to $\{0,1\}^m$ that can be computed using at most $s$ gates.

* We can describe a circuit/program $P$ as a string. For every $s$, there is a _universal_ circuit/program $U_s$ that can evaluate programs of length $s$ given their description as strings. We use this representation also to prove that some functions cannot be computed by circuit of smaller-than-exponential size.

* If there is a circuit of $s$ gates that computes a function $f$, then we can build a physical device to compute $f$ using about $s$ components (such as transistors). If $f$ is a function for which _every_ circuit requires at least $s$ gates then the "Physical Extended Church-Turing Thesis" postulates that _every_ physical device to compute $f$ will require about $s$ "physical resources". The main challenge to the PECTT is _quantum computing_ which we will discuss in [quantumchap](){.ref}.



## Exercises


::: {.exercise #reading-comp}
Which one of the following statements is false:

a. There is an $O(s^3)$ line NAND-CIRC program that given as input program $P$ of $s$ lines in the list-of-tuples representation computes the output  of $P$ when all its input are equal to $1$.

b. There is an $O(s^3)$ line NAND-CIRC program that given as input program $P$ of $s$ characters encoded as a string of $7s$ bits using the ASCII encoding, computes the output  of $P$ when all its input are equal to $1$.

c. There is an $O(\sqrt{s})$ line NAND-CIRC program that given as input program $P$ of $s$ lines in the list-of-tuples representation computes the output  of $P$ when all its input are equal to $1$.
:::


> # {.exercise title="Equals function" #equals}
For every $k \in \N$, show that there is an $O(k)$ line NAND-CIRC program that computes the function $EQUALS_k:\{0,1\}^{2k} \rightarrow \{0,1\}$ where $EQUALS(x,x')=1$ if and only if $x=x'$.

> # {.exercise title="Equal to constant function" #equalstwo}
For every $k \in \N$ and $x' \in \{0,1\}^k$, show that there is an $O(k)$ line NAND-CIRC program that computes the function $EQUALS_{x'} : \{0,1\}^k \rightarrow \{0,1\}$ that on input $x\in \{0,1\}^k$ outputs $1$ if and only if $x=x'$.



::: {.exercise title="Counting lower bound for multibit functions" #countingmultibitex}
Prove that there exist a number $\epsilon>0$ such that for every $n,m$ there exists a function $f:\{0,1\}^n \rightarrow \{0,1\}^m$ that requires at least $\epsilon m \cdot 2^n / n$ NAND gates to compute. See footnote for hint.^[How many functions from $\{0,1\}^n$ to $\{0,1\}^m$ exist?]
:::

::: {.exercise title="Size hierarchy theorem for multibit functions" #sizehiearchyex}
Prove that there exists a number $C$ such that for every $n,m$ and $s < m\cdot 2^n / (Cn)$ there exists a function $f \in SIZE_{n,m}(C\cdot s) \setminus SIZE_{n,m}(s)$.
See footnote for hint.^[Follow the proof of [sizehiearchythm](){.ref}, replacing the use of the counting argument with [countingmultibitex](){.ref}.]
:::

> # {.exercise title="Random functions are hard" #rand-lb-id}
Suppose $n>1000$ and that we choose a function $F:\{0,1\}^n \rightarrow \{0,1\}$ at random, choosing for every $x\in \{0,1\}^n$ the value $F(x)$ to be the result of tossing an independent unbiased coin. Prove that the probability that there is a $2^n/(1000n)$ line program that computes $F$ is at most $2^{-100}$.^[__Hint:__ An equivalent way to say this is that you need to prove that the set of functions that can be computed using at most $2^n/(1000n)$ has fewer than $2^{-100}2^{2^n}$ elements. Can you see why?]


::: {.exercise }
The following is a tuple representing a NAND program:  $(3, 1, ((3, 2, 2),   (4, 1, 1), (5, 3, 4),   (6, 2, 1),  (7, 6, 6), (8, 0, 0), (9, 7, 8),   (10, 5, 0),   (11, 9, 10))$.

1.  Write a table with the eight values $P(000)$, $P(001)$, $P(010)$, $P(011)$, $P(100)$, $P(101)$, $P(110)$, $P(111)$ in this order.

2.  Describe what the programs  does in words.
:::

::: {.exercise title="EVAL with XOR" #XOREVAL}
For every sufficiently large $n$, let $E_n:\{0,1\}^{n^2} \rightarrow \{0,1\}$ be the function that takes an $n^2$-length string  that encodes a pair $(P,x)$ where $x\in \{0,1\}^n$ and $P$ is a NAND program of $n$ inputs, a single output, and  at most $n^{1.1}$ lines, and returns the output of $P$ on $x$.^[Note that if $n$ is big enough, then it is easy to represent such a pair using $n^2$ bits, since we can represent the program using $O(n^{1.1}\log n)$ bits, and we can always pad our representation to have exactly $n^2$ length.] That is, $E_n(P,x)=P(x)$.

Prove that for every sufficiently large $n$, there _does not exist_ an XOR circuit $C$ that computes the function $E_n$, where a XOR circuit has the $XOR$ gate as well as the constants $0$ and $1$ (see [xorex](){.ref}).  That is, prove that there is some constant $n_0$ such that for every $n>n_0$ and XOR circuit $C$ of $n^2$ inputs and a single output, there exists a pair $(P,x)$ such that $C(P,x) \neq E_n(P,x)$.
:::





## Bibliographical notes {#bibnotescodeasdata }




The $EVAL$ function is usually known as _universal circuit_.
The implementation we describe is not the most efficient known.
Valiant [@Valiant1976] first showed a universal circuit of $O(n \log n)$ size where $n$ is the size of the input.
Universal circuits have seen in recent years new motivations due to their applications for cryptography, see [@lipmaa2016valiant, @Gunther2017] .



While we've seen that "most" functions mapping $n$ bits to one bit require circuits of exponential size $\Omega(2^n/n)$, we actually do not know of any _explicit_ function for which we can _prove_ that it requires, say, at least $n^{100}$ or even $100n$ size. At the moment, strongest such lower bound we know is that there are quite simple and explicit $n$-variable functions that require at least $(5-o(1))n$ lines to compute, see [this paper of Iwama et al](http://www.wisdom.weizmann.ac.il/~ranraz/publications/P5nlb.pdf) as well as this more recent [work of Kulikov et al](http://logic.pdmi.ras.ru/~kulikov/papers/2012_5n_lower_bound_cie.pdf).
Proving lower bounds for restricted models of circuits is an extremely interesting research area, for which Jukna's book [@Jukna12] (see also Wegener [@wegener1987complexity])  provides  very good introduction  and overview.


Scott Aaronson's blog post on how [information is physical](http://www.scottaaronson.com/blog/?p=3327) is a good discussion on issues related to the physical extended Church-Turing Physics.
Aaronson's [survey on  NP complete problems and physical reality](http://www.arxiv.org/abs/quant-ph/0502072) is also a great source for some of these issues, though might be easier to read after we reach [cooklevinchap](){.ref} on $\mathbf{NP}$  and $\mathbf{NP}$-completeness.
