[A link to the original note in case I didn't/couldn't fix Github's display issues](https://share.note.sx/t2v6eqnm#fUPvMK3tKhQrX/EC5ooHO0g9nj9j537hXQ8lQSgODi8)

# Introduction
The [Bernstein-Vazirani](https://young.physics.ucsc.edu/150/bv.pdf) problem is like [Deutsch-Joza Algorithm](https://www.qi.damtp.cam.ac.uk/files/QIC-9.pdf), A black box algorithm that demonstrates exponential speedup on a QTM(Quantum Turing Machine) compared to a DTM(Deterministic Turing Machine). [1]

# Complexity
The problem has a complexity class of BQP(bounded-error quantum polynomial time) and was used to show if ${} BQP = BPP {}$ -bounded-error probabilistic polynomial time-.
Which could lead to physical systems being efficiently simulated on DTMs. [1]

# Problem Statement
Consider a function ${} f(x) = a \cdot x {}$, where `dot` means a bitwise inner product modulo 2 addition. [2]
$$a \cdot x \equiv a_0x_0 \oplus a_1x_1 \oplus \dots \oplus a_{n-1}x_{n-1}$$
### Example
let n = 4 (the number of bits in each number)
a = 110**1**
x = 111**0**
We started the [LSB](https://bit-calculator.com/most-and-least-significant-bit) (Least significant bit i.e the right most bit shown in bold).

we start with 

$$ a \cdot x = (1 \times 0) + (0 \times 1) + (1 \times 1) + (1 \times 1)$$

gives us

$$a \cdot x = 0 + 0 + 1 + 1 = 2$$

$$2 \\% 2 = 0$$

> `%` is the modulo operator in many programming languages.
# Theory
## Classically
We can determine the bits of `a` one at a time by applying ${} f(2^k) {}$ where k is the position of the bit.

to show this, we start with defining `a` as a sum of numbers
$$a = a_0+a_12^1+ \dots + a_{n-1}2^{n-1}$$

let ${} k = 3 {}$ , ${} 2^3 = 8 = 0001 {}$

$$a_3 = a_0 \times 0 + a_1 \times 0 + a_2 \times 0 + a_3 \times 1 = 0 + 0 + 0 + a_3$$

Leads to ${} f(2^k) = a_k {}$ as only ${} x_k = 1 {}$, and we have to do that for each bit for `a`; hence, we need `n` operations to get our result.
### Generalisation
${} f(1, 0, 0, \dots, 0)$ gets $s_0$

$f(0, 1, 0, \dots, 0)$ gets ${} s_1 {}$

[//]: <> (An implementation will be attached as classic.py and also in the notebook.)

## Quantum
### Preliminaries 

We start by defining ${} \mathbf{U}_f {}$ as a [reversible unitary transformation](https://youtu.be/dD-oYfhSKhg) as

$$\mathbf{U}_f(\ket{x}_n\ket{y}_m) = \ket{x}_n\ket{y \oplus f(x)}_m$$

We can see the reversibility of ${} \mathbf{U}_f$ as following:

$$\mathbf{U}_f\mathbf{U}_f(\ket{x}\ket{y}) = \mathbf{U}_f(\ket{x} \ket{y \oplus f(x)}) = \ket{x}\ket{y \oplus f(x) \oplus f(x)} = \ket{x}\ket{y}$$

We define ${} \mathbf{H} {}$ as the the [Hadamard operator](https://www.wikiwand.com/en/Quantum_logic_gate#Hadamard_gate) which has the following property known as n-fold [tensor product](https://www.wikiwand.com/en/Tensor_product)

$$\mathbf{H}^{\otimes n} \ket{0}n = \frac{1}{2^{n/2}} \sum_{0 \leq x \lt 2^n} \ket{x}_n$$

where

$$\mathbf{H}^{\otimes n} = \mathbf{H} \otimes \mathbf{H} \otimes \dots \otimes \mathbf{H} \text{ for n times}$$

We use this to convert the state into a `an equal weighted superposition` of all possible inputs, we then apply our operator ${} \mathbf{U}_f {}$ and by linearity


$${} \mathbf{U}_f(\mathbf{H^{\otimes n} \otimes 1_{m}}) = \frac{1}{2^{n/2}} \sum_{0 \leq x \lt 2^n} \mathbf{U}_f(\ket{x}_n \ket{0}_m) {}$$


$$ = \frac{1}{2^{n/2}} \sum_{0 \leq x \lt2^n} \ket{x}_n \ket{f(x)}_m$$

So what happened here is that we achieved what's called *Quantum Magic*, more formally `Quantum Parallelism` when we applied ${} \mathbf{H}^{\otimes {n}} {}$ on the state, then we used the unitary operator we defined earlier ${} \mathbf{H}_f {}$ to encode the result into output. 

I'm not a maths expert by any means, my reference for this part is N. David Mermin's Book Quantum Computer Science[3], it provides better explanation and more details on this topic.

Now, we shall continue with our problem.
### Quantum Speedup
We start with our statement defined earlier

$$a \cdot x \equiv a_0x_0 \oplus a_1x_1 \oplus \dots \oplus a_{n-1}x_{n-1}$$

As we discussed before, the runtime of the classical implementation is `O(n)` i.e: we need `n` operations to get `a`. However, using *Quantum Magic* we need only a single invocation-aka O(1)- to get `a` we are looking for.

We start by defining the operation

$$\mathbf{H}\mathbf{X} \ket{0} = H\ket{0} = \frac{1}{\sqrt{2}}(\ket{0} - \ket{1})$$

We then apply $\mathbf{U}_f$, which has a property that flips they value of $y {}$ -of output register- to 1 when applied on ${} \ket{x}_n \ket{y}_1 {}$ state, iff $f(x) = 1$.

$$\mathbf{U}_f \ket{x}_n \frac{1}{\sqrt{2}}(\ket{0} - \ket{1}) = (-1)^{f(x)}\ket{x}_n \frac{1}{\sqrt{2}}(\ket{0} - \ket{1})$$

We perform a `bit flip` on the state of the output ${} \frac{1}{\sqrt{2}}(\ket{0} - \ket{1}) {}$ to change its sign, so we summarise to the following:  

$$H \lvert x \rangle_1 = \frac{1}{\sqrt{2}} \left( \lvert 0 \rangle + (-1)^x \lvert 1 \rangle \right) = \frac{1}{\sqrt{2}} \sum_{y=0}^{1} (-1)^{xy} \lvert y \rangle$$

If we apply ${} H^{\otimes n} \ket{x}_n {}$ to an n-Qbit computational-basis state ${} \ket{x}_n$ we get

$$H^{\otimes n} \lvert x \rangle_n = \frac{1}{2^{n/2}} \sum_{y_{n-1}=0}^{1} \cdots \sum_{y_0=0}^{1} (-1)^{\sum_{j=0}^{n-1} x_j y_j} \lvert y_{n-1} \rangle \cdots \lvert y_0 \rangle$$

$$= \frac{1}{2^{n/2}} \sum_{y=0}^{2^n-1} (-1)^{x \cdot y} \lvert y \rangle_n$$

Where the product of ${} x \cdot y {}$ is our problem statement, and -1 is raised to $\sum x_j y_j$, represents our operation (the dot product modulo 2).

So we start with n-Qbit input registers with ${} \mathbf{H}^{\otimes n} \ket{0} {}$ applied on it, then put the 1-Qbit output register into the state ${} \mathbf{H} \ket{1}$, apply ${} \mathbf{U}_f$ and then apply ${} \mathbf{H}^{\otimes n} {}$ to the input register again

$$(\mathbf{H}^{\otimes n} \otimes 1) \mathbf{U}_f(\mathbf{H}^{\otimes n} \mathbf{H}) \ket{0}_n\ket{1}_1 = $$

$$\dots$$

$$= \frac{1}{2^n} \sum_{x=0}^{2^n-1} \sum_{y=0}^{2^n-1} (-1)^{f(x) + x \cdot y} |y\rangle \frac{1}{\sqrt{2}} (|0\rangle - |1\rangle)$$

I trimmed the equation, you can see the full form of it in the book (2.31)[3].

We then sum over over ${} x {}$ first. If the function ${} f(x) {}$ is ${} a \cdot x {}$ then this sum produces the factor[3].

$$\sum_{x = 0}^{2^{n-1}} (1)^{(a \cdot x)}(-1)^{(y \cdot x)} = \prod_{j = 1}^{n}\sum_{x_j=0}^{1}(-1)^{(a_j + y_j) x_j}$$

"At least one term in the product vanishes unless every bit ${} y_j {}$ of $y$ is equal to the corresponding bit ${} a_j {}$ of a – i.e. unless ${} y = a {}$. Therefore the entire computational process reduces to "[3]

$$\mathbf{H}^{\otimes(n+1)} \mathbf{U}_f \mathbf{H^{\otimes (n+1)}} \ket{0}_n \ket{1}_1 = \ket{a}_n\ket{1}_1$$

And we are done, the final circuit looks like this
![](https://i.imgur.com/QpA7QYX.png)

It will be implemented in the notebook as well.
# References
1. [Bernstein, Ethan, and Umesh Vazirani. "Quantum complexity theory." Proceedings of the twenty-fifth annual ACM symposium on Theory of computing. 1993.](https://dl.acm.org/doi/pdf/10.1145/167088.167097).
2. [Prof. Peter Young, University of California Santa Cruz](https://young.physics.ucsc.edu/150/bv.pdf)
3. [Mermin, N. David. _Quantum computer science: an introduction_. Cambridge University Press, 2007.](https://www.google.com.eg/books/edition/Quantum_Computer_Science/q2S9APxFdUQC?hl=en&gbpv=1&dq=Quantum%20Computer%20Science&pg=PA1&printsec=frontcover)

# Additional Resources 
- [Time and Space Classes and P Exposition by William Gasarch, University of Maryland](https://www.cs.umd.edu/~gasarch/COURSES/452/F14/p.pdf)
- [Proof of generalisation of n-fold tensor product](https://math.stackexchange.com/questions/2637995/universal-property-of-the-n-fold-tensor-product#2639046) 
- [MIT Tensor Product notes](https://ocw.mit.edu/courses/8-05-quantum-physics-ii-fall-2013/ffe665c0cba2eae19a83e88dec42925c_MIT8_05F13_Chap_08.pdf)
- [Medium Article about the problem](https://medium.com/quantum-untangled/the-bernstein-vazirani-algorithm-quantum-algorithms-untangled-67e58d4a5096) 
- [A helpful YouTube video](https://youtu.be/f0ChZip0u9I?list=TLPQMjcwNjIwMjRfl-NFr2YziA)
