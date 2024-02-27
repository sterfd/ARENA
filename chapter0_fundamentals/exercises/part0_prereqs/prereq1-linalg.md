## The Overall Gameplan

What's the overall map of where we're trying to go today?

It turns out that modern AI/ML relies essentially on matrices everywhere. If we
want to understand what an AI model is doing, then we must understand what a
matrix is doing, in a way that is more comprehensible than simply a blob of
floating point numbers. To understand what a matrix is doing, we must then
understand their theoretical foundations, namely linear algebra.

By doing so, we can demystify matrices away from blobs of floating point numbers
and towards more understandable concepts such as "this matrix represents a
rotation."

By the end of this session you should be able to understand:

1. What a vector space is
2. What a linear function is
3. The relationship between a matrix and a linear function
4. Why matrix multiplication is defined the way it is

I will refrain from using equations too much in this introduction, because
equations can often make it seem like vector spaces only apply to things built
out of numbers when they are in fact more general than that. However, I will
still use them from time to time, because almost all machine learning papers
will use equations. An extremely important skill to learn as an ML practitioner
and technical AI safety practitioner is to not be afraid of equations!

Many programmers' eyes glaze over when it comes to equations, but power through
it! Simply not having an "oh no!" knee-jerk reaction to equations is one of the
biggest superpowers when it comes to reading ML research directly.

## Motivating Vector Spaces

Linear algebra, as the name suggests, is the study of linear functions.

One motivation for vector spaces arises from an intuition that in our world
both addition and multiplication play very central roles. However, it often
seems a lot easier to define how to add things together than it is to define how
to multiply things together.

Let's take colors for example. It seems pretty simple to define what it means to
"add" two colors together: just mix them together! But it's a lot trickier to
define what it means to "multiply" two colors together.

However, there is a restricted form of multiplication that seems much easier to
define in more cases, and that is multiplication by a constant, i.e. scaling a
value. While it seems a bit difficult to understand what it means to "directly
multiply two colors together," saying that I'm "adding 2.0 units of red and 0.9
units of blue" seems a lot easier to understand. There we have a restricted form
of multiplication in the form of "scaling red by 2.0 units" or "scaling blue by
0.9 units."

Other examples of this abound in nature. For example sound. It's not very clear
what it means to multiply two sounds together (although as we'll see later on in
this course it's not impossible to come up with a notion of multiplication
either), but it is much more straightforward to think about what adding two
sounds together is: just play them together at the same time! Then
scaling/multiplying by a constant is just making the sound louder or softer.

The notion of a vector space takes this idea and generalizes it by defining what
it means to "add" two "vectors" and what the word "scaling" means.

A vector space consists of the following elements:

+ A set of elements each called a "vector" along with some operator called
  "(vector) addition," usually denoted with $+$. Vector addition must obey the
  following rules:
    * Our vectors are closed under addition: adding two vectors together must
      always result in another vector
    * Addition commutes: $v + w = w + v$
    * Addition is associative: $v + (w + x) = (v + w) + x$
    * There is a zero vector $0$ such that $0 + v = v + 0 = v$ for all $v$
    * There are additive inverses: for every $v$ there exists a vector $w$ such that $v + w = 0$
+ A set of elements each called a "scalar" along with operations called
  "addition among scalars" and "multiplication among scalars". For almost all purposes in
  machine learning we assume that these scalars are the real numbers and
  addition/multiplication among scalars are just the usual addition and
  multiplication on real numbers. The
  crucial operation is multiplying a vector *by* a scalar. This is
  also often just called "scaling" a vector by a scalar. Apart from being able
  to add and multiply scalars among themselves like we can for the real numbers, multiplying a
  vector by a scalar must distribute over vector addition. That is:
    * $k(v + w) = kv + kw$ for a scalar $k$ and vectors $v$ and $w$
    * $(k_0 + k_1)v = k_0v + k_1v$ for scalars $k_0$ and $k_1$ and a vector $v$
    * We also require that $1v = v$.

Note that the notion of a "vector" here is extremely broad. Vectors do not have
to be vectors in the sense of what we ordinarily consider "mathematical"
objects. We can describe physical phenomena as vector spaces.

For example, let's see how we could define sounds as a vector space, where each
sound is considered a vector, and our scalars are the real numbers. 

+ Vector addition is sound mixing, that is playing one sound over another.
  * Sounds are closed over mixing: if I mix two sounds together I always get
    another sound. Otherwise it's a category error. I can't accidentally mix two
    sounds together and get a color (unless you have synesthesia!)
  * Mixing two sounds commutes: the order we mix sounds doesn't matter
  * Mixing sounds is associative: if I have a lion roar, a car horn, and a violin
    holding a note, no matter if I first mix the roar and horn then violin or if I first
    mix the horn and violin, then the roar we still end up with the same sound.
  * The zero vector is complete silence. Adding silence to any sound never changes
    it.
  * Additive inverses: the same sound just played out of phase is the additive
    inverse to the original sound. If you play the two together you get
    silence. This is the principle behind noise cancellation. Every sound has an
    inverse because every sound can be shifted out of phase.
+ Scalar multiplication is increasing the volume of the sound:
  * It distributes over vector addition: if you double the volume of one sound
    and then double the volume of another sound and mix them together, it's the
    same thing as if you had mixed them together first and then doubled the
    volume of the final result.

But we can also define more common "mathematical" objects as vector spaces as
well.

The most common example of this is $R^n$. $R^n$ consists of the set of
real-valued n-tuples. For example vectors in $R^2$ are pairs that look like
$(0.1, 0.2)$ or $(-5, 91.1)$. Vectors in $R^3$ are triplets that look like $(2,
1, 0.5)$ and so on and so forth. For $R^n$, we define vector addition as
element-wise addition, and scalar multiplication as element-wise multiplication
by that scalar. E.g. $(x_{0}, x_{1}, \ldots, x_{n}) + (y_{0}, y_{1}, \ldots,
y_{n}) = (x_{0} + y_{0}, x_{1} + y_{1}, \ldots, x_{n} + y{n})$.

That is $(x_i)_{i=0} ^n + (y_j)_{j=0}^n = (x_i + y_i)_{i=0}^n$.

For a scalar $k$, $k(x_0, x_1, \ldots, x_n) = (kx_0, k_x1, \ldots, kx_n)$, or
equivalently $k(x_i)_{i=0} = k(x_i)_{i=0} ^n$.

So for example $(1, 2) + (2, 3) = (3, 5)$ and $3 \cdot (1, 2) = (3, 6)$.

$R^n$ by far is the most common vector space you will see in machine learning,
so we'll spend just a few more paragraphs on it.

Note that to properly define a (real, i.e. using real numbers as scalars)
vector space, we must always specify not only what the vectors are, but also
what exact operation vector addition corresponds to as well as what exact
operation scalar multiplication corresponds to.  However, as you read machine
learning papers, you may notice that a paper only says "take the vector space
such-and-such," that is they only define the vectors and don't define vector
addition and scalar multiplication. This is because by convention, we almost
always use certain vector addition definitions and scalar multiplication
definitions for certain sets of vectors.

So for example, technically speaking, just saying $R^2$ forms a vector space is a
category error, since $R^2$ merely describes a set of vectors and there are
many different notions of vector addition and scalar multiplication that we
could add to the set to fully form a vector space. For example, we could define
$(x_0, y_0) + (x_1, y_1) = (x_0 + y_1, y_0 + x_1)$.

But almost always, the vector addition we use on $R^2$ is $(x_0, y_0) + (x_1,
y_1) = (x_0 + x_1, y_0 + y_1)$ and scalar multiplication will be $k(x, y) =
(kx, ky)$. Therefore by convention we often just say the "vector space $R^2$" and
implicitly assume that vector addition and scalar multiplication are exactly
those operators. This is strictly speaking an abuse of terminology, or at least
ambiguous, as $R^2$ is a set of vectors, not a vector space itself, but it is
*extremely* common and so is widely used and accepted in almost all fields that
use linear algebra.

However, it is *always* a valid question when someone simply defines a set of
vectors as a vector space to ask the speaker to clarify which vector addition
and scalar multiplication operators are intended.

As a reminder, we're focusing mainly on real vector spaces, i.e. those vector
spaces whose scalars are real numbers, because those are the main spaces we
deal with in machine learning. Nonetheless the concept of a vector can use
other scalars as well. A common other set of scalars that are sometimes used
are the complex numbers. While this is useful in physics, this shows up rather
rarely in machine learning, so we won't talk about them.

<details>
<summary>Optional Aside</summary>
For those who would like to read more about the general concept of scalars for a
vector space, the term here is "field", that is any field can be used as
scalars. See
[https://en.wikipedia.org/wiki/Field_(mathematics)]("https://en.wikipedia.org/wiki/Field_(mathematics")).
</details>

*Exercise*:

> Let's say I instead defined additon on $R^2$ to be element-wise maximums.
> That is $(x_0, y_0) + (x_1, y_1) = (\text{max}(x_0, x_1), \text{max}(y_0,
> y_1))$. We keep scalar multiplication the same as the usual definition. Is
> this a valid vector space?

<details>
<summary>Solution</summary>
No this is not. There is no zero vector. In particular, if we specify any $(x,
y)$ to be the zero vector, $(x - 1, y - 1)$ will always be less than it, so
vector addition of $(x, y)$ and $(x - 1, y - 1)$ will result in $(x, y)$,
which violates what a zero vector is for $(x - 1, y - 1)$ (adding a zero vector
to $(x - 1, y - 1)$ should still return $(x - 1, y - 1)$).
</details>


*Exercise*: 

> Let our vectors be the positive, non-zero real numbers. How can we
define vector addition and the zero vector such that this forms a real vector
space? Remember our zero vector cannot be the real number $0.0$ because we have
only the positive, non-zero real numbers. How can we define scalar
multiplication using all real numbers (positive, negative, and zero) as our
scalars (so our vectors are the positive real numbers while our scalars are all
possible real numbers)?

<details>
<summary>Hint</summary>
Let's say that our zero factor is the real number $1.0$. Since if $+$ is the
usual addition on real numbers, $x + 1 \not= x$, to minimize confusion we'll
replace the usual $+$ notation with a $\circ$. What operation would cause $1.0
\circ v = v \circ 1.0 = v$? Does this operation satisfy all the other
requirements for vector addition? If you use this operation, what does scalar
multiplication become?
</details>

<details>
<summary>Solution</summary>
Vector addition is real number multiplication. The zero vector is $1.0$. Scalar
multiplication is exponentiation, where the exponent is the scalar and the base
is the vector. Note that a positive real number raised to any exponent, positive
or negative, is always still positive. Therefore we are closed under scalar
multiplication. Verifying the distributive properties relies on how
addition of exponents turns into multiplication of elements.

In particular:

+ Vectors are closed under addition: the product of two positive real numbers is always another positive real number
+ Vector addition commutes: real number multiplication commutes
+ Vector addition is associative: real number multiplication is associative
+ There is a zero vector: let the zero vector be the real number $1.0$ and
  notice that $1.0 \cdot r = r \cdot 1.0 = r$ for all positive real numbers $r$.
+ There are (vector) additive inverses: there are multiplicative inverses for
  every positive real number $r$, namely $\frac{1}{r}$.
+ There are 
</details>

*Exercise*:

> Let's turn our attention to the Fibonacci numbers, i.e. the numbers $1, 1, 2, 3,
5, \ldots $ where the $i$-th element $x_i$ is defined as $x_{i - 1} + x_{i -
2}$, except for $x_0$ and $x_1$, which here are $1$ and $1$.
>
> Notice that the first two elements of the Fibonacci sequence ($1$ and $1$) are
essentially an arbitrary choice. We can always generate a Fibonacci-like
sequence from any two starting real numbers. E.g. if we chose $100.2, 1.1$  as our
first two elements, we would have the sequence $100.2, 1.1, 101.3, 102.4, 203.7, 306.1,
\ldots$. Or if we chose $-5$ and $3$ as our first two numbers we would have $-5,
3, -2, 1, -1, 0, -1, -1, -2, \ldots$.
>
> Let $F_{x, y}$ denote the Fibonacci sequence starting with the real numbers $x$
and $y$. How can you form a real vector space where each vector is a sequence
$F_{x, y}$ for some $x$ and $y$?

Let's turn our attention now to how we can specify a vector space without
needing to exhaustively list every single vector, via a basis.

A basis can be thought of as the "fundamental set" of vectors that carries all the
information about a vector space, such that every vector can be reduced to a sum
of those vectors.

More formally, a basis is a minimal set of vectors $v_0, \ldots, v_n$ such that
every vector in our vector space can be written as a scaled sum of just those
$n$ vectors, i.e. as $k_1v_1 + k_2v_2 + \cdots + k_nv_n$ for some $k_1, \ldots,
k_n$. When we say it is "a minimal set," what we mean is that if you removed
any $v_i$ from that set, this property would no longer hold true.

<details>
<summary>Terminology Aside</summary>
<p>
The set of all vectors $W$ that can be formed via scaled sums of some other set
of vectors $V$ is known as known as the span of $V$. Hence a more compact way
of describing a basis is that is a minimal set of vectors whose span is the
entirety of the vector space, or equivalently that it is a minimal set of
vectors that spans the vector space.
</p>

<p>
Yet another piece of terminology is linear independence. We say that a vector
$v$ is linearly independent of a set of other vectors $V$ if there is no way to
write $v$ as a scaled sum of the vectors in $V$. We can also say that a set $V$
is linearly independent if no vector in $V$ can be written as the scaled sum of
the other vectors. Therefore another way we could define a basis is to replace
the word "minimal" with "linearly independent" and say that a basis is a
linearly independent set of vectors that spans a vector space.
</p>

<p>
Finally though I've been using the term "scaled sum," the more common term for
$k_0 v_0 + k_1 v_1 + \cdots$ is a linear combination of the vectors $v_0, v_1,
\ldots$.
</p>
</details>

For example for $R^3$, the most commonly used basis is the set $\{(1, 0, 0),
(0, 1, 0), (0, 0, 1)\}$. It is so common that it is often called $R^3$'s
"standard basis."

The scaled sum/linear combination used to form any other vector is just using
the components of the other vector as scalars. For example to write $(4, 3,
-2)$ using this basis, we can just write it as $4(1, 0, 0) + 3(0, 1, 0) +
(-2)(0, 0, 1)$.

On the other hand $\{(1, 0, 0), (0, 1, 0), (0, 0, 1), (1, 0, 1)\}$ is not a
valid basis. This is because it is no longer a minimal set, since we can omit
$(1, 0, 1)$, without harming our ability to represent every vector as a scaled
sum of this set of vectors.

*Exercise*:

> Let's say for some reason we really wanted to keep $(1, 0, 1)$. 
> + What other vector could we throw out to this set minimal again, i.e. to
>   make a valid basis? 
> + Are there multiple choices to the question above?
> + Which vector(s) cannot be thrown out and still maintain our property that
>   every vector can be written as a scaled sum/linear combination of this set?
> + Using your new basis, can you write $(4, 3, -2)$ as a scaled sum of that basis?

<details>
<summary>Solution</summary>

+ You could throw away $(1, 0, 0)$, $(0, 0, 1)$, or $(1, 0, 1)$ (but not more
  than one of them!).
+ Yes.
+ You cannot throw away $(0, 1, 0)$. If you do, you cannot form any vector with
  a non-zero second component from a scaled sum of the resulting set of vectors.
+ If we take $\{(1, 0, 0), (0, 1, 0), (1, 0, 1)\}$, we can write $(4, 3, -2) =
  6(1, 0, 0) + 3(0, 1, 0) + (-2)(1, 0, 1)$.

</details>

As you may have noticed from that exercise, there is no single basis for a
vector space. Every vector space has many possible bases (unless the vector
space itself consists of only one vector).

This raises the question "do all bases of a vector space have the same number
of vectors?" The answer is yes. It is not obvious that this is true and proving
it may be a bit tricky if you're new to this stuff. For our purposes we'll just
state this by fiat, but if you are so inclined, you may want to prove this as
an optional exercise.

The following three exercises are optional. It may be useful to look at them to
understand facts about vector spaces even if you don't prove them.

<details>
<summary>Optional Exercise</summary>
Prove that for a given vector space, if all its bases contain a finite number of
vectors, all its bases must be of the same size. That is all its bases must
have the same number of vectors.
</details>

<details>
<summary>Optional Exercise</summary>
Note that this requires some familiarity with infinite sets and cardinality.
Just ignore this exercise if you don't have this familiarity.

Prove that if a vector space has a basis, even if infinite, all its bases must
be of the same size (i.e. cardinality).
</details>

<details>
<summary>Optional Exercise</summary>
Note that this will require knowledge of the Axiom of Choice
[https://en.wikipedia.org/wiki/Axiom_of_choice](https://en.wikipedia.org/wiki/Axiom_of_choice).
If you are not familiar with set theory axioms, don't worry about completing
this.

Prove that all vector spaces have a basis.
</details>

Because all vector spaces have a basis and because all the bases of a vector
space must be of the same size, we can talk about "the" size of a vector
space's bases, even though the bases themselves may consist of different
vectors. We call the size of a vector space's bases the dimension of a vector
space.

This leads to a very nice statement when it comes to $R^n$. The dimension of
$R^n$ is exactly $n$.

*Exercise*:

> Prove this. That is prove that any basis of $R^n$ must have exactly $n$ vectors.

<details>
<summary>Hint</summary>
We already know that all bases of a vector space must have the same number of
elements, so all that's needed is to find a basis of $R^n$ that has $n$
vectors.
</details>

*Exercise*:

> Can you come up with any other alternative basis for $R^3$ that aren't its
> standard basis? Can you come up with a basis that doesn't have any zeroes in
> any of its triplets' components?

<details>
<summary>Solution</summary>
One possible alternative basis for $R^3$ is to simply take the standard basis
and double one of the vectors, e.g. so that we have $(2, 0, 0)$, $(0, 1, 0)$,
and $(0, 0, 1)$.

One possible basis that has no zeroes in any of its triplets is $\{(1, 1, 1),
(2, 1, 1), (1, 1, 2)\}$.
</details>

Because every basis for a vector space has the same length, we can use this
length to define a global property about the vector space in question. The
length of every basis of a vector space is called the dimension of that vector
space.

## Linear Subspace

A (linear) subspace of a vector space is a subset of vectors such that the
subset remains closed under vector addition and scalar multiplication from the
original vector space. That is for any two vectors contained in the subset,
their sum is also contained in the subset. For any vector contained in the
subset, any rescaling of that vector by scalar multiplication must also be in
the subset.

So for example, in $R^2$, the set of vectors that all have a zero in their
second component, i.e. the set $(x, 0)$ for all $x \in R$, is a
(linear) subspace of $R^2$.

*Exercise*:

> Given the vector space $R^2$ with the usual addition and scalar multiplication
> operations, which one of the following subsets of $R^2$ are valid linear
> subspaces and which aren't? For the ones that aren't, can you give examples
> that violate closure of vector addition or closure of scalar multiplication?
>
> + The set of all vectors $(x, 1)$ for $x \in R$, i.e. vectors
>   that have a one in their second component.
> + The set of all vectors $(x, y)$ such that $x$ and $y$ are both even numbers.
> + The set of all vectors $(x, y)$ such that $x$ and $y$ are equal to each
>   other.

<details>
<summary>Solution</summary>
The set $\left\{ (x, 1) \vert x \in R \right\}$ is not a valid linear subspace
of $R^2$ under the usual vector addition and scalar multplication operators. It
is not closed under scalar multiplication or vector addition.
If I multiply by a scalar constant $k$ I get $k(x, 1) = (kx, k)$, which is not
a part of $\left\{ (x, 1) \vert x \in R \right\}$ if $k /not= 1$. Likewise if I
add two vectors $(x, 1)$ and $(y, 1)$ I get $(x + y, 2)$, which is not a member
of the subset.

The set of all vectors $(x, y)$ such that $x$ and $y$ are both even numbers is
also not a valid linear subspace of $R^2$. This is not closed under scalar
multiplication since I can multiply e.g. $0.5 \cdot (2, 2) = (1, 1)$ , which is
not contained in my subset.

The set of all vectors $(x, y)$ such that $x$ and $y$ are equal to each other
is a valid linear subspace of $R^2$. Given $(a, a)$ and $(b, b)$, adding them
together results in a tuple whose two components are still equal to each other,
namely $(a + b, a + b)$, same for multiplying by a scalar.
</details>

## Linear Functions

Linear functions are intimately tied to the notion of vector spaces. They are
functions from a vector space to another vector space that "preserve" all the
properties of vector addition and scalar multiplication.

But first let's get some preliminaries about functions out of the way.

I'll often write a function's "type signature" as $f: V \to W$, where $V$ is
the input vector space and $W$ is the output vector space.

We call the former the domain of a function and the latter the codomain of a
function. This is exactly the same as programming, where we might have a
function `f : Int -> String`.

Also like in programming `f : Int -> String` doesn't mean that `f` can output
every string. We might define `f(x) = "hello"`. It is hence useful to
distinguish the set of values that a function can output from the full set of
values denoted by its "type signature." This is called the range of a function.
So in this case, even though the codomain of `f` is `String`, i.e. all strings,
its range is only the set consisting of the single `"hello"`.

Similarly for vector spaces, the range of a function `f` is not necesssarily
its codomain; it can be a strict subset of the codomain.

Let's return our attention to specifically linear functions.  Another way of
saying that linear functions preserve vector addition and scalar multiplication
is that linear functions distribute over vector addition and scalar
multiplication. In particular,

> A function f $f$ is linear if and only if $f(k_0v_0 + k_1v_1) = k_0(f(v_0)) +
> k_1(f(v_1))$.
>
> Specialized just to vector addition this means $f(v_0 + v_1) = f(v_0) +
> f(v_1)$.
>
> Specialized just to scalar multiplication this means $f(k_0v_0) = k_0f(v_0)$.

Let's look at some examples of this.

The function $f : R^2 \to R$ that sums its components together, so that $(x, y)
\mapsto x + y$ is a linear function. For example for $f(2 \cdot (1, 3) +
3\cdot(4, 2))$, I could calculate this  starting inside the $f$, so that I get
$f((2, 6) + (12, 6)) = f((14, 12)) = 26$.

I could also calculate this by first distributing $f$, so that I get $2f((1,
3)) + 3(f(4, 2)) = 2\cdot 4 + 3 \cdot 6 = 26$.

Since $f$ is a linear function, we see that we get the same answer either way.
Note that $+$ in $f(2 \cdot (1, 3) + 3\cdot(4, 2))$ refers to vector addition
in $R^2$, while $+$ in $2f((1, 3)) + 3(f(4, 2))$ refers to vector addition in
$R$.

The range of a linear function is always a linear subspace of its codomain.

*Exercise*:

> Prove this. Namely given two vector spaces $V$ and $W$ as well as a linear
> function $f: V \to W$, prove that the range of $f$ is a linear subspace of
> $W$. That is prove that if any $w_0$ and $w_1$ are in the range of $f$, their
> linear combination must also be in the range of $f$.

<details>
<summary>Solution</summary>
Take two vectors from $f(W)$, i.e. the range of $f$, $w_0$ and $w_1$. Examine
their linear combination as represented by $z = k_0w_0 + k_1w_1$ for scalars
$k_0$ and $k_1$.

Since we know that $w_0$ and $w_1$ are in the range of $f$, there must have
been at least two (although possibly more) vectors $v_0$ and $v_1$ such that
$f(v_0) = w_0$ and $f(v_1) = w_1$.

We can thus write $z = k_0f(v_0) + k_1f(v_1)$. By using the distributive
property of linear functions, but just reversing the direction of equality, we
get that $z = f(k_0v_0 + k_1v_1)$, which implies that there is a vector which
$f$ maps to $z$, that is $z$ is in fact in the range of $f$.
</details>

If we restrict our attention to $R^n$, we can get a more geometric
interpretation of linear functions as laid out in
[https://www.youtube.com/watch?v=kYB8IZa5AuE](https://www.youtube.com/watch?v=kYB8IZa5AuE).
If you'd like, you can watch that video, and then think about how to prove the
statements said in that video using our definition of linear functions.

*Exercise*:

> Which of the following functions on $R \to R$ is linear according to the
> definition we've given? For those which aren't linear, can you examples of $x$
> that violate our linearity requirements?
> 
> $f(x) = x$
>
> $f(x) = 2x$
>
> $f(x) = 2x + 1$
>
> $f(x) = x^2$

*Exercise*:

> Which of the following transformations $R^2 \to R^2$ is linear according to the
> definition we've given?
>
> 1. Rotating all our points by 90 degrees around the origin.
> 2. Point-wise doubling all the components of every point.

## Matrices as notation

We use notation for all sorts of things in life. For example, we use *numerals*
as notation for *numbers*, but it is important to distinguish between the two.
6, 110, and VI are all different numerals, but they all represent the same number.

Matrices, as we shall see, spring from the same intuition, and are a form of
notation for linear functions. In particular, just like we have different base
systems for numbers, such as base-10 or base-2, that result in different
numerals for the same number, matrices constructed with different basis vectors
will result in different matrices for the same linear function.

In practice, much like how we often assume that a given numeral is a base-10
numeral without additional elaboration, when working with $R^n$, we generally
assume that a given matrix uses the standard basis vectors.

Just like how numerals allow us to perform arithmetic computation with numbers,
we shall see that matrices allow us to perform computation with linear
functions.

However, when we move away from direct computation, it will often be easier to
understand what a matrix is "really doing," by understanding what the function
it notates is doing, rather than getting bogged down in a two-dimensional array
of floating point numbers.

With that in mind, let's return to linear functions. So far we've been defining
our linear functions either as equations, or as English prose. The latter can
be very difficult to actually perform calculations and as for the former, it's
not obvious how to write a linear function as a single equation.

Our motivating question is going to be "can we define a notation to calculate
linear functions for arbitrary linear functions?"

To begin with, let's remind ourselves that given a basis for a vector space,
any vector in that vector space can be written as a linear combination of those
basis vectors. This is somewhat analogous to how a base-n numeral system lets you
write any number as a combination of $n$ numbers which forms a numeral.

And just like how if you know how to add each individual number of the $n$
numbers that form a base-n numeral system, via some extrapolation rules, you
can define how to define how to add any two numbers by manipulating the base-n
numerals.

In particular, notice that as long as we define what a linear function $f$ does to
each individual basis vector $v_i$, i.e. we know $f(v_i)$ we can use the fact
that linear functions distribute over linear combinations to describe what the
linear function does to any vector $w$ via the following steps:

+ Define $w = k_0v_0 + k_1v_1 + \cdots + k_nv_n$
+ Hence $f(w) = f(k_0v_0 + k_1v_1 + \cdots + k_nv_n)$
+ By linearity $f(k_0v_0 + k_1v_1 + \cdots + k_nv_n) = k_0f(v_0) + k_1f(v_1) + \cdots + k_nf(v_n)$
+ We know what the values $f(v_i)$ are, so as long as we know how to scale and
  add those values together, we know what the final result should be.

Let's take the linear function $f : R^2 -> R^2$ that rotates all points by 90
degrees around the origin to illustrate how this works. If you haven't seen it
before, it might not be obvious how to write down $f$ in a way that let's us
calculate what $f$ should do to any arbitrary value of $R^2$.

Let's first instead just specify what $f$ should do the standard basis of
$R^2$. It should map $(1, 0)$ to $(0, 1)$ and it should map $(0, 1)$ to $(-1,
0)$.

Based on that let's take an arbitrary element of $R^2$, $(1.2, 3.1)$ and figure
out what $f$ maps it to.

+ Define $(1.2, 3.1) = 1.2 \cdot (1, 0) + 3.1 \cdot (0, 1)$
+ Hence $f((1.2, 3.1)) = f(1.2 \cdot (1, 0) + 3.1 \cdot (0, 1))$
+ By linearity $f(1.2 \cdot (1, 0) + 3.1 \cdot (0, 1)) = 1.2f((1, 0)) + 3.1f((0, 1))$
+ But we know $f((1, 0)) = (0, 1)$ and $f((0, 1)) = (-1, 0)$
+ So $1.2f((1, 0)) + 3.1f((0, 1)) = 1.2 \cdot (0, 1) + 3.1 \cdot (-1, 0) =
  (-3.1, 1.2)$

Great so just by knowing what $f$ does to basis vectors, and knowing how to
write every vector as a linear combination of basis vectors, we know how to
calculate how to calculate any linear function $f$!

*Exercise*:

> Can you do the same calculation for the same $f$, but now for an alternative basis
> consisting of $(0.6, 0)$ and $(0, 1.55)$? You should end up with the same
> answer $(-3.1, 1.2)$ at the end of the day.

*Exercise*:

> Let's use the basis vectors $(1, 0)$ and $(0, 1)$. Remember that rotating $(1,
> 0)$ gets you $(0, 1)$ and rotating $(0, 1)$ gets you $(-1, 0)$. Can you
> describe what the result is of applying the 90-degree rotation function $f$ to
> the vector $(-1, 3)$?
>
> Can you do the same thing now but using the basis vectors $(1, 1)$ and $(1,
> 2)$?

An easy way to notate what a linear function does is via a table that shows
what we do to each basis vector. For example, we could write down our previous
function that rotates all vectors by 90 degrees using the following table. Each
column header of this table is a basis vector in the domain of the function and
each value below the header is the vector that we map that basis vector to via
$f$.

| (1, 0) | (0, 1) |
| ------ | ------ |
| (0, 1) | (-1, 0)|

*Exercise*:

> Can you write down what the table should look like for rotation by 90 degrees
> if I use $(1, 1)$ and $(1, 2)$ as my basis vectors instead?

*Exercise*:

> Can you write down what the table should look like for rotation by 180 degrees
> if I use $(1, 0)$ and $(0, 1)$ as my basis vectors?

Note that all this holds even for times where $f$ does not have the same domain
and codomain. For example, let's take the linear function $f : R^2 \to R$ such
that $(x, y) \mapsto x + y$. Its table for the standard basis on $R^2$ would
look like the following:

| (1, 0) | (0, 1) |
| ------ | ------ |
| 1 | 1 |

Note that these tables are an "isomorphic" description of linear functions.
That is every linear function can be described with such a table and every such
table is a valid linear function.  For example, 

| (1, 1) | (2, 1) |
| ------ | ------ |
| (2, 1, 3, 9) | (9, 2, 1, -1) |

describes some linear function $f : R^2 \to R^4$, even if I don't know how to
describe this function in English or with traditional equations!

This is very powerful, since we now have a rigorous way of notating *any*
linear function in a way that is amenable to performing computation.

We don't even have to stick to $R^n$! For example the following table describes
a linear function on some vector space of a restricted set of sounds!

| sounds of frequency 90 hertz | sounds of frequency 180 hertz |
| ---------------------------- | ----------------------------- |
| sounds of frequency 40 hertz | sounds of frequency 300 hertz |

But it turns out we can actually break down this notation even further. So far
we've been taking advantage of the fact that every vector in the domain of a
function can be written as a linear combination of vectors, but we can notice
that the same is true of a function's codomain. Every vector in a function's
codomain (and therefore every vector in a function's range), can be written as
a linear combination of basis vectors as well!

So for example, we could write 

| (1, 0) | (0, 1) |
| ------ | ------ |
| (0, 1) | (-1, 0)|

as 

| (1, 0) | (0, 1) |
| ------ | ------ |
| $0 \cdot (1, 0) + 1 \cdot (0, 1)$ | $-1 \cdot (1, 0) + 0 \cdot (0, 1)$ |

If we give each component of the liner combination its own row we get

| (1, 0) | (0, 1) |
| ------ | ------ |
| $0 \cdot (1, 0)$ | $-1 \cdot (1, 0)$ |
| $1 \cdot (0, 1)$ | $0 \cdot (0, 1)$ |

Finally if just tell the reader of the table separately what the basis vectors
are, i.e. tell the reader "this table is using the standard basis of $R^2$ for
its domain and the standard basis of $R^2$ for its codomain," we can get rid of
explicitly writing the basis vectors.

| Column 0 | Column 1 |
| ------ | ------ |
| 0 | -1 |
| 1 | 0 |

And that's a matrix!

```math
\begin{bmatrix} 0 & -1\\\ 1 & 0 \end{bmatrix}
```

*Exercise*:

> Given the basis vectors $(1, 0)$ and $(0, 1)$ for both the domain and codomain
> of $f$ and still taking $f : R^2 \to R^2$ as the function that performs a 180
> degree rotation, what is the resulting matrix that describes $f$?
>
> What about if I change the basis vectors to $(1, 1)$ and $(1, 2)$ for both the
> domain and codomain?
>
> What about if the basis for the domain is $(1, 0)$ and $(0, 1)$ but the basis
> vectors for the codomain are $(1, 1)$ and $(1, 2)$?

*Exercise*:

> Can you write what linear function this matrix corresponds to in English
> , assuming that our basis for both the domain and codomain are the standard
> basis $(1, 0, 0)$, $(0, 1, 0)$, $(0, 0, 1)$?
>
> ```math
> \begin{bmatrix} 1 & 0 & 0\\\ 1 & 0 & 0 \\ 1 & 0 & 0 \end{bmatrix}
> ```

Note that unlike the function itself (e.g. "rotates points by 90 degrees around
the origin") or the dimension of a vector space, a matrix is basis-dependent.
That is, different basis vectors will generate different matrices for the same
function between the same vector spaces.

As stated before, this is very similar to base-n notation for numerals, where
different bases will give rise to different numerals for the same number. And
again, much like how base-10 is the usual assumed base for a numeral if no
additional clarification is provided, if we are working with a function $f :
R^n \to R^m$ and no additional clarification is provided, we generally assume
that the bases for $R^n$ and $R^m$ are their respective standard bases.
