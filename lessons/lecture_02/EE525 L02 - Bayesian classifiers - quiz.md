---
course: EE 525 Pattern Recognition
lesson: EE525 L02 - Bayesian classifiers
type: quiz
questions: 36
---

# EE525 L02 - Bayesian classifiers - quiz

Lesson note: [[EE525 L02 - Bayesian classifiers]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Why probability (slide 2)

### Q1 (slide 2)

A classmate says: "If the sensors had no noise at all, the features of one class would always be the same, so a classifier would never be wrong." Which answer is correct?

- A) The claim is right. Sensor noise is the only reason why the features of one class vary.
- B) The claim is wrong. A classifier without sensor noise needs many more training patterns.
- C) The claim is wrong. The patterns of one class vary by themselves, so the classes can still overlap.
- D) The claim is wrong. Without noise the features of all classes become equal, so no decision is possible.

> [!success]- Answer
> **C.** The slide names two sources of randomness: the statistical variation of the patterns and the sensor noise. Without noise the first source stays. Two fish of the same species still have different lightness, so the classes can overlap.
> - A: This is the classmate's mistake. Noise is only one of the two sources.
> - B: The number of training patterns is not the point. The features stay random even with perfect sensors.
> - D: Removing noise does not make the classes equal. It only removes one source of variation.

## 2. Fruit basket: total probability and Bayes' theorem (slides 3-7)

### Q2 (slide 3)

Basket: apples are $\omega_1$, oranges are $\omega_2$. The feature is $x=1$ for a red fruit and $x=0$ for a fruit that is not red. The slide gives $P(x=1\mid\omega_1)=0.80$ and $P(x=1\mid\omega_2)=0.20$. Their sum is 1. Which pair must add to 1 in every problem of this kind?

- A) $P(x=1\mid\omega_1)$ and $P(x=1\mid\omega_2)$, because both describe the same event "red".
- B) $P(x=1\mid\omega_1)$ and $P(x=0\mid\omega_1)$, because the class is fixed and $x$ takes all its values.
- C) $P(x=1\mid\omega_1)$ and $P(x=0\mid\omega_2)$, because the class and the value both change.
- D) $P(x=1\mid\omega_1)$ and $P(\omega_1\mid x=1)$, because Bayes' theorem links these two numbers.

> [!success]- Answer
> **B.** For a fixed class the feature is red or not red, so $P(x=1\mid\omega_i)+P(x=0\mid\omega_i)=1$. That is why $P(x=0\mid\omega_1)=1-0.80=0.20$. The priors also add to 1.
> - A: The two numbers are conditioned on different classes. No rule links them. $0.90$ and $0.30$ would also be allowed. Here the sum is 1 by chance.
> - C: Here the sum is $0.80+0.80=1.60$. Nothing forces a sum of 1.
> - D: A likelihood and a posterior answer different questions. Here they are $0.80$ and $0.857$.

### Q3 (slide 4)

Basket: $P(\omega_1)=0.60$ (apples), $P(\omega_2)=0.40$ (oranges). A fruit is not red ($x=0$) with $P(x=0\mid\omega_1)=0.20$ and $P(x=0\mid\omega_2)=0.80$. What is $P(x=0)$, the probability that a random fruit is not red?

- A) $0.12$
- B) $0.50$
- C) $0.56$
- D) $0.44$

> [!success]- Answer
> **D.** Total probability: $P(x=0)=P(x=0\mid\omega_1)P(\omega_1)+P(x=0\mid\omega_2)P(\omega_2)=(0.20)(0.60)+(0.80)(0.40)=0.12+0.32=0.44$.
> Check: $P(x=1)=0.56$ and $0.44+0.56=1$.
> - A: Only the apple term $(0.20)(0.60)$. The orange term is missing.
> - B: The average of the two likelihoods, $(0.20+0.80)/2$. The priors were forgotten.
> - C: This is $P(x=1)$, or the result with the two priors swapped.

### Q4 (slide 5)

Same basket: $P(\omega_1)=0.60$, $P(\omega_2)=0.40$, $P(x=0\mid\omega_1)=0.20$, $P(x=0\mid\omega_2)=0.80$. We pick a fruit and it is not red ($x=0$). What are the posteriors $P(\omega_1\mid x=0)$ and $P(\omega_2\mid x=0)$?

- A) $0.2727$ and $0.7273$
- B) $0.2000$ and $0.8000$
- C) $0.7273$ and $0.2727$
- D) $0.2143$ and $0.5714$

> [!success]- Answer
> **A.** Joint values: $(0.20)(0.60)=0.12$ and $(0.80)(0.40)=0.32$. Evidence: $P(x=0)=0.12+0.32=0.44$.
> $P(\omega_1\mid x=0)=0.12/0.44=0.2727$ and $P(\omega_2\mid x=0)=0.32/0.44=0.7273$. Sum: 1.
> - B: These are the likelihoods $P(x=0\mid\omega_i)$. The priors were not used.
> - C: The two classes are swapped. "Not red" speaks for the orange.
> - D: The joint values were divided by $P(x=1)=0.56$ instead of $P(x=0)=0.44$. The sum is not 1.

### Q5 (slide 6)

Same basket: $P(\omega_1)=0.60$, $P(\omega_2)=0.40$, $P(x=1\mid\omega_1)=0.80$, $P(x=1\mid\omega_2)=0.20$. The classifier says "apple" for every red fruit and "orange" for every fruit that is not red. How often is it wrong in total?

- A) $0.08$
- B) $0.20$
- C) $0.40$
- D) $0.4156$

> [!success]- Answer
> **B.** Error 1: a red orange, $P(x=1,\omega_2)=(0.20)(0.40)=0.08$. Error 2: an apple that is not red, $P(x=0,\omega_1)=(0.20)(0.60)=0.12$.
> $P(\text{error})=0.08+0.12=0.20$.
> - A: Only the red oranges. The apples that are not red are also errors.
> - C: This is the error without the colour (always say apple, wrong for every orange). The feature cuts it in half.
> - D: The two posterior errors $0.1429$ and $0.2727$ were added without the weights $P(x=1)$ and $P(x=0)$.

### Q6 (slide 7)

The slide's basket has 60% apples and gives $P(\omega_1\mid x=1)=0.8571$ for a red fruit. Now the basket has 50% apples and 50% oranges. The colours of each kind of fruit stay the same: $P(x=1\mid\omega_1)=0.80$, $P(x=1\mid\omega_2)=0.20$. What changes, and what is the new $P(\omega_1\mid x=1)$?

- A) Only the prior changes. The posterior stays $0.8571$.
- B) The prior and the class-conditional probability change. The posterior becomes $0.50$.
- C) The prior, the evidence and the posterior change. The posterior becomes $0.50$.
- D) The prior, the evidence and the posterior change. The posterior becomes $0.80$.

> [!success]- Answer
> **D.** Prior: $0.50$ each. Evidence: $P(x=1)=(0.80)(0.50)+(0.20)(0.50)=0.50$. Posterior: $0.40/0.50=0.80$.
> The class-conditional probability describes the apples only, so it stays $0.80$.
> - A: The posterior contains the prior, so it changes with it.
> - B: The class-conditional probability does not depend on the mix of the basket.
> - C: $0.50$ is the new evidence $P(x=1)$, not the posterior.

## 3. The Bayes classifier for M classes (slides 8-9)

### Q7 (slide 8)

There are $M=3$ classes. For one pattern the posteriors are $P(\omega_1\mid\mathbf{x})=0.40$, $P(\omega_2\mid\mathbf{x})=0.35$, $P(\omega_3\mid\mathbf{x})=0.25$. What does the Bayes classifier do, and how probable is a wrong decision?

- A) It chooses $\omega_1$. The decision is wrong with probability $0.60$.
- B) It chooses $\omega_1$. The decision is wrong with probability $0.40$.
- C) It chooses $\omega_1$. The decision is wrong with probability $0.35$.
- D) It chooses no class, because no posterior is above $0.50$.

> [!success]- Answer
> **A.** The largest posterior is $0.40$, so $\omega_1$. The decision is wrong when the truth is $\omega_2$ or $\omega_3$: $0.35+0.25=0.60=1-0.40$.
> The winner need not be above $0.5$. It is still the best decision for this $\mathbf{x}$.
> - B: $0.40$ is the probability that the decision is right.
> - C: Only $\omega_2$ was counted. $\omega_3$ also makes the decision wrong.
> - D: The rule asks only for the largest posterior. With three classes it can be below $0.5$.

### Q8 (slide 9)

Three classes have priors $0.6$, $0.3$, $0.1$. At the measured $\mathbf{x}$ the densities are $p(\mathbf{x}\mid\omega_1)=0.3$, $p(\mathbf{x}\mid\omega_2)=0.5$, $p(\mathbf{x}\mid\omega_3)=0.6$. Which class wins, and what is its posterior?

- A) $\omega_3$, with posterior $0.4286$
- B) $\omega_1$, with posterior $0.1800$
- C) $\omega_1$, with posterior $0.4615$
- D) $\omega_1$, with posterior $0.6000$

> [!success]- Answer
> **C.** Joint values: $(0.3)(0.6)=0.18$, $(0.5)(0.3)=0.15$, $(0.6)(0.1)=0.06$. Evidence: $0.39$.
> Posteriors: $0.18/0.39=0.4615$, $0.15/0.39=0.3846$, $0.06/0.39=0.1538$. Decision: $\omega_1$.
> - A: Only the likelihoods were used: $0.6/(0.3+0.5+0.6)=0.4286$. $\omega_3$ has the largest likelihood but it is rare.
> - B: $0.18$ is the joint value. It was not divided by the evidence $0.39$.
> - D: $0.60$ is the prior. The measurement was not used.

## 4. Discrete and continuous features (slides 10-11)

### Q9 (slide 10)

Two binary features: $x_1=1$ if the fruit is red, $x_2=1$ if it is round. The slide gives this table.

| $\mathbf{x}=[x_1,x_2]$ | apple $P(\mathbf{x}\mid\omega_1)$ | orange $P(\mathbf{x}\mid\omega_2)$ |
|---|---|---|
| $[0,0]$ | 0.05 | 0.10 |
| $[0,1]$ | 0.10 | 0.70 |
| $[1,0]$ | 0.75 | 0.05 |
| $[1,1]$ | 0.10 | 0.15 |

What fraction of the apples is red (round or not), and what fraction of the oranges is round (red or not)?

- A) $0.85$ and $0.85$
- B) $0.75$ and $0.70$
- C) $0.10$ and $0.15$
- D) $0.20$ and $0.20$

> [!success]- Answer
> **A.** Red means $x_1=1$: add the rows $[1,0]$ and $[1,1]$ of the apple column, $0.75+0.10=0.85$.
> Round means $x_2=1$: add the rows $[0,1]$ and $[1,1]$ of the orange column, $0.70+0.15=0.85$.
> - B: Only one row each. $0.75$ is "red and not round", $0.70$ is "round and not red".
> - C: Only the row $[1,1]$, that is "red and round" together.
> - D: The features are swapped: round apples ($0.10+0.10$) and red oranges ($0.05+0.15$).

### Q10 (slide 11)

The weight $x$ of a fruit is continuous. Assume a constant density $p(x\mid\omega_1)=0.1$ per gram between 150 g and 160 g, and 0 outside. What are $P(152\le x\le153.5\mid\omega_1)$ and $P(x=152.3\mid\omega_1)$?

- A) $0.15$ and $0.1$
- B) $0.1$ and $0.1$
- C) $1.5$ and $0$
- D) $0.15$ and $0$

> [!success]- Answer
> **D.** Interval: $\int_{152}^{153.5}0.1\,dx=0.1\cdot1.5=0.15$. One exact value is an interval of width 0: $0.1\cdot0=0$.
> - A: $0.1$ is the density at $152.3$, not a probability. A density becomes a probability only after integration.
> - B: The density value was used as a probability two times.
> - C: $1.5$ is the width of the interval. It was not multiplied by the density $0.1$. A probability cannot be above 1.

## 5. The two-class Bayes rule (slides 12-13)

### Q11 (slide 12)

A training set has $N=200$ fish: $N_1=150$ sea bass ($\omega_1$) and $N_2=50$ salmon ($\omega_2$). The slide estimates the priors with $P(\omega_i)\approx N_i/N$. Which statement is correct?

- A) $P(\omega_1)\approx0.75$, $P(\omega_2)\approx0.25$. The estimate is good for every training set of this size.
- B) $P(\omega_1)\approx0.75$, $P(\omega_2)\approx0.25$. The estimate is good only if the set is a random sample of real use.
- C) $P(\omega_1)\approx0.25$, $P(\omega_2)\approx0.75$. The estimate is good only if the set is a random sample of real use.
- D) $P(\omega_1)\approx0.75$, $P(\omega_2)\approx0.25$. The estimate is good only if both classes have the same $N_i$.

> [!success]- Answer
> **B.** $150/200=0.75$ and $50/200=0.25$. The numbers $N_i/N$ show how common the classes are only when the fish were collected the way they arrive in real use.
> - A: The size is not enough. If somebody chose the fish on purpose, $N_i/N$ says nothing about the real frequencies.
> - C: The classes are swapped. Sea bass is the larger group.
> - D: This is the wrong direction. A set with equal $N_i$ chosen on purpose is a case where the estimate fails.

### Q12 (slide 13)

Rule (2.4): decide $\omega_1$ if $p(\mathbf{x}\mid\omega_1)P(\omega_1)>p(\mathbf{x}\mid\omega_2)P(\omega_2)$. Rule (2.5): decide $\omega_1$ if $p(\mathbf{x}\mid\omega_1)>p(\mathbf{x}\mid\omega_2)$. At a point $\mathbf{x}$: $p(\mathbf{x}\mid\omega_1)=0.30$, $p(\mathbf{x}\mid\omega_2)=0.50$, $P(\omega_1)=0.7$, $P(\omega_2)=0.3$. What do the two rules give, and which one is valid here?

- A) (2.4) gives $\omega_2$ and (2.5) gives $\omega_1$. Rule (2.4) is valid here: decide $\omega_2$.
- B) (2.4) gives $\omega_1$ and (2.5) gives $\omega_1$. Both rules are valid here: decide $\omega_1$.
- C) (2.4) gives $\omega_1$ and (2.5) gives $\omega_2$. Rule (2.4) is valid here: decide $\omega_1$.
- D) (2.4) gives $\omega_1$ and (2.5) gives $\omega_2$. Rule (2.5) is valid here: decide $\omega_2$.

> [!success]- Answer
> **C.** (2.4): $(0.30)(0.7)=0.21>(0.50)(0.3)=0.15$, so $\omega_1$. (2.5): $0.30<0.50$, so $\omega_2$.
> (2.5) is valid only for equal priors. Here the priors differ, so (2.4) decides. Posteriors: $0.21/0.36=0.5833$ and $0.4167$.
> - A: The two results are swapped.
> - B: (2.5) compares $0.30$ with $0.50$ and gives $\omega_2$.
> - D: (2.5) ignores the priors. That is allowed only when $P(\omega_1)=P(\omega_2)$.

## 6. Decision regions and the probability of error (slides 14-15)

### Q13 (slide 14)

![[ee525_lecture_02_lecture_2_bayesian_classifiers_p014.jpg]]

The figure shows two equiprobable classes and the threshold $x_0$ of the Bayes classifier. Left of $x_0$ there is a hatched area under the blue curve $p(x\mid\omega_2)$. Which statement is correct?

- A) The curves cross at $x_0$. The hatched area on the left shows patterns of $\omega_1$ that are classified as $\omega_2$.
- B) The $\omega_1$ curve has its peak at $x_0$. The hatched area on the left shows patterns of $\omega_2$ classified as $\omega_1$.
- C) The curves cross at $x_0$. The hatched area on the left shows patterns of $\omega_2$ that are classified correctly.
- D) The curves cross at $x_0$. The hatched area on the left shows patterns of $\omega_2$ that are classified as $\omega_1$.

> [!success]- Answer
> **D.** With equal priors the rule compares the likelihoods, so the decision changes where $p(x_0\mid\omega_1)=p(x_0\mid\omega_2)$. The left hatched area lies under $p(x\mid\omega_2)$ but inside $R_1$: these $\omega_2$ patterns are called $\omega_1$.
> - A: This is the hatched area on the right of $x_0$ (under the black curve, inside $R_2$).
> - B: The peak of $\omega_1$ is left of $x_0$. The threshold is at the crossing point.
> - C: Left of $x_0$ the classifier says $\omega_1$. So an $\omega_2$ pattern there is an error.

### Q14 (slide 15)

$\omega_1$: $N(10,2^2)$, $\omega_2$: $N(15,2^2)$, equal priors. The Bayes threshold is $x_0=12.5$. Formula (2.6): $P_e=\frac12\int_{-\infty}^{x_0}p(x\mid\omega_2)\,dx+\frac12\int_{x_0}^{+\infty}p(x\mid\omega_1)\,dx$. Use $\Phi(-1.25)=0.1056$, $\Phi(-1)=0.1587$, $1-\Phi(1.5)=0.0668$. What is $P_e$ at $x_0$, and what is the error for the threshold $t=13$?

- A) $0.1056$ and $0.1127$
- B) $0.2113$ and $0.2255$
- C) $0.2660$ and $0.2676$
- D) $0.1056$ and $0.0334$

> [!success]- Answer
> **A.** At $x_0$: $\Phi\left(\frac{12.5-15}{2}\right)=\Phi(-1.25)=0.1056$ and $1-\Phi\left(\frac{12.5-10}{2}\right)=0.1056$. $P_e=\tfrac12(0.1056)+\tfrac12(0.1056)=0.1056$.
> At $t=13$: $\Phi\left(\frac{13-15}{2}\right)=\Phi(-1)=0.1587$ and $1-\Phi\left(\frac{13-10}{2}\right)=1-\Phi(1.5)=0.0668$. Error $=\tfrac12(0.1587+0.0668)=0.1127$. It is larger than at $x_0$.
> - B: The two tails were added without the factors $\tfrac12$.
> - C: The distances were divided by the variance 4 instead of $\sigma=2$: $\Phi(-0.625)=0.2660$.
> - D: At $t=13$ only the $\omega_1$ term $\tfrac12(0.0668)$ was kept. An error below the Bayes error is not possible.

## 7. Sea bass and salmon again, minimum error (slides 16-18)

### Q15 (slide 16)

No measurement is available. The priors are $P(\omega_1)=2/3$ (sea bass) and $P(\omega_2)=1/3$ (salmon). The slide's rule is: decide $\omega_1$ if $P(\omega_1)>P(\omega_2)$, otherwise decide $\omega_2$. What does it do, and how often is it wrong?

- A) It says $\omega_1$ for every fish. It is wrong with probability $2/3$.
- B) It says $\omega_1$ for two of three fish. It is wrong with probability $4/9$.
- C) It says $\omega_1$ for every fish. It is wrong with probability $1/3$.
- D) It says $\omega_1$ for every fish. It is wrong with probability $1/2$.

> [!success]- Answer
> **C.** $2/3>1/3$, so the rule says $\omega_1$ every time. It is wrong for every salmon: $P(\text{error})=P(\omega_2)=1/3$, the smaller prior. The weak point: it never finds a salmon.
> - A: $2/3$ is the probability that the rule is right.
> - B: The rule does not guess at random. Random guessing with the priors would give $\tfrac23\cdot\tfrac13+\tfrac13\cdot\tfrac23=4/9$, which is worse.
> - D: $1/2$ belongs to equal priors. Here the priors are $2/3$ and $1/3$.

### Q16 (slide 17)

![[ee525_lecture_02_lecture_2_bayesian_classifiers_p017.jpg]]

Read from the left plot at $x=11.5$: $p(x\mid\omega_1)\approx0.155$ and $p(x\mid\omega_2)\approx0.36$. The priors are $P(\omega_1)=2/3$, $P(\omega_2)=1/3$. What is $P(\omega_2\mid x)$, and what is the decision?

- A) About $0.699$. Decide $\omega_2$ (salmon).
- B) About $0.537$. Decide $\omega_2$ (salmon).
- C) About $0.463$. Decide $\omega_1$ (sea bass).
- D) About $0.823$. Decide $\omega_2$ (salmon).

> [!success]- Answer
> **B.** Joint values: $0.155\cdot\tfrac23=0.1033$ and $0.36\cdot\tfrac13=0.12$. Evidence: $0.2233$.
> $P(\omega_2\mid x)=0.12/0.2233=0.537$, $P(\omega_1\mid x)=0.463$. Salmon wins narrowly. The right plot agrees: near $x=11.5$ the red curve is just above $0.5$.
> - A: The priors were forgotten: $0.36/(0.155+0.36)=0.699$.
> - C: $0.463$ is the posterior of $\omega_1$, not of $\omega_2$.
> - D: The priors were swapped: $0.36\cdot\tfrac23=0.24$, $0.155\cdot\tfrac13=0.0517$, $0.24/0.2917=0.823$.

### Q17 (slide 18)

At some $x$ the posteriors are $P(\omega_1\mid x)=0.3$ and $P(\omega_2\mid x)=0.7$. What is $P(\text{error}\mid x)$ for each decision, and what follows for the average error $P(\text{error})=\int P(\text{error}\mid x)\,p(x)\,dx$?

- A) Decide $\omega_2$: $0.3$. Decide $\omega_1$: $0.7$. Taking the smaller one at every $x$ makes the average error smallest.
- B) Decide $\omega_2$: $0.7$. Decide $\omega_1$: $0.3$. Taking the smaller one at every $x$ makes the average error smallest.
- C) Decide $\omega_2$: $0.3$. Decide $\omega_1$: $0.3$. The decision at one $x$ has no effect on the average error.
- D) Decide $\omega_2$: $0.3$. Decide $\omega_1$: $0.7$. Taking the smaller one at every $x$ need not make the average smallest.

> [!success]- Answer
> **A.** If we decide $\omega_2$ we are wrong when the truth is $\omega_1$: $0.3$. If we decide $\omega_1$ we are wrong when the truth is $\omega_2$: $0.7$. The Bayes rule takes $\omega_2$.
> The weight $p(x)$ is never negative and does not depend on the decision. So the smallest $P(\text{error}\mid x)$ at every $x$ gives the smallest integral.
> - B: The error is the posterior of the class we did NOT choose. Here the two values are swapped.
> - C: $\min[0.3,0.7]=0.3$ is the error of the best decision only. The other decision has error $0.7$.
> - D: The integrand is smallest at every $x$ and the weight $p(x)\ge0$ is fixed. No rule can give a smaller integral.

## 8. Actions, loss and conditional risk (slides 19-22)

### Q18 (slide 19)

The slide lists four generalisations: more than one feature, more than two states of nature, actions other than deciding the state of nature, and a loss function more general than the probability of error. Three cases: (a) Iris flowers with 4 measurements and 3 species. (b) A spam filter where deleting a real mail is much worse than letting spam through. (c) A system that may answer "not sure, ask a human". Which assignment is correct?

- A) (a) more features and more classes. (b) other actions. (c) a general loss function.
- B) (a) more features only. (b) a general loss function. (c) other actions.
- C) (a) more features and more classes. (b) a general loss function. (c) more classes.
- D) (a) more features and more classes. (b) a general loss function. (c) other actions.

> [!success]- Answer
> **D.** (a) 4 measurements form a feature vector, and 3 species are more than two states of nature. (b) The two kinds of mistake do not cost the same: a loss function. (c) "Ask a human" is a rejection. It is an action that names no class.
> - A: (b) and (c) are swapped. The spam filter still only decides a class. Its mistakes have different costs.
> - B: Iris also has 3 species, so it needs more than two states of nature.
> - C: "Not sure" is not a state of nature. No mail is truly of class "not sure". It is an action.

### Q19 (slide 20)

A problem has $c=3$ classes and $a=4$ actions: $\alpha_1$, $\alpha_2$, $\alpha_3$ decide $\omega_1$, $\omega_2$, $\omega_3$, and $\alpha_4$ is "reject". How many loss values $\lambda(\alpha_i\mid\omega_j)$ must be given, and what does $\lambda(\alpha_4\mid\omega_2)$ mean?

- A) 7 values. $\lambda(\alpha_4\mid\omega_2)$ is the loss for rejecting when the true class is $\omega_2$.
- B) 12 values. $\lambda(\alpha_4\mid\omega_2)$ is the probability of rejecting when the true class is $\omega_2$.
- C) 12 values. $\lambda(\alpha_4\mid\omega_2)$ is the loss for rejecting when the true class is $\omega_2$.
- D) 9 values. $\lambda(\alpha_4\mid\omega_2)$ is the loss for rejecting when the true class is $\omega_2$.

> [!success]- Answer
> **C.** One value for every pair (action, true class): $a\cdot c=4\cdot3=12$. The first index is the action, the second is the truth.
> - A: $a+c=7$ adds the numbers. Every action needs a loss for every class, so they are multiplied.
> - B: $\lambda$ is a loss (a cost), not a probability.
> - D: $c\cdot c=9$ forgets the reject action. The numbers of actions and classes need not be equal.

### Q20 (slide 21)

Three classes, three actions ($\alpha_i$ = decide $\omega_i$). Posteriors at $\mathbf{x}$: $0.5$, $0.3$, $0.2$. Correct decisions cost 0. The other losses are $\lambda(\alpha_1\mid\omega_2)=2$, $\lambda(\alpha_1\mid\omega_3)=4$, $\lambda(\alpha_2\mid\omega_1)=1$, $\lambda(\alpha_2\mid\omega_3)=3$, $\lambda(\alpha_3\mid\omega_1)=1$, $\lambda(\alpha_3\mid\omega_2)=1$. What are the conditional risks $R(\alpha_1\mid\mathbf{x})$, $R(\alpha_2\mid\mathbf{x})$, $R(\alpha_3\mid\mathbf{x})$, and which action is chosen?

- A) Risks $1.4$, $1.1$, $0.8$. Choose $\alpha_1$.
- B) Risks $1.4$, $1.1$, $0.8$. Choose $\alpha_3$.
- C) Risks $0.5$, $1.2$, $2.9$. Choose $\alpha_1$.
- D) Risks $0.5$, $0.7$, $0.8$. Choose $\alpha_1$.

> [!success]- Answer
> **B.** $R(\alpha_i\mid\mathbf{x})=\sum_j\lambda(\alpha_i\mid\omega_j)P(\omega_j\mid\mathbf{x})$.
> $R(\alpha_1)=2(0.3)+4(0.2)=1.4$. $R(\alpha_2)=1(0.5)+3(0.2)=1.1$. $R(\alpha_3)=1(0.5)+1(0.3)=0.8$.
> The smallest risk wins: $\alpha_3$, although $\omega_3$ is the least probable class. Missing $\omega_3$ is expensive (losses 4 and 3).
> - A: The risks are right, but the largest one was chosen. We want the smallest expected loss.
> - C: The two indices of $\lambda$ were swapped (truth and action exchanged): $1(0.3)+1(0.2)=0.5$, and so on.
> - D: These are $1-P(\omega_i\mid\mathbf{x})$, the risks of the zero-one loss. The given losses were not used.

### Q21 (slide 22)

The feature takes two values with $P(x=a)=0.7$ and $P(x=b)=0.3$. The conditional risks are $R(\alpha_1\mid a)=0.2$, $R(\alpha_2\mid a)=0.9$, $R(\alpha_1\mid b)=1.5$, $R(\alpha_2\mid b)=0.4$. What are the overall risk of the rule "always $\alpha_1$" and the Bayes risk?

- A) $1.70$ and $0.60$
- B) $0.59$ and $0.59$
- C) $0.59$ and $0.20$
- D) $0.59$ and $0.26$

> [!success]- Answer
> **D.** For a discrete feature $R=\sum_xR(\alpha(x)\mid x)P(x)$.
> Always $\alpha_1$: $(0.2)(0.7)+(1.5)(0.3)=0.14+0.45=0.59$.
> Bayes rule: $\alpha_1$ at $a$ ($0.2<0.9$), $\alpha_2$ at $b$ ($0.4<1.5$). Bayes risk: $(0.2)(0.7)+(0.4)(0.3)=0.14+0.12=0.26$.
> - A: The conditional risks were added without the weights $P(x)$: $0.2+1.5$ and $0.2+0.4$.
> - B: $0.59$ is the best rule with one fixed action. A decision rule may choose a different action at each $x$.
> - C: $0.20$ is the smallest single conditional risk. The overall risk is an average over all $x$.

## 9. Two categories and the likelihood ratio (slides 23-24)

### Q22 (slide 23)

Notation: $\lambda_{ij}=\lambda(\alpha_i\mid\omega_j)$ is the loss for deciding $\omega_i$ when the truth is $\omega_j$. Losses: $\lambda_{11}=1$, $\lambda_{12}=6$, $\lambda_{21}=3$, $\lambda_{22}=1$. At $\mathbf{x}$: $P(\omega_1\mid\mathbf{x})=0.6$. What are $R(\alpha_1\mid\mathbf{x})$ and $R(\alpha_2\mid\mathbf{x})$, and what is the decision?

- A) $R(\alpha_1\mid\mathbf{x})=3.0$ and $R(\alpha_2\mid\mathbf{x})=2.2$. Decide $\omega_2$.
- B) $R(\alpha_1\mid\mathbf{x})=2.4$ and $R(\alpha_2\mid\mathbf{x})=1.8$. Decide $\omega_2$.
- C) $R(\alpha_1\mid\mathbf{x})=1.8$ and $R(\alpha_2\mid\mathbf{x})=4.0$. Decide $\omega_1$.
- D) $R(\alpha_1\mid\mathbf{x})=3.0$ and $R(\alpha_2\mid\mathbf{x})=2.2$. Decide $\omega_1$.

> [!success]- Answer
> **A.** $P(\omega_2\mid\mathbf{x})=0.4$. $R(\alpha_1\mid\mathbf{x})=\lambda_{11}(0.6)+\lambda_{12}(0.4)=0.6+2.4=3.0$. $R(\alpha_2\mid\mathbf{x})=\lambda_{21}(0.6)+\lambda_{22}(0.4)=1.8+0.4=2.2$.
> $2.2<3.0$: decide $\omega_2$, although $\omega_1$ is more probable. Check: $(3-1)(0.6)=1.2>(6-1)(0.4)=2.0$ is false, so not $\omega_1$.
> - B: The costs of the correct decisions ($\lambda_{11}=\lambda_{22}=1$) were left out.
> - C: The indices were swapped: $\lambda_{21}$ was used in $R(\alpha_1)$ and $\lambda_{12}$ in $R(\alpha_2)$.
> - D: The risks are right, but the larger risk (or the larger posterior) was chosen. The smaller risk wins.

### Q23 (slide 24)

Rule: decide $\omega_1$ if the likelihood ratio $\frac{p(\mathbf{x}\mid\omega_1)}{p(\mathbf{x}\mid\omega_2)}$ is above $\theta=\frac{\lambda_{12}-\lambda_{22}}{\lambda_{21}-\lambda_{11}}\cdot\frac{P(\omega_2)}{P(\omega_1)}$. Losses: $\lambda_{11}=1$, $\lambda_{12}=6$, $\lambda_{21}=3$, $\lambda_{22}=1$. A pattern has likelihood ratio $1.5$. What are $\theta$ and the decision for equal priors, and then for $P(\omega_1)=0.8$?

- A) $\theta=2.5$: decide $\omega_2$. Then $\theta=10$: decide $\omega_2$.
- B) $\theta=2.5$: decide $\omega_2$. Then $\theta=0.625$: decide $\omega_1$.
- C) $\theta=2$: decide $\omega_2$. Then $\theta=0.5$: decide $\omega_1$.
- D) $\theta=0.4$: decide $\omega_1$. Then $\theta=0.1$: decide $\omega_1$.

> [!success]- Answer
> **B.** Loss factor: $\frac{6-1}{3-1}=2.5$. Equal priors: $\theta=2.5\cdot1=2.5$, and $1.5<2.5$ gives $\omega_2$.
> $P(\omega_1)=0.8$: $\theta=2.5\cdot\frac{0.2}{0.8}=0.625$, and $1.5>0.625$ gives $\omega_1$. The ratio stayed $1.5$. Only the threshold moved.
> - A: The prior ratio was inverted: $2.5\cdot\frac{0.8}{0.2}=10$. A more common $\omega_1$ must lower the threshold.
> - C: $\lambda_{11}$ and $\lambda_{22}$ were forgotten: $\frac{6}{3}=2$.
> - D: The loss factor was inverted: $\frac{3-1}{6-1}=0.4$.

## 10. Minimum-error-rate classification (slides 25-26)

### Q24 (slide 25)

Zero-one loss: $\lambda(\alpha_i\mid\omega_j)=0$ for $i=j$ and $1$ for $i\neq j$. Three classes have the posteriors $0.2$, $0.5$, $0.3$ at $\mathbf{x}$. What are the three conditional risks $R(\alpha_i\mid\mathbf{x})=\sum_j\lambda(\alpha_i\mid\omega_j)P(\omega_j\mid\mathbf{x})$, and which action is taken?

- A) Risks $0.2$, $0.5$, $0.3$. Take $\alpha_1$, because its risk is the smallest.
- B) Risks $0.8$, $0.5$, $0.7$. Take $\alpha_1$, because its value is the largest.
- C) Risks $0.8$, $0.5$, $0.7$. Take $\alpha_2$, because its risk is the smallest.
- D) Risks $0.40$, $0.25$, $0.35$. Take $\alpha_2$, because its risk is the smallest.

> [!success]- Answer
> **C.** The term $j=i$ has loss 0, all others have loss 1. So $R(\alpha_i\mid\mathbf{x})=\sum_{j\neq i}P(\omega_j\mid\mathbf{x})=1-P(\omega_i\mid\mathbf{x})$.
> $R(\alpha_1)=0.5+0.3=0.8$, $R(\alpha_2)=0.2+0.3=0.5$, $R(\alpha_3)=0.2+0.5=0.7$. The risk is the probability that the decision is wrong. Smallest risk = largest posterior: $\alpha_2$.
> - A: These are the posteriors, not the risks. The correct class has loss 0, so its own posterior drops out of the sum.
> - B: The risks are right, but the largest risk was chosen.
> - D: The sum of the two other posteriors was divided by 2. The formula is a sum, not an average.

### Q25 (slide 26)

A classmate says: "The Bayes error is the error of one special classifier. A better classifier, trained with more data on the same features, can get below it." Which statement is correct?

- A) It is the smallest error any rule can reach for the given pdfs and priors. Only a change of the problem, like better features, lowers it.
- B) It is the error of the Bayes classifier on its training set. A larger training set with the same features makes it smaller.
- C) It is the smallest error any rule can reach for the given pdfs and priors. It is zero when the pdfs and priors are known exactly.
- D) It is the smallest error of all linear classifiers. A classifier with a curved boundary can get below it on the same features.

> [!success]- Answer
> **A.** The Bayes error is "the best performance that can be achieved". It is a property of the problem: how much the classes overlap in the chosen features. To lower it, the problem must change, for example with better or more features.
> - B: It is not a training-set error. More data only helps a real classifier to come closer to the Bayes error.
> - C: With known pdfs the Bayes error is still above zero when the densities overlap.
> - D: The minimum is over all decision rules, not only linear ones. No boundary of any shape gets below it.

## 11. Likelihood-ratio pictures (slides 27-28)

### Q26 (slide 27)

Fish example with $P(\omega_1)=2/3$, $P(\omega_2)=1/3$ and zero-one loss. The rule is: decide $\omega_1$ if $\frac{p(x\mid\omega_1)}{p(x\mid\omega_2)}>\theta_a$, with $\theta_a=\frac{P(\omega_2)}{P(\omega_1)}$. At $x=14$: $p(x\mid\omega_1)\approx0.18$ and $p(x\mid\omega_2)\approx0.03$. What are the likelihood ratio, $\theta_a$ and the region of $x=14$?

- A) Ratio $6$, $\theta_a=2$. The point is in $R_1$.
- B) Ratio $0.167$, $\theta_a=0.5$. The point is in $R_2$.
- C) Ratio $6$, $\theta_a=0.5$. The point is in $R_1$.
- D) Ratio $6$, $\theta_a=1$. The point is in $R_1$.

> [!success]- Answer
> **C.** Ratio: $0.18/0.03=6$. Threshold: $\theta_a=\frac{1/3}{2/3}=0.5$. $6>0.5$: $R_1$.
> This test is the same as $p(x\mid\omega_1)P(\omega_1)>p(x\mid\omega_2)P(\omega_2)$, that is $P(\omega_1\mid x)>P(\omega_2\mid x)$. So it gives the same boundaries as the posterior plot ($0.92>0.08$ at $x=14$).
> - A: The prior ratio was inverted: $\frac{2/3}{1/3}=2$. The threshold has $P(\omega_2)$ on top.
> - B: The likelihood ratio was inverted: $0.03/0.18=0.167$. $\omega_1$ is on top.
> - D: $\theta=1$ belongs to zero-one loss with equal priors. Here the priors differ.

### Q27 (slide 28)

![[ee525_lecture_02_lecture_2_bayesian_classifiers_p028.jpg]]

The figure shows the likelihood ratio of the fish example with $P(\omega_1)=2/3$, $P(\omega_2)=1/3$. The threshold is $\theta=\frac{\lambda_{12}-\lambda_{22}}{\lambda_{21}-\lambda_{11}}\cdot\frac{P(\omega_2)}{P(\omega_1)}$, and zero-one loss gives $\theta_a=0.5$. Now take $\lambda_{12}=1.4$, $\lambda_{21}=1$, $\lambda_{11}=\lambda_{22}=0$. At the top of the first bump of the curve (near $x=10.5$) the posterior is $P(\omega_1\mid x)\approx0.62$. What is $\theta_b$, and in which region is $x=10.5$?

- A) $\theta_b=2.8$. The point is in $R_2$, because the ratio $0.816$ is below $2.8$.
- B) $\theta_b=0.7$. The point is in $R_2$, because the value $0.62$ is below $0.7$.
- C) $\theta_b=1.4$. The point is in $R_2$, because the ratio $0.816$ is below $1.4$.
- D) $\theta_b=0.7$. The point is in $R_1$, because the ratio $0.816$ is above $0.7$.

> [!success]- Answer
> **D.** $\theta_b=\frac{1.4}{1}\cdot\frac{1/3}{2/3}=0.7$.
> Likelihood ratio from the posteriors: posterior ratio $0.62/0.38=1.632$, divided by the prior ratio $2$: $0.816$. $0.816>0.7$: still $R_1$.
> The line at $\theta_b$ is higher than at $\theta_a$, so the middle piece of $R_1$ is narrower. It disappears only for $\theta_b>0.816$.
> - A: The prior ratio was inverted: $1.4\cdot2=2.8$.
> - B: $0.62$ is a posterior. The threshold is compared with the likelihood ratio $0.816$.
> - C: The prior factor $\frac{P(\omega_2)}{P(\omega_1)}=0.5$ was forgotten.

## 12. Discriminant functions and decision regions (slides 29-31)

### Q28 (slide 29)

A classifier assigns $\mathbf{x}$ to $\omega_i$ if $g_i(\mathbf{x})>g_j(\mathbf{x})$ for all $j\neq i$. For one input it computes $g_1(\mathbf{x})=-2.1$, $g_2(\mathbf{x})=-0.4$, $g_3(\mathbf{x})=-3.0$. What is the output?

- A) $\omega_3$. The value $-3.0$ is the largest in size, and the size decides.
- B) $\omega_2$. Negative values are fine, because only the order of the $g_i$ counts.
- C) No class. The values must be positive and add to 1 before a decision.
- D) $\omega_2$. But the decision is not valid, because a discriminant is a probability.

> [!success]- Answer
> **B.** The largest value is $-0.4$, so $\omega_2$. A discriminant only has to give the right order of the classes. For example $g_i=-R(\alpha_i\mid\mathbf{x})$ and $g_i=\ln p(\mathbf{x}\mid\omega_i)+\ln P(\omega_i)$ are usually negative.
> - A: The rule takes the largest value, not the largest absolute value. $-3.0$ is the smallest.
> - C: Nothing in the rule asks for positive values or a sum of 1.
> - D: A discriminant need not be a probability. The decision is fully valid.

### Q29 (slide 30)

All $g_i(\mathbf{x})$ are replaced by $f(g_i(\mathbf{x}))$. Four candidates: (a) $f(g)=2g+5$. (b) $f(g)=\ln g$, when all $g_i>0$. (c) $f(g)=-g$. (d) $f(g)=g^2$, when some $g_i$ are negative. For which $f$ do the decisions always stay the same?

- A) Only (a)
- B) (a), (b) and (d)
- C) (a) and (b)
- D) (a), (b) and (c)

> [!success]- Answer
> **C.** The decisions stay the same when $f$ is monotonically increasing, because then the order of the $g_i$ is kept. (a) and (b) are increasing. Test for (b): $g_1=0.02$, $g_2=0.05$ gives $\ln0.02=-3.912<\ln0.05=-2.996$. Class 2 wins before and after.
> - A: $\ln$ is also increasing for positive arguments, so (b) is fine too.
> - B: $g^2$ is not monotone for negative values: $g_1=-3$, $g_2=1$ gives $9>1$, the order flips.
> - D: $-g$ is monotone but decreasing. The largest becomes the smallest, so the worst class would win.

### Q30 (slide 31)

One feature. $\omega_1$: $N(0,1)$. $\omega_2$: $N(0,9)$, so $\sigma_2=3$. Equal priors. Without the common terms, $g_i(x)=-\frac{x^2}{2\sigma_i^2}-\ln\sigma_i$. Where are the decision boundaries, and what are the regions?

- A) $x=\pm1.572$. $R_1$ is the middle part, $R_2$ is the two outer parts.
- B) $x=\pm1.572$. $R_2$ is the middle part, $R_1$ is the two outer parts.
- C) $x=\pm2.223$. $R_1$ is the middle part, $R_2$ is the two outer parts.
- D) $x=\pm1.815$. $R_1$ is the middle part, $R_2$ is the two outer parts.

> [!success]- Answer
> **A.** $g_1=-\frac{x^2}{2}$, $g_2=-\frac{x^2}{18}-\ln3$. Set $g_1=g_2$: $\frac{x^2}{2}-\frac{x^2}{18}=\frac{4x^2}{9}=\ln3=1.0986$, so $x^2=2.472$ and $x=\pm1.572$.
> At $x=0$: $g_1=0>g_2=-1.099$, so $\omega_1$ wins in the middle. $R_2=(-\infty,-1.572)\cup(1.572,\infty)$ has two separate pieces, as on the slide.
> - B: The regions are swapped. The narrow class is higher near the centre, the wide class far away.
> - C: $\ln9$ was used instead of $\ln3$: the term is $\ln\sigma_2$, not $\ln\sigma_2^2$.
> - D: $\sigma_2=3$ was used in the exponent instead of $\sigma_2^2=9$: $\frac{x^2}{2}-\frac{x^2}{6}=\ln3$.

## 13. The normal density (slides 32-35)

### Q31 (slide 32)

![[ee525_lecture_02_lecture_2_bayesian_classifiers_p016.jpg]]

The slide gives two reasons for the Gaussian model: it is analytically tractable, and it fits a class whose feature vectors are randomly corrupted versions of one prototype. Look at the black curve $p(x\mid\omega_1)$ of the sea bass. Would one Gaussian be a good model for it?

- A) Yes. The curve has one prototype near $x=13$, and the left part is only noise.
- B) Yes. By the Central Limit Theorem the pdf of every class is close to a Gaussian.
- C) No. The curve stays below $0.4$, and the peak of a Gaussian must reach $0.4$.
- D) No. The curve has two peaks, and a Gaussian has one peak and is symmetric.

> [!success]- Answer
> **D.** The black curve has peaks near $x=10.8$ and near $x=13$. One Gaussian has one peak and is symmetric around it. The picture looks like two prototypes, not one.
> - A: Noise around one prototype gives one hill. The second peak near $10.8$ is a clear structure, not noise.
> - B: The theorem is about sums of many random effects around one prototype. It does not make every class pdf Gaussian.
> - C: The peak of a Gaussian is $\frac{1}{\sqrt{2\pi}\,\sigma}$. It is $0.4$ only for $\sigma=1$.

### Q32 (slide 33)

$x\sim N(20,4^2)$, so $\mu=20$ and $\sigma=4$. The density is $p(x)=\frac{1}{\sqrt{2\pi}\,\sigma}\exp\left[-\frac12\left(\frac{x-\mu}{\sigma}\right)^2\right]$. Use $\Phi(2)=0.9772$. What are $p(26)$ and the fraction of the samples above 28?

- A) $p(26)=0.1295$ and fraction $0.0228$
- B) $p(26)=0.0324$ and fraction $0.0228$
- C) $p(26)=0.0324$ and fraction $0.0455$
- D) $p(26)=0.0471$ and fraction $0.0228$

> [!success]- Answer
> **B.** $\frac{26-20}{4}=1.5$, exponent $-\frac12(1.5)^2=-1.125$, constant $\frac{1}{\sqrt{2\pi}\cdot4}=0.09974$. $p(26)=0.09974\,e^{-1.125}=0.0324$.
> Above 28: $z=\frac{28-20}{4}=2$, fraction $1-\Phi(2)=0.0228$. (Between 16 and 24, that is $\mu\pm\sigma$, lie $0.6827$ of the samples.)
> - A: The factor $\frac1\sigma$ in the constant was forgotten: $0.3989\,e^{-1.125}=0.1295$.
> - C: $0.0455$ is both tails together (below 12 or above 28). The question asks for one tail.
> - D: The square in the exponent was forgotten: $0.09974\,e^{-0.75}=0.0471$.

### Q33 (slide 34)

$d=2$, $\boldsymbol\mu=[0,0]^{\mathsf T}$, and $\Sigma$ has the rows $(2,-1)$ and $(-1,1)$. The density is $p(\mathbf{x})=\frac{1}{(2\pi)^{d/2}\lvert\Sigma\rvert^{1/2}}\exp\left[-\frac12r^2\right]$ with the squared Mahalanobis distance $r^2=(\mathbf{x}-\boldsymbol\mu)^{\mathsf T}\Sigma^{-1}(\mathbf{x}-\boldsymbol\mu)$. What are $r^2$ and $p(\mathbf{x})$ for $\mathbf{x}=[1,1]^{\mathsf T}$?

- A) $r^2=1$ and $p(\mathbf{x})=0.0965$
- B) $r^2=2$ and $p(\mathbf{x})=0.0585$
- C) $r^2=5$ and $p(\mathbf{x})=0.0131$
- D) $r^2=5$ and $p(\mathbf{x})=0.0011$

> [!success]- Answer
> **C.** $\lvert\Sigma\rvert=2\cdot1-(-1)^2=1$. Inverse: swap the diagonal, change the sign off the diagonal, divide by 1: rows $(1,1)$ and $(1,2)$.
> $\Sigma^{-1}[1,1]^{\mathsf T}=[2,3]^{\mathsf T}$, so $r^2=1(2)+1(3)=5$. Constant: $\frac{1}{2\pi\cdot1}=0.1592$. $p(\mathbf{x})=0.1592\,e^{-2.5}=0.0131$.
> - A: $\Sigma$ was used instead of $\Sigma^{-1}$ (or the sign change in the inverse was forgotten): $2-1-1+1=1$.
> - B: $r^2=2$ is the squared Euclidean distance $1^2+1^2$. The covariance matrix was ignored.
> - D: The factor $\frac12$ in the exponent was forgotten: $0.1592\,e^{-5}=0.0011$.

### Q34 (slide 35)

![[ee525_lecture_02_lecture_2_bayesian_classifiers_p035.jpg]]

Take one of the red ellipses. Point A lies on it at the end of its long axis. Point B lies on the same ellipse at the end of its short axis. In the options, "far" means the ordinary (Euclidean) distance. Which statement is correct?

- A) A is farther from $\boldsymbol\mu$ than B. Both have the same density. The covariance of $x_1$ and $x_2$ is positive.
- B) A is farther from $\boldsymbol\mu$ than B. B has the higher density. The covariance of $x_1$ and $x_2$ is positive.
- C) A is farther from $\boldsymbol\mu$ than B. Both have the same density. The covariance of $x_1$ and $x_2$ is negative.
- D) A and B are equally far from $\boldsymbol\mu$. Both have the same density. The covariance of $x_1$ and $x_2$ is positive.

> [!success]- Answer
> **A.** The long axis is longer, so A has the larger Euclidean distance. A red ellipse is a line of equal probability density, so $p(\mathbf{x})$ and the Mahalanobis distance are the same at A and B. The cloud goes up to the right: a large $x_1$ comes with a large $x_2$, so the covariance is positive.
> - B: The density depends on the Mahalanobis distance, not on the Euclidean distance. It is constant on one ellipse.
> - C: A negative covariance would tilt the cloud down to the right.
> - D: Equal for both points is the Mahalanobis distance. The Euclidean distances differ, because an ellipse is not a circle.

## 14. Discriminant functions for the normal density (slides 36-37)

### Q35 (slide 36)

One feature. $\omega_1$: $N(0,1)$ with $P(\omega_1)=0.6$. $\omega_2$: $N(2,2^2)$ with $P(\omega_2)=0.4$. For $d=1$ the Gaussian discriminant without the common term $-\frac12\ln2\pi$ is $g_i(x)=-\frac12\left(\frac{x-\mu_i}{\sigma_i}\right)^2-\ln\sigma_i+\ln P(\omega_i)$. What are $g_1(1)$ and $g_2(1)$, and what is the decision at $x=1$?

- A) $g_1=-0.5000$ and $g_2=-0.1250$. Decide $\omega_2$.
- B) $g_1=-1.0108$ and $g_2=-1.0413$. Decide $\omega_1$.
- C) $g_1=-1.0108$ and $g_2=-2.4276$. Decide $\omega_1$.
- D) $g_1=-1.0108$ and $g_2=-1.7344$. Decide $\omega_1$.

> [!success]- Answer
> **D.** $g_1(1)=-\frac12(1)^2-\ln1+\ln0.6=-0.5-0-0.5108=-1.0108$.
> $g_2(1)=-\frac12\left(\frac{1-2}{2}\right)^2-\ln2+\ln0.4=-0.125-0.6931-0.9163=-1.7344$. $g_1>g_2$: decide $\omega_1$.
> - A: Only the distance terms were used. The terms $-\ln\sigma_i$ and $\ln P(\omega_i)$ differ between the classes and must stay.
> - B: The term $-\ln\sigma_2=-0.6931$ was dropped. It may be dropped only when all classes have the same variance.
> - C: $\ln\sigma_2^2=1.3863$ was used instead of $\ln\sigma_2$. The slide's term is $\frac12\ln\lvert\Sigma_i\rvert=\ln\sigma_i$.

### Q36 (slide 37)

Two classes in 2-D with $\Sigma_1=\Sigma_2=\sigma^2I$, $\sigma^2=2$, $\boldsymbol\mu_1=[0,0]^{\mathsf T}$, $\boldsymbol\mu_2=[4,0]^{\mathsf T}$. The boundary is $(\boldsymbol\mu_1-\boldsymbol\mu_2)^{\mathsf T}\mathbf{x}=\tfrac12\left(\|\boldsymbol\mu_1\|^2-\|\boldsymbol\mu_2\|^2\right)-\sigma^2\ln\frac{P(\omega_1)}{P(\omega_2)}$, and $R_1$ is the side of $\boldsymbol\mu_1$. Where is the boundary for equal priors and for $P(\omega_1)=0.75$, and how is $\mathbf{x}=[2.3,\,7]^{\mathsf T}$ classified in the two cases?

- A) $x_1=2$, then $x_1=1.451$. The point is $\omega_2$ in both cases.
- B) $x_1=2$, then $x_1=2.549$. The point is $\omega_2$, then $\omega_1$.
- C) $x_1=2$, then $x_1=2.275$. The point is $\omega_2$ in both cases.
- D) $x_1=2$, then $x_1=3.099$. The point is $\omega_2$, then $\omega_1$.

> [!success]- Answer
> **B.** Left side: $-4x_1$. Right side: $\tfrac12(0-16)-2\ln\frac{P(\omega_1)}{P(\omega_2)}$. Divide by $-4$: $x_1=2+\tfrac12\ln\frac{P(\omega_1)}{P(\omega_2)}$.
> Equal priors: $x_1=2$, and $2.3>2$ gives $\omega_2$. $P(\omega_1)=0.75$: $x_1=2+\tfrac12\ln3=2.549$, and $2.3<2.549$ gives $\omega_1$. The value $x_2=7$ plays no role, because the boundary is a vertical line.
> - A: Wrong sign of the prior term: $2-\tfrac12\ln3$. A larger $P(\omega_1)$ moves the line towards $\boldsymbol\mu_2$, so $R_1$ grows.
> - C: The factor $\sigma^2=2$ was forgotten: $2+\tfrac14\ln3=2.275$.
> - D: The prior term was divided by 2 instead of 4: $2+\ln3=3.099$.
