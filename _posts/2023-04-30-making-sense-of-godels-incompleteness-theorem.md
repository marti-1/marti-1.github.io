---
layout: default
title: Making Sense of Gödel's Incompleteness Theorem
---

My first encounter with Gödel's Incompleteness Theorem was when I was reading "Gödel, Escher, Bach: an Eternal Golden Braid" by Douglas Hofstadter. On page 18 he immediately hits you with the following statement:

> This statement of number theory does not have any proof in the system of _Principia Mathematica_.

Apparently, Gödel encoded this sentence into a valid mathematical statement of [Principia Mathematica](https://en.wikipedia.org/wiki/Principia_Mathematica) (PM) and thus proved that PM is incomplete. What’s also interesting is that PM was designed in such way so that self-reference could not be expressed in its language —

<img src="/assets/img/making_sense_of_godels_incompleteness_theorem/huh.gif" width="400">

First of all, how can you express self-reference using mathematical notation? Second, how can you express that something can or can't be proved by some system mathematically? Finally, how can you do it in a system that forbids self-reference to begin with?

Personally, I am the level of a mathematician that can only handle "4 is even" statements. That’s easy 😂:

$$
\exists e:(SS0*e)=SSSS0
$$

But the one above was too meta.

## Mathematics is a Science of Definitions

I think the really important thing to internalize about mathematics is that formally as a system it only deals with definitions. A mathematical system (number theory, logic, set theory, etc) always starts with a list of initially defined truths (_axioms_) and some initially defined _rules_ that let you create new definitions or truths (_theorems_). Thus you use definitions to create definitions.

The scientific part comes when somebody gives you some strange but correctly expressed definition in a system like number theory (NT); if a chain of rules being applied on some of the axioms can be found that lead to the provided statement, then that statement passes the test of truth.

## Valid Definitions in PM

The condition that PM had for its definitions was that they could not be _recursively_ defined. If you are a programmer, a simple rule to tell whether a definition is allowed in PM or not, is to ask yourself if it can be implemented without while loops or recursion.

For instance, say you have two definitions `SUB(a,a,262)` and `PROOF(x,z)`. The `SUB(a,a,262)` definition says that there is a number which is a result of every sequence of digits '262' being replaced by the number itself. You can implement this with a for-loop of length size(y)/3. The `PROOF(x,z)` definition says that $x$ has a path from axioms to $z$, where every step comes from applying one of the defined rules. Both the number of possible axioms, rules, and steps in the path are known ahead of the check, thus again can be implemented by using multiple for-loops. 

There are also invalid definitions, like `PROOF(z)`. It is invalid because it is impossible to know ahead of time how many times rules will have to be applied. If $z$ is not a theorem, the search might never terminate. You can’t implement this without using the recursion.

## Completeness and Consistency

_Completeness_ and _consistency_ are themselves definitions, or rather meta definitions, since they are definitions about formal systems that deal with definitions. They are as follows:

* Completeness — all statements that are true in a real (or imaginable world) should also be true.
* Consistency — both G & ~G can't be theorems, e.g.: "0=0" and "~(0=0)" can't both be true. 

In a nutshell, completeness means "Can I express all the truths that I see in the world?", whilst consistency means "There are no logic bombs amongst those truths."

## Interpretations of Definitions

The most important part, in my opinion, to understand the theorem, is to realize that definitions have multiple valid interpretations, even in mathematics. To use a concrete example, say you and your friends use a special code made from numbers that you use in your math class. For example it could look something like this:

* 0 → 666
* S → 123
* = → 111
* …
* a → 262
* ‘ → 163

One day your math teacher writes "a=0" on a blackboard and asks you to write the correct number in place of the symbol "a". The number that you write is "262,111,666", so the final result looks like this: 

$$
262,111,666=0
$$

As your math teacher is contemplating his existance, your friends can see what just happened. Using your secret interpration they can see that the statement actually reads:

$$
(a=0)=0
$$

which in itself can also be interpreted as saying “The result of 262,111,666=0 is equal to 0”, which in turn — "**I** am equal to 0".

Thus, from your math teacher's point of view you wrote some nonsensical answer, namely that some large number is equal to 0. However, from your friends point of view you wrote a self-referential statement. We can further define the teacher's interpretation as a literal or _mechanical_, whilst you and your friend's interpretation as "in between the lines" or _intelligent_. In theory, computers are only capable of the mechanical interpretation, whilst you humans can do both. 

And this is how Gödel broke PM.

## Gödel's Code

If you think about it, application of definitions in mathematics is just symbol shunting. All you really do is just add and remove symbols in some predefined manner. That's what multiplication, addition and division does too. Thus to break PM, Gödel devised a codebook for PM's different symbols, converting all the axioms into numbers and all the rules as arithmetical operations. All of a sudden, you could write down an NT statements that looked like some innocent addition and multiplication, and NT would think it is just doing arithmetic, whilst in reality it would be talking about itself. 

## Encoding Self-Reference in PM

First, let's do a recap. Previously we saw a valid definition of PM called `SUB(a, a, 262)`. Just to have some useful visual before we continue, let's initialize a with some random number and see what we get:

$$
\begin{align*}
a&=262,111,666 \\
SUB(a,a,262) &\Rightarrow  \textbf{262,111,666},111,666
\end{align*}
$$

Cool. Let's start with a sentence, call it the _uncle_:

> The formula with Gödel's number SUB(a,a,262) cannot be proved in PM.

This can be expressed in the PM’s notation as follows:

$$
\sim(\exists x):\text{PROOF}(x, \text{SUB}(a,a,262))
$$

Note: `SUB(a,a,262)` and Gödel's number of `SUB(a,a,262)`, can be referred to interchangeable, since both are the same thing expressed in a different notation.

$a$ is undefined, in the above formal definition of the sentence, it can be any Gödel’s number. The definition can be converted into Gödel's number, which we can call u. We can use u to write another sentence, call it _G_ for "Gödel's sentence":

$$
G=\text{SUB}(u, u, 262)
$$

The substitution operation on the number u results in a new number whose English interpretation has the symbol a replaced by the u, thus resulting in the following statement:

> The formula with Gödel's number SUB(u,u,262) cannot be proved PM

Again, we can convert this statement into Gödel's number, call it g. What is that number? Well, it is `SUB(u,u,262)` expressed as Gödel's number. It is a bit loopy, but if you stare at it long enough, after a while the statement "says"

<center>"I can't be proved in PM"</center>

## Incompleteness and Undecidability

Ok, so is G a theorem? If yes, it means it can't be proven, thus ~G. That’s inconsistent. If it is ~G, it means that can be proved, thus G. If it is G, then it means... well you get the point, it is undecidable within PM.

However, we know that the statement is true in reality (outside the system), because that's exactly what it is trying to tell us, that it can't be proven! So PM is incomplete — there exists a truth in the real world that doesn't “exist” in PM.

## In Retrospect

Once the proof "clicks", it starts to make sense why Hofstadter chose this theorem as a centerpiece of the masterpiece that GEB is. It also makese sense why he spent so much time further writing about it and discussing it afterwards. If you stare long enough into the proof, the proof stares back at you.

In the book itself Hofstadter discusses how this theorem is like one of those optical illusions:

<img src="/assets/img/making_sense_of_godels_incompleteness_theorem/Spinning_Dancer.gif" width="300">

If you stare long enough the rotation direction switches, it goes from clockwise to counter-clockwise, then back again. Similarly the the proof of the Gödel's theorem, if you keep _looping_ back and forth between different levels of interpretation you can actually start to see how "I" **emerges**. Thus the theorem itself can be used as a potential explanation of how the conscious "I" can appear seemingly out of a dead/inanimate space. No matter, if it is some biological mush or electronic circuit.