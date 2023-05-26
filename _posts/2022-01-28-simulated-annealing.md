---
layout: default
title: Notes on Simulated Annealing
category: Artificial-Intelligence
---

## Metropolis algorithm for simulating the evolution of a solid in a heat bath to thermal equilibrium

The algorithm samples a state space by perturbating current _i_ and transtioning to the perturbed state _j_ with prob = 1 if the energy of the state is lower or with prob = $\text{exp}(\frac{E_i - E_j}{k_BT})$ otherwise.

If you sample long enough the probability distribution of visiting every state approaches the _Boltzmann distribution_:

$$
P(X=i) = \frac{e^{\frac{-E_i}{k_BT}}}{\sum_j e^{\frac{-E_j}{k_BT}}}
$$



## Simulated annealing

Compared to the Metropolis algorithm, simulated annealing uses cost of a state $f(i)$ as energy $E_i$ and acceptance probability without the _Boltzmann's constant_ $k_B$:

$$
P_c\{\text{accept j}\} = \left\{\begin{matrix}
1 & \text{ if } f(j) \leq f(i)
\\ 
e^{\frac{f(i) - f(j)}{c}} & \text{ else}
\end{matrix}\right.
\label{eq:2.4} (2.4)
$$

### Is it optimal?

In other words, can it find a global optimum?

**Conjecture 2.1** Given and instance (S,f) of a combinatorial optimization problem and a suitable neighborhood structure then, after a sufficiently large number of transitions at a fixed value of `c`, applying the acceptance probability of (2.4), the simulated annealing algorithm will find a solution $i \in S$ with a probability equal to:

$$
P_c\{X = i\} = q_i(c) = \frac{e^{-\frac{f(i)}{c}}}{\sum_{j \in S}e^{-\frac{f(j)}{c}}} 
\label{eq:2.5} (2.5)
$$

<span class="marginnote">Basically if you were to random walk using simulated annealing algorithm at temperature c long enough, the prob of visiting every state will converge to $P_c(X=i)$</span>

where $X$ is a stochastic variable denoting the current solution obtained by the simulated annealing.

MATLAB code that checks this using 4-queens problem:

```matlab
%% sample using simulated annealing
solution = randperm(4);
score = fitness(solution);

N = 10000;
H = [solution];
count = [1];

t = 1;
for i = 1:N
    succ = mutate(solution);
    fsucc = fitness(succ);
    fsol = fitness(solution);
    if fsucc <= fsol || rand <= exp((fsol - fsucc)/t) 
        solution = succ;
    end

    [tf, index] = ismember(solution, H, 'rows');
    if index == 0
        H = [H; solution];
        count = [count; 1];
    else
        count(index) = count(index) + 1;
    end
end

%% check if probs match Boltzman's distribution
pbar = count/sum(count);
f = zeros(1,length(H));
for i = 1:length(H)
    f(i) = fitness(H(i,:));
end
z = sum(exp(-f/t));
p = exp(-f/t)/z;
plot(pbar, 'DisplayName','pbar');
hold on
plot(p, 'DisplayName', 'p')
legend
```

Result:

![image](https://marti-1.s3.amazonaws.com/notes/simulated_annealing/sa_boltzmann_dist.png)


A set of useful quantities can be derived from 2.5.

The _expected cost_: 

$$
\begin{aligned}
E_c(f) &\triangleq ~ <f>_c \\
&= \sum_{i \in S}f(i)P_c\{X=i\} \\
&= \sum_{i \in S}f(i)q_i(c)
\end{aligned}
$$

The _expected squared cost_:

$$
\begin{aligned}
E_c(f) &\triangleq ~ <f^2>_c \\
&= \sum_{i \in S}f^2(i)P_c\{X=i\} \\
&= \sum_{i \in S}f^2(i)q_i(c)
\end{aligned}
$$

_Variance_:

$$
\begin{aligned}
\text{Var}_c(f) &\triangleq ~ \sigma^2_c \\
&= <f^c> - <f>^2_c
\end{aligned}
$$

_Entropy_:

$$
S_c = -\sum_{i \in S}q_i(c)\text{ln}(q_i(c))
$$

In the case of simulated annealing, or optimization in general, the entropy can be interpreted as a quantitative measure for the degree of optimality.
<span class="marginnote">The more certainty there is for the system to stay in a certain state, the smaller the entropy, and the higher the optimality.</span>

**Corollary 2.1** Given an instance (S,f) of a combinatorial optimization problem and a suitable neighborhood structure. Furthermore, let the stationary distribution be given by (2.5), then:

$$
\text{lim}_{c\rightarrow 0}q_i(c) = q_i^{*} = \frac{1}{|S_{opt}|}\chi (S_{opt})(i)
$$

<span class="marginnote">Basically the 2.5 collapses into a uniform dist of optimal solutions -- system can only be at one of those optimal states</span>

where $S_{opt}$ denotes the set of globally optimal solutions. Let A and $A \subset A'$ be two sets. Then the characteristic function $\chi(A'):A \rightarrow \{0, 1\}$ of the set $A'$ if defined as $\chi_{A'}(a) = 1$ if $a \in A'$ and $\chi_(A')(a) = 0$ otherwise.

_Proof_: Using the fact that for all $a \leq 0$ $\text{lim}_{x \rightarrow 0} e^{a/x} = 1$ if a = 0 ($e^{0/x}=e^0=1$), and 0 otherwise:

![image](https://marti-1.s3.amazonaws.com/notes/simulated_annealing/exp_minus_1_div_x.png)

and the following facts:

**(1)**

$e^{\frac{(f_{opt}-f_{opt})}{c}} = 1$

**(2)**

$$
\begin{aligned}
\text{lim}_{c \rightarrow 0} \sum_{j \in S}e^{\frac{(f_{opt}-f(j))}{c}} &= \text{lim}_{c \rightarrow 0} (\sum_{j \in S_{opt}}e^{\frac{(f_{opt}-f_{opt})}{c}} + \sum_{j \in S \backslash S_{opt}}e^{\frac{-f(i)}{c}}) \\
&= \text{lim}_{c \rightarrow 0} (\sum_{j \in S_{opt}}e^{\frac{(f_{opt}-f_{opt})}{c}}) + \text{lim}_{c \rightarrow 0} ( \sum_{j \in S \backslash S_{opt}}e^{\frac{-f(i)}{c}}) \\
&= (1 + 1 + \dots + 1_{|S_{opt}|}) + (0 + 0 + \dots + 0_{|S \backslash S_{opt}|}) \\
&= |S_{opt}| + 0
\end{aligned}
$$

we obtain:

<span class="marginnote">$f_{opt} = 0$</span>

$$
\begin{aligned}
\text{lim}_{c \rightarrow 0} q_i(c) &= \text{lim}_{c \rightarrow 0} \frac{e^{-f(i)/c}}{\sum_{j \in S}e^{-f(j)/c}} \\
&= \text{lim}_{c \rightarrow 0} \frac{e^{(f_{opt}-f(i))/c}}{\sum_{j \in S}e^{(f_{opt}-f(j))/c}} \\
\end{aligned}
$$


<span class="marginnote">$\chi(S)(i) \rightarrow 1$ if $i \in S$, 0 otherwise</span>

$$
\begin{aligned}
&= \text{lim}_{c \rightarrow 0} \frac{e^{\frac{(f_{opt}-f_{opt})}{c}}}{\sum_{j \in S}e^{\frac{(f_{opt}-f(j))}{c}}}\chi (S_{opt})(i) \\
&+\text{lim}_{c \rightarrow 0} \frac{e^{\frac{(f_{opt}-f(i))}{c}}}{\sum_{j \in S}e^{\frac{(f_{opt}-f(j))}{c}}} \chi (S \backslash S_{opt})(i) \\
&= \frac{1}{|S_{opt}|} \chi (S_{opt})(i) + 0
\end{aligned}
$$

<span class="marginnote">$S \backslash S_{opt}$ -- all non-optimal states</span>


Thus given conjecture 2.1 and corollary 2.1, we can say that simulated annealing reaches a uniform distribution of optimal solutions as temperature $c$ goes to 0 and number of steps taken at every temperature point approaches infinity:

$$
\text{lim}_{c \rightarrow 0} \text{lim}_{k \rightarrow \infty} P_c\{X(k) = i\} = \text{lim}_{c \rightarrow 0} q_i(c) = q^*_i
$$

Thus simulated annealing finds an optimal solution if an infinate amount of transitions is allowed. Practically, that is not possible.

### Can we approximate arbitrarily closely?

In the previous section it was shown that if one would generate an infinate homogenous Markov chain it would eventually converge to a stationary distribution. From that point we could decrease temperature an repeat the process again, which would guarantee to eventually reach a uniform stationary distribution of only optimal solutions. However, in practice generate an infinate amount of transitions at every temperature is impossible.

Instead of an infinate amount of transitions, we could generate a finate sequence of transtions at ever temperature, combine that sequence into one inhomogenous sequence and see whether it could approximate the $q^*$ arbitrarily closely. 

**Definition 3.11** Let $c_{l}^{'}$ denote the value of the control parameter of the $l^{th}$ homogenous Markov chain, $L$ denote the length of the homogeneous Markov chains, and $c_k$ denote the value of the control parameter at the $k^{th}$ trial. Then we define the sequence $\{c_k\}$, $k=1,\dots$ as follows:

$$
c_k = c_l^{'}, ~ lL < k \leq (l+1)L
$$

_NOTE: I am not sure about the "k" parameter, from the above it seems like it would be in the range of `(lL, (l+1)L]`. In the book it further mentions that "[it] is taken to be piecewise constant". So it doesn't change when sampling at a certain temperature?_


Our goal is to generate such inhomogeneus Markov chain that it would approximate $q^*$ to an arbitrary degree:

$$
\left \| a(k) - q^* \right \| < \epsilon
$$

where $a_i(k) = P(X(k) = i)$ (probablity disribution of outcomes at the k-th trial).

According to **Theorem 3.6**, if the temperature sequence $$\{ c_l^{'} \}$$ satisfies the inequality below, then the Markov chain converges to $q^{*}$:

$$
c_l^{'} \geq \frac{(L+1)\delta}{log(l+2)}, ~ l = 0, 1,\dots,
$$

where 
$$\delta = \text{max}_{i,j \in S} \{ f(j) - f(i) | j \in S_i \}$$ (maximum cost difference over all neighborhoods) and $L$ is chosen as the maximum of the minimum number of transitions required to reach an $i_{opt}$ from $j$, for all $j \in S$.

_NOTE: How is the $\delta$ computed? Do you have to find a maximum diff of all the states or just the immediate neighborhood of current state?_

<span class="marginnote">L -- minimum amount of permutations in order to get to the goal state from furthest state</span>

We can estimate the complexity of the transitions $k$ required for a problem based on the following equation:

$$
k = O((\frac{1}{\epsilon})^{\frac{1}{\text{min(a,b)}}})
$$

where:

$$
a = \frac{1}{(L+1)\Theta^{L+1}} ~, \text{and} ~ b = \frac{\hat{f} - f_{opt}}{(L+1)\delta}
$$

with $$\hat{f} = \text{min}_{i \in S \backslash S_{opt}} f(i)$$ (minimum cost of all non-optimal solutions)

### Example: TSP problem

Let's assume we have a 2-change neighborhood structure. Then we have $L = n - 2$ (min number of permutations of any route in order to get the most optimal route), where $n$ is a number of cities, and $\Theta = (n-1)(n-2)$ (size of the neighborhood -- $\|S_i\|$).

For $a$ we have:

$$
\begin{aligned}
a &= \frac{1}{(L+1)\Theta^{L+1}} \\
&= \frac{1}{(n-2+1)((n-1)(n-2))^{n-2+1}} \\
&= \frac{1}{(n-1)}\left ( \frac{1}{(n-1)(n-2)} \right )^{n-1}
\end{aligned}
$$

and $b$:

<span class="marginnote">$\frac{\hat{f} - f_{opt}}{\text{max}_{i,j \in S} \{ f(j) - f(i) | j \in S_i \}} < 0$</span>

$$
\begin{aligned}
b &= \frac{\hat{f} - f_{opt}}{(L+1)\delta} \\
&= \frac{\hat{f} - f_{opt}}{(n-1)\delta} < \frac{1}{n-1}
\end{aligned}
$$

In the book $a \ll b$, the only way this is true is if:

$$
\left ( \frac{1}{(n-1)(n-2)} \right )^{n-1} \ll \frac{\hat{f} - f_{opt}}{\text{max}_{i,j \in S} \{ f(j) - f(i) | j \in S_i \}}
$$

_NOTE: don't have an intuition of why this could be the case. The easiest thing would be to play around this empirically._

By choosing $\frac{1}{\epsilon} = n$ (the epsilon is chosen here this way so that the transition amount complexiities could be compared as functions of n, but it could be any other value) we obtain:


$$
k = O(n^{n^{2n-1}})
$$

whereas $\|S\| = O((n-1)!)$:

![image](https://marti-1.s3.amazonaws.com/notes/simulated_annealing/k_vs_total_states.png)

The plot for $k$ terminates at n=2, because afterwards it just shoots to infinity. Thus it is far more efficient to just enumerate all of the states, then solve it using simulated annealing.

### Example: n-queens

TBA

## Finate-Time Approximation

Trying to approximate asymptotic convergence of simulated annealing towards a distribution of optimal solutions might require a number of transitions that is bigger than the state space itself. Resulting for most problems in exponential time complexity. An alternative is to relax the optimality and instead aim for solutions that are in between local and global optimas of state space. Instead of trying to achieve stationary distribution at ever temperature value, we could instead aim for some quasi stationary distribution:


$$
\left \| \mathbf{a}(L_k,c_k) - \mathbf{q}(c_k) \right \| < \epsilon ~ (4.1)
$$

where $L_k$ denotes the length of the $k^{th}$ Markov chain. Then _quasi equilibrium_ is achieved if $\mathbf{a}(L_k, c_k)$ is "sufficiently close" to $\mathbf{q_k(c_k)}$, the stationary distribution at $c_k$ for some specified positive value of $\epsilon$.

The quasi equilibrium requires careful design of a cooling schedule. The idea behind the cooling schedules is to start with some $c_0$ that would be guaranteed to generate a stationary distribution (e.g. uniform over all states) and then decrease $c_k$ with small decrements and to generate a fixed length Markov chain that would restore the quasi equilibrium at the end. Finally, a _final value_ of $c_k$ has to be specified in order for the search to terminate. To summarize, the cooling schedule consists of:

* an initial value -- $c_0$;
* a decrement function of $c_k$;
* a final value of $c_k$;
* $L_k$ -- a finate number of transitions at each value of the temp parameter.

An example cooling schedule proposed by Kirkpatrick, Gelatt & Vecchi ([1983?](http://www2.stat.duke.edu/~scs/Courses/Stat376/Papers/TemperAnneal/KirkpatrickAnnealScience1983.pdf)):

### Initial value $c_0$

You start with a small $c_0$ and keep increasing by a constant factor > 1, until the probability computed from the samples is close to 1. Note, the _acceptance ratio_ is defined as:

$$
\chi(c) = \frac{\text{number of accepted transitions}}{\text{number of proposed transitions}}
$$

### Decrement function

You can have either small $\triangle c$ or large $L_k$. In practive, small $\triangle c$ is favoured. Frequently used decrement function is given by:

$$
c_{k+1} = \alpha c_k, ~ k = 1,2,\dots
$$

where $\alpha$ is a constant smaller than but close to 1. Typically between 0.8 and 0.99.

### Final value

Execution of the algorithm is terminated if the value of the cost function of the solution obtained in the last trial of a Markov chain remains unchanged for a number of consecutive chains.

### Length of Markov chain

The number of transitions needed to achieve a quasi equilibrium comes from an intuitive argument that quasi equilibrium will be restored after acceptance of at least some fixed number of transitions. However, since transitions are accepted with decreasing probability, one could obtain $L_k \rightarrow \infty$ for $c_k \rightarrow 0$. Thus it needs to be bounded by some constant $\bar{L}$.


## Resources

* Simulated annealing and Boltzmann machines: a stochastic approach to combinatorial optimization and neural computing

---

**Update 2022-02-14**: [MATLAB code](https://marti-1.s3.amazonaws.com/notes/simulated_annealing/simulated_annealing.tar.gz) for 8-queens problem using Kirkpatrick, Gelatt & Vecchi cooling schedule.

---

**Update 2022-02-17**: A Polynomial-Time Cooling Schedule

**Postulate 2.1**. Let $R_1$ and $R_2$ be two regions of the value of the cost function, where $R_1$ denotes the region of a few standard deviations $\sigma_{\infty}$ around $\left \langle f \right \rangle_{\infty}$ and $R_2$ the region close to $f_{min}$. Then, for a typical combinatorial optimization problem, $\omega(f)$ is given by a normal distribution $\omega_{N}(f)$ in the region $R_1$ and by an exponential distribution $\omega_{\epsilon}(f)$ in the region $R_2$. Furthermore, we conjecture that the number of solutions in $R_1$ is much larger than the number of solutions in $R_2$.

Here:
* $\omega(f)$ is a density funciton of fitness values;
* $\text{lim}_{c \rightarrow \infty} \left \langle f \right \rangle_c \triangleq \left \langle f \right \rangle\_{\infty} = \frac{1}{\|S\|}\sum\_{i \in S} f(i)$
* $\text{lim}_{c \rightarrow \infty} \sigma^2_c \triangleq \sigma^2\_{\infty} = \frac{1}{\|S\|}\sum\_{i \in S} (f(i) - \left \langle f \right \rangle\_{\infty})^2$

Figure below depicts how the transition from $R_1$ to $R_2$ happens as fitness approaches $f_{min}$. The blue bars show the density of fitnesses of states explored at $c_k$, the red line depicts normal distribution with $\sigma = \sigma\_{\infty}$ and $\mu = \left \langle f \right \rangle_{\infty}$, the gold line depicts an exponential function defined as $e^{-f\gamma}$ where $f$ is all possible fitness values and $gamma$ is computed at $\frac{1}{2c_k}$ in order to satisfy the $0 < \gamma < c^{-1}$ constraint (_NOTE_ this comes straight from the book, not sure why $\gamma$ should be in this range, but for depiction purposes it doesn't matter too much since all we care is to show that an exponential distribution is approached the closer we get to the optimal solution).


![image](https://marti-1.s3.amazonaws.com/notes/simulated_annealing/postulate2_1.gif)

### Initial value $c_0$

The acceptance ratio is defined as:

$$
\chi(c) = \frac{\text{accepted}}{\text{proposed}}
$$

All proposals that satisfy $f(i) >= f(j)$ are accepted, let's call them $m_1$. The rest ($f(i) < f(j)$) defined as $m_2$ are accepted with probability $e^{-\frac{\Delta \bar{f}^+}{c}}$, where $\Delta \bar{f}^+$ is average cost difference of proposals with higher then current solution cost. Thus we can approximate the acceptance ratio by:

$$
\chi(c) = \frac{m_1 + m_2*e^{-\frac{\Delta \bar{f}^+}{c}}}{m_1 + m_2}
$$

from the above we can express $c$ as:

$$
c = \frac{\Delta \bar{f}^+}{ln(\frac{m_2}{m_2*\chi_0 - m_1(1-\chi_0)})}
$$

where $\chi_0$ is a hyperparamter of preferred acceptance ratio.

The $c_0$ is calculated in the following way:

1. $c_0 = 0$
2. generate a sequence of transitions
3. compute new $c_0$ from the equation above
4. if not converged go to step 2

Apparently you can converge fast to the final $c_0$ value this way.

### Decrement function

First we make an assumpition that if $q(c_{k+1})$ does not deviate far from the $q(c_k)$ then quasi equilibrium is maintained, assuming it was at $c_0$. This can be quantified as:

$$
\forall i \in S: \frac{1}{1+\delta} < \frac{q_i(c_k))}{q_i(c_{k+1})} < 1 + \delta
$$

_Theorem_: The above inequality is satisfied if the following condition holds:

$$
\forall i \in S: \frac{exp(-\frac{\delta_i}{c_k})}{exp(-\frac{-\delta_i}{c_{k+1}})} < 1+\delta
$$

where $\delta_i = f(i) - f_{opt}$

The above equation can be rewritten in order to extract required inequality between $c_k$ and $c_{k+1}$:

$$
\forall i \in S: c_{k+1} > \frac{c_k}{1+\frac{c_k ln(1+\delta)}{f(i) - f_{opt}}}
$$

The Postulate 2.1 we can simplify the above expression by using a smaller set of states:

$$
S_{c_k} = \{i \in S | f(i) - f_{opt} \leq \left \langle  f \right \rangle_{c_k} - f_{opt} + 3\sigma_{c_k} \}
$$

The $S_{c_k}$ set includes states with costs that are up to 3 standard deviations away from average. Considering that during the cooling cost density function evolves from a normal to exponential, we can guarantee to capture 99% and 95% of states respectively.

The $f(i) - f_{opt} \leq \left \langle  f \right \rangle_{c_k} - f_{opt} + 3\sigma_{c_k}$ acts as an upper bound of all $f(i) - f_{opt}$, thus if the later is replaced by the former we get a stronger inequality condition:


<span class="marginnote">The inequality condition is stronger because there is a stronger guarantee that $c_{k+1}$ is going to be larger than $c_k$</span>

$$
c_{k+1} > \frac{c_k}{1+\frac{c_k ln(1+\delta)}{f(i) - f_{opt} + 3\sigma_{c_k}}} > \frac{c_k}{1+\frac{c_k ln(1+\delta)}{f(i) - f_{opt}}}
$$

Getting $f_{opt}$ for most of the combinatorial problems is not possible. However, since $\left \langle  f \right \rangle_{c_k}$ and $3\sigma_{c_k}$ co-varies, we approximately have a function that is a scaled version of one of those variables, thus we can ommit $\left \langle  f \right \rangle_{c_k} - f_{opt}$, and rescale the fraction by using smaller $\delta$ values:

$$
c_{k+1} = \frac{c_k}{1+\frac{c_k ln(1+\delta)}{3\sigma_{c_k}}} 
$$

_NOTE:_ How is $\delta$ selected? Seems like something that needs to be eye-balled a priori.

### Stopping condition

The algorithm should terminate when $\Delta \left \langle f \right \rangle_{c_k}$ (gradient of average cost at temperateure $c_k$) is "sufficiently" small with respect to $\left \langle f \right \rangle_{c_0}$:

$$
\frac{\Delta \left \langle f \right \rangle_{c_k}}{\left \langle f \right \rangle_{c_0}} < \epsilon_s 
$$

For suffiently large values of $c_0$, we have $\left \langle f \right \rangle_{c_0} \approx \left \langle f \right \rangle_{\infty}$. Also, for $c_k \ll 1$ (which is true always towards the end):

$$
\Delta \left \langle f \right \rangle_{c_k} \approx c_k \frac{\delta \left \langle f \right \rangle_{c_k}}{\delta c_k}
$$

which gives:

$$
\frac{c_k \delta \left \langle f \right \rangle_{c_k}}{\left \langle f \right \rangle_{c_0} \delta c_k} < \epsilon_s
$$

In practice $\left \langle f \right \rangle_{c_k}$ fluctuates, so needs to be smoothed in order to avoid premature termination.

### Length of Markov chain

We want to select $L_k$ to have such value that there would be a guarantee that most/all of the neighbours are going to be visited in case none of the proposed states are accepted. This makes sense, because we want to make sure that if after iteration $k$ we have not selected some neighbour as our new state is because there was no neighbour with lower cost.

<span class="marginnote">
[Prob of selecting an element from a set S in N samplings](/notes/math/statistics/2022/02/19/sample.html#prob-of-selecting-an-element-from-a-set-s-in-n-samplings)
</span>

For large $\|S_i\|$ -- cardinality of $i$th state and large $N$ large number of samplings, and $\|S_i\| = N$ the probability of visiting any of i's neighbours can be approximated as  $1 - e^{-\frac{N}{\|S_i\|}} = 1 - e^{-1} \approx 2/3$. The probability assumes that no solutions were accepted an therefore total of N samples were made.

If we take $N = 3\|S_i\|$, then $1 - e^{-3} \approx 1$.

In the literature the $L_k$ ($L_k = N$) is adviced to range from $\|S_i\|$ to $3\|S_i\|$.

This approximation seems to hold for $\|S_i\| > 100$.

### Proof

What is the probability of selecting an element from a set S in N samplings?

P of not selecting an element = 
$$
1 - \frac{1}{|S|}
$$

P not selecting in N samplings = 
$$
(1 - \frac{1}{|S|})^N = (\frac{|S|-1}{|S|})^N
$$


log(P of not selecting in N) = 
$$
\text{log}((1 - \frac{1}{|S|})^N) = N\text{log}(1 - \frac{1}{|S|})
$$

**Log approximation**

$$
\text{log}(1 + \sigma) \approx \sigma ~~ \text{for small}~ \sigma
$$

thus, if S is large
$$
N\text{log}(1-\frac{1}{|S|}) = N(-\frac{1}{|S|}) = -\frac{N}{|S|}
$$

P of not selecting in N = 
$$
\text{e}^{-\frac{N}{|S|}}
$$

P of selecting in N = 
$$
1 - \text{e}^{-\frac{N}{|S|}}
$$
