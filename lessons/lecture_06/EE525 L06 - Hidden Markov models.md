---
course: EE 525 Pattern Recognition
lecture: 6
title: Hidden Markov models
tags: [ee525, lecture]
slides: lectures/Lecture_06/Lecture 6 Hidden Markov Models.pdf
---
# EE 525 · Lecture 6 · Hidden Markov models

> [!summary] In short
> - Earlier lectures classify one feature vector. An HMM classifies a **sequence**: the order of the observations matters.
> - Markov chain: the next state depends only on the current state. The states are seen directly. The model is the transition matrix $A$.
> - Hidden Markov model: the states are not seen. Each hidden state emits an observation with the emission probabilities $B$. Full model: $\lambda=(A,B,\pi)$.
> - Two assumptions: the state depends only on the previous state, and the observation depends only on the current state.
> - Three problems: evaluation $P(O\mid\lambda)$ (forward algorithm), decoding the best state sequence (Viterbi), learning $\lambda$ from data (Baum-Welch, an EM algorithm).
> - Classification: train one HMM per class and choose the class with the largest $P(O\mid\lambda_c)$, times the class prior if the priors differ.

One handout (document, 14 pages). Symbols: $N$ hidden states $\omega_1,\dots,\omega_N$; $M$ observation symbols $v_1,\dots,v_M$; $T$ the length of the sequence; $a_{ij}$ the probability to go from state $\omega_i$ to state $\omega_j$; $b_{jk}$ the probability that state $\omega_j$ emits symbol $v_k$; $\pi_i$ the probability to start in state $\omega_i$; $O=(o_1,\dots,o_T)$ the observation sequence; $Q=(q_1,\dots,q_T)$ a hidden-state sequence. The handout writes the states as $\omega$ and as $w$; both mean the same. The handout names the forward algorithm and Baum-Welch but does not explain them. Sections 8 to 10 of this note add them.

## 1. Why time matters, and the parameters of an HMM (pages 1-2)
![[ee525_lecture_06_lecture_6_hidden_markov_models_p001.jpg]]
![[ee525_lecture_06_lecture_6_hidden_markov_models_p002.jpg]]

**Notes.** A Gaussian classifier looks at one feature vector and ignores time (static pattern recognition: a flower from petal length and width). Speech, gestures and activities are sequences. The same features in another order are a different pattern. An HMM models the features and also how a hidden state changes in time.

An HMM is defined by three groups of parameters:

$$\lambda=(A,B,\pi)$$

- $A$: **state transition probabilities**, from one hidden state to the next.
- $B$: **observation (emission) probabilities**, what is observed in each hidden state.
- $\pi$: **initial state probabilities**, where the sequence starts. Here $\pi$ is a vector of probabilities, not 3.14, and not the mixing weight of Lecture 5.

Example of page 1: the word "HELLO" with three hidden states /H/, /E/, /L-O/. The states are stages of the pronunciation. The observations are acoustic features of the signal.

**Training** (page 2): the parameters are chosen so that the training sequences $X$ of one class are as likely as possible. This is maximum likelihood, as in Lecture 5:

$$\lambda^*=\arg\max_{\lambda}P(X\mid\lambda)$$

The hidden states of the training data are unknown, so there is no closed formula. The **Baum-Welch algorithm** (an EM algorithm) solves this.

**Classification** (page 2): train one HMM per class (HELLO, GOODBYE, THANKS). Give a new recording $X$ to all models. Each model returns the likelihood $P(X\mid\lambda_c)$. Choose the class with the largest value (equal priors assumed).

> [!warning] Watch out
> - Page 2 says the test pattern is classified by the model with "the highest posterior probability", and then compares likelihoods $P(X\mid\lambda_c)$. The two agree only when the class priors are equal. The handout assumes this ("assuming equal class priors"), so its rule is correct, but the wording is loose. With different priors compare $P(X\mid\lambda_c)P(C_c)$ (page 14).
> - The handout calls the observation sequence $X$ here and $O$ or $V^T$ later. It is the same thing.

> [!info] Left-to-right models (beyond the slides)
> The HELLO picture has arrows only from State 1 to State 2 to State 3. Such a model is called **left-to-right**: the state index never goes down, so $a_{ij}=0$ for $j<i$, and the start is fixed, $\pi=(1,0,0)$. A state may repeat ($a_{ii}>0$), which models a sound that is spoken slowly.
> Example: $A$ has the rows $(0.7,\ 0.3,\ 0)$, $(0,\ 0.8,\ 0.2)$, $(0,\ 0,\ 1)$.
> The number of steps spent in state $i$ has the mean $\frac{1}{1-a_{ii}}$. For $a_{11}=0.7$: $\frac{1}{0.3}=3.33$ steps.
> Speech and gesture models are usually left-to-right. A model in which every transition is possible (page 4 figure) is called ergodic.

> [!info] How many numbers are in $\lambda$ (beyond the slides)
> With $N$ states and $M$ symbols: $A$ has $N^2$ entries, $B$ has $NM$, $\pi$ has $N$. Every row of $A$ and $B$ sums to 1, and $\pi$ sums to 1, so the free numbers are $N(N-1)+N(M-1)+(N-1)$.

> [!question]- Check (page 1): An HMM has $N=3$ hidden states and $M=4$ observation symbols. How many entries do $A$, $B$ and $\pi$ have? How many of them are free?
> $A$: $N\times N=3\times3=9$ entries.
> $B$: $N\times M=3\times4=12$ entries.
> $\pi$: $N=3$ entries. Total $9+12+3=24$.
> Free: each of the 3 rows of $A$ sums to 1, so $3\times2=6$. Each of the 3 rows of $B$ sums to 1, so $3\times3=9$. $\pi$ sums to 1, so $2$.
> Free total: $6+9+2=17$.

> [!question]- Check (page 1, HELLO model): A left-to-right model with three states has $a_{11}=0.6$, $a_{22}=0.9$ and no skips (state 1 can only stay or go to state 2). Write the matrix $A$. How long does the model stay in state 2 on average?
> Row 1: stay $0.6$, so go to state 2 with $1-0.6=0.4$, and $a_{13}=0$: $(0.6,\ 0.4,\ 0)$.
> Row 2: no way back, so $a_{21}=0$. Stay $0.9$, go on $0.1$: $(0,\ 0.9,\ 0.1)$.
> Row 3: last state, it can only stay: $(0,\ 0,\ 1)$.
> Mean number of steps in state 2: $\frac{1}{1-a_{22}}=\frac{1}{0.1}=10$ steps.

> [!question]- Check (page 2): A recording gives the log-likelihoods $\ln P(X\mid\lambda_{\text{HELLO}})=-310.2$, $\ln P(X\mid\lambda_{\text{GOODBYE}})=-305.7$, $\ln P(X\mid\lambda_{\text{THANKS}})=-320.4$. Which word is chosen (equal priors)? How many times more likely is the winner than the second?
> The logarithm is increasing, so the largest log-likelihood belongs to the largest likelihood.
> Largest: $-305.7$, so the word is GOODBYE.
> Second: HELLO with $-310.2$. Difference: $-305.7-(-310.2)=4.5$.
> Ratio of the likelihoods: $e^{4.5}=90.0$. GOODBYE explains the recording 90 times better than HELLO.
> The likelihoods themselves are tiny ($e^{-305.7}\approx10^{-133}$). This is normal for long sequences and is the reason to work with logarithms.

## 2. Links to earlier methods, and the Markov property (page 3)
![[ee525_lecture_06_lecture_6_hidden_markov_models_p003.jpg]]

**Notes.** The table connects the HMM with what we know:

| Earlier method | Use in an HMM |
|---|---|
| Maximum likelihood (L05) | $\lambda$ is chosen to maximise the likelihood of the training sequences |
| EM (L05) | Baum-Welch is EM; the hidden states are the latent variables |
| Gaussian mixtures (L05) | the emission density of a state can be a GMM (GMM-HMM) |
| Bayes classification (L02) | $P(O\mid\lambda_c)$ is the class-conditional likelihood; multiply by the prior |

**Markov property**: the future state depends only on the present state, not on the states before it. The process is "memoryless". If we know today's weather, the weather of the five days before does not help to predict tomorrow.

A **Markov model** describes a system that jumps between states with transition probabilities. Examples: weather (Sunny, Cloudy, Rainy), human activity (Walking, Running, Standing), a traffic light (Red, Yellow, Green).

Notation: $N$ distinct states $\omega_1,\dots,\omega_N$. $\omega(t)$ is the state at time $t=1,2,\dots$

> [!info] First order and higher orders (beyond the slides)
> "First order" means: one past state is enough. A second-order model uses the last two states, $P(\omega(t+1)\mid\omega(t),\omega(t-1))$. It can describe more, but it needs $N^3$ numbers instead of $N^2$.

> [!question]- Check (page 3): The weather has $N=3$ states (Sunny, Cloudy, Rainy). How many transition probabilities does a first-order Markov model need? How many would a second-order model need? Count also the free numbers.
> First order: one probability for every pair (current state, next state): $N^2=3^2=9$. Each of the 3 rows sums to 1, so $3\times2=6$ are free.
> Second order: one probability for every triple (state before, current state, next state): $N^3=3^3=27$. There are $3^2=9$ different pasts, each with a row that sums to 1, so $9\times2=18$ are free.
> The second-order model needs three times as many numbers. With the same amount of data each number is estimated worse. This is the price of a longer memory.

## 3. First-order Markov models (pages 4-5)
![[ee525_lecture_06_lecture_6_hidden_markov_models_p004.jpg]]
![[ee525_lecture_06_lecture_6_hidden_markov_models_p005.jpg]]

**Notes.** In general the next state could depend on the whole history: $P(\omega(t+1)\mid\omega(t),\omega(t-1),\dots,\omega(1))$. The first-order Markov model assumes

$$P(\omega(t+1)\mid\omega(t),\omega(t-1),\dots,\omega(1))=P(\omega(t+1)\mid\omega(t))$$

It is also **time homogeneous**: the probabilities are the same at every time $t$. So one table is enough, the **transition probabilities**

$$a_{ij}=P(\omega_j(t+1)\mid\omega_i(t)),\qquad a_{ij}\ge0,\qquad\sum_{j=1}^{N}a_{ij}=1\ \text{ for every } i$$

$\omega_j(t+1)$ means "the state at time $t+1$ is $\omega_j$". The first index is the state we leave, the second is the state we enter.

- Not symmetric: $a_{ij}\ne a_{ji}$ in general.
- A state may repeat: $a_{ii}\ne0$ in general (the loops in the figure).
- Not every state must be visited.

Figure: the nodes are the states, the arrows are the transition probabilities.

Example of page 4: Sunny, Rainy, Rainy, Sunny on days 1 to 4. To predict day 5 the model uses only day 4 (Sunny). This is a modelling assumption, not a law of nature.

**Probability of a state sequence** (page 5). $\theta$ is the model (all $a_{ij}$). $\omega^T=\{\omega(1),\dots,\omega(T)\}$ is a sequence of length $T$. Multiply the transition probabilities along the sequence. For $\omega^6=\{\omega_1,\omega_3,\omega_2,\omega_2,\omega_1,\omega_3\}$:

$$P(\omega^T\mid\theta)=a_{13}\,a_{32}\,a_{22}\,a_{21}\,a_{13}$$

A sequence of length $T$ has $T-1$ transitions, here 5.

This is an **observable Markov model**: the output at each time is the state itself, and each state is a physical event that we can see.

> [!warning] Watch out (page 5): the start factor is left out
> The product $a_{13}a_{32}a_{22}a_{21}a_{13}$ has no factor for the first state. Strictly it is the probability of the sequence given that it starts in $\omega_1$. The full probability is $P(\omega(1)=\omega_1)\,a_{13}a_{32}a_{22}a_{21}a_{13}$. Page 7 does include the first factor ($0.6$), so the handout is not consistent here. Also, "$w(t)=w_i$, $i=1\dots N$" only says that every element of the sequence is one of the $N$ states.

> [!info] Where the product comes from (beyond the slides)
> Chain rule for three states: $P(\omega(1),\omega(2),\omega(3))=P(\omega(1))\,P(\omega(2)\mid\omega(1))\,P(\omega(3)\mid\omega(2),\omega(1))$.
> Markov assumption: $P(\omega(3)\mid\omega(2),\omega(1))=P(\omega(3)\mid\omega(2))$.
> Result: $P(\omega(1))\,a_{\omega(1)\omega(2)}\,a_{\omega(2)\omega(3)}$. For length $T$ the same step is repeated: one start factor and $T-1$ transition factors.

> [!question]- Check (page 4): A three-state model has the rows $(0.5,\ 0.2,\ a_{13})$, $(0.1,\ 0.6,\ 0.3)$, $(0.4,\ 0.4,\ a_{33})$. Find $a_{13}$ and $a_{33}$. Is the matrix symmetric? The system was in $\omega_2$ at time $t-1$ and is in $\omega_1$ at time $t$. What is the probability of $\omega_3$ at time $t+1$?
> Each row sums to 1.
> Row 1: $a_{13}=1-0.5-0.2=0.3$.
> Row 3: $a_{33}=1-0.4-0.4=0.2$.
> Symmetric: no. $a_{12}=0.2$ but $a_{21}=0.1$. Also $a_{13}=0.3$ but $a_{31}=0.4$. Symmetry is not required.
> Next state: by the first-order assumption only the current state $\omega_1$ counts. The state at $t-1$ is ignored. $P(\omega_3(t+1)\mid\omega_1(t))=a_{13}=0.3$.

> [!question]- Check (page 5): Use the matrix of the previous question (rows $(0.5,0.2,0.3)$, $(0.1,0.6,0.3)$, $(0.4,0.4,0.2)$). Compute the probability of the sequence $\{\omega_2,\omega_2,\omega_3,\omega_1,\omega_3\}$, first given the start in $\omega_2$, then with $P(\omega(1)=\omega_2)=0.5$.
> Transitions: $\omega_2\to\omega_2$, $\omega_2\to\omega_3$, $\omega_3\to\omega_1$, $\omega_1\to\omega_3$. Four transitions for $T=5$.
> Given the start: $a_{22}\,a_{23}\,a_{31}\,a_{13}=0.6\times0.3\times0.4\times0.3$.
> $0.6\times0.3=0.18$; $0.18\times0.4=0.072$; $0.072\times0.3=0.0216$.
> With the start probability: $0.5\times0.0216=0.0108$.

## 4. Weather example: transition matrix, counting, sequence probability (pages 6-7)
![[ee525_lecture_06_lecture_6_hidden_markov_models_p006.jpg]]
![[ee525_lecture_06_lecture_6_hidden_markov_models_p007.jpg]]

**Notes.** The example starts at the bottom of page 5 (table of the four probabilities). Two states: $\omega_1=S$ (Sunny), $\omega_2=R$ (Rainy). After a sunny day: sunny $0.8$, rainy $0.2$. After a rainy day: sunny $0.4$, rainy $0.6$. As a matrix (the handout calls it $A$ and also $\theta$):

$$A=\begin{bmatrix}a_{11}&a_{12}\\a_{21}&a_{22}\end{bmatrix}=\begin{bmatrix}P(S\mid S)&P(R\mid S)\\P(S\mid R)&P(R\mid R)\end{bmatrix}=\begin{bmatrix}0.8&0.2\\0.4&0.6\end{bmatrix}$$

Rows are the current state, columns are the next state. Every row sums to 1. A column does not have to sum to 1 (here $0.8+0.4=1.2$).

**Estimating the transition probabilities from data.** Count how often each transition occurs and divide by the number of transitions that leave the same state:

$$a_{ij}=\frac{N_{ij}}{\sum_{j}N_{ij}}$$

$N_{ij}$ is the number of observed transitions from state $i$ to state $j$. Handout counts (page 6): $S\to S$ 80, $S\to R$ 20, $R\to S$ 40, $R\to R$ 60. So $P(S\to S)=\frac{80}{80+20}=0.8$ and $P(S\to R)=\frac{20}{80+20}=0.2$ (top of page 7). The handout stops there. In the same way $P(R\to S)=\frac{40}{100}=0.4$, $P(R\to R)=0.6$. This relative frequency is the maximum-likelihood estimate.

**Probability of a state sequence** (page 7). For $S\to R\to R\to S$ with $P(\omega(1)=\omega_1)=0.6$:

$$P(\omega^T\mid\theta)=P(\omega_1)\,a_{12}\,a_{22}\,a_{21}=0.6\times0.2\times0.6\times0.4=0.0288$$

**Two steps ahead.** "Rain after two days if today is sunny": the middle day is unknown, so add over both possibilities. Path $S\to S\to R$: $0.8\times0.2=0.16$. Path $S\to R\to R$: $0.2\times0.6=0.12$. Sum: $0.28$.

The bottom of page 7 starts the next topic: what if the state cannot be seen directly?

> [!warning] Watch out
> On page 6 the line "$A=[\dots]\ A=[\dots]$" writes $A$ twice, side by side: first with the symbols $P(S\mid S),\dots$, then with the numbers. It is one matrix, not a product of two.

> [!info] Several steps ahead and the long run (beyond the slides)
> Adding over the unknown middle state is a matrix product. The two-step probabilities are $A^2$:
> $$A^2=\begin{bmatrix}0.8&0.2\\0.4&0.6\end{bmatrix}\begin{bmatrix}0.8&0.2\\0.4&0.6\end{bmatrix}=\begin{bmatrix}0.72&0.28\\0.56&0.44\end{bmatrix}$$
> The entry in row $S$, column $R$ is the handout's $0.28$. In general the $n$-step probabilities are $A^n$.
> For large $n$ both rows of $A^n$ approach the same vector, the **stationary distribution** $p$ with $pA=p$. Here $p=(2/3,\ 1/3)$. Check: $\frac23(0.8)+\frac13(0.4)=0.5333+0.1333=0.6667$. In the long run two days of three are sunny, whatever the start was.

> [!question]- Check (page 6): A weather diary shows the 10 days S S R R R S S S R S. Estimate the transition matrix.
> Write the 9 pairs of neighbouring days: SS, SR, RR, RR, RS, SS, SS, SR, RS.
> Counts: $N_{SS}=3$, $N_{SR}=2$, $N_{RS}=2$, $N_{RR}=2$. Check: $3+2+2+2=9$ transitions.
> From $S$: $3+2=5$ transitions. $a_{SS}=\frac35=0.6$, $a_{SR}=\frac25=0.4$.
> From $R$: $2+2=4$ transitions. $a_{RS}=\frac24=0.5$, $a_{RR}=\frac24=0.5$.
> Rows: $(0.6,\ 0.4)$ and $(0.5,\ 0.5)$. Both sum to 1.
> With only 9 transitions these estimates are rough. The handout's table uses 200 transitions.

> [!question]- Check (page 7): Use the handout's matrix (rows $(0.8,0.2)$ and $(0.4,0.6)$) and the start probability $P(R)=0.4$. Compute the probability of the sequence $R\to R\to S\to S$. Then compute the probability that it is sunny two days after a rainy day.
> Sequence: $P(R)\,a_{RR}\,a_{RS}\,a_{SS}=0.4\times0.6\times0.4\times0.8$.
> $0.4\times0.6=0.24$; $0.24\times0.4=0.096$; $0.096\times0.8=0.0768$.
> Two days after rain, two paths:
> $R\to S\to S$: $0.4\times0.8=0.32$.
> $R\to R\to S$: $0.6\times0.4=0.24$.
> Sum: $0.32+0.24=0.56$. This is the entry (row $R$, column $S$) of $A^2$.

## 5. From Markov models to hidden Markov models (pages 8-9)
![[ee525_lecture_06_lecture_6_hidden_markov_models_p008.jpg]]
![[ee525_lecture_06_lecture_6_hidden_markov_models_p009.jpg]]

**Notes.** In the weather model we see the state. In activity recognition we do not: the phone gives acceleration signals, and the activity (Walking, Running, Standing) must be inferred. The activities are the **hidden states**. The measurements are the **observations** (visible states). The pictures on page 8 show such projects: a phone on the body, and the acceleration curves of different activities.

| Property | Markov model | Hidden Markov model |
|---|---|---|
| States | observable | hidden |
| Transition probabilities | yes | yes |
| Observations | the states themselves | generated by the hidden states |
| Main challenge | predict the next state | infer the hidden states from the observations |
| Example | weather transitions | speech and activity recognition |

An HMM has two sequences of the same length $T$:

1. the hidden-state sequence $\omega^T=\{\omega(1),\dots,\omega(T)\}$, each $\omega(t)$ one of the $N$ states;
2. the observation sequence $V^T=\{v(1),\dots,v(T)\}$, each $v(t)$ one of the $M$ symbols $v_1,\dots,v_M$.

Table on page 9: times 1 to 4, hidden states Walking, Walking, Running, Running, observations $v_1,v_2,v_3,v_4$. We know the observation column, not the state column.

**Assumption 1 (first-order Markov property).** The hidden state depends only on the previous hidden state:

$$P(\omega_j(t+1)\mid\omega_i(t))=a_{ij}$$

**Assumption 2 (observation independence).** The observation at time $t$ depends only on the hidden state at time $t$:

$$P(v_k(t)\mid\omega_j(t))=b_{jk}$$

$b_{jk}$ is the **emission probability**: state $\omega_j$ emits symbol $v_k$. First index: state. Second index: symbol. The model is called hidden because only the $v$ are visible.

> [!warning] Watch out
> - Assumption 2 does not say that the observations are independent of each other. They are independent only when the hidden states are given. Without the states, $v(1)$ tells something about $\omega(1)$, which tells something about $\omega(2)$ and so about $v(2)$. A number example is in the check questions of section 6.
> - Page 9 writes "$w(t)=w_j$, $i=1\dots N$": the index should be the same letter. It also says the Markov assumption was introduced "in the previous lecture"; it is the assumption of pages 3 and 4 of this handout.

> [!info] The HMM as a two-stage random machine (beyond the slides)
> An HMM produces a sequence like this:
> 1. Pick the first state with the probabilities $\pi$.
> 2. The current state emits one symbol with its row of $B$.
> 3. Jump to the next state with the row of $A$ of the current state. Go to step 2.
>
> Compare with the mixture model of Lecture 5: there a component is picked new for every sample with the same weights. In an HMM the "component" (state) of the next sample depends on the current one. An HMM whose rows of $A$ are all equal, with $\pi$ equal to that same row, is a mixture model.

> [!question]- Check (page 8): For each task say what the hidden states and the observations are: (a) recognising a spoken word, (b) guessing the weather from whether a colleague carries an umbrella each day. Then: an activity model has $N=3$ states. How many different hidden-state sequences are possible for $T=10$ measurements?
> (a) Hidden: the stage of the pronunciation (the sound being spoken, such as /H/, /E/, /L-O/). Observed: acoustic features of the signal.
> (b) Hidden: the weather (Sunny, Rainy). Observed: umbrella or no umbrella.
> Number of sequences: each of the $T$ positions can be any of the $N$ states, so $N^T=3^{10}=59049$.
> This number grows very fast with $T$. For $T=20$ it is $3^{20}=3\,486\,784\,401$. This is why section 8 needs a smarter method than listing all sequences.

> [!question]- Check (page 9): Use the table of page 9 (hidden states Walking, Walking, Running, Running with observations $v_1,v_2,v_3,v_4$; Walking is $\omega_1$, Running is $\omega_2$). Write the joint probability of both columns with the symbols $\pi_i$, $a_{ij}$, $b_{jk}$. How many factors are there? Which factor would change if Assumption 2 did not hold?
> Start: $\pi_1$. Emission at $t=1$: $b_{11}$ (state 1 emits $v_1$).
> $t=2$: transition $a_{11}$, emission $b_{12}$.
> $t=3$: transition $a_{12}$, emission $b_{23}$.
> $t=4$: transition $a_{22}$, emission $b_{24}$.
> Joint: $\pi_1\,b_{11}\,a_{11}\,b_{12}\,a_{12}\,b_{23}\,a_{22}\,b_{24}$.
> Factors: 1 start, $T-1=3$ transitions, $T=4$ emissions: $2T=8$ factors.
> Without Assumption 2 an emission factor such as $b_{23}$ would have to be $P(v(3)\mid\omega(3),\omega(2),\omega(1),v(2),v(1))$: it would depend on the whole past. The assumption reduces it to a number from the table $B$.

## 6. The activity HMM: A, B, pi and the joint probability (pages 10-12)
![[ee525_lecture_06_lecture_6_hidden_markov_models_p010.jpg]]
![[ee525_lecture_06_lecture_6_hidden_markov_models_p011.jpg]]
![[ee525_lecture_06_lecture_6_hidden_markov_models_p012.jpg]]

**Notes.** Figure on page 10: three hidden states (black) with all transitions $a_{ij}$, and from every state four red arrows to the visible symbols $v_1,\dots,v_4$ with the emission probabilities $b_{jk}$. So this picture has $N=3$, $M=4$.

The example has two hidden states, $\omega_1=W$ (Walking) and $\omega_2=R$ (Running), and two symbols, $v_1=L$ (low acceleration) and $v_2=H$ (high acceleration).

$$A=\begin{bmatrix}0.8&0.2\\0.3&0.7\end{bmatrix},\qquad B=\begin{bmatrix}0.7&0.3\\0.2&0.8\end{bmatrix},\qquad \pi=\begin{bmatrix}0.6&0.4\end{bmatrix}$$

- $A$: rows = current state ($W$, $R$), columns = next state. Walking continues with $0.8$, changes to running with $0.2$. Running continues with $0.7$, changes to walking with $0.3$.
- $B$: rows = hidden state ($W$, $R$), columns = symbol ($L$, $H$). A walker shows low acceleration with $0.7$. A runner shows high acceleration with $0.8$.
- $\pi_i=P(\omega(1)=\omega_i)$: the sequence starts with walking with $0.6$, with running with $0.4$.

All rows of $A$ and $B$ and the vector $\pi$ sum to 1. High acceleration does not prove running: a walker shows $H$ with $0.3$. The same observation can come from different states.

**Complete example** (pages 11-12). Observed: $O=(L,H,H)$. Candidate hidden sequence: $Q=(W,R,R)$. From here the handout writes $o_t$ for the observation and $q_t$ for the state at time $t$. The joint probability of a state sequence and an observation sequence is

$$P(Q,O\mid\lambda)=P(q_1)\,P(o_1\mid q_1)\prod_{t=2}^{T}P(q_t\mid q_{t-1})\,P(o_t\mid q_t)$$

For the example:

$$P(W,R,R,L,H,H\mid\lambda)=\underbrace{0.6}_{\pi_W}\times\underbrace{0.7}_{L\mid W}\times\underbrace{0.2}_{W\to R}\times\underbrace{0.8}_{H\mid R}\times\underbrace{0.7}_{R\to R}\times\underbrace{0.8}_{H\mid R}=0.037632$$

This is the probability of this path **and** the observations. It is not the probability of the observations alone, because other paths such as $(W,W,R)$ or $(R,R,R)$ also produce $(L,H,H)$. The **observation likelihood** adds over all paths:

$$P(O\mid\lambda)=\sum_{Q}P(Q,O\mid\lambda)$$

The forward algorithm computes this sum efficiently (section 8).

The bottom of page 12 starts the continuous case, which continues on page 13 (section 7).

> [!example] All eight paths for $O=(L,H,H)$
> Two states and $T=3$ give $2^3=8$ paths.
>
> | Path $Q$ | $P(Q,O\mid\lambda)$ |
> |---|---|
> | $W,W,W$ | $0.6(0.7)(0.8)(0.3)(0.8)(0.3)=0.024192$ |
> | $W,W,R$ | $0.6(0.7)(0.8)(0.3)(0.2)(0.8)=0.016128$ |
> | $W,R,W$ | $0.6(0.7)(0.2)(0.8)(0.3)(0.3)=0.006048$ |
> | $W,R,R$ | $0.6(0.7)(0.2)(0.8)(0.7)(0.8)=0.037632$ |
> | $R,W,W$ | $0.4(0.2)(0.3)(0.3)(0.8)(0.3)=0.001728$ |
> | $R,W,R$ | $0.4(0.2)(0.3)(0.3)(0.2)(0.8)=0.001152$ |
> | $R,R,W$ | $0.4(0.2)(0.7)(0.8)(0.3)(0.3)=0.004032$ |
> | $R,R,R$ | $0.4(0.2)(0.7)(0.8)(0.7)(0.8)=0.025088$ |
>
> Sum: $P(O\mid\lambda)=0.116$. The handout's path $(W,R,R)$ is the most probable single path.

> [!warning] Slide error (page 12)
> The page says "We want to calculate $P(Q\mid O,\lambda)$" and then computes the joint probability $P(Q,O\mid\lambda)=0.037632$. These are different. The conditional probability of the path given the observations is
> $$P(Q\mid O,\lambda)=\frac{P(Q,O\mid\lambda)}{P(O\mid\lambda)}=\frac{0.037632}{0.116}=0.3244$$
> So even the best path has only a 32% chance to be the true one.

> [!warning] Slide error (page 11)
> - "Suppose $\pi_i=[0.6\ 0.4]$": the vector is $\pi$; $\pi_1=0.6$ and $\pi_2=0.4$ are its entries.
> - "$w(t)=w_i$, $i=1,2,3$": the example has only two states, so $i=1,2$. The range $1,2,3$ fits the time index $t$ ($T=3$), not the state index.
> - Notation changes on these pages: $O=V^T$ and $Q=\omega^T$, $o_t=v(t)$, $q_t=\omega(t)$.

> [!info] Where the joint formula comes from (beyond the slides)
> For $T=2$. Chain rule: $P(q_1,o_1,q_2,o_2)=P(q_1)\,P(o_1\mid q_1)\,P(q_2\mid q_1,o_1)\,P(o_2\mid q_2,q_1,o_1)$.
> Assumption 1: the state $q_2$ depends only on $q_1$, so $P(q_2\mid q_1,o_1)=P(q_2\mid q_1)$.
> Assumption 2: the observation $o_2$ depends only on $q_2$, so $P(o_2\mid q_2,q_1,o_1)=P(o_2\mid q_2)$.
> Result: $P(q_1)\,P(o_1\mid q_1)\,P(q_2\mid q_1)\,P(o_2\mid q_2)$. Each further time step adds one transition factor and one emission factor.
> Short notation used from here on: $b_j(o_t)$ is the emission probability of the symbol that was observed at time $t$, so $b_j(o_t)=b_{jk}$ when $o_t=v_k$. Then
> $$P(Q,O\mid\lambda)=\pi_{q_1}\,b_{q_1}(o_1)\prod_{t=2}^{T}a_{q_{t-1}q_t}\,b_{q_t}(o_t)$$

> [!question]- Check (page 10): (a) In the figure ($N=3$ states, $M=4$ symbols) state $\omega_2$ has $b_{21}=0.1$, $b_{22}=0.4$, $b_{23}=0.3$. Find $b_{24}$. What size is $B$? (b) With the example's $A$ (rows $(0.8,0.2)$ and $(0.3,0.7)$): a person walks now. What is the probability that the person walks two steps later?
> (a) A state must emit one of the four symbols, so the row sums to 1: $b_{24}=1-0.1-0.4-0.3=0.2$. $B$ has one row per state and one column per symbol: $3\times4$.
> (b) The middle state is unknown, so add over both paths.
> $W\to W\to W$: $0.8\times0.8=0.64$.
> $W\to R\to W$: $0.2\times0.3=0.06$.
> Sum: $0.64+0.06=0.70$.

> [!question]- Check (page 11): Use the example's $B$ and $\pi$. What is the probability that the first observation is $H$? If the first observation is $H$, what is the probability that the person is walking at time 1?
> Total probability over the two possible first states:
> $P(o_1=H)=\pi_W\,b_W(H)+\pi_R\,b_R(H)=0.6\times0.3+0.4\times0.8=0.18+0.32=0.50$.
> Bayes' rule: $P(q_1=W\mid o_1=H)=\frac{0.18}{0.50}=0.36$, and $P(q_1=R\mid o_1=H)=\frac{0.32}{0.50}=0.64$.
> Before the observation walking had $0.6$. One high value moves it down to $0.36$, but walking is still possible.

> [!question]- Check (page 11, independence): With the example's model, compute $P(o_1=H,\ o_2=H)$ and compare with $P(o_1=H)\,P(o_2=H)$. Are the observations independent?
> $P(o_1=H)=0.50$ (previous question).
> State at time 2: $P(q_2=W)=0.6(0.8)+0.4(0.3)=0.60$, $P(q_2=R)=0.40$. So $P(o_2=H)=0.6(0.3)+0.4(0.8)=0.50$.
> Product: $0.50\times0.50=0.25$.
> Joint, adding over the four state pairs $\pi_{q_1}b_{q_1}(H)\,a_{q_1q_2}\,b_{q_2}(H)$:
> $WW$: $0.6(0.3)(0.8)(0.3)=0.0432$.
> $WR$: $0.6(0.3)(0.2)(0.8)=0.0288$.
> $RW$: $0.4(0.8)(0.3)(0.3)=0.0288$.
> $RR$: $0.4(0.8)(0.7)(0.8)=0.1792$.
> Sum: $P(o_1=H,o_2=H)=0.28$.
> $0.28\ne0.25$: the observations are not independent. A high value now makes a high value at the next step more likely, because the hidden state tends to stay. They are independent only when the states are given.

> [!question]- Check (page 12): For $O=(L,H,H)$ compute the joint probability of the path $Q=(R,R,R)$ by hand. Then give $P(Q\mid O,\lambda)$ for this path, using $P(O\mid\lambda)=0.116$.
> Factors: $\pi_R=0.4$, $b_R(L)=0.2$, $a_{RR}=0.7$, $b_R(H)=0.8$, $a_{RR}=0.7$, $b_R(H)=0.8$.
> $0.4\times0.2=0.08$; $0.08\times0.7=0.056$; $0.056\times0.8=0.0448$; $0.0448\times0.7=0.03136$; $0.03136\times0.8=0.025088$.
> $P(Q,O\mid\lambda)=0.025088$. It is smaller than $0.037632$ of $(W,R,R)$, because a runner rarely shows $L$ at time 1.
> $P(Q\mid O,\lambda)=\frac{0.025088}{0.116}=0.2163$.

## 7. Continuous observations and one HMM per class (pages 13-14)
![[ee525_lecture_06_lecture_6_hidden_markov_models_p013.jpg]]
![[ee525_lecture_06_lecture_6_hidden_markov_models_p014.jpg]]

**Notes.** Real accelerometer values are continuous, for example $O=(0.12,\ 0.35,\ 0.82,\ 1.15,\ 0.95)$. A table $B$ with one column per value is impossible. Each state gets a **probability density** instead (bottom of page 12, top of page 13):

$$P(o_t\mid q_t=j)\ \longrightarrow\ p(o_t\mid q_t=j),\qquad p(o_t\mid W)=\mathcal N(o_t;\mu_W,\sigma_W^2),\quad p(o_t\mid R)=\mathcal N(o_t;\mu_R,\sigma_R^2)$$

Handout values: Walking $\mu_W=0.4$, $\sigma_W=0.2$. Running $\mu_R=1.0$, $\sigma_R=0.3$. In every formula $b_j(o_t)$ is replaced by the density value $\mathcal N(o_t;\mu_j,\sigma_j^2)$. $A$ and $\pi$ stay the same. The structure of the HMM does not change. A state density can also be a Gaussian mixture (GMM-HMM).

**Classification** (pages 13-14). Three activities, three HMMs. Training: the walking sequences train HMM 1, the running sequences HMM 2, the standing sequences HMM 3. Testing: a new sequence $O$ is evaluated by all three models.

$$\hat C=\arg\max_{i}P(O\mid\lambda_i)$$

If the class priors are not equal:

$$\hat C=\arg\max_{i}P(O\mid\lambda_i)\,P(C_i)$$

This is the Bayes classifier of Lecture 2. The HMM supplies the class-conditional likelihood of a whole sequence.

> [!warning] Watch out (page 13): the figure does not match the table
> The figure does not show the example's densities, and the handout does not say so. Its legend says $\mu_1=-3$, $\sigma_1=1$ and $\mu_2=2$, $\sigma_2=2$, a general picture of two Gaussians. The table above it says $\mu_W=0.4$, $\sigma_W=0.2$ and $\mu_R=1.0$, $\sigma_R=0.3$. The true curves would have peaks at $0.4$ (height $1.99$) and at $1.0$ (height $1.33$).

> [!example] Density values and a small classification (the second model is invented, beyond the slides)
> Emission of $o_t=0.6$: $\mathcal N(0.6;0.4,0.2^2)=\frac{1}{0.2\sqrt{2\pi}}e^{-(0.2)^2/(2\cdot0.04)}=1.9947\times e^{-0.5}=1.2099$ for walking. $\mathcal N(0.6;1.0,0.3^2)=1.3298\times e^{-0.8889}=0.5467$ for running. A density can be larger than 1.
> Classification with two discrete models. The handout gives no numbers for this step, so this part is an invented example. $\lambda_1$ is the handout's model of section 6, used here as the model of the class "walking" (the handout does not use it this way). $\lambda_2$ is invented for the class "running": $A$ with rows $(0.5,0.5)$ and $(0.1,0.9)$, the same $B$, $\pi=(0.2,0.8)$.
> For $O=(L,H,H)$: $P(O\mid\lambda_1)=0.1160$, $P(O\mid\lambda_2)=0.1426$. Choose class 2.
> For $O=(L,L,L)$: $P(O\mid\lambda_1)=0.1610$, $P(O\mid\lambda_2)=0.0378$. Choose class 1.

> [!info] What the decision rule assumes (beyond the slides)
> Each $\lambda_i$ is trained only on the sequences of its own class. The rule is the MAP rule of Lecture 2: $P(C_i\mid O)=\frac{P(O\mid\lambda_i)P(C_i)}{\sum_cP(O\mid\lambda_c)P(C_c)}$. The denominator is the same for every class, so it is not needed for the decision. In practice the logarithm is compared: $\ln P(O\mid\lambda_i)+\ln P(C_i)$.

> [!question]- Check (page 13): Use the handout's densities ($\mu_W=0.4$, $\sigma_W=0.2$; $\mu_R=1.0$, $\sigma_R=0.3$). Compute both emission densities of the value $o_t=0.7$. Which state explains this value better?
> Formula: $\mathcal N(o;\mu,\sigma^2)=\frac{1}{\sigma\sqrt{2\pi}}\exp\left(-\frac{(o-\mu)^2}{2\sigma^2}\right)$, with $\sqrt{2\pi}=2.5066$.
> Walking: front factor $\frac{1}{0.2\times2.5066}=1.9947$. Exponent $-\frac{(0.7-0.4)^2}{2(0.04)}=-\frac{0.09}{0.08}=-1.125$, $e^{-1.125}=0.3247$. Density $1.9947\times0.3247=0.6476$.
> Running: front factor $\frac{1}{0.3\times2.5066}=1.3298$. Exponent $-\frac{(0.7-1.0)^2}{2(0.09)}=-\frac{0.09}{0.18}=-0.5$, $e^{-0.5}=0.6065$. Density $1.3298\times0.6065=0.8066$.
> Running explains $0.7$ a little better ($0.8066>0.6476$). The value is at the same distance $0.3$ from both means, but this is $1.5$ standard deviations for walking and only $1$ for running.
> In the HMM this is only the emission factor. The transition probabilities and the neighbouring observations also count.

> [!question]- Check (page 14): For $O=(L,H,H)$ the two models of the example box ($\lambda_2$ is invented, not from the handout) give $P(O\mid\lambda_1)=0.1160$ (walking) and $P(O\mid\lambda_2)=0.1426$ (running). The person walks 70% of the time: $P(C_1)=0.7$, $P(C_2)=0.3$. Which class is chosen with the priors? Give the posterior probability of the chosen class.
> Without priors: $0.1426>0.1160$, running.
> With priors: walking $0.1160\times0.7=0.0812$. Running $0.1426\times0.3=0.0428$.
> $0.0812>0.0428$: walking is chosen. The prior changes the decision.
> Posterior: $P(C_1\mid O)=\frac{0.0812}{0.0812+0.0428}=\frac{0.0812}{0.1240}=0.655$.

## 8. Evaluation: the forward and backward algorithms (beyond the slides)

**Notes.** The handout names two algorithms but does not work them out: page 12 says "the Forward algorithm efficiently calculates this sum" (and calls the next topic "HMM Evaluation"), and page 2 names Baum-Welch. It does not mention decoding or Viterbi. The three standard problems of an HMM are:

| Problem | Question | Algorithm |
|---|---|---|
| Evaluation | how probable is $O$ under the model, $P(O\mid\lambda)$? | forward (section 8) |
| Decoding | which hidden path $Q$ explains $O$ best? | Viterbi (section 9) |
| Learning | which $\lambda$ makes the training sequences most probable? | Baum-Welch (section 10) |

**Why not list all paths.** $P(O\mid\lambda)=\sum_QP(Q,O\mid\lambda)$ has $N^T$ terms, each with $2T$ factors. For $N=5$, $T=100$: $5^{100}\approx7.9\times10^{69}$ paths. Impossible.

**Forward variable.** Many paths share their beginning. Store the sum over all beginnings once:

$$\alpha_t(i)=P(o_1,o_2,\dots,o_t,\ q_t=\omega_i\mid\lambda)$$

It is the probability of seeing the first $t$ observations and being in state $\omega_i$ at time $t$.

1. Start: $\alpha_1(i)=\pi_i\,b_i(o_1)$.
2. Recursion for $t=1,\dots,T-1$: $\alpha_{t+1}(j)=\left[\sum_{i=1}^{N}\alpha_t(i)\,a_{ij}\right]b_j(o_{t+1})$.
3. End: $P(O\mid\lambda)=\sum_{i=1}^{N}\alpha_T(i)$.

Reading of step 2: to be in state $j$ at time $t+1$, the model was in some state $i$ at time $t$ (probability $\alpha_t(i)$, with the observations so far), moved to $j$ ($a_{ij}$), and state $j$ emitted the new observation ($b_j(o_{t+1})$). Add over all $i$.

**Cost.** Each time step computes $N$ values, each a sum of $N$ products: about $N^2T$ multiplications. For $N=5$, $T=100$: $2500$, against $2T\,N^T\approx10^{72}$ for the list of all paths.

> [!example] Forward algorithm for the handout's model, $O=(L,H,H)$
> $t=1$ ($o_1=L$): $\alpha_1(W)=0.6\times0.7=0.42$. $\alpha_1(R)=0.4\times0.2=0.08$.
> $t=2$ ($o_2=H$):
> $\alpha_2(W)=(0.42\times0.8+0.08\times0.3)\times0.3=(0.336+0.024)\times0.3=0.108$.
> $\alpha_2(R)=(0.42\times0.2+0.08\times0.7)\times0.8=(0.084+0.056)\times0.8=0.112$.
> $t=3$ ($o_3=H$):
> $\alpha_3(W)=(0.108\times0.8+0.112\times0.3)\times0.3=(0.0864+0.0336)\times0.3=0.036$.
> $\alpha_3(R)=(0.108\times0.2+0.112\times0.7)\times0.8=(0.0216+0.0784)\times0.8=0.080$.
> End: $P(O\mid\lambda)=0.036+0.080=0.116$. This equals the sum of the eight paths in section 6.

**Backward variable.** The same idea from the end:

$$\beta_t(i)=P(o_{t+1},o_{t+2},\dots,o_T\mid q_t=\omega_i,\lambda)$$

It is the probability of the rest of the observations, given state $\omega_i$ at time $t$.

1. Start: $\beta_T(i)=1$ (nothing is left to observe).
2. Recursion for $t=T-1,\dots,1$: $\beta_t(i)=\sum_{j=1}^{N}a_{ij}\,b_j(o_{t+1})\,\beta_{t+1}(j)$.
3. Check: $P(O\mid\lambda)=\sum_{i=1}^{N}\pi_i\,b_i(o_1)\,\beta_1(i)$.

$\beta$ is not needed for evaluation. It is needed for learning (section 10). At every time $t$: $\sum_i\alpha_t(i)\beta_t(i)=P(O\mid\lambda)$.

> [!example] Backward values for the same case
> $\beta_3(W)=\beta_3(R)=1$.
> $\beta_2(W)=0.8(0.3)(1)+0.2(0.8)(1)=0.24+0.16=0.40$. $\beta_2(R)=0.3(0.3)(1)+0.7(0.8)(1)=0.09+0.56=0.65$.
> $\beta_1(W)=0.8(0.3)(0.40)+0.2(0.8)(0.65)=0.096+0.104=0.20$. $\beta_1(R)=0.3(0.3)(0.40)+0.7(0.8)(0.65)=0.036+0.364=0.40$.
> Check: $0.6(0.7)(0.20)+0.4(0.2)(0.40)=0.084+0.032=0.116$.
> Check at $t=2$: $\alpha_2(W)\beta_2(W)+\alpha_2(R)\beta_2(R)=0.108(0.40)+0.112(0.65)=0.0432+0.0728=0.116$.

> [!warning] Underflow: use scaling or logarithms
> Every step multiplies by numbers below 1, so $\alpha_t$ shrinks fast. If each step gives a factor of about $0.3$, then after $T=1000$ steps $\alpha\approx0.3^{1000}\approx10^{-523}$. The smallest normal double-precision number is about $10^{-308}$, and nothing below about $10^{-324}$ can be stored, so the computer stores 0.
> Scaling: after every step divide the $\alpha_t$ values by their sum $c_t$, so that they sum to 1. Then $\ln P(O\mid\lambda)=\sum_{t=1}^{T}\ln c_t$.
> For the example: $c_1=0.42+0.08=0.5$, scaled values $(0.84,\ 0.16)$. $c_2=0.44$, scaled $(0.4909,\ 0.5091)$. $c_3=0.5273$. $\ln P=\ln0.5+\ln0.44+\ln0.5273=-0.6931-0.8210-0.6400=-2.1542=\ln0.116$.
> Viterbi (section 9) has only products and a maximum, so it can work directly with sums of logarithms.

The check questions of sections 8 to 10 use a new model $\lambda_Q$ with two states and three symbols $v_1,v_2,v_3$:

$$A=\begin{bmatrix}0.7&0.3\\0.4&0.6\end{bmatrix},\qquad B=\begin{bmatrix}0.5&0.4&0.1\\0.1&0.3&0.6\end{bmatrix},\qquad \pi=\begin{bmatrix}0.5&0.5\end{bmatrix}$$

> [!question]- Check (forward algorithm): For the model $\lambda_Q$ and $O=(v_1,v_3,v_2)$ compute all $\alpha_t(i)$ and $P(O\mid\lambda_Q)$.
> Emission columns: $v_1$: $(0.5,\ 0.1)$. $v_3$: $(0.1,\ 0.6)$. $v_2$: $(0.4,\ 0.3)$.
> $t=1$: $\alpha_1(1)=0.5\times0.5=0.25$. $\alpha_1(2)=0.5\times0.1=0.05$.
> $t=2$ ($v_3$):
> $\alpha_2(1)=(0.25\times0.7+0.05\times0.4)\times0.1=(0.175+0.020)\times0.1=0.0195$.
> $\alpha_2(2)=(0.25\times0.3+0.05\times0.6)\times0.6=(0.075+0.030)\times0.6=0.0630$.
> $t=3$ ($v_2$):
> $\alpha_3(1)=(0.0195\times0.7+0.0630\times0.4)\times0.4=(0.01365+0.02520)\times0.4=0.015540$.
> $\alpha_3(2)=(0.0195\times0.3+0.0630\times0.6)\times0.3=(0.00585+0.03780)\times0.3=0.013095$.
> $P(O\mid\lambda_Q)=0.015540+0.013095=0.028635$.
> Check by listing the 8 paths: $0.004900+0.001575+0.007200+0.008100+0.000560+0.000180+0.002880+0.003240=0.028635$ (order $111,112,121,122,211,212,221,222$).

> [!question]- Check (complexity): A model has $N=3$ states and the sequence has $T=20$ observations. How many paths would the direct sum have, and about how many multiplications? How many does the forward algorithm need?
> Paths: $N^T=3^{20}=3\,486\,784\,401$, about $3.5\times10^9$.
> Each path is a product of $2T=40$ factors, so $39$ multiplications: about $39\times3.5\times10^9\approx1.4\times10^{11}$.
> Forward: about $N^2T=9\times20=180$ multiplications (more exactly $N$ at the start and $N(N+1)$ for each of the $T-1$ further steps: $3+19\times12=231$).
> The ratio is about $1.4\times10^{11}/231\approx6\times10^{8}$. The forward algorithm is linear in $T$; the direct sum is exponential in $T$.

> [!question]- Check (backward algorithm): For $\lambda_Q$ and $O=(v_1,v_3,v_2)$ compute $\beta_2(i)$ and $\beta_1(i)$, and check $P(O\mid\lambda_Q)$ with them.
> $\beta_3(1)=\beta_3(2)=1$.
> $t=2$ (next observation $v_2$, emissions $0.4$ and $0.3$):
> $\beta_2(1)=0.7(0.4)(1)+0.3(0.3)(1)=0.28+0.09=0.37$.
> $\beta_2(2)=0.4(0.4)(1)+0.6(0.3)(1)=0.16+0.18=0.34$.
> $t=1$ (next observation $v_3$, emissions $0.1$ and $0.6$):
> $\beta_1(1)=0.7(0.1)(0.37)+0.3(0.6)(0.34)=0.0259+0.0612=0.0871$.
> $\beta_1(2)=0.4(0.1)(0.37)+0.6(0.6)(0.34)=0.0148+0.1224=0.1372$.
> Check: $\sum_i\pi_ib_i(v_1)\beta_1(i)=0.25(0.0871)+0.05(0.1372)=0.021775+0.006860=0.028635$. Same value as the forward pass.

> [!question]- Check (underflow): A sequence has $T=500$ observations and every step multiplies the forward values by about $0.2$. Can $P(O\mid\lambda)$ be stored as a double-precision number? What is $\ln P(O\mid\lambda)$?
> $P\approx0.2^{500}$. $\log_{10}0.2=-0.699$, so $\log_{10}P\approx500\times(-0.699)=-349.5$, $P\approx10^{-349.5}$.
> The smallest normal double is about $10^{-308}$. $10^{-349.5}$ is smaller, so the stored value becomes 0 (underflow), and a comparison between classes is impossible.
> With logarithms: $\ln P\approx500\times\ln0.2=500\times(-1.6094)=-804.7$. This number is stored without any problem. The scaled forward algorithm gives it as $\sum_t\ln c_t$.

## 9. Decoding: the Viterbi algorithm (beyond the slides)

**Notes.** Decoding asks for the single best hidden path:

$$Q^*=\arg\max_{Q}P(Q,O\mid\lambda)$$

It is the same path that maximises $P(Q\mid O,\lambda)$, because the two differ only by the constant $P(O\mid\lambda)$. Listing all $N^T$ paths is again impossible. The **Viterbi algorithm** is the forward algorithm with the sum replaced by a maximum.

$$\delta_t(i)=\max_{q_1,\dots,q_{t-1}}P(q_1,\dots,q_{t-1},\ q_t=\omega_i,\ o_1,\dots,o_t\mid\lambda)$$

$\delta_t(i)$ is the probability of the best path that ends in state $\omega_i$ at time $t$. $\psi_t(j)$ stores which previous state that best path came from.

1. Start: $\delta_1(i)=\pi_i\,b_i(o_1)$.
2. Recursion: $\delta_{t+1}(j)=\left[\max_i\delta_t(i)\,a_{ij}\right]b_j(o_{t+1})$, and $\psi_{t+1}(j)=\arg\max_i\delta_t(i)\,a_{ij}$.
3. End: $P^*=\max_i\delta_T(i)$, $q_T^*=\arg\max_i\delta_T(i)$.
4. **Backtracking** for $t=T-1,\dots,1$: $q_t^*=\psi_{t+1}(q_{t+1}^*)$.

Why it works: the best path to state $j$ at time $t+1$ must pass through some state $i$ at time $t$, and its part up to $i$ must itself be the best path to $i$. So only one path per state has to be kept. Cost: $N^2T$, as for the forward algorithm.

> [!example] Viterbi for the handout's model, $O=(L,H,H)$
> $t=1$: $\delta_1(W)=0.42$, $\delta_1(R)=0.08$.
> $t=2$ ($H$):
> To $W$: from $W$ $0.42\times0.8=0.336$, from $R$ $0.08\times0.3=0.024$. Max $0.336$ (from $W$). $\delta_2(W)=0.336\times0.3=0.1008$, $\psi_2(W)=W$.
> To $R$: from $W$ $0.42\times0.2=0.084$, from $R$ $0.08\times0.7=0.056$. Max $0.084$ (from $W$). $\delta_2(R)=0.084\times0.8=0.0672$, $\psi_2(R)=W$.
> $t=3$ ($H$):
> To $W$: from $W$ $0.1008\times0.8=0.08064$, from $R$ $0.0672\times0.3=0.02016$. $\delta_3(W)=0.08064\times0.3=0.024192$, $\psi_3(W)=W$.
> To $R$: from $W$ $0.1008\times0.2=0.02016$, from $R$ $0.0672\times0.7=0.04704$. $\delta_3(R)=0.04704\times0.8=0.037632$, $\psi_3(R)=R$.
> End: the larger value is $\delta_3(R)=0.037632$, so $q_3^*=R$.
> Backtracking: $q_2^*=\psi_3(R)=R$, $q_1^*=\psi_2(R)=W$.
> Best path: $(W,R,R)$ with $P^*=0.037632$. This is the path the handout chose on page 11, and the largest row of the table in section 6.

> [!warning] Watch out
> - $P^*=\max_QP(Q,O\mid\lambda)$ is not $P(O\mid\lambda)$. Here $0.037632$ against $0.116$. Viterbi keeps one path, the forward algorithm adds all of them.
> - The path must be read backwards with $\psi$. Taking the larger $\delta_t$ at every time separately can give a different, wrong path.
> - In practice use logarithms: $\ln\delta_{t+1}(j)=\max_i\left[\ln\delta_t(i)+\ln a_{ij}\right]+\ln b_j(o_{t+1})$.

> [!question]- Check (Viterbi): For the model $\lambda_Q$ of section 8 and $O=(v_1,v_3,v_2)$ find the best path with $\delta$, $\psi$ and backtracking.
> $t=1$: $\delta_1(1)=0.5\times0.5=0.25$. $\delta_1(2)=0.5\times0.1=0.05$.
> $t=2$ ($v_3$, emissions $0.1$ and $0.6$):
> To state 1: $0.25\times0.7=0.175$, $0.05\times0.4=0.020$. Max $0.175$ from state 1. $\delta_2(1)=0.175\times0.1=0.0175$, $\psi_2(1)=1$.
> To state 2: $0.25\times0.3=0.075$, $0.05\times0.6=0.030$. Max $0.075$ from state 1. $\delta_2(2)=0.075\times0.6=0.045$, $\psi_2(2)=1$.
> $t=3$ ($v_2$, emissions $0.4$ and $0.3$):
> To state 1: $0.0175\times0.7=0.01225$, $0.045\times0.4=0.018$. Max $0.018$ from state 2. $\delta_3(1)=0.018\times0.4=0.0072$, $\psi_3(1)=2$.
> To state 2: $0.0175\times0.3=0.00525$, $0.045\times0.6=0.027$. Max $0.027$ from state 2. $\delta_3(2)=0.027\times0.3=0.0081$, $\psi_3(2)=2$.
> End: $\delta_3(2)=0.0081>\delta_3(1)=0.0072$, so $q_3^*=2$.
> Backtracking: $q_2^*=\psi_3(2)=2$, $q_1^*=\psi_2(2)=1$.
> Best path: $(\omega_1,\omega_2,\omega_2)$, $P^*=0.0081$. In the list of 8 paths (forward question) $0.008100$ is the largest value.
> Probability of this path given $O$: $\frac{0.0081}{0.028635}=0.283$.

> [!question]- Check (Viterbi against single states): For $\lambda_Q$ and $O=(v_1,v_3,v_2)$ the probabilities of state $\omega_1$ at times 1, 2, 3 given all observations are $0.7604$, $0.2520$, $0.5427$ (these are the $\gamma_t(1)$ of section 10). Which state is the most probable at each time? Is this the Viterbi path?
> Time 1: $0.7604>0.5$, state $\omega_1$.
> Time 2: $0.2520<0.5$, state $\omega_2$ (probability $0.7480$).
> Time 3: $0.5427>0.5$, state $\omega_1$.
> Sequence of the single best states: $(\omega_1,\omega_2,\omega_1)$. The Viterbi path is $(\omega_1,\omega_2,\omega_2)$. They differ at time 3.
> Both answers are correct for their own question. The path $(\omega_1,\omega_2,\omega_1)$ has the joint probability $0.0072$, less than $0.0081$, so it is not the best whole path. But at time 3 the paths that end in $\omega_1$ have together more probability ($0.015540$) than those that end in $\omega_2$ ($0.013095$).
> Viterbi maximises the probability of the whole path. Picking the best state at each time maximises the expected number of correct states, and can even give a path that is impossible when some $a_{ij}=0$.

## 10. Learning: the Baum-Welch algorithm (beyond the slides)

**Notes.** Learning: find $\lambda^*=\arg\max_\lambda P(O\mid\lambda)$ (page 2). If the hidden states of the training data were known, we would count, as on page 6: $a_{ij}=N_{ij}/\sum_jN_{ij}$, and in the same way for $B$ and $\pi$. The states are hidden, so the counts are replaced by **expected counts**, computed with the current model. This is EM (Lecture 5), and the hidden states are the latent variables.

**E-step.** Run the forward and the backward pass with the current $\lambda$. Then:

$$\gamma_t(i)=P(q_t=\omega_i\mid O,\lambda)=\frac{\alpha_t(i)\,\beta_t(i)}{P(O\mid\lambda)}$$

$$\xi_t(i,j)=P(q_t=\omega_i,\ q_{t+1}=\omega_j\mid O,\lambda)=\frac{\alpha_t(i)\,a_{ij}\,b_j(o_{t+1})\,\beta_{t+1}(j)}{P(O\mid\lambda)}$$

- $\gamma_t(i)$: probability of state $i$ at time $t$, given all observations. $\sum_i\gamma_t(i)=1$ at every $t$. It is the responsibility of Lecture 5, with "state" in the place of "component".
- $\xi_t(i,j)$: probability of the transition $i\to j$ between $t$ and $t+1$. $\sum_j\xi_t(i,j)=\gamma_t(i)$.
- Why the product $\alpha\beta$: $\alpha_t(i)$ covers the observations up to $t$ and the state $i$, $\beta_t(i)$ covers the observations after $t$. Together $\alpha_t(i)\beta_t(i)=P(O,\ q_t=\omega_i\mid\lambda)$. Dividing by $P(O\mid\lambda)$ gives the conditional probability.

**M-step (re-estimation formulas).**

$$\hat\pi_i=\gamma_1(i),\qquad \hat a_{ij}=\frac{\sum_{t=1}^{T-1}\xi_t(i,j)}{\sum_{t=1}^{T-1}\gamma_t(i)},\qquad \hat b_{jk}=\frac{\sum_{t:\,o_t=v_k}\gamma_t(j)}{\sum_{t=1}^{T}\gamma_t(j)}$$

- $\hat a_{ij}$: expected number of transitions $i\to j$, divided by the expected number of transitions out of $i$.
- $\hat b_{jk}$: expected number of times in state $j$ while $v_k$ was observed, divided by the expected number of times in state $j$.
- Gaussian emissions: $\hat\mu_j=\frac{\sum_t\gamma_t(j)\,o_t}{\sum_t\gamma_t(j)}$, $\hat\sigma_j^2=\frac{\sum_t\gamma_t(j)(o_t-\hat\mu_j)^2}{\sum_t\gamma_t(j)}$. These are the M-step formulas of the GMM in Lecture 5.

Repeat E-step and M-step. $P(O\mid\lambda)$ never decreases. The result is a local maximum that depends on the start.

> [!example] One Baum-Welch step for the handout's model, $O=(L,H,H)$
> From section 8: $\alpha_1=(0.42,0.08)$, $\alpha_2=(0.108,0.112)$, $\alpha_3=(0.036,0.080)$; $\beta_1=(0.20,0.40)$, $\beta_2=(0.40,0.65)$, $\beta_3=(1,1)$; $P(O\mid\lambda)=0.116$. Order of the entries: $(W,R)$.
>
> | $t$ | $\gamma_t(W)$ | $\gamma_t(R)$ |
> |---|---|---|
> | 1 | $0.42(0.20)/0.116=0.7241$ | $0.08(0.40)/0.116=0.2759$ |
> | 2 | $0.108(0.40)/0.116=0.3724$ | $0.112(0.65)/0.116=0.6276$ |
> | 3 | $0.036(1)/0.116=0.3103$ | $0.080(1)/0.116=0.6897$ |
>
> Every row sums to 1. All sums below use the unrounded values, so the last digit can differ by 1 from a sum of the rounded numbers.
> $\xi_1$: $\xi_1(W,W)=0.42(0.8)(0.3)(0.40)/0.116=0.3476$. $\xi_1(W,R)=0.42(0.2)(0.8)(0.65)/0.116=0.3766$. $\xi_1(R,W)=0.08(0.3)(0.3)(0.40)/0.116=0.0248$. $\xi_1(R,R)=0.08(0.7)(0.8)(0.65)/0.116=0.2510$.
> $\xi_2$: $\xi_2(W,W)=0.108(0.8)(0.3)(1)/0.116=0.2234$. $\xi_2(W,R)=0.108(0.2)(0.8)/0.116=0.1490$. $\xi_2(R,W)=0.112(0.3)(0.3)/0.116=0.0869$. $\xi_2(R,R)=0.112(0.7)(0.8)/0.116=0.5407$.
> Check: $\xi_1(W,W)+\xi_1(W,R)=0.7241=\gamma_1(W)$.
> New start: $\hat\pi=(0.7241,\ 0.2759)$.
> New $A$, row $W$: denominator $\gamma_1(W)+\gamma_2(W)=1.0966$. $\hat a_{WW}=\frac{0.3476+0.2234}{1.0966}=0.5208$, $\hat a_{WR}=\frac{0.3766+0.1490}{1.0966}=0.4792$.
> Row $R$: denominator $0.2759+0.6276=0.9034$. $\hat a_{RW}=\frac{0.0248+0.0869}{0.9034}=0.1237$, $\hat a_{RR}=\frac{0.2510+0.5407}{0.9034}=0.8763$.
> New $B$: $L$ was observed only at $t=1$. $\hat b_W(L)=\frac{0.7241}{0.7241+0.3724+0.3103}=\frac{0.7241}{1.4069}=0.5147$, $\hat b_W(H)=0.4853$. $\hat b_R(L)=\frac{0.2759}{1.5931}=0.1732$, $\hat b_R(H)=0.8268$.
> With the new model $P(O\mid\hat\lambda)=0.2060$. It went up from $0.116$.

> [!warning] Watch out
> - The example trains on one sequence of three observations only to show the arithmetic. A real model is trained on many sequences: add the numerators and the denominators over all training sequences. With one short sequence the model only learns that sequence by heart.
> - A parameter that starts at 0 stays 0 ($\xi_t(i,j)$ contains the factor $a_{ij}$). This keeps a left-to-right structure during training. It also means that a start with wrong zeros can never be repaired.
> - Different starts can give different results (local maxima), as for the GMM.
> - Use the scaled $\alpha$ and $\beta$ for long sequences.

> [!info] Baum-Welch and the GMM of Lecture 5 side by side
>
> | | GMM (Lecture 5) | HMM (Baum-Welch) |
> |---|---|---|
> | Latent variable | component of each sample | state at each time |
> | E-step | responsibilities $\gamma_{ik}$ by Bayes' rule | $\gamma_t(i)$ and $\xi_t(i,j)$ by forward-backward |
> | M-step | weighted means, variances, weights | weighted counts for $\pi$, $A$, $B$ |
> | Extra in the HMM | none | $\xi$, because neighbouring states are linked by $A$ |
> | Guarantee | likelihood never decreases, local maximum | the same |

> [!question]- Check (gamma and xi): For $\lambda_Q$ (section 8) and $O=(v_1,v_3,v_2)$ use $\alpha_1=(0.25,0.05)$, $\alpha_2=(0.0195,0.0630)$, $\alpha_3=(0.015540,0.013095)$, $\beta_1=(0.0871,0.1372)$, $\beta_2=(0.37,0.34)$, $\beta_3=(1,1)$, $P(O\mid\lambda_Q)=0.028635$. Compute all $\gamma_t(i)$ and $\xi_1(1,2)$.
> $\gamma_t(i)=\alpha_t(i)\beta_t(i)/P$.
> $t=1$: $\gamma_1(1)=\frac{0.25\times0.0871}{0.028635}=\frac{0.021775}{0.028635}=0.7604$. $\gamma_1(2)=\frac{0.05\times0.1372}{0.028635}=\frac{0.006860}{0.028635}=0.2396$. Sum $1$.
> $t=2$: $\gamma_2(1)=\frac{0.0195\times0.37}{0.028635}=\frac{0.007215}{0.028635}=0.2520$. $\gamma_2(2)=\frac{0.0630\times0.34}{0.028635}=\frac{0.021420}{0.028635}=0.7480$. Sum $1$.
> $t=3$: $\gamma_3(1)=\frac{0.015540}{0.028635}=0.5427$. $\gamma_3(2)=\frac{0.013095}{0.028635}=0.4573$. Sum $1$.
> $\xi_1(1,2)=\frac{\alpha_1(1)\,a_{12}\,b_2(v_3)\,\beta_2(2)}{P}=\frac{0.25\times0.3\times0.6\times0.34}{0.028635}=\frac{0.0153}{0.028635}=0.5343$.
> Reading: the transition $\omega_1\to\omega_2$ after the first observation has probability $0.53$. The symbol $v_3$ at time 2 points strongly to state 2 ($0.6$ against $0.1$).

> [!question]- Check (re-estimation): Continue the previous question. Given also $\xi_2(1,2)=0.0613$, compute the new $\hat\pi$, $\hat a_{12}$ and $\hat b_{11}$ (state 1 emits $v_1$).
> $\hat\pi=(\gamma_1(1),\ \gamma_1(2))=(0.7604,\ 0.2396)$.
> $\hat a_{12}=\frac{\xi_1(1,2)+\xi_2(1,2)}{\gamma_1(1)+\gamma_2(1)}=\frac{0.5343+0.0613}{0.7604+0.2520}=\frac{0.5956}{1.0124}=0.5883$.
> $\hat b_{11}$: $v_1$ was observed only at $t=1$. Numerator $\gamma_1(1)=0.7604$. Denominator $\gamma_1(1)+\gamma_2(1)+\gamma_3(1)=0.7604+0.2520+0.5427=1.5551$. $\hat b_{11}=\frac{0.7604}{1.5551}=0.4890$.
> After the complete step the likelihood of this sequence rises from $0.028635$ to $0.050422$.
> Note the two different denominators: $\hat a$ uses the times $1$ to $T-1$ (a transition needs a next time), $\hat b$ uses all times $1$ to $T$.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Markov property | $P(\omega(t+1)\mid\omega(t),\dots,\omega(1))=P(\omega(t+1)\mid\omega(t))$ |
| Transition probability | $a_{ij}=P(\omega_j(t+1)\mid\omega_i(t))$, $\sum_ja_{ij}=1$ |
| Estimate from counts | $a_{ij}=N_{ij}\big/\sum_jN_{ij}$ |
| State sequence probability | $P(\omega^T\mid\theta)=P(\omega(1))\prod_{t=2}^{T}a_{\omega(t-1)\omega(t)}$ |
| $n$ steps ahead (beyond slides) | entries of $A^n$ |
| HMM | $\lambda=(A,B,\pi)$: transitions, emissions, initial distribution |
| Emission probability | $b_{jk}=P(v_k(t)\mid\omega_j(t))$, $\sum_kb_{jk}=1$; continuous: $\mathcal N(o_t;\mu_j,\sigma_j^2)$ |
| Two assumptions | state depends only on the previous state; observation depends only on the current state |
| Joint probability | $P(Q,O\mid\lambda)=\pi_{q_1}b_{q_1}(o_1)\prod_{t=2}^{T}a_{q_{t-1}q_t}b_{q_t}(o_t)$ |
| Observation likelihood | $P(O\mid\lambda)=\sum_QP(Q,O\mid\lambda)$, $N^T$ paths |
| Forward | $\alpha_1(i)=\pi_ib_i(o_1)$, $\alpha_{t+1}(j)=\left[\sum_i\alpha_t(i)a_{ij}\right]b_j(o_{t+1})$, $P(O\mid\lambda)=\sum_i\alpha_T(i)$; cost $N^2T$ |
| Backward | $\beta_T(i)=1$, $\beta_t(i)=\sum_ja_{ij}b_j(o_{t+1})\beta_{t+1}(j)$ |
| Viterbi | $\delta_{t+1}(j)=\left[\max_i\delta_t(i)a_{ij}\right]b_j(o_{t+1})$, $\psi$ stores the argmax, then backtracking |
| State posterior | $\gamma_t(i)=\alpha_t(i)\beta_t(i)/P(O\mid\lambda)$ |
| Transition posterior | $\xi_t(i,j)=\alpha_t(i)a_{ij}b_j(o_{t+1})\beta_{t+1}(j)/P(O\mid\lambda)$ |
| Baum-Welch | $\hat\pi_i=\gamma_1(i)$, $\hat a_{ij}=\sum_{t<T}\xi_t(i,j)\big/\sum_{t<T}\gamma_t(i)$, $\hat b_{jk}=\sum_{t:o_t=v_k}\gamma_t(j)\big/\sum_t\gamma_t(j)$ |
| Scaling | divide $\alpha_t$ by its sum $c_t$; $\ln P(O\mid\lambda)=\sum_t\ln c_t$ |
| Left-to-right model | $a_{ij}=0$ for $j<i$; mean stay in state $i$: $1/(1-a_{ii})$ |
| Classification | $\hat C=\arg\max_iP(O\mid\lambda_i)P(C_i)$ |

## Check yourself
> [!question]- 1. A Markov chain has the rows $(0.9,\ 0.1)$ and $(0.5,\ 0.5)$ and starts in state 1 with probability $0.7$. Compute the probability of the state sequence $1,1,2,2$, and the probability to be in state 2 two steps after state 1.
> Sequence: $P(1)\,a_{11}\,a_{12}\,a_{22}=0.7\times0.9\times0.1\times0.5=0.0315$.
> Two steps, paths $1\to1\to2$ and $1\to2\to2$: $0.9\times0.1+0.1\times0.5=0.09+0.05=0.14$.

> [!question]- 2. For the model $\lambda_Q$ of section 8 compute the joint probability of the path $Q=(\omega_2,\omega_1,\omega_1)$ with the observations $O=(v_3,v_1,v_2)$.
> Factors: $\pi_2=0.5$, $b_2(v_3)=0.6$, $a_{21}=0.4$, $b_1(v_1)=0.5$, $a_{11}=0.7$, $b_1(v_2)=0.4$.
> $0.5\times0.6=0.3$; $0.3\times0.4=0.12$; $0.12\times0.5=0.06$; $0.06\times0.7=0.042$; $0.042\times0.4=0.0168$.
> $P(Q,O\mid\lambda_Q)=0.0168$.

> [!question]- 3. For the handout's activity model compute $P(O\mid\lambda)$ of the short sequence $O=(H,L)$ with the forward algorithm.
> $\alpha_1(W)=0.6\times0.3=0.18$. $\alpha_1(R)=0.4\times0.8=0.32$.
> $\alpha_2(W)=(0.18\times0.8+0.32\times0.3)\times0.7=(0.144+0.096)\times0.7=0.168$.
> $\alpha_2(R)=(0.18\times0.2+0.32\times0.7)\times0.2=(0.036+0.224)\times0.2=0.052$.
> $P(O\mid\lambda)=0.168+0.052=0.220$.

> [!question]- 4. For the same model and $O=(H,L)$ find the Viterbi path. Is the first state of the path the state with the larger $\delta_1$?
> $\delta_1(W)=0.18$, $\delta_1(R)=0.32$.
> To $W$: $0.18\times0.8=0.144$, $0.32\times0.3=0.096$. Max from $W$. $\delta_2(W)=0.144\times0.7=0.1008$, $\psi_2(W)=W$.
> To $R$: $0.18\times0.2=0.036$, $0.32\times0.7=0.224$. Max from $R$. $\delta_2(R)=0.224\times0.2=0.0448$, $\psi_2(R)=R$.
> End: $\delta_2(W)=0.1008$ is larger, $q_2^*=W$. Backtracking: $q_1^*=\psi_2(W)=W$.
> Path $(W,W)$ with $P^*=0.1008$. Given $O$ its probability is $\frac{0.1008}{0.220}=0.458$.
> No: $\delta_1(R)=0.32$ is larger than $\delta_1(W)=0.18$, but the best path starts with $W$. The second observation $L$ changes the picture. This is why backtracking is needed.

> [!question]- 5. For the handout's model and $O=(H,L,L)$ the state posteriors are $\gamma_1=(0.5294,\ 0.4706)$, $\gamma_2=(0.8471,\ 0.1529)$, $\gamma_3=(0.8824,\ 0.1176)$ in the order $(W,R)$. Compute the re-estimated $\hat\pi$ and $\hat b_W(L)$.
> $\hat\pi=\gamma_1=(0.5294,\ 0.4706)$.
> $L$ was observed at $t=2$ and $t=3$. Numerator: $\gamma_2(W)+\gamma_3(W)=0.8471+0.8824=1.7295$.
> Denominator: $0.5294+0.8471+0.8824=2.2589$.
> $\hat b_W(L)=\frac{1.7295}{2.2589}=0.7656$, and $\hat b_W(H)=1-0.7656=0.2344$.

> [!question]- 6. Two HMMs give $\ln P(O\mid\lambda_1)=-42.1$ and $\ln P(O\mid\lambda_2)=-40.9$. The priors are $P(C_1)=0.8$, $P(C_2)=0.2$. Which class wins without the priors, and which with them?
> Without priors: $-40.9>-42.1$, class 2.
> With priors add the log prior: $\ln0.8=-0.223$, $\ln0.2=-1.609$.
> Class 1: $-42.1-0.223=-42.323$. Class 2: $-40.9-1.609=-42.509$.
> $-42.323>-42.509$: class 1 wins. The likelihood ratio $e^{1.2}=3.32$ in favour of class 2 is smaller than the prior ratio $0.8/0.2=4$ in favour of class 1.

## Links
- Course index: [[EE525 Home]]
- Previous: [[EE525 L05 - MLE, Bayesian estimation and EM]] (maximum likelihood, EM and responsibilities, used by Baum-Welch). Bayes' rule and priors: [[EE525 L02 - Bayesian classifiers]].
- Handout (PDF): [Lecture 6 Hidden Markov Models.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/Lecture_06/Lecture%206%20Hidden%20Markov%20Models.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/Lecture_06/Lecture 6 Hidden Markov Models.pdf`

Slides not embedded: none.
