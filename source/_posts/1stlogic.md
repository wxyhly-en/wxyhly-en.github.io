---
title: Introduction to Axiomatic Set Theory
tags:
  - Mathematical Logic
  - Mathematics
  - Games
index_img: /img/1stlogic001.png
date: 2024-11-17 17:55:32
excerpt: Formal systems were proposed to check proofs. I’ll start with simple formal systems then to propositional logic and ZFC set theory. I also built a web-based simulator, which later evolved into a game called Deductrium.
---

<span class="likecode">// Note: This article is merely a rough summary of the author's recent study of set theory. If you want to delve deeper, I strongly recommend finding a mathematical logic textbook for systematic study. Wikipedia is also good.</span>

As we all know, what distinguishes mathematics from other disciplines most importantly is rigor. For example, [the previous article](/archives/ggg-ord/) introduced ordinals and cardinals that transcend infinity, where the slightest carelessness can easily lead to errors. As mathematical theories and proof processes become increasingly complex, mathematicians urgently need a system that can mechanically standardize proofs, making it convenient for machines to verify them. Before the invention of computers, someone proposed it—the **formal system**. This article will start with simple formal systems, introducing propositional logic, first-order logic, Peano arithmetic, and finally **ZFC set theory**, the formal system that appears most frequently in mainstream mathematics.

With the goal of learning by doing, I slowly built a formal system simulator called "[Deductrium](/deductrium/)" that runs on web pages, which I later turned into a game. ![Proving "a → a" in Deductrium](/img/1stlogic001.png)<!--more-->

## Introduction: The pq Formal System
Simply put, a formal system is a string collection game. First, you have some initial strings in hand, the system provides some mechanical rules, and by applying these rules to the strings you have, you get new strings, which can then be used with the rules to generate more strings. The book [*Gödel, Escher, Bach*](https://book.douban.com/subject/1291204/) mentions the simplest "pq" formal system:

1. All legal strings in this system can only contain these three characters: "s", "p", "q";
2. You have these initial strings: "pq", and if X represents a string containing only "s", then "XpqX" is an initial string, such as "spqs", "sssspqssss";
3. Rule: Let X, Y, Z all be strings. If you have "XpYqZ", then you can get "XpYsqZs".

For example: Starting with the initial string (X is "s") "spqs", applying the rule (X is "s", Y is "", Z is "s") we get "spsqss", continuing to apply the rule (X is "s", Y is "s", Z is "ss") we get "spssqsss".

Now, can you tell which strings can be obtained in finite steps and which can never be obtained?
- sspq
- spspq
- sspsqsss
- sspssqssss

Perhaps you've already seen that "XpYqZ" represents exactly $X+Y=Z$, where numbers are represented by the count of "s"s. Note that the formal system itself is meaningless; it only gains meaning when we assign meaning to it. The formal system itself only has symbols and rules; any meaning attached to it doesn't belong to the formal system but is something we externally assign.

## Propositional Logic Formal System
Although formal systems themselves have no meaning, we certainly hope they can do meaningful things, such as being used for mathematical proofs. Below we construct a deductive formal system for propositional logic without negation:

- All legal strings in this system are:
  1. Let $n$ be a natural number, then propositional symbols $A_n$ are all legal strings;
  2. If X and Y are legal strings, then "(X → Y)" is also a legal string.
- Let X, Y, Z be any legal strings. The initial strings in this system are:
  1. "(X → (Y → X))" are all initial strings;
  2. "((X → (Y → Z)) → ((X → Y) → (X → Z)))" are all initial strings.
- Rule:
  If you have both strings "(X → Y)" and "X", then you can get string "Y".

So far, those arrows and letters have no actual meaning. Below we externally assign specific logical meaning to the system, which can be translated as:
- Term translation: legal strings correspond to propositions, strings you have correspond to theorems, initial strings correspond to axioms.
- Symbol translation:
  1. Propositional symbol $A_n$ represents specific true or false propositions, whose content we don't care about for now.
  2. "(X → Y)" represents the proposition "proposition X implies proposition Y", i.e., "→" represents logical implication.
- Axiom schemas: For any propositions X, Y, Z:
  1. The proposition "(X → (Y → X))" is an axiom (a1)
  2. The proposition "((X → (Y → Z)) → ((X → Y) → (X → Z)))" is an axiom (a2)
- Inference rule (mp):
  If propositions "(X → Y)" and "X" are both theorems, then proposition "Y" is also a theorem.

Let's look at an example of a formal proof, proving that for any proposition "A", "(A → A)" is a theorem:
1. "(A → (A → A))"  (a1 axiom)
2. "(A → ((A → A) → A))"  (a1 axiom)
3. "((A → ((A → A) → A)) → ((A → (A → A)) → (A → A)))"  (a2 axiom)
4. "((A → (A → A)) → (A → A))" (mp rule applied to 3 and 2)
5. "(A → A)" (mp rule applied to 4 and 1)

Obviously, every step here can be mechanically verified (such as by computer verification), which perfectly satisfies the wishes of rigorous mathematicians.

Here's a thinking question for readers:

> Prove that for any propositions "A", "B", "C", if "(A → B)" and "(B → C)" are theorems, then "(A → C)" is also a theorem.

Hint: Starting from this (a2) axiom seems like a good choice: "((A → (B → C)) → ((A → B) → (A → C)))".

a1, a2, and mp actually specify three things: "how to introduce the logical implication symbol", "how to use the logical implication symbol", and "how to eliminate the logical implication symbol", allowing us to express and handle the logic of "what can be implied", but we currently cannot express "what cannot be implied". To enable the system to handle negative propositions, we introduce the negation symbol "¬" and add the following to the formal system:
- If X is a proposition, then "¬X" is also a proposition.
- Add axiom schema: If X and Y are any propositions, then "((¬X → ¬Y) → (Y → X))" is an axiom (a3)

The formal system with negation added is the complete propositional logic system. It can be proven that: Let A and B be any propositions, then in this system the following propositions are all theorems:
- (¬¬A → A) (Double negation elimination)
- (A → ¬¬A) (Double negation introduction)
- (A → (¬A → B)) (Principle of explosion)
- ((¬A → B) → ((¬A → ¬B) → A)) (Principle of proof by contradiction)

The proof method is to write out the derivation steps. Independently thinking of these derivations is somewhat difficult. If readers don't want to do more of these brain-burning reasoning exercises, they can directly refer to [the answers on Wikipedia](https://zh.wikipedia.org/wiki/%E4%B8%80%E9%98%B6%E9%80%BB%E8%BE%91#%E5%B8%B8%E7%94%A8%E7%9A%84%E6%8E%A8%E7%90%86%E6%80%A7%E8%B3%AA).

Perhaps you've noticed that this system doesn't even have the most basic logical connectives "and" and "or", but actually "A or B" can be expressed through "(¬A → B)", and "A and B" can be expressed through "¬(A → ¬B)". We can also introduce new axioms to add "and" and "or" symbols to the system. [Click to expand/collapse how to introduce new symbols and axioms](javascript:$('#ifff').toggle()).

<div style="background-color:var(--color-EEF);display:none" id="ifff">

### Optional Reading: Introducing New Symbols and Axioms
You might think this formal system is too cumbersome, requiring even concepts like "and" and "or" to be written out in expanded form. For example, the common "A if and only if B" can be expressed as "A → B and B → A", expanded into a big mess "¬((A → B) → ¬(B → A))". Actually, we can specifically introduce some symbol definition axioms to bring abbreviation symbols into the system:
- Define binary propositional connective symbols "$\land$", "$\lor$", "↔", representing "and", "or", and "if and only if" respectively. Two propositions connected by these symbols can form new propositions, such as (A ↔ B), (A $\lor$ (A $\land$ B)).
- Introduce two axioms for the introduction and elimination of "if and only if":
  1. (diff1) (¬((A → B) → ¬(B → A)) → (A ↔ B))
  2. (diff2) ((A ↔ B) → ¬((A → B) → ¬(B → A)))
- Now we can use the "if and only if" symbol to continue defining abbreviations for other symbols:
  1. (dor) (A $\lor$ B) ↔ (¬A → B)
  2. (dand) (A $\land$ B) ↔ ¬(A → ¬B)

It can be proven that adding these axioms to propositional logic enables arbitrary expansion or contraction of all or partial occurrences of and, or, and if-and-only-if definitions.
</div>

Or you might think two symbols "→" and "¬" are too many, going to another kind of extremism: actually, NAND logic alone can express all propositional logic, such as this [Sheffer stroke formal system](https://zh.wikipedia.org/wiki/%E8%B0%A2%E8%B4%B9%E5%B0%94%E7%AB%96%E7%BA%BF), but it has a cost—too many inference rules.

It's hard to understand these formal systems just by reading verbal descriptions. Since computers are best at doing these mechanical determinations anyway, I strongly recommend trying to experience them in Deductrium's formal system. If you find starting Deductrium from scratch too difficult and can't get to the if-and-only-if and logical symbols, I've left a [Deductrium creative mode](/deductrium/?creative) where you can directly use all axioms, symbol definitions, and metatheorems.

There are many ways to define propositional logic formal systems. More commonly, systems directly include "and", "or", and "not". The formal system we introduced is called **Hilbert-style**, characterized by many axioms but only one inference rule (mp). This inference rule combined with axioms yields the very important **deduction metatheorem**, which tells us why this one rule is sufficient:
> If B can be derived as a theorem in the formal system under the assumption that A is known to be a theorem, then the system can derive (A → B) as a theorem without any assumptions.

See Wikipedia for the proof. Its converse is simpler, being directly the mp rule:
> If the system can derive (A → B) as a theorem without assumptions, then B can be derived as a theorem in the formal system under the assumption that A is known to be a theorem.

The deduction metatheorem and mp rule tell us that the external behavior of the symbol "→" is indeed the same as logical implication.

Systems that include both "and" and "or" often have more inference rules, but they are equivalent in some sense—starting from the same assumptions, they can derive the same things, although the intermediate steps may differ greatly.

## Soundness and Completeness, Internal and External Theorems

I wonder if readers might doubt whether the formal system with three axiom schemas and one inference rule we just introduced can really handle propositional logic reasoning? This question can actually be divided into two aspects: First (soundness), are the propositions it derives all true propositions? Second (completeness), for all true propositions, can they all be derived from this formal system? For propositional logic, both answers are affirmative. The soundness proof only requires listing the truth tables of all propositions involved in axioms and inference steps and verifying their correctness; while the completeness proof is a bit complex. In the later stages of the game Deductrium, an "automatic proof" metatheorem ability will be unlocked—as long as the system exhaustively verifies that all truth table propositions for atomic propositions hold, there is a method to write out the inference steps within the system.

Note that the relationship between propositions inside and outside the formal system is actually quite subtle. For example, we proved:
> In the propositional logic formal system, for any proposition A, "(A → A)" is a theorem.

A proven proposition is called a theorem, and the above statement itself is also a theorem—specifically, it's a theorem about theorems (in the propositional logic formal system). Note that these two "theorems" are at two different levels and are not the same:
1. Things derived by the formal system, we just call them "theorems", or the system's "internal theorems". They are just concepts within a certain formal system. Although they somewhat resemble ordinary mathematical theorems, they are not the same thing. The way to verify internal theorems is to provide inference steps within the system.
2. The theorem we proved is a theorem in the theory about formal systems. For convenience of distinction, it's also called a "metatheorem" or "external theorem". External theorems are the real mathematical theorems, just like the commutative law of addition in arithmetic or Taylor expansion theorem in advanced mathematics—they are all derived within the mathematical system we've learned.

## The Ultimate Question: Which Came First, the Chicken or the Egg?

Some people might be confused: to mechanically verify that proofs of mathematical theorems are error-free, we need to define the entire mathematical system as a more complex meta-formal system to describe and determine it. And to verify the correctness of the larger meta-formal system, we need to construct a meta-meta system, mechanically verify meta-meta theorems... (P.S. If you're interested in this nesting, you can refer to this [story of Aladdin's lamp and the creator god](https://www.physixfan.com/zaowushendegushi/)). Specifically, formal systems can be precisely defined using the language of set theory, but as we'll see later, set theory is defined by formal systems! We've fallen into the chicken-and-egg dilemma.

To avoid infinite nesting, we no longer use formal systems or other mechanical methods to verify the correctness of proofs of mathematical theorems. Reading proofs of metatheorems can only rely on our mathematical experience. That is, **we cannot keep digging to the bottom; we must stop at some level and choose to directly believe in them**. This is like when we learn a foreign language (formal system), we need a native language (our mathematical experience) for teaching, and the ability to acquire the source native language is an innate instinct that no longer depends on any language.

However, isn't our purpose to formalize all mathematical theory? Mathematical theory should certainly also include formal systems and mathematical logic themselves. Currently, people do it this way: define a formal system that is powerful enough to express propositions in almost any mathematical theory, then we manually prove that this system is sound. As long as we believe the proof process of this system's soundness is error-free (this part indeed cannot be mechanically verified), we can happily use this system to verify any mathematical proof, even including the proof process of the system's soundness. Here's a quote from [*Gödel, Escher, Bach*](https://book.douban.com/subject/1291204/):

> One can never give a final, absolute proof that a proof in some system is correct. Of course, one can give a proof of a proof, or a proof of a proof of a proof—but the validity of the outermost system always remains an unproven assumption, accepted on faith.

This is also much like compiler bootstrapping in computers—the C++ compiler is written in C++! This seems like another chicken-and-egg dilemma, but it's not: First, manually write a program that translates assembly language into machine code. This program can only be written in machine code, and whether the machine code runs correctly depends on whether we believe the electrons in the CPU chip move according to physical laws. Once this program is written, we can use assembly language to write a more advanced C language compiler, then use C to write a more advanced C++ compiler. Once we have a C++ compiler, we can abandon the original C language version of the C++ compiler and completely switch to using C++ to compile the C++ compiler.

## First-Order Logic
In propositional logic, we only use letters to represent "atomic propositions" and haven't dealt with how to specifically describe their "internal structure". Let's now look at first-order logic. First, let's introduce the syntactic aspects:

In first-order logic, besides the concept of propositions, we also introduce the concepts of "terms" and "predicates": variables and constant symbols are "atomic terms", n terms acted upon by an n-ary function yield a term, and acted upon by an n-ary predicate yield an "atomic proposition". Note that first-order logic is a class of systems; choosing different constant symbols or predicates results in different formal systems. Here are two examples of first-order logic formal systems:
- If terms represent integers, then "0", "1", "-2" are constant symbols, "x" is a variable symbol; addition is a binary function, so "add(1,1)" is also a term; ">", "<", "=" are all binary predicate symbols, so "2>1", "add(1,1)=2" are all atomic propositions. Atomic propositions can continue to be used to construct more complex propositions according to propositional logic construction methods, such as: "¬(add(x,0)=0 → x=1)".
- If terms represent sets, then the empty set "$\phi$" is a constant symbol, "x" is a variable symbol, curly braces are an n-ary function for constructing new sets from given elements; intersection $\cap$ and union $\cup$ are binary functions, "$\in$", "=", "$\subset$" are predicates. For example, $\left\\{x,y\right\\}\cap\phi$ is a term, $\phi\in\left\\{\phi\right\\}$ is an atomic proposition.

The purpose of introducing variables is to express propositions with **quantifiers** like **for all** ($\forall$) and **there exists** ($\exists$), which is the biggest difference between first-order logic and propositional logic. We continue to add the following rules:
- If A is any proposition and x is any variable, then ($\forall$ x : A) is also a proposition

After adding the new symbol $\forall$, we also need to add axioms that can handle it. Let capital letters be any propositions, lowercase letters be any variables, and Greek letters be any terms:
- (a4) (($\forall$ x : A) → A[x/$\alpha$]), where A[x/$\alpha$] means replacing all free occurrences of x in A with term $\alpha$, and A must satisfy the condition of being substitutable. These concepts will be explained in detail in the optional reading section later.
- (a5) (($\forall$ x : (A → B)) → (($\forall$ x : A) → ($\forall$ x : B)))
- (a6) (A → ($\forall$ x : A)) if x does not occur free in A
- (a7) For any axiom K, ($\forall$ x : K) is also an axiom

These four axioms correspond to the elimination (a4), use (a5), and introduction (a6 and a7) of quantifiers.

We don't separately introduce axioms about the existence symbol "$\exists$", but treat it as an abbreviation for "¬$\forall$¬".

Finally, let's talk about predicates. Generally, we assume formal systems have the equality predicate "=", and add two axiom schemas:
- (eq1) $\alpha$=$\alpha$ 
- (eq2) ($\alpha$=$\beta$ → (A → A'))

where proposition A' is the result of replacing all or part of the free $\alpha$ in proposition A with $\beta$.

Using (eq1) and (eq2), it's not difficult to prove that equality has reflexivity and transitivity, i.e.:
- $\alpha$=$\beta$ → $\beta$=$\alpha$
- $\alpha$=$\beta$ → ($\beta$=$\gamma$ → $\alpha$=$\gamma$)

Let me emphasize again: although words like "for all" and "there exists" appear in first-order logic, the formal system only has these mechanized axiom schemas; it cannot understand the meaning behind these quantifiers. Correct logic can be achieved through mechanization alone, although these axiom schemas are somewhat complex. [Click here to expand/collapse specific details about substitution/free occurrence in axioms](javascript:$('#fst-detail').toggle()).

<div style="background-color:var(--color-EEF);display:none" id="fst-detail">

### Optional Reading:

#### 1. Bound Variables and Free Occurrence
Free occurrence is different from mere occurrence. First, let's introduce the concept of quantifier scope, which refers to the expression range of the proposition after the quantifier's colon. For example, in

$\forall$ x : $\forall$ y : (($\forall$ z : X) → (Y → X))

the scope of $\forall$ z is X, and the scope of $\forall$ y is (($\forall$ z : X) → (Y → X)).

Free occurrence means ignoring bound variables—those variables that appear after quantifiers with the same variable name as the quantifier. For example: neither x nor y occur free in the following expression, while z does have free occurrences:
- $\forall$ x : $\forall$ x : z=x
- $\forall$ x : (($\forall$ y : y=x → z=x) → ($\forall$ z : z=x))

Note that in the second proposition, although z appears multiple times, only the first z is free; the later ones are bound by $\forall$ z.

#### 2. Axiom Schema Explanation
Let's explain these axiom schemas. We'll go backwards:
- For example, from propositional logic (a1) axiom continuously applying (a7), we get the following are all axioms:
  1. $\forall$ z : (X → (Y → X))
  2. $\forall$ y : $\forall$ z : (X → (Y → X))
  3. $\forall$ x : $\forall$ y : $\forall$ z : (X → (Y → X))
- y=1 obviously cannot imply that all numbers equal 1, so (y=1 → ($\forall$ y : y=1)) doesn't hold, but (y=1 → ($\forall$ x : y=1)) does hold, because y=1 has nothing to do with x, so whatever value x takes doesn't affect the truth of y=1. These two examples explain why (a6) axiom requires no free occurrence.
- (a5) is simple, nothing much to say.
- From the fact that no set belongs to the empty set, we know that 0, 2+x, etc. also don't belong to the empty set. That is, axiom (a4):
  1. ($\forall$ x : ¬(x $\in\phi$)) → ¬(0 $\in\phi$)
  2. ($\forall$ x : ¬(x $\in\phi$)) → ¬(2+x $\in\phi$)

#### 3. Substitutability

However, axiom (a4) still has some detailed issues. For any integer x, we can find a number y different from it. This is obvious, i.e.,

$\forall$ x : $\exists$ y : ¬(x=y)

Then it should also hold for integer y. Directly replacing x with y in the expression gives:

$\exists$ y : ¬(y=y)

There exists a number y such that y is not equal to y—this is absurd. The reason is that the originally free y was "captured" by the inner quantifier y after substitution, making it a bound variable. This situation is called "non-substitutable". The names of bound variables don't actually matter, so correct substitution requires first changing the names of inner bound variables:

$\forall$ x : $\exists$ z : ¬(x=z)

Then substituting y expresses what we want:

$\exists$ z : ¬(y=z)

However, there's no axiom or inference rule that allows us to directly rename bound variables as a whole. This is actually an external theorem (metatheorem). The specific proof process is left for readers to think about.

#### 4. Other Approaches to First-Order Logic
Although concepts like "free occurrence" and "substitutability" can be precisely defined recursively, they are indeed much more complex than the simple match-and-replace rules of propositional logic. The game Deductrium specifically introduces fuzzy logic and a special class of assertion functions to handle them. Although reasoning currently works normally, I really can't guarantee there are no bugs, i.e., I can't guarantee the system won't derive false propositions. For mathematicians, they need more easily verifiable computer programs to handle first-order logic. [Metamath](https://us.metamath.org/mpeuni/mmset.html) is one of the few mathematical reasoning libraries based on first-order logic and ZFC set theory formal systems. It doesn't have concepts like free occurrence and substitutability, only introducing the simplest assertion that variables are distinct. However, the cost is many more axioms, and these axioms aren't very intuitive or easy to understand. Of course, although the number and content of axioms differ greatly, these two axiom systems are completely equivalent.
</div>

Finally, let's talk about what "first-order logic" means. Propositional logic is also called "zeroth-order logic"; it only discusses relationships between propositions. First-order logic goes inside propositions and can discuss arbitrary terms. Higher-order logic can discuss arbitrary functions between terms, arbitrary predicates, arbitrary propositions, and other relationships. However, generally speaking, first-order logic is sufficient, so second-order and higher logics are rarely mentioned.

The famous Peano axiom system describing properties of natural numbers is constructed on the basis of first-order logic. [Click here to expand/collapse details](javascript:$('#peano').toggle()).

<div style="background-color:var(--color-EEF);display:none" id="peano">

## Optional Reading: Peano Axiom System
Let's first look at a simple and practical axiom system built on first-order logic—the Peano axioms. It defines integers and mathematical induction, allowing us to express almost everything about integers. The informal statement of Peano axioms is:
1. 0 is a natural number;
2. Every definite natural number a has a definite successor Sa, and Sa is also a natural number;
3. For any natural numbers b and c, b=c if and only if b's successor = c's successor;
4. 0 is not the successor of any natural number;
5. For any proposition $\varphi$ about natural numbers, if we prove: it is true for natural number 0 ($\varphi[0]$), and assuming it's true for natural number a ($\varphi[a]$), we can prove it's also true for Sa ($\varphi[Sa]$). Then the proposition is true for all natural numbers.

Many readers should have heard of these five axioms for natural numbers. Below we use more rigorous formal language to describe the Peano axiom system with the help of first-order logic:

In this system, all terms are interpreted as integers, the only predicate is equality, there's also the constant symbol 0 and the successor unary function S. Besides the axioms of first-order logic, this system has the following axioms:
1. $\forall$ x : ¬(Sx=0)
2. $\forall$ x : $\forall$ y : (Sx=Sy → x=y)
3. ($\varphi$[0] $\land$ ( $\forall$ x : $\varphi$[x] → $\varphi$[Sx])) → ($\forall$ x : $\varphi$[x])

Note: Since the formal system has already introduced the constant symbol 0 and successor function S, there's no need to additionally state their existence like in the first two informal axioms.

Additionally, to make the system convenient for handling general arithmetic problems, we introduce addition and multiplication as binary functions with corresponding axioms, obtaining the Peano arithmetic system.

The last of the Peano axioms, mathematical induction, is the core of the entire system. It means that for a property P(n) of natural numbers, if P(0) is known to hold and P(n+1) can be derived from P(n), then it holds for all natural numbers.

<div style="background-color:var(--color-DDF); margin:0.5em">

### Optional reading within optional reading:

By induction, holding for all natural numbers means holding for all elements in the ordinal $\omega$. If we want to continue applying mathematical induction to larger ordinals, we encounter limit ordinals, and induction needs to be slightly modified into something called "[transfinite induction](https://www.bananaspace.org/wiki/%E8%B6%85%E9%99%90%E5%BD%92%E7%BA%B3%E6%B3%95)". In fact, transfinite induction in Peano axioms can only be proven up to the ordinal $\varepsilon_0$ (the limit of the $\omega$ exponential tower), and ordinals beyond $\varepsilon_0$ cannot be proven in the Peano axiom system. We say this ordinal $\varepsilon_0$ is the **proof-theoretic ordinal** of the Peano axiom system, denoted PTO(PA) (PA is the abbreviation for Peano axioms). For example, the famous "Hydra problem" and "Goodstein sequence problem". See the relevant chapters on proof-theoretic ordinals in "Large Number Theory". For the proof process, see [this series of articles on Zhihu](https://www.zhihu.com/column/c_1676959615533875200).

</div>
<br> 
</div>

<!-- It needs to be emphasized again here: there's no concept of truth or falsity of propositions inside formal systems. Just as we externally assign truth values to propositional logic, we can also assign truth values to first-order logic statements. Since quantifiers like "for all" and "there exists" appear, discussing their correctness necessarily requires specifying a "domain of discourse", such as natural numbers for the Peano axiom system; then mapping constant symbols, predicate symbols, and function symbols to real mathematical constants, relations, and functions respectively, thus enabling judgment of the truth or falsity of first-order logic formulas. Note that the formal system itself only contains symbols and rules for generating symbol strings (so-called propositions), not models. Choosing different models may lead to different truth values for propositions within the system, but may also have no effect on truth values. For example, in ZFC set theory introduced below, the existence of uncountable sets can be derived from the axiom of infinity and power set axiom, yet we can even find a countable model of ZFC. It will "incorrectly" think it has uncountably many objects. -->

## ZFC Set Theory
ZFC set theory considers everything, including elements of sets, to be sets. Building on first-order logic, it only adds the predicate "belongs to" symbol "$\in$", along with 9 axioms about sets:
- Axiom of Extensionality (defines the condition for set equality)
- Axiom of Pairing (defines how to construct sets from elements)
- Axiom of Union (defines the existence of unions)
- Axiom of Power Set (defines the existence of power sets)
- Axiom of Infinity (defines the existence of sets containing natural numbers)
- Axiom Schema of Separation (allows us to filter elements from large sets by conditions to form subsets, equivalent to an array's filter function)
- Axiom Schema of Replacement (provides a way to construct other sets from known sets and relations, not limited to subsets, equivalent to an array's map function)
- Axiom of Regularity (eliminates infinitely nested sets like "{ { {...} } }", eliminating the barber paradox)
- Axiom of Choice (allows us to select one element from each of infinitely many arbitrary sets to form a new set)

The specific forms of these axioms are somewhat complex and won't be listed here; they can be found online (and also in the game Deductrium).

![ZFC axioms in Deductrium, where the # function is an assertion check for free occurrence and substitutability of variables in axiom schemas](/img/1stlogic002.png)

Note that the above 9 axioms are completely described by first-order logic and don't include any symbols besides the membership symbol (such as empty set symbol, intersection/union/complement, etc.). However, mathematicians certainly wouldn't only use these few symbols. [Click here to expand/collapse about the specification problem of defining new symbols](javascript:$('#empty-set').toggle())

<div style="background-color:var(--color-EEF);display:none" id="empty-set">

### Optional Reading: Symbol Definition Problem

Here I want to share a confusion I had when learning set theory: Although first-order logic allows constant symbols, ZFC set theory never mentions the empty set symbol from beginning to end! Actually, the existence of the empty set can be derived from the axiom of separation (just set a self-contradictory condition to filter a set and you get the empty set), i.e., derive that there's a set x such that no y is its element: $$\exists x:\forall y :\lnot y\in x$$

There's no empty set symbol in the system. If we want to express that the empty set belongs to another set k, we can't directly write $\phi \in k$. We can only expand the definition of empty set and say that for any set x satisfying the definition of empty set, it belongs to set k: $$\forall x:((\forall y :\lnot y\in x) → (x \in k))$$

This way of speaking is indeed quite verbose. Just like introducing the if-and-only-if symbol, we introduce axioms for constructing constants:
1. First define a new quantifier symbol "uniquely exists", written as $\exists!$: Let $A(x)$ be a proposition containing free occurrences of x, then introduce the symbol definition axiom $$(\exists!x:A(x))↔((\exists x:A(x))\land\forall x:\forall y:((A(x)\land A(y))→x=y))$$ where y must be a variable that has never appeared to avoid being "captured".
2. Let c be an unused constant symbol, then we can introduce Hilbert's axiom to define it: $$(\exists!x:A(x)) → A(c)$$

For example, it's easy to prove that sets satisfying the empty set property are all equal, i.e., $$\exists!x:\forall y: \lnot y\in x$$
Then using Hilbert's axiom, combined with the mp rule, we finally get the definition of the empty set symbol $\phi$: $$\forall y: \lnot y\in \phi$$

Hilbert's axiom requires confirming unique existence before introducing constant symbols. This requirement is not superfluous; otherwise, the system before and after introduction would no longer be equivalent. See [Wikipedia](https://zh.wikipedia.org/wiki/%E4%B8%80%E9%98%B6%E9%80%BB%E8%BE%91#%E5%87%BD%E6%95%B8%E7%AC%A6%E8%99%9F%E8%88%87%E5%94%AF%E4%B8%80%E6%80%A7) for details.

Finally, I want to say that the "cleanest" ZFC set theory axiom system can have no constants or function symbols at all. Those nine axioms (schemas) can all be accomplished using only the five symbols →, ¬, =, $\forall$, $\in$. However, such axioms have poor readability and are quite difficult to use (you can also experience this in Deductrium), so we don't need to deliberately maintain the system's "purity" by making things difficult for ourselves. Usually, we still define various symbol abbreviations, constants, and function symbols to equivalently express these axioms.
</div>

The ZFC axiom system can not only simulate Peano axioms and prove relatively complex propositions like the infinity of primes, but can also prove mathematical theories in almost all fields such as analysis and topological geometry by introducing more symbol concept definitions.

There are also some details to note about set theory itself. For example, all sets don't form a set, and all ordinals can't form a set either. According to the definition of ordinals, the set formed by all ordinals should also be a larger ordinal, then we could continue stacking even larger ordinals on this basis, which contradicts the largest ordinal. Actually, the axiom of regularity in ZFC set theory excludes things formed by all ordinals from being sets, thus avoiding contradiction. Extending the set theory formal system further by introducing the concept of Class allows us to describe all ordinals: the thing they form is called a "proper class". Proper classes are very similar to sets, with the only difference being that they cannot appear as elements in other sets or classes.

Set theory has a very close relationship with ordinals. [Click here to expand/collapse how to describe the strength of ZFC set theory using ordinals](javascript:$('#pto').toggle()).

<div style="background-color:var(--color-EEF);display:none" id="pto">

### Optional Reading: The Strength of ZFC Set Theory
Mathematical induction can be derived from the axiom of choice in ZFC, so the strength of ZFC set theory is greater than (actually far greater than) Peano axioms. However, ZFC set theory is not omnipotent. For example, the existence of certain large cardinals cannot be proven in ZFC; we can only add more axioms to ZFC to get stronger systems. Let's start from the empty set and see how formal systems gradually become more powerful:
1. Starting from the empty set, through the axioms of pairing and union, we can construct all finite natural numbers, but obtaining the infinite set of natural numbers $\omega$ requires introducing the axiom of infinity to guarantee its existence.
2. Starting from the natural number set $\omega$, continuing to use the axioms of pairing and union, we can construct all ordinals of the form $\omega+n$ (where $n$ is a natural number), but without other axioms we currently cannot prove the existence of $\omega+\omega=\omega2$.
3. We can map each element $n$ of the natural number set to $\omega+n$. The axiom of replacement guarantees that the image of this mapping is still a set. Similarly, through the axiom of replacement we can finally construct sets $\omega2$, $\omega3$... even $\omega^2$, $\omega^\omega$, etc.
4. Now we can construct most of the recursive ordinals mentioned in the previous article; the power set axiom also allows us to construct many cardinals. However, many large cardinals including "inaccessible cardinals" cannot have their existence proven, so we add "there exists an inaccessible cardinal" as an axiom to the system.

The new system after adding the axiom is more powerful than ZFC. For example, "no contradiction can be derived in the ZFC axiom system" (the consistency of ZFC) cannot be proven within the ZFC axiom system, but can be proven after adding certain large cardinal existence axioms. Actually, the proof-theoretic ordinal in the ZFC system is still a recursive ordinal. When we add large cardinals, the system's proof-theoretic ordinal also increases further, although the proof-theoretic ordinal PTO(ZFC) of the ZFC system is already large enough that there's almost no notation besides proof-theoretic ordinals to represent it.

Next, we can also add the existence axiom of Mahlo cardinals, the existence axiom of measurable cardinals... Can we once and for all add infinitely many such large cardinal existence axioms to get the strongest formal system? Gödel's incompleteness theorem dashes our hopes: no matter how many axioms we add to ZFC, as long as the system is consistent (produces no contradictions), there will always be undecidable propositions (propositions that can neither be proven correct nor proven wrong). Gödel's incompleteness theorem is wonderful and there's a lot of introduction online (for example, see [this video by BiDao](https://www.bilibili.com/video/BV19u4y1D7GT/)), so I won't expand on it here.
</div>

## Conclusion

Since there are abundant online resources about set theory and I've only recently learned about these topics, there are far more exciting aspects of set theory than what's covered here. For example, this article doesn't mention the specific content of the famous **Gödel's incompleteness theorem** at all. If you want to explore deeper, please refer to any mathematical logic textbook. Although ZFC set theory is the most widely recognized and used formal axiom system in mathematics, when playing Deductrium to later stages, you'll find that this Hilbert-style formal system's derivations are really verbose. If you want to derive well-ordering and mathematical induction from the axiom of choice, although proof ideas can be found online, the workload of formally operating within the game system to finally derive them is too large. Apart from [Metamath](https://us.metamath.org/mpeuni/mmset.html) (a mathematical reasoning library based on first-order logic and ZFC set theory formal systems), the proof assistant tools used by mainstream mathematicians today use a completely different system—type theory. This is the hole to be filled in the next article.
