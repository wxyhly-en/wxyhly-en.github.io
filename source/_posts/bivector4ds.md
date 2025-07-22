---
title: "4D Space (VII): N-Dimensional Vectors"
date: 2016-04-23 20:20:48
categories: 4D Space Series
tags:
- 4D
- Algebra
- Series Articles
- Mathematics
excerpt: Calculations are essential for programming 4D graphics. It's time to talk about quantitative calculations in 4D. Feature Content - Using "2D vectors" to represent planes to solve angle problems.
index_img: /img/N_vector_positive.png
---

<span class="likecode"># This series discusses geometry in pure space, but some properties of **k-vectors** discussed in this article also hold in four-dimensional spacetime (just with different metrics). This article requires high mathematical ability: many mathematical formulas will appear. **k-vectors** have applications in physics beyond relativity (spacetime), see <span style="color:#F00">CFY's article [Angular Velocity, Axial Vectors, Bivectors](http://hadroncfy.com/articles/2016/04/21/axial-vectors/) which introduces k-vectors from physics.</span></span>

![Image from en.wikipedia: Geometric meaning of multivectors](/img/N_vector_positive.png?size=300x)

　　It's time to talk about quantitative calculations in four-dimensional space. On one hand, this can verify the correctness of our geometric intuition, otherwise our intuition is just blind guessing without evidence; on the other hand, calculations are essential for programming 4D graphics.
 <!--more-->
<a name="jihe"></a>

### Vectors No Longer Suit 4D Geometry
　　It is well-known that n-dimensional vector dot and cross products can calculate angles and normal vectors. The dot product has long been extended to N dimensions, so calculating line-line angles is no problem. In three-dimensional space, when we encounter a plane, we use its normal vector to participate in angle calculations—because the plane's normal vector direction is unique; for three-dimensional cells in four-dimensional space, we also use their normal vectors for calculations—because their normal vector direction is also unique. But the problem is: a plane's **normal vector is not unique**. In 4D space, a plane's normal space is also two-dimensional, meaning the normal plane is also a plane. This way **we can never convert to vector solutions**!
　　Of course, planes can be written in analytical form: the intersection of two cell equations, but it's too cumbersome—do you like using intersection equations to represent lines in 3D solid geometry?
　　Planes can be represented by vectors: $\vec x=\lambda\vec m+\mu\vec n {,}\lambda,\mu\in \mathbf R$, but the choice of vectors and parameters here is arbitrary, and it's inconvenient for calculations. We can only break through on another level: **extend the cross product to higher dimensions**.

　　Of course, we have another path: directly use a vector-like quantity to represent planes, which can do inner and outer products with other vectors, thus conveniently calculating projections and angles. This new quantity is called a **bivector (2-vector)**. From our previous analysis, bivectors represent planes, and planes are spanned by two non-collinear ordinary vectors (1-vectors), so we define an operation "$\vec a\wedge\vec b$" between two 1-vectors, which we call the **wedge product**.
　　Since we wanted to generalize the outer product (cross product), we bring it over and combine the two: $\vec a\wedge\vec b$ is a quantity with magnitude and directional orientation: the orientation is the plane spanned by $\vec a$ and $\vec b$, following the right-hand rule (meaning $\vec a\wedge\vec b=-\vec b\wedge\vec a$), and the magnitude (norm) is the area of the parallelogram spanned by $\vec a$ and $\vec b$, so if $\vec a$ and $\vec b$ are collinear, then $\vec a\wedge\vec b=0$. This idea is good, but it's not useful yet, because we still need to see how to represent and calculate it in coordinates.
<a name="bivector"></a>
### Wedge Product and Bivectors
  - Three-dimensional case
  　　This is a mathematical tool we haven't used even in three-dimensional space. So let's first discuss and familiarize ourselves with it in three-dimensional space, then generalize to four and N dimensions. Let $\vec m=(x_1,y_1,z_1), \vec n=(x_2,y_2,z_2)$, then
  
  $\vec m\wedge\vec n=(x_1\vec e_x+y_1\vec e_y+z_1\vec e_z)$ $\wedge(x_2\vec e_x+y_2\vec e_y+z_2\vec e_z)$.
  　　How to expand the parentheses? If $\wedge$ satisfies **distributivity**, the parentheses can be expanded! Let's first **assume** it does, expand, and use $\vec a\wedge\vec b=-\vec b\wedge\vec a$ to simplify and combine (we also need to assume it's a **linear operator** to combine): $\vec m\wedge\vec n=(x_1y_2-x_2y_1)e_x\wedge e_y + (x_1z_2-x_2z_1)e_x\wedge e_z + (y_1z_2-z_2y_1)e_y\wedge e_z$

  　　Look familiar? This is the cross product formula we know. We transferred the cross product to the "$\wedge$" operation, so deriving the cross product formula is normal. This bivector has three independent components $e_x\wedge e_y$, $e_y\wedge e_z$, $e_x\wedge e_z$, which represent unit $xy$, $yz$, $xz$ planes, corresponding exactly to normal vectors $\vec e_z$, $\vec e_x$, $-\vec e_y$ (note this correspondence follows the right-hand rule, sometimes differing by a sign), giving the impression they are isomorphic (identical). But $\vec a\wedge\vec b$ and $\vec a \times \vec b$ have essential differences: the former represents a plane, the latter represents the plane's normal line. We just found a special correspondence between them:
  
  $a(e_x\wedge e_y)+b(e_y\wedge e_z)+c(e_x\wedge e_z)\to a\vec e_z+b\vec e_x+c\vec e_y$

  This correspondence is called the **Hodge dual**, which we'll use extensively later.

  - 4D case
  　　In four-dimensional space, lines and planes (bivectors) no longer have a one-to-one correspondence:
　　Let $\vec m=(x_1,y_1,z_1,w_1), \vec n=(x_2,y_2,z_2,w_2)$, then$$\begin{align} \vec m\wedge\vec n&=(x_1\vec e_x+y_1\vec e_y+z_1\vec e_z+w_1\vec e_w)\wedge(x_2\vec e_x+y_2\vec e_y+z_2\vec e_z+w_2\vec e_w) \\\\ &=(x_1y_2-x_2y_1)e_x\wedge e_y+(x_1z_2-x_2z_1)e_x\wedge e_z+(x_1w_2-x_2w_1)e_x\wedge e_w+(y_1z_2-z_1y_2)e_y\wedge e_z+(y_1w_2-w_1y_2)e_y\wedge e_w+(z_1w_2-w_1z_2)e_z\wedge e_w\end{align}$$
　　We see that bivectors in four-dimensional space have six independent components, which are pairwise combinations of coordinates, with component magnitudes being cross multiplication then subtraction of the two coordinates. Good, we can finally represent planes.<a name="dot"></a>


### Bivector Inner Product and Its Meaning
　　The next step is to define the inner product between bivectors: the most natural definition is to multiply corresponding components and add them, i.e.$$\begin{align} A&=a\_1e\_{xy}+b\_1e\_{xz}+c\_1e\_{xw}+d\_1e\_{yz}+e\_1e\_{yw}+f\_1e\_{zw}  \\\\ B&=a\_2e\_{xy}+b\_2e\_{xz}+c\_2e\_{xw}+d\_2e\_{yz}+e\_2e\_{yw}+f\_2e\_{zw}  \\\\ \text{then } A\cdot B&=a\_1a\_2+b\_1b\_2+c\_1c\_2+d\_1d\_2+e\_1e\_2+f\_1f\_2 \end{align}$$
($e\_{ij}$ is shorthand for $e\_i\wedge e\_j$, same below)
　　Let's write the angle formula:
$$\lvert \cos\langle A,B\rangle\rvert={\lvert A\cdot B\rvert\over \lVert A \rVert \lVert B \rVert}$$
　　Here the norm (magnitude) of a bivector is of course the square root of the bivector's inner product with itself. It seems all problems are solved! But wait, something's not right: only one angle appears here! We analyzed earlier that two angles are needed to represent the relationship between planes.
　　For example, let's do some simple calculations:
The angles between $e\_{xy}$ and $e\_{xy}$, $e\_{xy}$ and $e\_{xz}$, $e\_{xy}$ and $e\_{zw}$ are: 0°, 90°, 90° respectively.
Well, the angle cosine of the $xy$ plane with itself is certainly 0°, but the latter two are both 90°: does this angle we calculated have meaning? What's its relationship with the two angles that measure planes?
　　Remember the generalization of the projection area theorem from the last article? We boldly conjecture: the essence of the bivector inner product is one area quantity multiplied by another area quantity's projection onto it, so we get the geometric meaning of the dot product between area quantities in four-dimensional space:
$$\lvert \cos\langle A,B\rangle_1\cos\langle A,B\rangle_2\rvert=\lvert \cos\theta_1\cos\theta_2\rvert={\lvert A\cdot B\rvert\over \lVert A \rVert \lVert B \rVert}$$
This formula has the same function as the projection area theorem. Generally, one equation with two unknowns cannot solve for $\theta_1, \theta_2$. No problem, at least we've obtained a coordinate solution method, while the projection area theorem is a geometric method.<a name="dual"></a>
The bivector inner product is very similar to the vector inner product, so we can also deduce: only when two bivectors have proportional corresponding components ($A=kB, k\in \mathbf R$) do we have $\lvert \cos\theta_1\cos\theta_2\rvert=1$, i.e., $\theta_1=\theta_2=0$. So just like vectors, if two bivectors are proportionally "collinear", they represent the same plane. Actually, the converse is also true, which we won't prove here.
### Generalizing the Cross Product
#### Hodge Dual
　　Recall the relationship between $\vec a \times \vec b$ and $\vec a\wedge\vec b$: they are mutually perpendicular, orthogonal complements, with the same magnitude (norm) and components, but different geometric dimensions and meanings. We define the **Hodge dual** mapping: mapping a k-vector to its orthogonal complement $(N-k)$-vector. We denote the Hodge dual of $A$ as $\*A$, where N is the space dimension, and we're discussing the case N=4. The Hodge dual also requires that the $(N-k)$-vector has the same magnitude (norm) as the k-vector, and the direction of the $(N-k)$-vector follows the right-hand rule—we can't gesture with our right hand in high dimensions, but this is actually an abstract rule. Let's illustrate with specific examples:
$\*e\_{xy}=e\_{zw}$, $\*e\_{yz}=e\_{wx}$, $\*e\_{zw}=e\_{xy}$, $\*e\_{wx}=e\_{yz}$
As long as we ensure the subscripts cycle from left to right as xyzw yzwx zwxy wxyz, the equations hold. Otherwise, arbitrarily swapping the positions of two letters in the order will change the sign. For example, $\*e\_{xy}=e\_{zw}$, but $\*e\_{xz}=-e\_{yw}$. In mathematics, this is called odd and even permutations. (Similar to the alternating signs in determinants)
$$\begin{align}\text{Let }F&=a\_1e\_{xy}+b\_1e\_{xz}+c\_1e\_{xw}+d\_1e\_{yz}+e\_1e\_{yw}+f\_1e\_{zw} \\\\ \text{then }\*F&=f\_1e\_{xy}-e\_1e\_{xz}+d\_1e\_{xw}+c\_1e\_{yz}-b\_1e\_{yw}+a\_1e\_{zw}  \end{align}$$<a name="cross"></a>
#### Bivector Outer Product
　　Now we can describe the essence of the cross product: an m-vector and n-vector cross product in N-dimensional space yields an (N-m-n)-vector: it's the orthogonal complement of the space spanned by the m-vector and n-vector (an (m+n)-vector), with direction following the right-hand rule. (Note: this is just one generalization of the vector cross product, **used only in this subsection.** [Later](/archives/gaqr/) we'll use the $\times$ symbol to denote another operation between bivectors similar to the vector cross product called the mixed product)
For example, bivectors $F\times G=\*(F\wedge G)$. Note here $F\wedge G$ is a 4-vector! This time we also need to assume the operation $\wedge$ is associative, i.e., $(A\wedge B)\wedge C=A\wedge (B\wedge C)$.
　　$$\begin{align} Let F&=a\_1e\_{xy}+b\_1e\_{xz}+c\_1e\_{xw}+d\_1e\_{yz}+e\_1e\_{yw}+f\_1e\_{zw} \\\\ G&=a\_2e\_{xy}+b\_2e\_{xz}+c\_2e\_{xw}+d\_2e\_{yz}+e\_2e\_{yw}+f\_2e\_{zw} \\\\ \text{then } F\times G&= (a\_1f\_2-b\_1e\_2+c\_1d\_2+d\_1c\_2-e\_1b\_2+f\_1a\_2)e\_{xyzw}\end{align}$$
The Hodge dual of $e\_{xyzw}$ is a 0-vector, i.e., "scalar": "1". This "scalar" is not a true scalar, because it changes sign under spatial reflection transformations (the right-hand rule at work), so we call it a pseudo-scalar.
Note: we haven't defined $F\wedge G=-G\wedge F$, $F\wedge F=0$ for bivectors. Actually $F\times G=G\times F$ because when we swap variables, the subscripts change from 1234 to 3412, with an even number of swaps, so the negative signs cancel! So we can happily use commutativity again. Similarly, $\*\*F=F$, but this conclusion doesn't necessarily hold for other k-vectors.
Finally, we obtain: the cross product between bivectors also yields a scalar. We notice its form is very similar to the inner product of two vectors—both are sums of multiplication terms. In fact, we have: $F\times G=F\cdot (\*G)=G\times F=G\cdot (\*F)$.<a name="shape"></a>
#### Geometric Meaning
$A \times B$ is a scalar: its absolute value equals the hypervolume of the hyper-parallelepiped they span (think of a parallelepiped): how do two planes span a parallelepiped? If $A=\vec a\wedge \vec b$, $B=\vec c\wedge \vec d$, then $A\wedge B=\vec a\wedge \vec b\wedge \vec c\wedge \vec d$—actually the parallelepiped formed by these four vectors. This definition is well-defined, because if $\vec a_1\wedge\vec b_1=\vec a_2\wedge\vec b_2=A$, although the parallelepipeds enclosed by $\vec a_1, \vec b_1, \vec c, \vec d$ and $\vec a_2, \vec b_2, \vec c, \vec d$ have different shapes, it can be proven that **their volumes are the same**.
![](/img/bivector1.gif)
For convenience, let's first choose a special pair $\vec a\wedge \vec b=A$: where $\vec a, \vec b$ are vectors parallel to the directions of maximum and minimum angles between the two planes (their angles with $B$ are $\theta_1, \theta_2$). The four-dimensional volume of the parallelepiped $V_4=V_3h$. $V_3$ refers to the three-dimensional volume of any parallelepiped cell in the parallelepiped (like the green one in the figure), $h$ is the height corresponding to this cell in the parallelepiped. And $V_3=SH$. From geometric relationships: $h=\lVert\vec a\rVert \sin\theta_1$, $H=\lVert\vec b\rVert \sin\theta_2$, we get: $V_4=S\lVert\vec a\rVert\lVert\vec b\rVert \sin\theta_1 \sin\theta_2=\lVert A \rVert \lVert B \rVert \sin\theta_1 \sin\theta_2$<a name="parfait"></a>


### Perfect Solution to the Problem!
$$\lvert \cos\theta_1\cos\theta_2\rvert={\lvert A\cdot B\rvert \over \lVert A \rVert \lVert B \rVert}  $$$$ \lvert \sin\theta_1\sin\theta\_2\rvert={\lvert A\times B\rvert\over \lVert A \rVert \lVert B \rVert}$$
　　Two equations, two unknowns, we can solve for the two angles! Can all angle problems in N-dimensional space be done this way? Unfortunately, this method only works in four and five dimensions—in six dimensions, two cells need three angle parameters for description, but inner and outer products only give two equations, so you'll have to use linear algebra.
　　Fortunately, we're not concerned with higher dimensions for now. Back to four-dimensional problems: given a vector $\vec m$ and a bivector $A$, how do we calculate the angle between them (line-plane angle)? We hope the previous formulas continue to hold. But the inner product is multiplication of corresponding terms, and in four-dimensional space vectors have 4 components while bivectors have 6 components, so we can't calculate the inner product. So we have to use the outer product—let's calculate: $\vec m\wedge A$. After simplification, we get a 3-vector: $ ae\_{xyz}+be\_{yzw}+ce\_{zwx}+de\_{wxy}$, which maps through Hodge duality to the vector $ae_w+be_x+ce_y+de_z$. Obviously, its magnitude (norm) represents the volume of the parallelepiped formed by $m$ and $A$, and its direction is the normal to the cell containing this parallelepiped—just like the mixed product, its geometric meaning is clear:
$$\lvert \sin\theta\rvert={\lVert\vec m\times A\rVert\over \lVert \vec m \rVert \lVert A \rVert}$$
Since the line-plane angle requires only one angle, we don't need to use the inner product anymore. Now we seem to have powerful computational tools, but are these conclusions correct? You can verify by using the bivector method to find line-plane angles, plane-plane angles, etc. in hypercubes and comparing with results obtained by geometric methods. I've verified many cases, and the results from both methods are always the same.<a name="nuage"></a>

### Dark Clouds

Previously, geometry led algebra; now let's try to think about some problems in reverse:
1. Four-dimensional space is not intuitive, these derived things have no rigorous proof, correctness cannot be guaranteed;
2. The concept of bivector "direction" is vague;
3. Vector addition has intuitive meaning: the diagonal of a parallelogram; does the plane corresponding to bivector addition have a geometric interpretation? What plane does $e\_{xy}+e\_{zw}$ represent? What about $e\_{xy}+e\_{xz}+e\_{xw}$?

Let's answer one by one:
1. First, proof is not a problem: we can first abstractly define a linear operator $\wedge$ satisfying certain properties, then show it has a one-to-one correspondence with planes, then use linear algebra to prove everything that follows.
2. When calculating $A=\vec a\wedge\vec b$, we can mark a vortex on plane A going from $\vec a$ to $\vec b$: this vortex is a kind of "direction". Let's look at the signs of $A\times B$ and $A\cdot B$: changing the direction of one face (like $A\to -A$) causes both inner and outer products to change sign, so as long as two planes are given, no matter what bivectors we choose to represent them, the sign of their inner and outer product is determined. This reflects that the figure formed by two planes in four-dimensional space has "chirality" (like left and right hands that cannot be rotated to overlap)! This is one method for quantitatively describing chirality in four-dimensional space.
3. Let's first find vectors $m(x,y,z,w)$ parallel to the plane $e\_{xy}+e\_{zw}$ by solving the equation $\vec m\times (e\_{xy}+e\_{zw})=0$:
$$\begin{align}(xe\_x+ye\_y+ze\_z+we\_w)\wedge(e\_{xy}+e\_{zw})&=0 \\\\ (ze\_z+we\_w)\wedge e\_{xy}+(xe\_x+ye\_y)e\_{zw}&=0 \\\\ ze\_{zxy}+we\_{wxy}+xe\_{xzw}+ye\_{yzw}&=0 \\\\ \text{Hodge dual gives: }ye\_x-xe\_y+we\_z-ze\_w&=0\end{align}$$Note this is a vector equation, requiring the left side to be the zero vector, **so here's the problem**—we get the unique solution $\vec m=\vec 0$! No lines are parallel to the face $e\_{xy}+e\_{zw}$? Don't panic, let's look at the angles of other lines with it:$$\begin{align}\lvert \sin\theta\rvert &={\lVert\vec m\times A\rVert\over \lVert \vec m \rVert \lVert A \rVert} \\\\ &={\sqrt{y^2+(-x)^2+w^2+(-z)^2}\over\sqrt{x^2+y^2+z^2+w^2}\sqrt 2} \\\\ &={\sqrt 2\over 2} \end{align}$$Here $m$ is any arbitrary vector, and they all make 45° angles with face $e\_{xy}+e\_{zw}$! What's going on with face $e\_{xy}+e\_{zw}$?
What about $e\_{xy}+e\_{xz}+e\_{xw}$? We similarly set up equations and simplify to get: $(z-w)e\_y+(w-y)e\_z+(z-y)e\_w=0$: setting these coefficients to 0, we get $y=z=w$, with $x$ unrestricted. This plane looks normal. We understand the addition of cocellular planes this way: vectors perpendicular to their intersection do normal vector addition, then span a plane with their intersection. In the figure below, blue is the intersection of all faces, you can see the red vectors perpendicular to the intersection add to give the green vector, finally spanning the yellow plane: $e\_{xy}+e\_{xz}+e\_{xw}$.
![](/img/bivector2.gif)<a name="strange"></a>


### Singular (Non-simple) Bivectors

　　What about non-cocellular planes? We know a vector's cross product with itself is 0, but $(e\_{xy}+e\_{zw})\wedge(e\_{xy}+e\_{zw})=2e\_{xyzw}$, the result is 2! So we can only conclude that no such plane corresponds to it! Let's look at the general case: Let $F=ae\_{xy}+be\_{xz}+ce\_{xw}+de\_{yz}+ee\_{yw}+fe\_{zw}$, $F\wedge F=2(af-be+cd)e\_{xyzw}$. It can be proven that only bivectors satisfying $F\wedge F=0$ can represent planes, in other words, there must exist $\vec a, \vec b$ such that $\vec a\wedge \vec b =F$. bivectors not satisfying $F\wedge F=0$ don't correspond to planes! Nor can they be written as $F=\vec a\wedge \vec b$.
　　We say bivectors satisfying $F\wedge F=0$ (i.e., can be written in the form of two vectors "$\wedge$") are **simple**, otherwise they're called **singular** or **non-simple**. For example, the bivector $e\_{xy}+e\_{zw}$ is non-simple. Vectors (1-vectors) don't have these situations, so we say vectors (1-vectors) are all simple.
　　Simple bivectors representating planes are also called **blade**. Non-simple bivectors seem useless. But bivectors not only describe planes, they can also describe rotations. We saw earlier that four-dimensional space has **simple rotations** corresponding to blades, and **double rotations** that three dimensions don't have correspond to non-simple bivectors!
Non-simple bivectors are nothing more than ordinary blades obtained through addition, so we need to decompose them into blades.<a name="dualde"></a>

------

<p class="likecode" style="display:none">//// The following content is about algebraic properties of bivectors, those only interested in geometry can skip it</p>

### Dual Decomposition
　　Let's look at the Hodge dual of bivector $F=e\_{xy}+e\_{zw}$: $\*F=\*(e\_{xy}+e\_{zw})=e\_{zw}+e\_{xy}=F$ it's dual to itself! Similarly, $G=e\_{xy}-e\_{zw}$ can be verified to be anti-self-dual: $G=-\*G$. Actually, any bivector $A$ can be decomposed into the sum of self-dual and anti-self-dual bivectors: $A={A+\*A\over 2}+{A-\*A\over 2}$. Here ${A+\*A\over 2}$ is the self-dual part of $A$, which we'll denote as $A^+$ for convenience, and ${A-\*A\over 2}$ is the anti-self-dual part, denoted as $A^-$. (This can be easily verified using the linearity of Hodge duality and the conclusion $\*\*A=A$)
  
　　For example $A=e\_{xy}$, decomposing gives ${(e\_{xy}+e\_{zw})/ 2}+{(e\_{xy}-e\_{zw})/ 2}$
  
　　This gives us a new set of basis to represent bivectors:
　　Three self-dual components: ${\sqrt 2\over 2}(e\_{xy}+e\_{zw}), {\sqrt 2\over 2}(e\_{xz}-e\_{yw}), {\sqrt 2\over 2}(e\_{xw}-e\_{yz})$;
　　Three anti-self-dual components: ${\sqrt 2\over 2}(e\_{xy}-e\_{zw}), {\sqrt 2\over 2}(e\_{xz}+e\_{yw}), {\sqrt 2\over 2}(e\_{xw}+e\_{yz})$;
　　It can be proven they're orthogonal—pairwise inner products are 0—actually their pairwise outer products are also 0! The coefficient ${\sqrt 2\over 2}$ serves to normalize them, ensuring they're unit bivectors.<a name="orth"></a>
　　Self-dual and anti-self-dual bivectors correspond to left- and right-handed isoclinic double rotations, so I also call this "isoclinic decomposition".
### Orthogonal Decomposition
　　A non-simple bivector $A$ can always be decomposed into the sum of bivectors representing two absolutely perpendicular planes. If this non-simple bivector is neither self-dual nor anti-self-dual, then the decomposition is unique:
  $$A=({A^+\over\lVert A^+\rVert}+{A^-\over\lVert A^-\rVert}){\lVert A^+\rVert+\lVert A^-\rVert\over 2} 　+ 　({A^+\over\lVert A^+\rVert}-{A^-\over\lVert A^-\rVert}){\lVert A^+\rVert-\lVert A^-\rVert\over 2}$$
　　If this non-simple bivector is self-dual, then decompose as:
  $A=({A^+\over 2}+I^-) + ({A^+\over 2}-I^-)$, where $I^-$ can be any anti-self-dual bivector with norm ${\lVert A^-\rVert\over 2}$.
　　If this non-simple bivector is anti-self-dual, then decompose as:
  $A=({A^-\over 2}+I^+) + ({A^-\over 2}-I^+)$, where $I^+$ can be any self-dual bivector with norm ${\lVert A^+\rVert\over 2}$.
  
　　You can verify these yourself. The significance of orthogonal decomposition is clear: a double rotation can be seen as the superposition of two absolutely perpendicular simple rotations! And if a double rotation is **isoclinic** (i.e., rotating at the same speed in two absolutely perpendicular planes), it can be composed from any two absolutely perpendicular simple rotations that are isoclinic with it.<a name="gen"></a>

### Relationship with Rotation Matrices: Generators
　　Now let's see how bivectors specifically represent rotations. (All the following information comes from the English Wikipedia entry "Rotations in 4-dimensional Euclidean space") In physics, bivectors are generally represented in matrix or tensor form. First, let me introduce the matrix form of bivectors: write $F=a\wedge b$ as $F=ba^T-ab^T$ (where $a, b$ are column vectors), then we get the antisymmetric matrix:$$F=\begin{pmatrix} 0 &  F\_{xy} &   F\_{xz} &   F\_{xw} \\\\     -F\_{xy} & 0 &  F\_{yz} &   F\_{yw} \\\\    -F\_{xz} &   -F\_{yz} & 0 &  F\_{zw} \\\\ -F\_{xw} &   -F\_{yw} &  F\_{zw} & 0 \end{pmatrix}$$
　　We know that rotational transformations in four-dimensional space are algebraically just orthogonal matrices $M$ with determinant 1, and our bivector $F$ is the generator of orthogonal matrices! First normalize the bivector, then calculate the magical **matrix exponential** operation $M=e^{F\theta}$, where $\theta$ is a scalar: the rotation angle. For specific algorithms, see the "Rotation case" section in Wikipedia's [Matrix exponential](https://en.wikipedia.org/wiki/Matrix_exponential#Rotation_case). With this algorithm, using bivectors to represent rotations in four-dimensional space is as convenient as using quaternions to represent three-dimensional rotations. (Quaternions can also represent rotations in four-dimensional space, see [here](/archives/gaqr/) for details) With this knowledge, writing a Jenn3d-like mouse control for rolling in four-dimensional space is no problem.

For more content on bivectors, see [Wikipedia: Exterior algebra](https://en.wikipedia.org/wiki/Exterior_algebra).



 