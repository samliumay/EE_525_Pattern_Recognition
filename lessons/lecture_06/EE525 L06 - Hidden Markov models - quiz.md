---
course: EE 525 Pattern Recognition
lesson: EE525 L06 - Hidden Markov models
type: quiz
questions: 24
---

# EE525 L06 - Hidden Markov models - quiz

Lesson note: [[EE525 L06 - Hidden Markov models]]. One question per check question of the lesson. Pick an option, then open the answer.

Conventions in every question: $a_{ij}$ is the probability to go **from** state $i$ **to** state $j$ (row = current state, column = next state). $b_{jk}$ is the probability that state $j$ emits symbol $v_k$ (row = state, column = symbol). $\pi_i$ is the probability to start in state $i$, and it is included in every joint probability unless the question says otherwise. $\ln$ is the natural logarithm. $\sqrt{2\pi}=2.5066$.

## 1. Why time matters, and the parameters of an HMM (pages 1-2)

### Q1 (page 1)

An HMM has $N=3$ hidden states and $M=4$ observation symbols. How many entries do $A$, $B$ and $\pi$ have together, and how many of them are free (not fixed by the sum-to-1 rules)?

- A) $24$ entries, $18$ free
- B) $24$ entries, $21$ free
- C) $24$ entries, $17$ free
- D) $24$ entries, $16$ free

> [!success]- Answer
> **C.** Entries: $A$ has $3\times3=9$, $B$ has $3\times4=12$, $\pi$ has $3$. Total $24$.
> Free: every row of $A$ sums to 1, so $3\times2=6$. Every row of $B$ sums to 1, so $3\times3=9$. $\pi$ sums to 1, so $2$. Total $6+9+2=17$.
> - A: $6+9+3=18$ forgets that $\pi$ also sums to 1.
> - B: $24-3=21$ removes only one number for each of $A$, $B$, $\pi$. But there is one rule per row, so $A$ and $B$ lose 3 numbers each.
> - D: $6+8+2=16$ lets the 4 columns of $B$ sum to 1. The rows of $B$ sum to 1: each state must emit some symbol.

### Q2 (page 1, HELLO model)

A left-to-right model has three states, $a_{11}=0.6$, $a_{22}=0.9$, and no skips (a state can only stay or go to the next state; it never goes back). What is row 2 of $A$, and how many steps does the model stay in state 2 on average? Use the mean stay $\frac{1}{1-a_{ii}}$.

- A) Row 2 is $(0,\ 0.9,\ 0.1)$. Mean stay $10$ steps.
- B) Row 2 is $(0.1,\ 0.9,\ 0)$. Mean stay $10$ steps.
- C) Row 2 is $(0,\ 0.9,\ 0.1)$. Mean stay $9$ steps.
- D) Row 2 is $(0,\ 0.9,\ 0.1)$. Mean stay $12.5$ steps.

> [!success]- Answer
> **A.** No way back, so $a_{21}=0$. Stay $0.9$, go on with $1-0.9=0.1$: $(0,\ 0.9,\ 0.1)$.
> Mean stay: $\frac{1}{1-0.9}=\frac{1}{0.1}=10$ steps.
> - B: The $0.1$ goes back to state 1. In a left-to-right model $a_{ij}=0$ for $j<i$.
> - C: $\frac{a_{22}}{1-a_{22}}=\frac{0.9}{0.1}=9$ counts only the extra repeats, not the first step in the state.
> - D: $\frac{1}{1-0.6}+\frac{1}{1-0.9}=2.5+10=12.5$ adds the stay in state 1. That is the mean time from the start until state 3 is reached, not the stay in state 2.

### Q3 (page 2)

A recording gives $\ln P(X\mid\lambda_{\text{HELLO}})=-310.2$, $\ln P(X\mid\lambda_{\text{GOODBYE}})=-305.7$, $\ln P(X\mid\lambda_{\text{THANKS}})=-320.4$. The priors are equal. Which word is chosen, and how many times larger is its likelihood than the likelihood of the second-best word?

- A) GOODBYE, $4.5$ times larger
- B) GOODBYE, $1.015$ times larger
- C) GOODBYE, $2.4\times10^{6}$ times larger
- D) GOODBYE, $90$ times larger

> [!success]- Answer
> **D.** $\ln$ is increasing, so the largest log-likelihood wins: $-305.7$, GOODBYE.
> Second: HELLO, $-310.2$. Difference $-305.7-(-310.2)=4.5$. Ratio of the likelihoods $e^{4.5}=90.0$.
> - A: $4.5$ is the difference of the logarithms, not the ratio. The ratio is $e^{4.5}$.
> - B: $\frac{310.2}{305.7}=1.015$ divides the log values. A ratio of logarithms means nothing here.
> - C: $e^{-305.7+320.4}=e^{14.7}=2.4\times10^6$ compares with THANKS, the worst word, not the second.

## 2. Links to earlier methods, and the Markov property (page 3)

### Q4 (page 3)

The weather has $N=3$ states. How many transition probabilities does a first-order Markov model need, and a second-order model? How many of them are free?

- A) First order $9$ ($8$ free). Second order $27$ ($26$ free).
- B) First order $9$ ($6$ free). Second order $27$ ($18$ free).
- C) First order $9$ ($6$ free). Second order $27$ ($24$ free).
- D) First order $9$ ($6$ free). Second order $18$ ($12$ free).

> [!success]- Answer
> **B.** First order: one number per pair (current, next): $3^2=9$. Each of the 3 rows sums to 1: $3\times2=6$ free.
> Second order: one number per triple (before, current, next): $3^3=27$. There are $3^2=9$ pasts, each row sums to 1: $9\times2=18$ free.
> - A: Only one sum-to-1 rule for the whole table. There is one rule per past.
> - C: $27-3=24$ uses only 3 rows. The past is a pair of states, so there are 9 rows.
> - D: Two separate $3\times3$ tables, $2\times9=18$. The second-order model needs one row for every pair of past states.

## 3. First-order Markov models (pages 4-5)

### Q5 (page 4)

A three-state model has the rows $(0.5,\ 0.2,\ a_{13})$, $(0.1,\ 0.6,\ 0.3)$, $(0.4,\ 0.4,\ a_{33})$. The system was in $\omega_2$ at time $t-1$ and is in $\omega_1$ at time $t$. Give $a_{13}$, $a_{33}$ and the probability of $\omega_3$ at time $t+1$.

- A) $a_{13}=0.3$, $a_{33}=0.2$, probability $0.3$
- B) $a_{13}=0.3$, $a_{33}=0.2$, probability $0.4$
- C) $a_{13}=0.3$, $a_{33}=0.2$, probability $0.03$
- D) $a_{13}=0.3$, $a_{33}=0.2$, probability $0.27$

> [!success]- Answer
> **A.** Rows sum to 1: $a_{13}=1-0.5-0.2=0.3$, $a_{33}=1-0.4-0.4=0.2$.
> First order: only the current state $\omega_1$ counts. $P(\omega_3(t+1)\mid\omega_1(t))=a_{13}=0.3$.
> Here $a_{23}$ is also $0.3$, so starting from the old state $\omega_2$ gives the same number by chance. The reason is still wrong: the state at $t-1$ plays no role.
> - B: $a_{31}=0.4$ swaps the indices. That is the step from $\omega_3$ to $\omega_1$.
> - C: $a_{21}a_{13}=0.1\times0.3=0.03$ also multiplies by the past step. That step has already happened and is known.
> - D: $(A^2)_{23}=0.1(0.3)+0.6(0.3)+0.3(0.2)=0.27$ predicts two steps from $\omega_2$ and ignores the known current state $\omega_1$.

### Q6 (page 5)

Same matrix: rows $(0.5,0.2,0.3)$, $(0.1,0.6,0.3)$, $(0.4,0.4,0.2)$. Give the probability of the state sequence $\{\omega_2,\omega_2,\omega_3,\omega_1,\omega_3\}$ first given that it starts in $\omega_2$, then with $P(\omega(1)=\omega_2)=0.5$.

- A) $0.0288$, then $0.0144$
- B) $0.072$, then $0.036$
- C) $0.0216$, then $0.0108$
- D) $0.0216$, then $0.0432$

> [!success]- Answer
> **C.** Transitions $\omega_2\to\omega_2$, $\omega_2\to\omega_3$, $\omega_3\to\omega_1$, $\omega_1\to\omega_3$: $a_{22}a_{23}a_{31}a_{13}=0.6\times0.3\times0.4\times0.3=0.0216$.
> With the start: $0.5\times0.0216=0.0108$.
> - A: $a_{22}a_{32}a_{13}a_{31}=0.6\times0.4\times0.3\times0.4=0.0288$ reads every $a_{ij}$ with the indices swapped (next state first).
> - B: $0.6\times0.3\times0.4=0.072$ uses only 3 transitions. A sequence of length $T=5$ has $T-1=4$.
> - D: $0.0216/0.5=0.0432$ divides by the start probability instead of multiplying.

## 4. Weather example: transition matrix, counting, sequence probability (pages 6-7)

### Q7 (page 6)

A weather diary shows the 10 days S S R R R S S S R S. Estimate the transition matrix (row = today, column = tomorrow, order $S$, $R$).

- A) Rows $(0.6,\ 0.4)$ and $(0.4,\ 0.6)$
- B) Rows $(0.6,\ 0.4)$ and $(0.5,\ 0.5)$
- C) Rows $(0.6,\ 0.5)$ and $(0.4,\ 0.5)$
- D) Rows $(0.5,\ 0.333)$ and $(0.5,\ 0.5)$

> [!success]- Answer
> **B.** The 9 pairs: SS, SR, RR, RR, RS, SS, SS, SR, RS. Counts $N_{SS}=3$, $N_{SR}=2$, $N_{RS}=2$, $N_{RR}=2$.
> From $S$: 5 transitions, $\left(\frac35,\frac25\right)=(0.6,\ 0.4)$. From $R$: 4 transitions, $\left(\frac24,\frac24\right)=(0.5,\ 0.5)$.
> - A: $N_{RR}=3$ counts the run R R R as three R-to-R steps. Three days give only two steps.
> - C: Divides by the column sums ($N_{SS}+N_{RS}=5$, $N_{SR}+N_{RR}=4$). The rows must sum to 1, not the columns.
> - D: $\frac36$, $\frac26$ divide by the 6 sunny days. The last day (S) has no next day, so only 5 transitions leave $S$.

### Q8 (page 7)

Use $A$ with rows $(0.8,\ 0.2)$ for $S$ and $(0.4,\ 0.6)$ for $R$, and $P(\omega(1)=R)=0.4$. Give the probability of the sequence $R\to R\to S\to S$, and the probability that it is sunny two days after a rainy day.

- A) $0.192$ and $0.56$
- B) $0.0768$ and $0.32$
- C) $0.0384$ and $0.28$
- D) $0.0768$ and $0.56$

> [!success]- Answer
> **D.** Sequence: $P(R)\,a_{RR}\,a_{RS}\,a_{SS}=0.4\times0.6\times0.4\times0.8=0.0768$.
> Two days, add the two paths: $R\to S\to S$: $0.4\times0.8=0.32$. $R\to R\to S$: $0.6\times0.4=0.24$. Sum $0.56$.
> - A: $0.6\times0.4\times0.8=0.192$ leaves out the start probability $0.4$.
> - B: $0.32$ keeps only the path $R\to S\to S$. The middle day is unknown, so both paths count.
> - C: Swapped indices: $a_{RS}$ is read as $0.2$, so $0.4\times0.6\times0.2\times0.8=0.0384$; two steps with the transposed matrix give $0.28$.

## 5. From Markov models to hidden Markov models (pages 8-9)

### Q9 (page 8)

(a) Recognising a spoken word. (b) Guessing the weather from whether a colleague carries an umbrella. What are the hidden states and the observations? And how many hidden-state sequences are possible for $N=3$ states and $T=10$ measurements?

- A) (a) hidden: acoustic features, observed: sound. (b) hidden: weather, observed: umbrella. $59049$ sequences.
- B) (a) hidden: sound, observed: acoustic features. (b) hidden: umbrella, observed: weather. $59049$ sequences.
- C) (a) hidden: sound, observed: acoustic features. (b) hidden: weather, observed: umbrella. $59049$ sequences.
- D) (a) hidden: sound, observed: acoustic features. (b) hidden: weather, observed: umbrella. $1000$ sequences.

> [!success]- Answer
> **C.** (a) The stage of the pronunciation (/H/, /E/, ...) is hidden; the acoustic features of the signal are measured. (b) The weather is hidden; the umbrella is seen.
> Each of the $T=10$ positions can be any of the 3 states: $3^{10}=59049$.
> - A: In (a) the roles are swapped. We measure the signal, so the acoustic features are the observations.
> - B: In (b) the roles are swapped. We see the umbrella; the weather is what we want to infer.
> - D: $10^3=1000$ is $T^N$. The count is $N^T$: 3 choices at each of 10 positions.

### Q10 (page 9)

![[ee525_lecture_06_lecture_6_hidden_markov_models_p009.jpg]]

The table of page 9 has the hidden states Walking, Walking, Running, Running with the observations $v_1,v_2,v_3,v_4$. Walking is $\omega_1$, Running is $\omega_2$. Which expression is the joint probability of both columns, and how many factors does it have?

- A) $\pi_1\,b_{11}\,a_{11}\,b_{12}\,a_{12}\,b_{23}\,a_{22}\,b_{24}$, $8$ factors
- B) $\pi_1\,b_{11}\,a_{11}\,b_{12}\,a_{21}\,b_{23}\,a_{22}\,b_{24}$, $8$ factors
- C) $\pi_1\,b_{11}\,a_{11}\,b_{21}\,a_{12}\,b_{32}\,a_{22}\,b_{42}$, $8$ factors
- D) $b_{11}\,a_{11}\,b_{12}\,a_{12}\,b_{23}\,a_{22}\,b_{24}$, $7$ factors

> [!success]- Answer
> **A.** Start $\pi_1$, emission $b_{11}$. Then $a_{11}$, $b_{12}$; $a_{12}$ (walking to running), $b_{23}$; $a_{22}$, $b_{24}$.
> Factors: 1 start, $T-1=3$ transitions, $T=4$ emissions: $8$.
> - B: $a_{21}$ is running to walking. The step at time 3 goes from walking ($\omega_1$) to running ($\omega_2$), so $a_{12}$.
> - C: The emissions are written $b_{kj}$ (symbol first). In $b_{jk}$ the state comes first: $b_{12}$, $b_{23}$, $b_{24}$.
> - D: The start factor $\pi_1$ is missing. Without it this is the probability given the start in $\omega_1$, not the joint probability.

## 6. The activity HMM: A, B, pi and the joint probability (pages 10-12)

The activity model of pages 10-12: states $W$ (walking), $R$ (running); symbols $L$ (low), $H$ (high) acceleration.

$$A=\begin{bmatrix}0.8&0.2\\0.3&0.7\end{bmatrix},\qquad B=\begin{bmatrix}0.7&0.3\\0.2&0.8\end{bmatrix},\qquad \pi=\begin{bmatrix}0.6&0.4\end{bmatrix}$$

Order of rows and columns: $(W,R)$ for states, $(L,H)$ for symbols.

### Q11 (page 10)

![[ee525_lecture_06_lecture_6_hidden_markov_models_p010.jpg]]

(a) In the figure ($N=3$ states, $M=4$ symbols) state $\omega_2$ has $b_{21}=0.1$, $b_{22}=0.4$, $b_{23}=0.3$. Give $b_{24}$ and the size of $B$. (b) With the activity $A$: a person walks now. What is the probability that the person walks two steps later?

- A) $b_{24}=0.2$, $B$ is $4\times3$, probability $0.70$
- B) $b_{24}=0.2$, $B$ is $3\times4$, probability $0.64$
- C) $b_{24}=0.2$, $B$ is $3\times4$, probability $0.78$
- D) $b_{24}=0.2$, $B$ is $3\times4$, probability $0.70$

> [!success]- Answer
> **D.** (a) Row 2 sums to 1: $b_{24}=1-0.1-0.4-0.3=0.2$. One row per state, one column per symbol: $3\times4$.
> (b) Middle state unknown: $W\to W\to W$: $0.8\times0.8=0.64$. $W\to R\to W$: $0.2\times0.3=0.06$. Sum $0.70$.
> - A: $4\times3$ puts the symbols in the rows. In $b_{jk}$ the row is the state $j$.
> - B: $0.64$ keeps only the path through $W$. The person can also run in between and come back.
> - C: $0.8\times0.8+0.2\times0.7=0.78$ uses $a_{RR}=0.7$ for the second step. Running to walking is $a_{RW}=0.3$.

### Q12 (page 11)

Use the activity $B$ and $\pi$. What is the probability that the first observation is $H$? If it is $H$, what is the probability that the person walks at time 1?

- A) $P(o_1=H)=0.50$, $P(W\mid H)=0.30$
- B) $P(o_1=H)=0.50$, $P(W\mid H)=0.36$
- C) $P(o_1=H)=0.55$, $P(W\mid H)=0.27$
- D) $P(o_1=H)=0.50$, $P(W\mid H)=0.18$

> [!success]- Answer
> **B.** Total probability: $0.6\times0.3+0.4\times0.8=0.18+0.32=0.50$.
> Bayes: $P(W\mid H)=\frac{0.18}{0.50}=0.36$.
> - A: $0.30=b_W(H)=P(H\mid W)$. The question asks the reverse, $P(W\mid H)$.
> - C: Ignores $\pi$ (equal weights): $\frac{0.3+0.8}{2}=0.55$ and $\frac{0.3}{0.3+0.8}=0.27$.
> - D: $0.18$ is the joint $P(W,H)$. It is not divided by $P(H)=0.50$.

### Q13 (page 11, independence)

With the activity model, give $P(o_1=H,\ o_2=H)$ and the product $P(o_1=H)\,P(o_2=H)$. Are the observations independent?

- A) $0.25$ and $0.25$. Independent.
- B) $0.28$ and $0.25$. Not independent.
- C) $0.1792$ and $0.25$. Not independent.
- D) $0.2848$ and $0.25$. Not independent.

> [!success]- Answer
> **B.** $P(o_1=H)=0.50$. $P(q_2=W)=0.6(0.8)+0.4(0.3)=0.60$, so $P(o_2=H)=0.6(0.3)+0.4(0.8)=0.50$. Product $0.25$.
> Joint, add $\pi_{q_1}b_{q_1}(H)\,a_{q_1q_2}\,b_{q_2}(H)$ over 4 state pairs: $0.0432+0.0288+0.0288+0.1792=0.28$.
> $0.28\ne0.25$: not independent. They are independent only when the states are given.
> - A: Assumption 2 is read as "the observations are independent". It holds only given the hidden states.
> - C: $0.1792$ is only the pair $RR$. All 4 state pairs must be added.
> - D: Swapped indices in $A$ ($a_{WR}$ read as $0.3$, $a_{RW}$ as $0.2$): $0.0432+0.0432+0.0192+0.1792=0.2848$.

### Q14 (page 12)

For $O=(L,H,H)$ give the joint probability $P(Q,O\mid\lambda)$ of the path $Q=(R,R,R)$, and $P(Q\mid O,\lambda)$ using $P(O\mid\lambda)=0.116$.

- A) $0.025088$ and $0.0029$
- B) $0.06272$ and $0.5407$
- C) $0.100352$ and $0.8651$
- D) $0.025088$ and $0.2163$

> [!success]- Answer
> **D.** $\pi_R\,b_R(L)\,a_{RR}\,b_R(H)\,a_{RR}\,b_R(H)=0.4\times0.2\times0.7\times0.8\times0.7\times0.8=0.025088$.
> $P(Q\mid O,\lambda)=\frac{0.025088}{0.116}=0.2163$.
> - A: $0.025088\times0.116=0.0029$ multiplies by $P(O\mid\lambda)$ instead of dividing.
> - B: $0.2\times0.7\times0.8\times0.7\times0.8=0.06272$ leaves out $\pi_R=0.4$; then $\frac{0.06272}{0.116}=0.5407$.
> - C: Uses $b_R(H)=0.8$ at time 1. The first symbol is $L$, so $b_R(L)=0.2$. $0.100352/0.116=0.8651$.

## 7. Continuous observations and one HMM per class (pages 13-14)

### Q15 (page 13)

Walking emits $\mathcal N(\mu_W=0.4,\ \sigma_W=0.2)$, running emits $\mathcal N(\mu_R=1.0,\ \sigma_R=0.3)$. Give both emission densities of $o_t=0.7$, and which state explains it better.

- A) Walking $0.6476$, running $0.8066$. Running.
- B) Walking $3.2379$, running $2.6886$. Walking.
- C) Walking $0.2102$, running $0.4892$. Running.
- D) Walking $1.5928$, running $1.1446$. Walking.

> [!success]- Answer
> **A.** $\mathcal N(o;\mu,\sigma^2)=\frac{1}{\sigma\sqrt{2\pi}}\exp\left(-\frac{(o-\mu)^2}{2\sigma^2}\right)$.
> Walking: $\frac{1}{0.2\times2.5066}=1.9947$, $e^{-0.09/0.08}=e^{-1.125}=0.3247$, product $0.6476$.
> Running: $\frac{1}{0.3\times2.5066}=1.3298$, $e^{-0.09/0.18}=e^{-0.5}=0.6065$, product $0.8066$. Running is larger.
> - B: The front factor uses $\sigma^2$ instead of $\sigma$: $\frac{0.3247}{0.04\times2.5066}=3.2379$ and $\frac{0.6065}{0.09\times2.5066}=2.6886$.
> - C: The 2 in the exponent is forgotten: $1.9947\,e^{-2.25}=0.2102$, $1.3298\,e^{-1}=0.4892$.
> - D: The exponent uses $2\sigma$ instead of $2\sigma^2$: $1.9947\,e^{-0.225}=1.5928$, $1.3298\,e^{-0.15}=1.1446$.

### Q16 (page 14)

For $O=(L,H,H)$ two models give $P(O\mid\lambda_1)=0.1160$ (walking) and $P(O\mid\lambda_2)=0.1426$ (running; this model is invented, not from the handout). The priors are $P(C_1)=0.7$, $P(C_2)=0.3$. Which class is chosen, and what is its posterior probability $P(C\mid O)$ (normalised over the two classes)?

- A) Running, $0.551$
- B) Walking, $0.0812$
- C) Walking, $0.655$
- D) Walking, $0.345$

> [!success]- Answer
> **C.** Walking $0.1160\times0.7=0.0812$. Running $0.1426\times0.3=0.0428$. Walking wins.
> Posterior: $\frac{0.0812}{0.0812+0.0428}=\frac{0.0812}{0.1240}=0.655$.
> - A: Ignores the priors: $\frac{0.1426}{0.1426+0.1160}=0.551$. The prior changes the decision.
> - B: $0.0812$ is $P(O\mid\lambda_1)P(C_1)$, not divided by the sum $0.1240$.
> - D: $\frac{0.0428}{0.1240}=0.345$ is the posterior of running, the class that loses.

## 8. Evaluation: the forward and backward algorithms (beyond the slides)

Questions 17 to 24 use the model $\lambda_Q$ with two states and three symbols $v_1,v_2,v_3$, and $O=(v_1,v_3,v_2)$:

$$A=\begin{bmatrix}0.7&0.3\\0.4&0.6\end{bmatrix},\qquad B=\begin{bmatrix}0.5&0.4&0.1\\0.1&0.3&0.6\end{bmatrix},\qquad \pi=\begin{bmatrix}0.5&0.5\end{bmatrix}$$

Forward: $\alpha_1(i)=\pi_ib_i(o_1)$, $\alpha_{t+1}(j)=\left[\sum_i\alpha_t(i)a_{ij}\right]b_j(o_{t+1})$, $P(O\mid\lambda)=\sum_i\alpha_T(i)$.

### Q17 (forward algorithm)

Give $\alpha_2(1)$, $\alpha_2(2)$ and $P(O\mid\lambda_Q)$.

- A) $\alpha_2=(0.0190,\ 0.0780)$, $P=0.031000$
- B) $\alpha_2=(0.0390,\ 0.1260)$, $P=0.057270$
- C) $\alpha_2=(0.0175,\ 0.0450)$, $P=0.015300$
- D) $\alpha_2=(0.0195,\ 0.0630)$, $P=0.028635$

> [!success]- Answer
> **D.** $\alpha_1=(0.5\times0.5,\ 0.5\times0.1)=(0.25,\ 0.05)$.
> $\alpha_2(1)=(0.25\times0.7+0.05\times0.4)\times0.1=0.0195$. $\alpha_2(2)=(0.25\times0.3+0.05\times0.6)\times0.6=0.0630$.
> $\alpha_3(1)=(0.0195\times0.7+0.0630\times0.4)\times0.4=0.015540$. $\alpha_3(2)=(0.0195\times0.3+0.0630\times0.6)\times0.3=0.013095$.
> $P=0.015540+0.013095=0.028635$.
> - A: Swapped indices ($a_{ji}$ in place of $a_{ij}$): $\alpha_2(1)=(0.25\times0.7+0.05\times0.3)\times0.1=0.0190$, and so on.
> - B: $\pi$ is left out, $\alpha_1=(0.5,\ 0.1)$. Every value doubles.
> - C: Takes the maximum instead of the sum in the recursion. These are the Viterbi values $\delta_2$; their sum at the end, $0.0072+0.0081=0.0153$, is not $P(O\mid\lambda_Q)$.

### Q18 (complexity)

A model has $N=3$ states and $T=20$ observations. Each path in the direct sum is a product of $2T$ factors. About how many paths and multiplications does the direct sum need, and about how many multiplications does the forward algorithm need?

- A) $3.5\times10^9$ paths, $1.4\times10^{11}$ multiplications; forward about $180$
- B) $8000$ paths, $3.1\times10^{5}$ multiplications; forward about $180$
- C) $3.5\times10^9$ paths, $1.4\times10^{11}$ multiplications; forward about $60$
- D) $3.5\times10^9$ paths, $7.0\times10^{10}$ multiplications; forward about $180$

> [!success]- Answer
> **A.** Paths $N^T=3^{20}=3\,486\,784\,401\approx3.5\times10^9$. Each has $40$ factors, so $39$ multiplications: $39\times3.49\times10^9\approx1.4\times10^{11}$.
> Forward: about $N^2T=9\times20=180$ (exactly $3+19\times12=231$).
> - B: $T^N=20^3=8000$ swaps base and exponent; $39\times8000=3.1\times10^5$.
> - C: $NT=60$ forgets that each new $\alpha$ is a sum over the $N$ previous states.
> - D: $20\times3.49\times10^9=7.0\times10^{10}$ counts one multiplication per time step. Each step has a transition and an emission factor.

### Q19 (backward algorithm)

Backward: $\beta_3(i)=1$, $\beta_t(i)=\sum_ja_{ij}\,b_j(o_{t+1})\,\beta_{t+1}(j)$. Give $\beta_2$, $\beta_1$ and the check value $\sum_i\pi_ib_i(o_1)\beta_1(i)$.

- A) $\beta_2=(0.25,\ 0.40)$, $\beta_1=(0.0995,\ 0.0740)$, check $0.028575$
- B) $\beta_2=(0.40,\ 0.30)$, $\beta_1=(0.1000,\ 0.1200)$, check $0.031000$
- C) $\beta_2=(0.37,\ 0.34)$, $\beta_1=(0.0871,\ 0.1372)$, check $0.028635$
- D) $\beta_2=(0.37,\ 0.34)$, $\beta_1=(0.2500,\ 0.4000)$, check $0.082500$

> [!success]- Answer
> **C.** $t=2$ (next symbol $v_2$: $0.4$, $0.3$): $\beta_2(1)=0.7(0.4)+0.3(0.3)=0.37$. $\beta_2(2)=0.4(0.4)+0.6(0.3)=0.34$.
> $t=1$ (next symbol $v_3$: $0.1$, $0.6$): $\beta_1(1)=0.7(0.1)(0.37)+0.3(0.6)(0.34)=0.0871$. $\beta_1(2)=0.4(0.1)(0.37)+0.6(0.6)(0.34)=0.1372$.
> Check: $0.25(0.0871)+0.05(0.1372)=0.028635$, the forward value.
> - A: Uses the symbol at time $t$ instead of $t+1$ ($v_3$ for $\beta_2$, $v_1$ for $\beta_1$). The check misses $0.028635$.
> - B: Swapped indices ($a_{ji}$): $\beta_2(1)=0.7(0.4)+0.4(0.3)=0.40$, and so on.
> - D: $\beta_1$ forgets the factor $\beta_2(j)$: $0.7(0.1)+0.3(0.6)=0.25$ and $0.4(0.1)+0.6(0.6)=0.40$.

### Q20 (underflow)

A sequence has $T=500$ observations, and every step multiplies the forward values by about $0.2$. The smallest normal double is about $10^{-308}$. Give $P(O\mid\lambda)$, whether it can be stored as a double, and $\ln P(O\mid\lambda)$.

- A) $P\approx10^{-349.5}$, it can be stored, $\ln P\approx-804.7$
- B) $P\approx10^{-349.5}$, it underflows to 0, $\ln P\approx-804.7$
- C) $P\approx10^{-349.5}$, it underflows to 0, $\ln P\approx-349.5$
- D) $P\approx10^{-100}$, it can be stored, $\ln P\approx-230.3$

> [!success]- Answer
> **B.** $\log_{10}P\approx500\times\log_{10}0.2=500\times(-0.699)=-349.5$. This is below $-308$, so the stored value is 0.
> $\ln P\approx500\times\ln0.2=500\times(-1.6094)=-804.7$, which is stored without problem.
> - A: $10^{-349.5}$ is smaller than $10^{-308}$, not larger. It cannot be stored.
> - C: $-349.5$ is $\log_{10}P$. The natural log is $\ln10=2.3026$ times larger in size: $-804.7$.
> - D: $0.2^{500}$ is read as $10^{-0.2\times500}=10^{-100}$. The exponent is $500\log_{10}0.2$.

## 9. Decoding: the Viterbi algorithm (beyond the slides)

### Q21 (Viterbi)

Viterbi: $\delta_1(i)=\pi_ib_i(o_1)$, $\delta_{t+1}(j)=\left[\max_i\delta_t(i)a_{ij}\right]b_j(o_{t+1})$, then backtracking. For $\lambda_Q$ and $O=(v_1,v_3,v_2)$ give $\delta_3(1)$, $\delta_3(2)$, the best path and $P^*$.

- A) $\delta_3=(0.0072,\ 0.0081)$, path $(\omega_1,\omega_2,\omega_2)$, $P^*=0.0081$
- B) $\delta_3=(0.0155,\ 0.0131)$, path $(\omega_1,\omega_2,\omega_1)$, $P^*=0.0155$
- C) $\delta_3=(0.0072,\ 0.0108)$, path $(\omega_1,\omega_2,\omega_2)$, $P^*=0.0108$
- D) $\delta_3=(0.0180,\ 0.0270)$, path $(\omega_1,\omega_2,\omega_2)$, $P^*=0.0270$

> [!success]- Answer
> **A.** $\delta_1=(0.25,\ 0.05)$.
> $t=2$: to 1: $\max(0.175,\ 0.020)\times0.1=0.0175$, $\psi_2(1)=1$. To 2: $\max(0.075,\ 0.030)\times0.6=0.045$, $\psi_2(2)=1$.
> $t=3$: to 1: $\max(0.01225,\ 0.018)\times0.4=0.0072$, $\psi_3(1)=2$. To 2: $\max(0.00525,\ 0.027)\times0.3=0.0081$, $\psi_3(2)=2$.
> $q_3^*=2$, $q_2^*=\psi_3(2)=2$, $q_1^*=\psi_2(2)=1$. Path $(\omega_1,\omega_2,\omega_2)$, $P^*=0.0081$.
> - B: Uses sums (the forward $\alpha_3$) and then the larger $\alpha_t$ at each time. That is not the best whole path.
> - C: Swapped indices ($a_{ji}$): $\delta_2(2)=\max(0.25\times0.4,\ 0.05\times0.6)\times0.6=0.06$, then $\delta_3(2)=0.06\times0.6\times0.3=0.0108$.
> - D: Forgets the last emission: $0.018$ and $0.027$ are not yet multiplied by $b_j(v_2)=0.4$, $0.3$.

### Q22 (Viterbi against single states)

For $\lambda_Q$ and $O=(v_1,v_3,v_2)$ the posteriors of state $\omega_1$ at times 1, 2, 3 are $\gamma_t(1)=0.7604$, $0.2520$, $0.5427$. The Viterbi path is $(\omega_1,\omega_2,\omega_2)$. Which sequence of single most probable states do you get, and is it the Viterbi path?

- A) $(\omega_1,\omega_2,\omega_1)$, and it is the Viterbi path
- B) $(\omega_2,\omega_1,\omega_2)$, and it is not the Viterbi path
- C) $(\omega_1,\omega_2,\omega_2)$, and it is the Viterbi path
- D) $(\omega_1,\omega_2,\omega_1)$, and it is not the Viterbi path

> [!success]- Answer
> **D.** $\gamma_1(1)=0.7604>0.5$: $\omega_1$. $\gamma_2(1)=0.2520<0.5$: $\omega_2$. $\gamma_3(1)=0.5427>0.5$: $\omega_1$.
> The Viterbi path ends in $\omega_2$, so they differ at time 3. The path $(\omega_1,\omega_2,\omega_1)$ has joint probability $0.0072<0.0081$.
> - A: The sequence is right, but the best states one by one need not form the best whole path.
> - B: Reads $\gamma_t(1)$ as the probability of $\omega_2$. The values belong to $\omega_1$.
> - C: Takes at each time the state with the larger Viterbi value $\delta_t$ ($\delta_3=(0.0072,\ 0.0081)$ gives $\omega_2$), so it copies the Viterbi path. The single best state uses the posterior: $\gamma_3(1)=0.5427>0.5$ gives $\omega_1$.

## 10. Learning: the Baum-Welch algorithm (beyond the slides)

### Q23 (gamma and xi)

For $\lambda_Q$ and $O=(v_1,v_3,v_2)$: $\alpha_1=(0.25,0.05)$, $\alpha_2=(0.0195,0.0630)$, $\alpha_3=(0.015540,0.013095)$, $\beta_1=(0.0871,0.1372)$, $\beta_2=(0.37,0.34)$, $\beta_3=(1,1)$, $P(O\mid\lambda_Q)=0.028635$. With $\gamma_t(i)=\frac{\alpha_t(i)\beta_t(i)}{P}$ and $\xi_t(i,j)=\frac{\alpha_t(i)a_{ij}b_j(o_{t+1})\beta_{t+1}(j)}{P}$, give $\gamma_1(1)$, $\gamma_2(1)$, $\gamma_3(1)$ and $\xi_1(1,2)$.

- A) $\gamma(1)=(0.7604,\ 0.2520,\ 0.5427)$, $\xi_1(1,2)=0.5815$
- B) $\gamma(1)=(0.7604,\ 0.2520,\ 0.5427)$, $\xi_1(1,2)=0.5343$
- C) $\gamma(1)=(0.8333,\ 0.2364,\ 0.5427)$, $\xi_1(1,2)=0.5343$
- D) $\gamma(1)=(0.7604,\ 0.2520,\ 0.5427)$, $\xi_1(1,2)=0.0890$

> [!success]- Answer
> **B.** $\gamma_1(1)=\frac{0.25\times0.0871}{0.028635}=0.7604$. $\gamma_2(1)=\frac{0.0195\times0.37}{0.028635}=0.2520$. $\gamma_3(1)=\frac{0.015540}{0.028635}=0.5427$.
> $\xi_1(1,2)=\frac{0.25\times0.3\times0.6\times0.34}{0.028635}=\frac{0.0153}{0.028635}=0.5343$.
> - A: Uses $\beta_2(1)=0.37$ instead of $\beta_2(2)=0.34$. After the step to state 2 the rest is seen from state 2.
> - C: $\gamma_t(1)=\frac{\alpha_t(1)}{\alpha_t(1)+\alpha_t(2)}$ ignores $\beta$, so it uses only the past: $\frac{0.25}{0.30}=0.8333$, $\frac{0.0195}{0.0825}=0.2364$.
> - D: Uses $b_1(v_3)=0.1$, the emission of the state that is left. The new state 2 emits $o_2$: $b_2(v_3)=0.6$.

### Q24 (re-estimation)

Continue Q23. Also $\gamma_1(2)=0.2396$ and $\xi_2(1,2)=0.0613$. Use $\hat\pi_i=\gamma_1(i)$, $\hat a_{12}=\frac{\sum_{t=1}^{T-1}\xi_t(1,2)}{\sum_{t=1}^{T-1}\gamma_t(1)}$, $\hat b_{11}=\frac{\sum_{t:\,o_t=v_1}\gamma_t(1)}{\sum_{t=1}^{T}\gamma_t(1)}$. Give $\hat\pi$, $\hat a_{12}$ and $\hat b_{11}$.

- A) $\hat\pi=(0.7604,\ 0.2396)$, $\hat a_{12}=0.3830$, $\hat b_{11}=0.4890$
- B) $\hat\pi=(0.7604,\ 0.2396)$, $\hat a_{12}=0.5883$, $\hat b_{11}=0.7511$
- C) $\hat\pi=(0.7604,\ 0.2396)$, $\hat a_{12}=0.5883$, $\hat b_{11}=0.4890$
- D) $\hat\pi=(0.8333,\ 0.1667)$, $\hat a_{12}=0.5883$, $\hat b_{11}=0.4890$

> [!success]- Answer
> **C.** $\hat\pi=\gamma_1=(0.7604,\ 0.2396)$.
> $\hat a_{12}=\frac{0.5343+0.0613}{0.7604+0.2520}=\frac{0.5956}{1.0124}=0.5883$.
> $v_1$ appears only at $t=1$: $\hat b_{11}=\frac{0.7604}{0.7604+0.2520+0.5427}=\frac{0.7604}{1.5551}=0.4890$.
> - A: $\hat a_{12}$ divides by all three $\gamma_t(1)$: $\frac{0.5956}{1.5551}=0.3830$. A transition needs a next time, so only $t=1,\dots,T-1$.
> - B: $\hat b_{11}$ divides by only $t=1,2$: $\frac{0.7604}{1.0124}=0.7511$. Emissions happen at all times $1,\dots,T$.
> - D: $\hat\pi$ is the normalised $\alpha_1=\frac{(0.25,\ 0.05)}{0.30}$. It ignores the later observations; the posterior $\gamma_1$ uses them through $\beta_1$.
