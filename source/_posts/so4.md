-----

title: "4D Computer Graphics: Rotation"
tags:
- 4D
- Math
- Geometry
- Graphics
- Algorithm
categories: 4D Computer Graphics
date: 2024-05-21 21:05:36
index_img: https://www.google.com/search?q=/img/so4001.gif
excerpt: This article is a overview of various algorithms for 4D rotation. It covers computational methods for 3D quaternions and 4D rotors, a comparison with Geometric Algebra, as well as various lookAt algorithms and 4D camera control methods.
-----

In this article, we'll look at how to implement the most fundamental coordinate transformation—rotation, which is the cornerstone of all technologies like rendering, modeling, and animation. While it doesn't require readers to be familiar with computer graphics, it does demand a slightly higher level of mathematical proficiency. The article mainly lists and describes algorithms, so the content is a bit boring. If readers only want to learn how to use these tools to build 4D interactive scenes, please follow the subsequent Tesserxel4D scene development tutorials.

## Prerequisites

I originally planned to start with 2D rotation, then 3D rotation, and finally 4D, gradually introducing rotation planes, 2-vectors, geometric algebra, and rotors. However, there's a lot of information on low-dimensional rotations online, and I've already written about rotation planes, 2-vectors, geometric algebra, and rotors before, so I won't be verbose (I'm lazy\~\~). So, I just give a list of prerequisites that readers should be familiar with before continuing:

1.  [《4D Space (VII): N-dimensional Vectors》](https://www.google.com/search?q=/archives/bivector4ds/) from the beginning to the section on simple 2-vectors.
2.  [《4D Space (XI): Geometric Algebra, Quaternions, and Rotation》](https://www.google.com/search?q=/archives/gaqr/) from the beginning to the section on quaternions and 4D spatial rotation.

## Handling 3D Rotation

First, points and vectors in space are stored as three floating-point numbers, defined as a 3D vector class (`class Vec3`). I use quaternions to represent 3D rotation, so a quaternion class (`class Quaternion`) needs to be defined. Finally, matrices (`class Mat3`, `class Mat4`), as the most classic graphics tool, are also essential.

It is recommended to store rotations using quaternions on a computer. First, Euler angles have the problem of gimbal lock. Second, matrices have 16 elements, far more than the 3 degrees of freedom of a 3D rotation, which wastes memory and makes it difficult to correct for accumulated floating-point errors. If you want to leverage the GPU's parallel optimization for matrix multiplication, you can convert it to a matrix only when sending it to the GPU.

### Basic Mathematical Class Operations

We will define member variables and some common methods for the above classes. Taking the 3D vector class Vec3 as an example, its pseudocode looks something like this:

```typescript
class Vec3{

    // Member variables:

    x: number; y: number; z: number;

    // Constructor:

    constructor(x: number, y: number, z: number){
        this.x = x; this.y = y; this.z = z;
    }

    // Common methods:

    // Negation
    neg():Vec3{
        return new Vec3(-this.x, -this.y, -this.z);
    }
    // Addition
    add(v3:Vec3):Vec3{
        return new Vec3(this.x + v3.x, this.y + v3.y, this.z + v3.z);
    }
    ...
    // Dot product
    dot(v3:Vec3):number{
        return this.x * v3.x + this.y * v3.y + this.z * v3.z;
    }
    ...
}
```

If you can't understand the above code, you can learn the concepts of classes in one of the following languages: typescript/javascript, java, C\#, or C++. They are not complicated. This article uses pseudocode similar to typescript to describe the algorithms, where the name after the colon in a function parameter represents the parameter type, and the function's return type is indicated after the parentheses with a colon.

The definition of the quaternion class is:

```typescript
class Quaternion{
    // r represents the real component, i/j/k represent the coefficients of the three imaginary parts
    r: number; i:number; j: number; k: number;
    ...
}
```

### Rotating Coordinates and Quaternion-to-Matrix Conversion

Given a rotation $R$ and a vector $p$, how to calculate the new rotated vector $R(p)$ is the most basic requirement. We will name this function `apply`. If the rotation is represented by a matrix, matrix multiplication is used to perform the calculation: (The formula for matrix-vector multiplication is well-defined, and this article assumes the reader is capable of implementing the details of this function, hence they are omitted, and so on for the following.)

```typescript
function apply(R:Mat3, p:Vec3):Vec3{
    return R.mul(p); // The mul function is for matrix-vector multiplication.
}
```

If the rotation is represented by a quaternion, we use the [rotation coordinate algorithm here](https://www.google.com/search?q=/archives/gaqr/%23quanternion_as_rotor3) for calculation:

```typescript
function apply(R:Quaternion, p:Vec3):Vec3{
    // Convert vector to quaternion, note the real part is 0.
    let p0 = new Quaternion(0, p.x, p.y, p.z); 
    // mul and conj are quaternion multiplication and conjugation functions.
    let p1 = R.mul(p0).mul(R.conj()); 
    // Convert quaternion back to vector.
    return new Vec3(p1.i, p1.j, p1.k); 
}
```

However, rotations are generally handled using matrices in GPUs. How do we convert a quaternion-represented rotation into a matrix representation? Here are two methods:

1.  Derive the formula directly: We can expand the quaternion multiplication in the above algorithm by coordinates, organize the linear transformation formula, and write it as a matrix. It is recommended to use tools like Mathematica to derive the formula. The result can be seen in the [toMat3 function of my tesserxel library's Quaternion class](https://github.com/wxyhly/tesserxel/blob/main/src/math/algebra/quaternion.ts#L170).
2.  Transform basis vectors: If you find formula derivation troublesome, you can directly partition the matrix by columns and you will find that it is composed of the new vectors obtained by rotating all coordinate basis vectors. Therefore, we can construct the matrix without needing to understand how quaternions rotate points in space (note that these are column vectors):

$$M=\begin{pmatrix}R(e_x)&R(e_y)&R(e_z)\end{pmatrix}$$

Translated into pseudocode, this is:

```typescript
function quaternion2mat3(R:Quaternion):Mat3{
    let ex = apply(R, new Vec3(1, 0, 0));
    let ey = apply(R, new Vec3(0, 1, 0));
    let ez = apply(R, new Vec3(0, 0, 1));
    return new Mat3(
        ex.x, ey.x, ez.x,
        ex.y, ey.y, ez.y,
        ex.z, ey.z, ez.z,
    );
}
```

Note that although the second method's code looks more elegant, it is less efficient than the first: the basis vectors are either 1 or 0, so many multiplications and additions are unnecessary. Simplifying the expression leads to higher efficiency, but that essentially turns it into the first method.

Conversely, converting a matrix to a quaternion is not easy. We will solve this problem [later](https://www.google.com/search?q=/archives/so4/%23mat32q). Now that we have the basic ability to handle rotations, let's first look at how to generate them.

### Rotation Composition and Inverse

To represent rotation using Euler angles or other multi-step composition: use the algorithm to generate the quaternion for each rotation. Note that if quaternions $p$ and $q$ are applied to a point $x$ in that order, the result is $q(pxp^\*)q^\*=(qp)x(qp)^\*$. This is equivalent to a single application of the quaternion $qp$. Therefore, composing rotations with quaternions, just like with matrices, is a direct multiplication in right-to-left order. Finding the inverse of a rotation is even simpler: a unit quaternion multiplied by its conjugate is 1, so the quaternion's conjugate is its inverse.

### Axis-Angle Rotation Generation

1.  Given the rotation axis and angle directly: The algorithm for this, along with the rotation coordinate algorithm, has been provided in the [link mentioned earlier](https://www.google.com/search?q=/archives/gaqr/%23quanternion_as_rotor3). Here's the pseudocode:

<!-- end list -->

```typescript
// Given a rotation axis represented by the unit vector axis, and a rotation angle, generate the quaternion for the rotation.
function axisAngle2quaternion(axis: Vec3, angle: number): Quaternion{
    // Halve the angle and calculate its sine and cosine values, s and c.
    angle *= 0.5;
    let s = Math.sin(angle);
    let c = Math.cos(angle);
    return new Quaternion(c, s * axis.x, s * axis.y, s * axis.z);
}
```

2.  Given the rotation generator 2-vector directly: Since a 3D 2-vector can be Hodge dualized to an ordinary 1-vector, this is equivalent to giving a vector parallel to the rotation axis, where the vector's length is the rotation angle. This combines the two parameters, axis and angle, into one, similar to an angular velocity vector. This representation, which combines magnitude and direction, is more convenient than the first method. The algorithm is essentially the same, just with some additional steps: first, calculate half the vector's length to get the half-angle $\\theta = ||l||/2$, then normalize the vector, and the rest is the same as the first method. We can simplify this: since normalization means dividing by the angle $2\\theta$, the imaginary part of the final quaternion is the unit vector multiplied by $\\sin(\\theta)$, the sine of the half-angle. Combining these two steps, we get the vector multiplied by $\\sin(\\theta)/(2\\theta)$. Clearly, this coefficient approaches $1/2$ when the angle is close to zero (the numerator and denominator both approach 0), but this is not a numerically stable method. To avoid this, when the angle is less than $\\epsilon$, we can use the first two terms of the Taylor expansion of the expression, $(1/2)(1-\\theta^2/3\!)$. This is equivalent to the exponential map from the generator to the rotation, so the function is named `exp`. Here's the pseudocode:

<!-- end list -->

```typescript
function exp(v: Vec3): Quaternion {
    // The length function calculates the vector's magnitude, giving the half-angle.
    let theta = v.length() * 0.5; 
    let epsilon = 0.005;
    // If the absolute value of the angle is greater than epsilon, calculate the coefficient s directly, otherwise use a Taylor expansion.
    let s:number;
    if (Math.abs(theta) > epsilon) {
        s = Math.sin(theta) / theta * 0.5;
    } else {
        s = 0.5 - theta * theta / 12;
    }
    return new Quaternion(Math.cos(theta), s * v.x, s * v.y, s * v.z);
}
```

### LookAt Rotation Generation

Given only the initial and final orientations of an object, find the required rotation. For example, in a shooting game, a cannon needs to automatically aim at a moving enemy aircraft. Assuming the cannon's barrel direction is the x-axis in its local coordinate system, we need a rotation that transforms the x-axis to the direction $v$ of the line from the cannon to the enemy. Almost all game engines have a ready-made function for this, generally called a "lookAt" function. Let's see how to implement it.
Given two unit vectors $u$ and $v$, we want to construct a rotation $R$ such that $R(u)=v$. Since two vectors define a plane, the simplest way is to rotate within the $u$-$v$ plane, or around the axis $u\\times v$. The rotation angle can be found by calculating the angle between the vectors. With the axis and angle known, we can use the previous algorithm to generate the rotation:

```typescript
function lookAt(u:Vec3, v:Vec3):Quaternion{
    // Calculate the rotation axis using the cross product function.
    let axis = u.cross(v); 
    // The normalize function scales the axis to a unit vector.
    let e_axis = axis.normalize(); 
    // The dot function finds the cosine of the angle, and Math.acos finds the angle.
    let angle = Math.acos(u.dot(v)); 
    // With the axis and angle known, use the previous algorithm to generate the rotation.
    return axisAngle2quaternion(e_axis, angle); 
}
```

The code above actually omits a special case: if $u$ and $v$ are in the same or opposite direction, the cross product gives an axis of $0$. Handling these special cases is left as an exercise.
Note that the rotation $R$ satisfying $R(u)=v$ is not unique: any rotation around axis $v$ composed with $R$ also satisfies the requirement. The algorithm above finds the rotation that is "closest" in some sense, but sometimes this can lead to an undesirable "tilting" effect, as shown in the figure below. For example, in camera work, you might want the screen to not tilt sideways. We will solve this problem right away.

Given the initial and final orientations of an object, as well as its upward direction, find a rotation that keeps the object from tilting. There are two methods: first, the rotation can be broken down into horizontal and vertical steps to prevent tilting; second, one can first use the `lookAt` function to align the object, and then apply another rotation around the target axis to correct the tilt. These two methods are essentially equivalent to Euler angles, just with different rotation orders. The specific code implementation is left as an exercise.
<a name="mat32q"></a>

### Rotation Matrix to Quaternion

As mentioned earlier, a rotation matrix's degrees of freedom exceed those of a rotation, so numerical errors can prevent it from being a precise rotation matrix. In such cases, there might not be a solution. However, with the "lookAt" function, we can gradually approximate a quaternion in a way similar to "Schmidt orthogonalization": a rotation matrix partitioned by columns tells us the new positions of the original coordinate axes. Therefore, we can first use the `lookAt` function to generate a rotation `r1` that aligns the x-axis. At this point, the y-axis and z-axis are not yet aligned. Then, we perform another `lookAt` to align the y-axis, and the remaining z-axis will automatically align. Multiplying the quaternions from the two `lookAt` steps gives the final conversion result.

```typescript
function mat32quaternion(m:mat3): Quaternion{

    // First rotation: align the x-axis

    // The x_ function returns the first column vector of matrix m.
    let r1 = lookAt(new Vec3(1, 0, 0), m.x_()); 

    // Second rotation: align the y-axis

    // Note that the y-axis has been rotated to newY by r1.
    let newY = apply(r1, new Vec3(0, 1, 0)); 
    // The y_ function returns the second column vector of matrix m.
    let r2 = lookAt(newY, m.y_()); 

    // Compose the two rotations.
    return r2.mul(r1); 
}
```

### Given a Rotation, Find the Axis and Angle

Sometimes, we encounter the inverse problem of rotation generation: given a rotation, find its generator. Since the generator maps to a rotation via an exponential map `exp`, this inverse function is called a logarithmic map `log`. The inverse mapping is not difficult to find; we can easily find the cosine of the half-angle from the real part of the quaternion. Below is the pseudocode:

```typescript
log(R: quaternion): Vec3 {
    // Find the half-angle from the quaternion's real part.
    let theta = Math.acos(R.r); 
    // Recover the rotation axis, and multiply by the angle value.
    let s = 2 * theta / Math.sin(theta); 
    return new Vec3(R.i * s, R.j * s, R.k * s);
}
```

### Rotation Interpolation

In animation, it's common to be given the initial and final states of an object and need to fill in the intermediate frames to create the animation. If the rotation at time $0$ corresponds to quaternion $R\_a$ and at time $1$ to quaternion $R\_b$, we need to find the quaternion $R\_t$ corresponding to the rotation at time $t$. Rotations are represented by unit quaternions, which is equivalent to interpolating on the hypersphere $S^3$. The algorithm can be found [here](https://zhuanlan.zhihu.com/p/538653027), and the code can be seen in the [slerp function here](https://github.com/wxyhly/tesserxel/blob/main/src/math/algebra/quaternion.ts#L134).

### Numerical Stability

In scene calculations, as 3D objects continuously rotate, quaternions are constantly being multiplied, which is numerically unstable. If there are any errors, the quaternion's length will not be 1, causing it to either decay to zero or diverge to infinity over time. The solution to this problem is simple: periodically (e.g., every frame) force the quaternion to be a unit quaternion by dividing it by its length.

## Handling 4D Rotation

Representing points and vectors in 4D space on a computer is very similar to 3D vectors. For example, there's a 4D vector class (`class Vec4`) and matrices (`class Mat4`), and you can even define affine matrices (`class Mat5`). It is also not recommended to represent 4D rotation using matrices. It uses rotors, and rotors come in two versions: a quaternion-based version and a geometric algebra-based version. No matter which version is used, a 2-vector class (`class Bivec`) is needed to represent planes. Let's first look at its definition.

### 2-Vector Class

A 4D space 2-vector is stored using six floating-point numbers, defined as a 2-vector class (class Bivec4). Since I do not plan to develop a general high-dimensional graphics library, there will be no Bivec5, Bivec6, etc. Also, due to Hodge duality, a 3D Bivec3 is actually just a Vec3, so only Bivec4 is useful. Thus, I abbreviate Bivec4 to Bivec without ambiguity. Its pseudocode definition is as follows:

```typescript
class Bivec{
    xy: number; xz:number; xw: number;
    yz: number; yw:number; zw: number;
}
```

It is very much like a 6D vector. For example, you can define methods for addition (add), subtraction (sub), negation (neg), scalar multiplication (mul), dot product (dot), and normalization (normalize). At the same time, we add some methods unique to 2-vectors, such as Hodge duality (dual) and cross product (cross), and add the wedge product operation (wedge) to the 4D vector class to generate 2-vectors.

### 4D Rotors

There are two ways to represent 4D rotors: one is the quaternion-based rotor (`class Rotorq`), and the other is the geometric algebra-based rotor (`class Rotorg`). The geometric algebra version is the most original and easy to understand, while the quaternion version has more convenient rotation algorithms. Since almost no one directly inputs the components of a rotor to describe a rotation (they are just internal intermediate data), when building a rendering engine, one usually only chooses one of them. For example, [Marc ten Bosch's 4D engine](https://marctenbosch.com/ndphysics/) only supports the geometric algebra version of rotors, while [my tesserxel engine](https://www.google.com/search?q=https://github.com/wxyhly/tesserxel/) only supports the quaternion version. Below, I will list the relevant rotation algorithms for these two versions separately. Please **expand and read as needed**:

<br/>

### <a href="javascript:void(0);" onclick="$('\#ga').toggle();$('\#ga\_').text($('\#ga\_').text()==='+'?'-':'+')"><span id="ga\_">+</span>Rotor Class (Geometric Algebra Version)</a>

<div style="background-color:var(--color-FFE); display:none" id="ga">

As described in [this introduction](https://www.google.com/search?q=/archives/gaqr/%23def_spinor), a rotor is a subalgebra composed of even-grade vectors in geometric algebra. Therefore, for 4D space, it consists of a scalar `r` (0-vector), a 2-vector `b`, and a pseudoscalar `i` (4-vector). Thus, the rotor class can be defined as:

```
class Rotorg{
    r: number; b: Bivec, i: number;
}
```

In addition, we define addition, subtraction, scalar multiplication, normalization, conjugation, and geometric product for rotors. Note that we can use the already-defined functions for 2-vectors to simplify some tasks, for example, multiplying a 2-vector by a pseudoscalar is equivalent to multiplying the Hodge dual of the 2-vector by a scalar, and so on.

#### Rotation Coordinate Algorithm

Given a rotor $R$, the coordinates of $R(p)$ after it acts on the coordinates $p$ are $RpR^\\dagger$, i.e.,

```typescript
function apply(R:Rotorg, p:Vec4){
    // How to define the mul function here?
    return R.mul(p).mul(R.conj()); 
}
```

Unfortunately, this leads to a problem: a rotor composed of even-grade k-vectors multiplied by a vector will produce a 1-vector or a 3-vector, which is not included in the definition of a rotor (class Rotorg). Therefore, we cannot implement the mul function directly. There are two solutions: one is to write a geometric algebra class (class GA) that contains all vectors from grade 0 to 4; the other is to directly expand the formula by coordinates and put the simplified result in the apply function. I recommend the second method because the first method contains components of all vector grades that are mostly unused and would waste computation steps and memory, while the second method has none of these problems and is the most performant after simplifying the formula.

#### Rotation Composition and Inverse

Similar to quaternions, the composition of 4D rotors is a geometric product performed in right-to-left order. The inverse corresponds to conjugation in geometric algebra: conjugation in geometric algebra reverses the order of all k-vectors, for example, $(e\_xe\_y)^\\dagger=e\_ye\_x=-e\_xe\_y$, and $(e\_xe\_ye\_ze\_w)^\\dagger=e\_we\_ze\_ye\_x=e\_xe\_ye\_ze\_w$. Applied to a 4D rotor, this means negating the 2-vector part, while the scalar and pseudoscalar parts remain unchanged.

#### Bivector Rotation Algorithm

4D has an additional case compared to 3D, where a rotation can act on a 2-vector (plane) by acting on the two basis vectors that span it. This is defined as $R(a\\wedge b) = R(a)\\wedge R(b)$. The most straightforward method is to write out the six coordinate basis bivectors $e\_ie\_j$, calculate the new 2-vector basis $R(e\_i)\\wedge R(e\_j)$ after rotation by $R$, and then treating the 2-vector as a 6D linear space, we get a 6x6 matrix for rotating 2-vectors. Geometric algebra offers a better method. Assuming $a$ and $b$ are orthogonal, note that $$R(a)\wedge R(b)=R(a)R(b)=(RaR^\dagger) (RbR^\dagger) = Ra(R^\dagger R)bR^\dagger=R a b R^\dagger$$
Therefore, we can directly perform the geometric product on both sides. Since all 2-vectors can be decomposed into a sum of wedge products of orthogonal coordinate basis vectors, this algorithm holds for any 2-vector:

```typescript
function apply(r:Rotorg, b:Bivec){
    return r.mul(b).mul(r.conj());
}
```

#### Plane-Angle Rotation Generation

This is similar to the 3D rotation generation requirement but with some differences:

1.  Given a rotation plane $B$ and an angle $\\alpha$, generate a rotation: Note that the rotation plane can only be represented by a 2-vector. It might be a simple 2-vector corresponding to a simple rotation, or a compound (singular) 2-vector corresponding to a double rotation. We first consider a simple rotation, because the [algorithm here](https://www.google.com/search?q=/archives/gaqr/%23formula_spinor_rotate) must assume the 2-vector $B$ is simple and normalized to obtain the rotation formula: $R=\\cos(\\alpha/2)+B\\sin(\\alpha/2)$, which is almost identical to the 3D quaternion formula. Here's the pseudocode:

<!-- end list -->

```typescript
// Given a simple normalized 2-vector plane representing the rotation plane, and a rotation angle, generate the rotor.
function planeAngle2rotorg(plane: Bivec, angle: number): Quaternion{
    // Halve the angle and calculate its sine and cosine values, s and c.
    angle *= 0.5; 
    let s = Math.sin(angle);
    let c = Math.cos(angle);
    return new Rotorg(
        c,            // Scalar part
        plane.mul(s), // 2-vector part, mul is scalar multiplication for 2-vectors
        0             // No pseudoscalar component
    );
}
```

Note that a simple rotation has no pseudoscalar component, so the last parameter when constructing `Rotorg` is 0. However, the composition of simple rotations can result in a double rotation, in which case the pseudoscalar component will no longer be 0.
2\.  Given the rotation generator 2-vector directly: Here, we no longer require the 2-vector to be simple or normalized. However, for a general 2-vector, the exponential map formula is not easy to simplify. One can either calculate it by definition $$R=\exp(B\theta/2)=I+B\theta/2+{B^2(\theta/2)^2\over2!}+{B^3(\theta/2)^3\over3!}+..$$ which is an infinite series that can only be truncated after the first $n$ terms; or one can decompose it into simple 2-vectors for calculation. [An algorithm for this decomposition is given here](https://www.google.com/search?q=/archives/bivector4ds/%23orth), but it is quite cumbersome. We will see later that the quaternion-based rotor version does not have these problems, which is why tesserxel uses quaternions to represent 4D rotors.<a name="lookat"></a>

#### lookAt Rotation Generation

1.  Given only the initial and final orientations of an object, find the required rotation. This is the 4D version of the `lookAt` function: just replace the cross product with its generalized wedge product.

<!-- end list -->

```typescript
function lookAt(u:Vec4, v:Vec4):Rotorg{
    // Calculate the rotation plane 2-vector using the wedge product function.
    let plane = u.wedge(v); 
    // The normalize function scales it to a unit 2-vector.
    let e_plane = plane.normalize(); 
    // The dot function finds the cosine of the angle, and Math.acos finds the angle.
    let angle = Math.acos(u.dot(v)); 
    // With the rotation plane and angle known, use the previous algorithm to generate the rotation.
    return planeAngle2rotorg(e_plane, angle); 
}
```

Similar to the 3D case, if u and v are in the same or opposite direction, the wedge product will result in a zero plane. Handling these special cases is also left as an exercise.

2.  Rotating a 4D camera with the lookAt function can also cause the problem of horizontal tilting. In this case, two methods can also be used: one is to perform a horizontal rotation first, then a vertical rotation; the other is to use the lookAt function to align the camera first, and then superimpose a rotation around the target axis to correct the tilt. In 4D, because there are more axes, the second method is actually more difficult, so the first method is recommended: assume the vertical direction is the $y$-axis, then the horizontal cell is the 3D subspace $xzw$. For the first rotation, we use the lookAt function only in the subspace $xzw$ to align the projections of the initial and target vectors. For the second rotation, we use lookAt again, but only in the vertical plane.

#### Rotation Matrix to Rotor

This is implemented by using the lookAt function multiple times. The method is exactly the same as for the geometric algebra version of rotors. Please refer to <a href='javascript:void(0);' onclick='if($("\#qa\_").text()=="+"){$("\#ga").show();$("\#ga\_").text("-");}window.location.href="\#lookat";'>here</a>.

#### Given a Rotation, Find the Plane and Angle

Similar to the 3D case, the rotation plane and angle can be found by inverting the exponential map:
$$B=\log(R)=(R-I)-(R-I)^2/2+(R-I)^3/3-..$$
Note that if the resulting 2-vector is simple, its magnitude is the rotation angle. If it is a compound (singular) 2-vector, it must be decomposed using [this algorithm](https://www.google.com/search?q=/archives/bivector4ds/%23orth) to find the specific rotation planes and angles of the double rotation. If you don't want to use a Taylor series expansion, the quaternion-based rotor version provides a <a href='javascript:void(0);' onclick='if($("\#qt\_").text()=="+"){$("\#qt").show();$("\#qt\_").text("-");}window.location.href="\#rotorq\_log";'>better algorithm</a>.

#### Rotation Interpolation

Interpolating rotors is not convenient because their topological space is no longer a high-dimensional sphere. We can use a logarithmic operation to first find the generators. After performing linear interpolation and scaling on the generators, we can reapply the exponential map: suppose the rotation at time $0$ corresponds to quaternion $R\_a$ and at time $1$ to quaternion $R\_b$. Then the quaternion for the rotation at time $t$ is $R\_t=\\exp(t\\log(R\_bR\_a^{-1}))R\_a$. This method is general and also applies to 3D spatial rotation.

#### Numerical Stability

In scene calculations, as 4D objects continuously rotate, rotors are constantly being multiplied, which also causes numerical instability. However, there is no simple algorithm to normalize geometric algebra-based rotors. [Marc ten Bosch's paper](https://marctenbosch.com/ndphysics/) mentions that he "factorizes" the rotor and then normalizes each part. The specific algorithm only references a book that I couldn't find a free full-text version for. It may be similar to the quaternion-based rotor.</div>
<br>

### <a href="javascript:void(0);" onclick="$('\#qt').toggle();$('\#qt\_').text($('\#qt\_').text()==='+'?'-':'+')"><span id="qt\_">+</span>Rotor Class (Quaternion Version)</a>

<div style="background-color:var(--color-FFE); display:none" id="qt">

First, let's clarify what a quaternion-based rotor is. A rotor's definition is actually only the geometric algebra one. The reason there are "geometric algebra" and "quaternion" versions is that different representations are chosen, like different coordinate systems. The geometric algebra version of the rotor directly expands all its components in a Cartesian coordinate system, while the quaternion-based rotor version factorizes the rotor into a left and a right isoclinic rotation part. Each part is analogous to a rotation in 3D space, so it is represented by two quaternions. But note that regardless of the representation, the special "two-to-one" property of rotors remains unchanged. For example, a 90-degree rotation in the $xy$ plane can be decomposed in two ways:

1.  A "right isoclinic double rotation with 45-degree rotation in the $xy$ plane and 45-degree rotation in the $zw$ plane" composed with a "left isoclinic double rotation with 45-degree rotation in the $xy$ plane and -45-degree rotation in the $zw$ plane".
2.  A "right isoclinic double rotation with -135-degree rotation in the $xy$ plane and -135-degree rotation in the $zw$ plane" composed with a "left isoclinic double rotation with -135-degree rotation in the $xy$ plane and 135-degree rotation in the $zw$ plane".

To give a more extreme example, a 180-degree rotation in both the $xy$ and $zw$ planes is a central inversion, which is the only double rotation without chirality. Viewing it as a left/right isoclinic double rotation corresponds to two rotor representations:

1.  A "right isoclinic double rotation with 180-degree rotation in the $xy$ plane and 180-degree rotation in the $zw$ plane" composed with a "left isoclinic double rotation with 0-degree rotation in the $xy$ plane and -0-degree rotation in the $zw$ plane".
2.  A "right isoclinic double rotation with 0-degree rotation in the $xy$ plane and 0-degree rotation in the $zw$ plane" composed with a "left isoclinic double rotation with 180-degree rotation in the $xy$ plane and -180-degree rotation in the $zw$ plane".

The pseudocode for defining the quaternion-based rotor class is simple; it contains two quaternions, left and right:

```typescript
class Rotorq{
    l: Quaternion; r: Quaternion;
}
```

We don't need to define addition or scalar multiplication for `Rotorq`. Unlike geometric algebra, which represents both 2-vectors and rotations, the quaternion-based rotor here cannot represent linear 2-vectors, so addition and scalar multiplication are meaningless. We will only define multiplication and inverse later.

#### Rotation Coordinate Algorithm

In 3D space, a spatial vector $v=(x,y,z)$ needs to be mapped to a purely imaginary quaternion $V=ix+jy+kz$. In 4D space, the coordinates have a one-to-one correspondence, and the spatial vector $v=(x,y,z,w)$ is directly mapped to the quaternion $V=x+iy+jz+kw$. Given a rotor $R$, since the left isoclinic part (self-dual generator) corresponds to left multiplication and the right isoclinic part (anti-self-dual generator) corresponds to right multiplication, let its left isoclinic rotation quaternion be $R\_l$ and its right be $R\_r$. The formula for calculating the coordinates of $R(p)$ after acting on a coordinate $p$ is $R\_lpR\_r$, i.e.,

```typescript
function apply(R:Rotorq, p:Vec4){
  let P = new Quaternion(p); // Convert vector p to quaternion P.
    return new Vec4(R.l.mul(P).mul(R.r)); // Convert the calculated quaternion back to a vector.
}
```

#### Bivector Rotation Algorithm

The quaternion-based rotor also has a more direct algorithm for acting on 2-vectors (planes), without needing a 6x6 matrix in the 6D linear space of 2-vectors. We decompose the 2-vector $B$ into its self-dual and anti-self-dual parts: $B=B\_a+B\_b=(B+B^*)/2+(B-B^*)/2$. These can be isomorphic to 3D vectors in the following way. For the self-dual part:
$(x\_a,y\_a,z\_a)$ corresponds to $B\_a = x\_a (e\_{xy}+e\_{zw}) + y\_a (e\_{xz}-e\_{yw}) + z\_a (e\_{xw}+e\_{yz})$
For the anti-self-dual part:
$(x\_b,y\_b,z\_b)$ corresponds to $B\_b = x\_b (e\_{xy}-e\_{zw}) + y\_b (e\_{xz}+e\_{yw}) + z\_b (e\_{xw}-e\_{yz})$

The left/right isoclinic rotations are isomorphic to (strictly speaking, a two-to-one homomorphism with) ordinary 3D rotations of 3D vectors. Therefore, we can use the left and right quaternions to rotate these two vector parts, and finally convert the 2-vector back to the ordinary Cartesian coordinate representation. Note that although these left and right isoclinic rotation spaces are locally isomorphic to 3D space, the isomorphic mapping on quaternions is not necessarily an identity map. Conjugation is also an isomorphic map, and by working through a specific example, one can find that the right isoclinic rotation applied via right multiplication requires conjugation. Note: The deeper reason why the left multiplication does not require conjugation but the right multiplication does is that the composition of general rotations is left multiplication, and a homomorphic mapping to right multiplication must be inverted to be consistent.

```typescript
function apply(r: Rotorq, b: Bivec): Bivec {
    // Calculate the self-dual and anti-self-dual parts of the 2-vector.
    let A = new Vec3(this.xy + this.zw, this.xz - this.yw, this.xw + this.yz);
    let B = new Vec3(this.xy - this.zw, this.xz + this.yw, this.xw - this.yz);
    // Rotate the dual component with the left isoclinic rotation.
    A = apply(r.l, A);
    // Rotate the anti-dual component with the right isoclinic rotation. Note that this isomorphism requires a conjugation map.
    B = apply(r.r.conj(), B);
    // Restore the rotated 2-vector from the dual decomposition representation to the ordinary Cartesian representation.
    return new Bivec(
        A.x + B.x, A.y + B.y, A.z + B.z, A.z - B.z, B.y - A.y, A.x - B.x
    ).mul(0.5);
}
```

#### Rotation Composition and Inverse

Since the quaternion-based rotor has two parts, both parts must be composed separately, and the order of rotation must be considered. For example, to rotate by R1 then R2: $$R_{2l}(R_{1l}pR_{1r})R_{2r}=(R_{2l}R_{1l})p(R_{1r}R_{2r})$$
This means the new $R\_l=R\_{2l}R\_{1l}$ and $R\_r=R\_{1r}R\_{2r}$. Thus, we define the rotor multiplication as:

```typescript
// Rotating by R1 and then by R2 is defined as r2 multiplied by r1:
mul(r2:Rotorq, r1:Rotorq){
    return new Rotorq(r2.l.mul(r1.l), r1.r.mul(r2.r));
}
```

The inverse of a rotor is found by taking the inverse (conjugate) of its left and right quaternions respectively:

```typescript
// Find the inverse of rotor r.
inverse(r:Rotorq){
    return new Rotorq(r.l.conj(), r.r.conj());
}
```

#### Plane-Angle Rotation Generation

Since the left and right isoclinic rotations do not interfere with each other, we can decompose the generator 2-vector into a self-dual and an anti-self-dual vector. Because the structure of left and right isoclinic rotations is similar to 3D rotations, we can apply the 3D rotation generation formula for quaternions:

```typescript
exp(p:Bivec): Rotorq {
    // A is the self-dual part of p, B is the anti-self-dual part.
    let A = new Vec3(p.xy + p.zw, p.xz - p.yw, p.xw + p.yz).mul(0.5);
    let B = new Vec3(p.xy - p.zw, p.xz + p.yw, p.xw - p.yz).mul(0.5);
    // a and b are the magnitudes of the self-dual/anti-self-dual 2-vectors.
    let a = A.norm(); let b = B.norm();
    // Use the 3D 2-vector to quaternion generation formula to calculate the left and right isoclinic quaternion parts separately.
    // Note that since the vector length was divided by 2 during the dual decomposition, the rotation angle here no longer needs to be divided by 2.
    let sa = Math.sin(a) / a;
    let sb = Math.sin(b) / b;
    return new Rotorq(
        new Quaternion(Math.cos(aa), sa * A.x, sa * A.y, sa * A.z),
        new Quaternion(Math.cos(bb), sb * B.x, sb * B.y, sb * B.z)
    );
}
```

#### lookAt Rotation Generation

The lookAt algorithm for generating a rotation given initial and final vectors does not depend on the specific rotor representation used. Please refer to the <a href='javascript:void(0);' onclick='if($("\#qa\_").text()=="+"){$("\#ga").show();$("\#ga\_").text("-");}window.location.href="\#lookat";'>lookAt algorithm in the Geometric Algebra version section</a>.
In 4D space, besides rotating a vector to align with another vector, there are two new requirements: aligning a vector with a plane, and aligning a plane with another plane.

1.  Vector to plane alignment: Given a vector $v$ and a simple, normalized 2-vector representing a plane $B$, find the rotation that rotates $v$ into plane $B$ by the shortest distance. We can use a geometric product trick to find the projection of vector $v$ onto plane $B$, and then use the ordinary vector-to-vector lookAt function to rotate $v$ to its projection. This fulfills the requirement: a certain partial inner product can be defined between a vector and a 2-vector as the 1-vector part of their geometric product. It is easy to verify that if they are both normalized, the new vector obtained by the inner product of the vector and the 2-vector is the projection of the vector onto the plane, rotated 90 degrees in the plane's vortex direction. We can take the inner product of this new vector with the plane again to get the vector opposite to the projection, and then take the inner product to get the projection. Thus, we define the projection and lookAt functions as follows:

<!-- end list -->

```typescript
function project(v:Vec4,b:Bivec):Vec4{
    // The dot function is the inner product of a vector and a 2-vector, neg negates the vector.
    return v.dot(b).dot(b).neg();
}
function lookAt(v:Vec4,b:Bivec):Rotorq{
    // Find the projection and normalize it.
    let pv = project(v,b).normalize();
    return lookAt(v, pv);
}
```

Note: The above algorithm works for both versions of rotors.

2.  Plane to plane alignment: Given two simple, normalized 2-vectors representing planes $A$ and $B$, find the rotation that rotates $A$ to overlap with $B$ by the shortest distance. Although one could select two vectors on plane $A$ and project them onto plane $B$, using multiple vector-to-vector lookAt methods, there are many directions on a plane, so selecting the directions is a troublesome problem, not to mention handling various special cases like perpendicularity and opposition. I found an elegant algorithm that only applies to the quaternion-based rotor version: we can perform a dual decomposition on the 2-vectors. The left isoclinic rotation of this rotation will align the self-dual parts of the two 2-vectors, while the right isoclinic rotation will align the anti-self-dual parts. Since the self-dual/anti-self-dual 2-vectors are isomorphic to 3D space, we can use the 3D vector-to-vector lookAt function to achieve alignment. The code is as follows:

<!-- end list -->

```typescript
function lookAt(A: Bivec, B: Bivec): Rotorq {
    let Aa = new Vec3(A.xy + A.zw, A.xz - A.yw, A.xw + A.yz);
    let Ab = new Vec3(A.xy - A.zw, A.xz + A.yw, A.xw - A.yz);
    let Ba = new Vec3(B.xy + B.zw, B.xz - B.yw, B.xw + B.yz);
    let Bb = new Vec3(B.xy - B.zw, B.xz + B.yw, B.xw - B.yz);
    // Note that this lookAt is the 3D vector-to-vector one defined earlier, this is an overload, not a recursive call to itself.
    return new Rotorq(lookAt(Aa, Ba), lookAt(Bb, Ab));
}
```

Note that although these left and right isoclinic rotation spaces are locally isomorphic to 3D space, for the same reason, the quaternion transformation of the right isoclinic component in right multiplication requires conjugation (i.e., the inverse of the rotation), so the second parameter returned is `lookAt(Bb, Ab)` instead of `lookAt(Ab, Bb)`.

#### Rotation Matrix to Rotor

This is implemented by using the lookAt function multiple times. The method is exactly the same as for the geometric algebra version of rotors. Please refer to <a href='javascript:void(0);' onclick='if($("\#ga\_").text()=="+"){$("\#ga").show();$("\#ga\_").text("-");}window.location.href="\#lookat";'>here</a>.<a name="rotorq\_log"></a>

#### Given a Rotation, Find the Plane and Angle

Besides using the Taylor expansion of the logarithmic map, the quaternion version has a more convenient approach: since a rotor is composed of two quaternions representing isoclinic rotations, we can use the 3D rotation method to find the generator to get the corresponding self-dual and anti-self-dual 2-vectors, and finally synthesize them to restore the entire 2-vector:

```typescript
function log(r: Rotorq): Bivec {
    // Calculate log(r.l). Since this is only half of the rotation, the angle does not need to be multiplied by 2 when restored.
    // Therefore, we cannot directly call the 3D quaternion logarithmic function from before.
    let ls = Math.acos(r.l.x);
    let a = new Vec3(r.l.y, r.l.z, r.l.w).mul(ls / Math.sin(ls));
    // Calculate log(r.r). The method is the same as above.
    let rs = Math.acos(r.r.x);
    let b = new Vec3(r.r.y, r.r.z, r.r.w).mul(rs / Math.sin(rs));
    // Merge the generators of the left and right halves and convert to the ordinary Cartesian representation.
    return new Bivec(
        a.x + b.x, a.y + b.y, a.z + b.z, a.z - b.z, b.y - a.y, a.x - b.x
    );
}
```

</div>

<br/>

## Generating Random Directions/Rotations

### Generating a Random Unit Vector

The problem of generating a random unit vector is equivalent to generating a uniformly distributed point on a sphere. This problem is well-studied in mathematics: first, generate random numbers uniformly over an interval, and then map them to the latitude and longitude values of the spherical coordinates using a nonlinear function based on the "scaling" coefficients of the coordinate mapping. The pseudocode is listed directly below:

```typescript
function randVec3(){
    let a = Math.random() * 2.0 * Math.PI;
    let c = Math.random() * 2.0 - 1.0;
    let b = Math.sqrt(1.0 - c * c);
    return new Vec3(b * Math.cos(a), b * Math.sin(a), c);
}
function randVec4(){
    let a = Math.random() * 2.0 * Math.PI;
    let b = Math.random() * 2.0 * Math.PI;
    let c = Math.random();
    let sc = Math.sqrt(c);
    let cc = Math.sqrt(1.0 - c);
    return new Vec4(sc * Math.cos(a), sc * Math.sin(a), cc * Math.cos(b), cc * Math.sin(b));
}
```

As a side note, these two functions are very useful for generating photorealistic renders with path tracing, as mentioned in the [previous article](https://www.google.com/search?q=/archives/cg4d/).<a name="randrotor"></a>

### Generating a Random Rotation

Since a 3D rotation can be represented by a unit quaternion, which is isomorphic to a hypersphere, we can use the algorithm for generating a random 4D unit vector to generate a quaternion. If a quaternion-based rotor is used, a 4D rotation is represented by two quaternions, left and right, so two random points on the hypersphere can be generated. If a geometric algebra-based rotor is used, I am currently unaware of a convenient generation algorithm.<a name="randsimplbiv"></a>

```typescript
function randQuaternion():Quaternion{
    return new Quaternion(randVec4());
}
function randRotorq():Rotorq{
    return new Rotorq(randQuaternion(), randQuaternion());
}
```

### Generating a Random Simple Rotation

The random rotation algorithm above generates an arbitrary orientation. If a simple rotation is required, one must first generate a simple unit 2-vector with a random orientation, and then generate a random angle to achieve it. The simplest way to generate a simple unit 2-vector with a random orientation is to first generate two random vectors, use the wedge product to span a 2-vector, and then normalize it. There is actually a faster generation method: the self-dual and anti-self-dual parts of a simple 2-vector have equal magnitudes, so their lengths are both $\\sqrt 2/2$. Since each part is equivalent to a 2D sphere, one can generate these unit self-dual/anti-self-dual 2-vectors by generating random points on a 2D sphere, and then multiply them by $\\sqrt 2/2$ and add them together to get a normalized simple 2-vector.

## Common 4D Camera Controller

### TrackBall Control Mode

This mode is similar to the view rotation in common 3D software, and corresponds to the operation style of Jenn3D in 4D. This controller stores a rotation center point and a distance, plus a rotor for the camera's orientation. In this mode, the camera always faces the center point. Assuming the camera's forward direction in its local coordinate system is the w-axis, its position can be updated as follows:

```typescript
class TrackBallController{
    // Rotation center coordinates
    center: Vec4;
    // Distance from the camera to the center
    distance: number;
    // Camera orientation
    rotor: Rotorq;
    update(camera: Object4D){
        // Update camera rotation
        camera.rotation = this.rotor;
        // The initial position is set to a vector of a given length in front of the camera. The camera's position relative to the center is found through the camera's orientation, and finally the center is added.
        camera.position = center.add(apply(this.rotor,new Vec4(0,0,0,distance)));
    }
}
```

The user interacts with the keyboard and mouse to update the distance and the rotor. For example, when the mouse is dragged left, right, up, or down with the left button, the movement in each frame is converted into a 2-vector $B$ in the camera's local coordinate system with only $yw$ and $xw$ components. The rotation generated by it, $V'=\\exp(B)$, is first transformed to world coordinates, $V=RV'R^{-1}$, and then superimposed on the current camera orientation $R$ to get the total rotation $VR = RV'R^{-1}R=RV'=R\\exp(B)$. This can be summarized as: if the superimposed rotation is expressed in the rotating object's local coordinate system, use right multiplication; if both are in the same global coordinate system, use the usual left multiplication. The pseudocode is as follows:

```typescript
class TrackBallController{
    ...
    onLeftButtonDrag(dx:number, dy:number){
        // Generate a 2-vector based on the mouse movement distance: dx*exw + dy * eyw
        let localBivec = new Bivec(0, 0, dx, 0, dy, 0);
        // Generate rotation R
        let localRotation = exp(localBivec);
        // Transform it to the world coordinate system, superimpose the rotation, and update the new camera orientation. Note that the superimposed rotation is in local coordinates, so the multiplication order is reversed.
        this.rotor = localRotation.mul(this.rotor);
    }
}
```

### FreeFly Control Mode

This mode generally simulates a first-person walk in a zero-gravity environment. It doesn't need to store separate information; it directly reads the camera's position and orientation and updates them based on the user's keyboard and mouse input. It's important to note that the user's rotation and translation should be defined in the camera's local coordinate system before being converted to world coordinates. Pseudocode is not provided here.

### KeepUp Control Mode

This mode generally simulates a first-person walk in a gravity environment, requiring the plane spanned by the camera's ana/kata direction and left/right direction to remain horizontal in the horizontal cell without tilting. There are two methods: the first is to use a technique similar to Euler angles, using two rotors to store the horizontal and vertical rotations separately; the second is to correct the tilt after each rotation. In practical use, I found the second method to have poorer stability, so the tesserxel engine uses the first method. The degrees of freedom for this rotation are only 4, consisting of a 3D free rotation within the horizontal cell plus a vertical pitch angle. Therefore, we use a rotor and a pitch angle to store the rotation. I use mouse dragging to control the rotation within the horizontal cell and the mouse wheel to control the pitch angle. Finally, the camera's orientation is updated each frame as follows:

```typescript
class KeepUpController{
    // The horizontal orientation is represented by a rotor.
    rotorH: Rotorq;
    // The vertical pitch angle is represented by a scalar number.
    rotorV: number;
    update(camera: Object4D){
        // Calculate the vertical rotation that generates the pitch angle in the camera's local coordinate system, considering only the horizontal cell rotation. It is generated by a 2-vector on the yw plane.
        let verticalRotation = planeAngle2rotorq(new Bivec(0,0,0,0,1,0), this.rotorV);
        // The final rotation should be composed in the order of horizontal then vertical rotation, but the superimposed rotation is in local coordinates, so the multiplication order is reversed.
        camera.rotation = rotorH.mul(this.verticalRotation);
    }
}
```

There are two modes for controlling camera movement: one is the flight mode, where besides using the above method to keep the horizontal cell from tilting during rotation, the experience is similar to free-flight mode. That is, if the camera has a pitch angle, moving forward and backward will change the camera's height. The second is the ground roaming mode, where moving the camera forward and backward does not consider the pitch angle, and always moves within the ground plane (like a player walking on the ground in Minecraft), with additional keyboard interaction to directly control the vertical ascent and descent of the camera.