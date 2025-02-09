# hypothesis complexity and generalisation

*practice finding VC dimension*

What kind of hypothesis class $H$ do we choose before searching for $h$?
$$ h \in H $$
We want the target function $c$ in universal function space to be in $H$.

$$c = \arg \min_h R(h) $$
## notation

**empirical risk** (empirical loss (i.e., an estimation of EV)):

$$ R_S(h) = \frac {1}{n} \sum_{i=1}^n \ell(X_i, Y_i, h) $$
**expected risk** (expected loss):
$$R(h) = \mathbb{E} [ R_S(h)]
= \mathbb{E}[\ell (X, Y, h)] $$
$$ = \int_{(X,Y)} \ell(X, Y, h) p(X,Y) \, dXdY $$

while target function $c$ (in universal function space) is given above, the optimal hypothesis function inside $H$ is denoted $h^*$ 
$$ h^* = \arg \min_{h \in H} R(h) $$

the hypothesis learned from data $S$ is denoted
$$h_S = \arg \min_{h \in H} R_S(h)$$
which we learn using empirical loss, as above.

**approximation error**:
- arises from difference between expected risks of h* and c
$$R(h^*) - R(c)$$
- if target $c$ is in $H$, this will be zero

**estimation error**:
- arises from difference between expected risks of $h^*$ and $h_S$
$$R(h_S) - R(h^*) $$
## PAC learning framework

'Probably approximately correct' learning framework.
Arises because if $H$ is too large and complex, the estimation error becomes large.
Explains how many training samples are needed to learn the best $h$ in $H$.

A hypothesis class $H$ is PAC-learnable if there is a learning algorithm $\mathcal{A}$ and a polynomial function $\text{poly}(\cdot , \cdot)$ s.t for any $\epsilon > 0$ and $\delta > 0$, for the whole domain $D$ of $X \times Y$, the following probability holds for 
- any sample size $n > \text{poly} ( 1 / \delta , 1 / \epsilon)$ 
- $h_S$ learned by $\mathcal{A}$
$$p \biggl( R(h_S) - R(h^*) \le \epsilon  \biggr) \ge 1 - \delta $$
This shows that when sample size is large enough, the learned hypothesis $h_S$ is an approximation of the best $h^*$ in $H$, i.e. expected estimation error is small.

If $H$ is too complex, we need more training samples, e.g. size given by
$$ n> \exp (1/\delta , 1/\epsilon) $$
and so $H$ is not PAC-learnable in this case.

When we learn $h_S$ using empirical risk minimisation, this 'verifies' (if we see evidence that $h_S$ approximates $h^*$) that $H$ is PAC-learnable.

### how do we decide if $H$ is PAC-learnable

The below proof shows that if $H$ has a finite size it is PAC-learnable. It's a bit how ya going though.

In this proof, we aim to prove an upper bound for $p \bigl( \text{magic} \ge \epsilon  \bigr)$, and then we call this upper bound $\delta$. i.e. we prove 
$$p \bigl( \text{magic} \ge \epsilon  \bigr) \le \delta$$
- where $2 (\text{magic}) \ge R(h_S) - R(h^*)$ 

Once we have the above expression, it is rewritten (since it's a probability) as $p( \text{magic} \le \epsilon) \ge 1 - \delta$.  $\text{magic}$ is then substituted to match our definition of PAC, that $p( R(h_S) - R(h^*) \le \epsilon) \ge 1 - \delta$ . From the final expression, it is clear by inspection that $H$ must be finite sized. 

We start writing an expression for expected estimation error (and showing it is $\le 2 (\text{magic})$).
$$R(h_S) - R(h^*) $$
We add in empirical risk terms while maintaining equality.
$$ = R(h_S) - R_S(h_S) + R_S(h_S) - R_S(h^*) + R_S(h^*) - R(h^*)   $$
notice $R_S(h_S)$ $\le$ $R_S(h^*)$, so we have
$$ \le R(h_S) - R_S(h_S) + R_S(h^*) - R(h^*) $$
which is bounded by the absolute value of each subtractive component
$$ \le \bigl|  R(h_S) - R_S(h_S) \bigr| + 
\bigl| R(h^*) - R_S(h^*)\bigr| $$
which is bounded by supremums[^1] with supersets generated by $H$. Notice also $\bigl| R(h^*) - R_S(h^*)\bigr| \le \sup_{h \in H} \bigl|  R(h) - R_S(h) \bigr|$. Consider that the $\sup$ represents the largest possible difference betewen expected risk and empirical risk, in $\mathcal{H}$.
$$\le \sup_{h \in H} \bigl|  R(h) - R_S(h) \bigr| 
+ \sup_{h \in H} \bigl|  R(h) - R_S(h) \bigr|$$
$$ = 2 \sup_{h \in H} \bigl| R(h) - R_S (h) \bigr| $$
[^1]: **supremum**: the supremum of some set $S$ is the minimal value in the superset of $S$, which is $\ge$ every value in $S$.

So we've shown that expected estimation error is $\le 2(\text{magic})$.
$$R(h_S) - R(h^*) \le 2\sup_{h \in H}\bigl| R(h) - R_S (h) \bigr| \quad (*) $$
We now set about getting $p(\text{magic} \ge \epsilon)$, for some $\epsilon > 0$, to be upper bounded by an expression that we will call $\delta$.

Notice from the above reasoning, we also found an inequality for **generalisation error**, where generalisation error is upper bounded by the same $\text{magic}$.
$$R(h_S) - R_S(h_S) \le \sup_{h \in H}\bigl| R(h) - R_S (h) \bigr|  \quad (**)$$
- (difference between expected risk and empirical risk for found $h_S$)

Notice with large enough sample size, $R_S(h)$ will converge to $R(h)$, asymptotically. While typically we find empirical mean to approximate under an asymptotic regime, we will now use measurement of difference between $R_S(h)$ and $R(h)$, as though under a non-asymptotic regime.

Namely, we upper bound the probability of absolute generalisation error being greater than some value $\epsilon$, using Hoeffding's inequality. We then turn this into an upper bound for $p(\text{magic} \ge \epsilon)$.

**Hoeffding's inequality**:
Let $X_1 \dots X_n$ be independent random variables, such that $X_i \in [a_i, b_i]$ with probability 1. Let $S_n = \frac{1}{n} \sum_{i=1}^n X_i$ . Then probability that difference between $S_n$ and its expected value will be higher than some $\epsilon$ is upper bounded as follows
$$p \bigl( |S_n - \mathbb{E}[S_n]| > \epsilon   \bigr)
\le 2 \exp \biggl( 
\frac {-2n^2 \epsilon^2} {\sum_{i=1}^n (b_i - a_i)^2}
\biggr)$$
for any $\epsilon > 0$.

Now consider that loss functions on varying training sets are independent random variables. We can assume that each $\ell (X_i, Y_i, h) \in [0, M]$. So we have
$$p \bigl( | R_S(h) - R(h)| > \epsilon   \bigr)
\le 2 \exp \biggl( 
\frac {-2n^2 \epsilon^2} {M^2}
\biggr) \quad (***)$$
for any $\epsilon > 0$. 

So we now aim to use $(***)$ to upper bound our $p(\text{magic} \ge \epsilon)$.

**union bound**:
For any events $A_1 \dots A_n$  we have
$$p(\cup_{i=1}^n A_i) \le \sum_{i=1}^n p(A_i) $$
**when A implies B**:
$$p(A) \le p(B) $$
Using implication we have
$$p \bigl( \sup_{h \in H}|R(h) - R_S(h)| \ge \epsilon   \bigr)
\le p \bigl( \cup_{h \in H} |R(h) - R_S(h) | \ge \epsilon \bigr)
$$
Using union bound we have
$$ p \bigl( \cup_{h \in H} |R(h) - R_S(h) | \ge \epsilon \bigr)
\le \sum_{h \in H} p \bigl( |R(h) - R_S(h)| \ge \epsilon \bigr)
$$
And finally from $(***)$ we have 
$$\sum_{h \in H} p \bigl( |R(h) - R_S(h)| \ge \epsilon \bigr)
\le 2|H| \exp \frac{-2n\epsilon^2} {M^2}$$
(i.e. we summed $(***)$ $|H|$ times.)

We have now upper bounded $p(\text{magic} \ge \epsilon)$, since we have
$$p(\sup_{h \in H}|R(h) - R_S(h)| \ge \epsilon) \le 
2|H| \exp \frac{-2n\epsilon^2} {M^2}$$
We now set about writing this in the form of the PAC definition, and recalling $2(\text{magic})$ is itself an upper bound for $R(h_S) - R(h^*)$.

Let $\delta = 2|H| \exp \frac{-2n\epsilon^2} {M^2}$
That is, we have
$$p \bigl( \sup_{h \in H}|R(h) - R_S(h)| \ge \epsilon   \bigr) \le \delta$$
with
$$\epsilon = M \sqrt{\frac{\log{|H|} + \log{2/\delta}}
{2n}} $$
- (just rearranging RHS to get an expression for $\epsilon$)
So we have (notice swapping inequality signs to match PAC definition)
$$p \Biggl(\sup_{h \in H}|R(h) - R_S(h)| \le 
M \sqrt{\frac{\log{|H|} + \log{2/\delta}} {2n}} \Biggr)
\ge 1 - \delta$$

Now we can finally write an expression matching $p\Bigl(R(h_S) - R(h^*) \le \epsilon \Bigr) \ge 1 - \delta$ using (∗).
$$ p \Biggl\{ 
R(h_S) - R(h^*) \le 2\sup_{h \in H}\bigl| R(h) - R_S (h) \bigr|
\le 2M \sqrt{\frac{\log{|H|} + \log{2/\delta}} {2n}}
\Biggr\} \ge 
1 - \delta
$$
By observation of the above, a hypothesis class $H$ is PAC-learnable if it is finite sized. Otherwise, it is *possible* that estimation error will be unbounded.
And from $\delta$ , the size of training data is given by $n$:
$$n = \frac {M^2}{\epsilon} \log \frac {2|H|} {\delta}$$
i.e., $n > \text{poly}(1/\delta, 1 / \epsilon)$
This also shows that as the size of $H$ increases, we need more training samples, so it is in our interest to keep $H$ small.

### can an infinitely sized $H$ be PAC-learnable?

If $H$ has infinitely many hypotheses, how can we upper bound $p(\text{magic} > \epsilon)$ and use this to write an expression matching the PAC definition?

Notice importance of deriving generalisation error bound, to make claims about $\text{magic}$, since the latter is given by the RHS:
$$R(h_S) - R_S(h_S) \le \sup_{h \in H}\bigl| R(h) - R_S (h) \bigr| $$

We will show, if the classifier is binary, then an infinitely sized $H$ can still be PAC-learnable, if it has finite number of VC dimensions.

Consider a binary classifier such that
$$H = \{ (h_1^1, \dots , h_{n_1}^1 ),  \dots , (h_1^G, \dots , h_{n_G}^G ) \}  $$
Although $H$ here has infinitely many hypotheses, we can group hypotheses into finite groups, where each group is given by the same hypotheses
$$h(X_1), \dots , h(X_n) $$
Let $h^1 \dots h^G$ represent each group, so we can write
$$ H' = \{ h^1,  \dots , h^G \} $$
**how to find $H'$**:

**growth function**:
The growth function $\prod_H : \mathbb{N} \mapsto \mathbb{N}$ , for a hypothesis class $H$ is defined by
$$\prod_H (n) = \max_{X_1 \dots X_n} 
| \{ h(X_1), \dots , h(X_n) : h \in H \}| \quad , 
\forall n \in \mathbb{N}$$
i.e. the growth function is the max group of hypotheses with the same predictions as the others.

We can use this to upper bound $p(\sup_{h \in H}\bigl| R(h) - R_S (h) \bigr| \ge \epsilon)$.

$$ p \biggl( \sup_{h \in H}\bigl| R(h) - R_S (h) \bigr|
\ge \epsilon \biggr) $$
swap expected risk for empirical risk from another set of samples?
$$ \le 2p \biggl( \sup_{h \in H}\bigl| R_{S'}(h) - R_S (h) \bigr|
\ge \epsilon /2 \biggr)$$
swap difference between empirical risks on two datasets, for empirical risk on one?
$$ \le 4p \biggl( \sup_{h \in H} 
\frac {1}{n} \biggl| 
\sum_{i=1}^n \sigma_i \ell(X_i, Y_i, h) \biggr|
\ge \epsilon / 4
\biggr)$$
multiply by growth function?
$$\le 4 \prod_H (n) p \biggl( \sup_{h \in H} 
\frac {1}{n} \biggl| 
\sum_{i=1}^n \sigma_i \ell(X_i, Y_i, h) \biggr|
\ge \epsilon / 4
\biggr) $$
?
$$\le 8 \prod_H (n) \exp( - n \epsilon^2 / 32 M^2) $$
**shattering**:
The data points $\{ X_1, \dots X_n \}$ is shattered by $H$ when $H$ realises all possible binary predictions. That is, $\prod_H(n) = 2^n$.

**VC dimension**:
The VC dimension of $H$, is the size of the largest set that can be fully shattered by $H$.
$$\text{VC dimension} (H) = \max_n \{n : \prod_H (n) = 2^n \} $$
If the $H$ is of finite VC dimension, it is PAC learnable as follows.

Let $H$ be a hypothesis class with $\text{VC dimension} (H) = d$, 
then for all $n \ge d$ 
$$\prod_H(n) \le \Bigl( \frac {en} {d} \Bigr)^d $$
See Foundations of Machine Learning Ch. 3 for proof of above.

So we continue to upper bound $\sup_{h \in H}\bigl| R(h) - R_S (h) \bigr|$ as follows.

$$8 \prod_H (n) \exp( - n \epsilon^2 / 32 M^2)
\le 8 \Bigl( \frac {en} {d} \Bigr)^d 
\exp( - n \epsilon^2 / 32 M^2)$$
Let $\delta = 8 \Bigl( \frac {en} {d} \Bigr)^d \exp( - n \epsilon^2 / 32 M^2)$ 
- so we have $p \bigl( \sup_{h \in H}\bigl| R(h) - R_S (h) \bigr| \ge \epsilon \bigr) \le \delta$ 
We have
$$\epsilon =  M \sqrt{\frac{32 \bigl( d \log {\frac {en} {d}}
+ \log{8/\delta} \bigr)}
{n}}  $$
So we have
$$p\left( \sup_{h \in H}\bigl| R(h) - R_S (h) \bigr| \le
M \sqrt{\frac{32 \bigl( d \log {\frac {en} {d}}
+ \log{(8/\delta)} \bigr)}
{n}} \right) \ge 1 - \delta  $$
and
$$p\left( R(h_S) - R(h^*) \le
2M \sqrt{\frac{32 \bigl( d \log {\frac {en} {d}}
+ \log{(8/\delta)} \bigr)}
{n}} \right) \ge 1 - \delta$$
By inspection of the above, $H$ is PAC-learnable, despite being infinitely sized (there is no $|H|$ term in the above constraint on probability of estimation error.)

From $\delta$ we have
$$n = \frac{32 M^2} {\epsilon^2}
\bigl( d \log {\frac {en} {d}} + \log{(8/\delta)} \bigr)$$
and so $n > \text{poly} ( 1/\delta, 1/\epsilon)$