# Solutions to the exercises of “Real & Complex Analysis (Rudin)”



## Chapter 1 Abstract Integration

### Exercise 1 

Problem:

Does there exist an infinite $\sigma$-Algebra which has only countably many member ?

Solution:

No. A proof I learned during my studies involves the following four steps:

1. For $x,z \in \Omega: x \in M_z \Leftrightarrow z \in M_x$, where $M_y = \bigcap \{A \in \mathscr{A}: y \in A\}$ for every $y \in \Omega$.
2. For all $x,y \in \Omega$ either $M_x = M_y$ or $M_x \cap M_y = \emptyset$.
3. For each $A \in \mathscr{A} $ is $ A = \bigcup _{x \in A} M_x$.
4. Combine 1.-3. to conclude that if $\mathscr{A}$ was countably-infinite then $\mathscr{P}(\mathbb{N})$ would be, which is false.

I will try to fill out the details per step:

1. -
2. -
3. -
4. Suppose that $\mathscr{A}$ ist countably-infinite. Because of step 3. there must be infinitely many different $M_x$(**why**?). Let $(x_k)_{k \in \mathbb{N}}$ be an enumeration of these (pairwise) different and according to step 2. (pairwise) disjunct $M_x$. Consider $\Phi: \mathscr{P}(\mathbb{N} )\rightarrow \mathscr{A}$ with $N \mapsto \bigcup_{n \in N} M_{x_n}$. The map $\Phi$ has to be injective (**write out argument**).

