---
course: EE 525 Pattern Recognition
lecture: 2
title: Bayesian classifiers
tags: [ee525, lecture]
slides: lectures/lecture_02/Lecture 2- Bayesian Classifiers.pdf
full_notes: lecture_notes/lecture_02/EE525_Lecture_02_Bayesian_Classifiers.pdf
---
# EE 525 · Lecture 2 · Bayesian classifiers

> [!summary] In short
> - Bayes' rule: posterior = likelihood × prior / evidence. The Bayes classifier picks the class with the largest posterior.
> - The evidence $p(\mathbf{x})$ is the same for all classes, so compare $p(\mathbf{x}\mid\omega_i)P(\omega_i)$. With equal priors compare the likelihoods only.
> - The Bayes rule gives the smallest possible error probability (Bayes error). It is not zero when the densities overlap.
> - With a loss $\lambda(\alpha_i\mid\omega_j)$, choose the action with the smallest conditional risk. For two classes this is a likelihood-ratio test.
> - Zero-one loss: minimum risk = maximum posterior.
> - A classifier is a set of discriminant functions $g_i(\mathbf{x})$ plus "pick the largest". For Gaussian classes $g_i$ has a closed form.

Symbols: $\omega_i$ is class $i$ (state of nature). $P(\cdot)$ is a probability, $p(\cdot)$ a density. The bar reads "given".

## 1. Why probability (slide 2)
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p002.jpg]]

**Notes.** Features are statistical: patterns of one class vary and sensors add noise. So a classifier can only say which class is more probable. The lecture defines what "most probable" means.

> [!question]- Check (slide 2): Find the mistake: "If the sensors had no noise at all, the features of one class would always be the same, so a classifier would never be wrong."
> The slide names two sources of randomness: the statistical variation of the patterns and the noise of the sensors.
> Removing the noise removes only the second source. Two fish of the same species still have different lightness.
> So the features of two classes can still overlap, and the classifier can only pick the most probable class.

## 2. Fruit basket: total probability and Bayes' theorem (slides 3-7)
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p003.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p004.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p005.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p006.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p007.jpg]]

**Notes.** Basket: 60% apples ($\omega_1$), 40% oranges ($\omega_2$). Feature $x=1$ if the fruit is red.

$$P(\omega_1)=0.60,\quad P(\omega_2)=0.40,\quad P(x=1\mid\omega_1)=0.80,\quad P(x=1\mid\omega_2)=0.20$$

- **Prior** $P(\omega_i)$: how common the class is before looking at the feature.
- **Class-conditional probability** (likelihood) $P(x\mid\omega_i)$: how likely $x$ is inside class $\omega_i$.
- **Evidence** $P(x)$: how likely $x$ is over all classes.
- **Posterior** $P(\omega_i\mid x)$: how likely the class is after seeing $x$.

$$P(x)=\sum_{i=1}^{M}P(x\mid\omega_i)P(\omega_i),\qquad P(\omega_i\mid x)=\frac{P(x\mid\omega_i)P(\omega_i)}{P(x)}$$

> [!example] Red fruit
> $P(x=1)=(0.80)(0.60)+(0.20)(0.40)=0.48+0.08=0.56$.
> $P(\omega_1\mid x=1)=0.48/0.56=0.8571$, $P(\omega_2\mid x=1)=0.08/0.56=0.1429$. Decision: apple.

> [!warning] Watch out
> The likelihood $P(x=1\mid\omega_1)=0.80$ and the posterior $P(\omega_1\mid x=1)=0.857$ answer different questions. Only the posteriors add to 1 over the classes.

> [!question]- Check (slide 3): On the slide $P(x=1\mid\omega_1)+P(x=1\mid\omega_2)=0.80+0.20=1$. Must these two numbers always add to 1? Which pairs on the slide must add to 1?
> No. The two numbers are conditioned on different classes, so there is no rule that links them. For example $0.90$ and $0.30$ would also be allowed. Here the sum is 1 by chance.
> The pairs that must add to 1 keep the class fixed and go over all feature values: $P(x=1\mid\omega_i)+P(x=0\mid\omega_i)=1$. That is why the slide writes $P(x=0\mid\omega_1)=1-0.80=0.20$ and $P(x=0\mid\omega_2)=1-0.20=0.80$.
> The priors also add to 1: $0.60+0.40=1$.

> [!question]- Check (slide 4): Compute $P(x=0)$, the probability that a random fruit is not red, with the total probability theorem. Then check the result with $P(x=1)=0.56$.
> Formula: $P(x=0)=P(x=0\mid\omega_1)P(\omega_1)+P(x=0\mid\omega_2)P(\omega_2)$.
> Substitute: $P(x=0)=(0.20)(0.60)+(0.80)(0.40)=0.12+0.32=0.44$.
> Check: a fruit is red or not red, so $P(x=0)+P(x=1)=0.44+0.56=1$.

> [!question]- Check (slide 5): We pick a fruit and it is not red ($x=0$). Compute both posteriors.
> Joint values: $P(x=0\mid\omega_1)P(\omega_1)=(0.20)(0.60)=0.12$ and $P(x=0\mid\omega_2)P(\omega_2)=(0.80)(0.40)=0.32$.
> Evidence: $P(x=0)=0.12+0.32=0.44$.
> $P(\omega_1\mid x=0)=0.12/0.44=0.2727$, $P(\omega_2\mid x=0)=0.32/0.44=0.7273$.
> Check: $0.2727+0.7273=1$. Before the observation an orange had probability $0.40$. After seeing "not red" it has $0.7273$.

> [!question]- Check (slide 6): The classifier says "apple" for every red fruit and (by the same rule) "orange" for every fruit that is not red. How often is it wrong in total? Compare with deciding without looking at the colour.
> A red fruit is called apple. This is wrong when the fruit is a red orange: $P(x=1,\omega_2)=(0.20)(0.40)=0.08$.
> A fruit that is not red is called orange. This is wrong when it is an apple that is not red: $P(x=0,\omega_1)=(0.20)(0.60)=0.12$.
> $P(\text{error})=0.08+0.12=0.20$.
> Without the colour we always say apple (the larger prior) and are wrong for every orange: $P(\text{error})=0.40$. The feature cuts the error in half.

> [!question]- Check (slide 7): The basket changes to 50% apples and 50% oranges. The colours of each kind of fruit stay the same. Which of the four quantities in the table change? Compute the new $P(\omega_1\mid x=1)$.
> Prior: changes, $P(\omega_1)=P(\omega_2)=0.50$.
> Class-conditional probability: does not change. It describes the apples only, $P(x=1\mid\omega_1)=0.80$.
> Total probability: changes, $P(x=1)=(0.80)(0.50)+(0.20)(0.50)=0.40+0.10=0.50$.
> Posterior: changes, $P(\omega_1\mid x=1)=0.40/0.50=0.80$.
> With equal priors the posterior is the likelihood divided by the sum of the likelihoods: $0.80/(0.80+0.20)=0.80$.

## 3. The Bayes classifier for M classes (slides 8-9)
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p008.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p009.jpg]]

**Notes.** A pattern is a feature vector $\mathbf{x}=[x(1),\dots,x(l)]^{\mathsf T}\in\mathbb{R}^l$ and belongs to exactly one of $M$ known classes. Form the $M$ **a posteriori probabilities** $P(\omega_i\mid\mathbf{x})$ and assign $\mathbf{x}$ to the class with the largest one.

$$P(\omega_i\mid\mathbf{x})=\frac{p(\mathbf{x}\mid\omega_i)P(\omega_i)}{p(\mathbf{x})}$$

Recipe: joint values $J_i=p(\mathbf{x}\mid\omega_i)P(\omega_i)$, evidence $p(\mathbf{x})=\sum_iJ_i$, posteriors $J_i/p(\mathbf{x})$, largest wins. "Largest posterior" equals "largest likelihood" only when the priors are equal.

> [!question]- Check (slide 8): $M=3$ classes. For one pattern the posteriors are $0.40$, $0.35$, $0.25$. Which class does the classifier choose? What is the probability that this decision is wrong? Must the winning posterior be above $0.5$?
> It chooses $\omega_1$, because $0.40$ is the largest of the three values.
> The decision is wrong when the true class is $\omega_2$ or $\omega_3$: $0.35+0.25=0.60$, the same as $1-0.40$.
> No. The winner only has to be larger than each other posterior. With more than two classes the largest value can be below $0.5$, and then the decision is more often wrong than right. It is still the best possible decision for this $\mathbf{x}$.

> [!question]- Check (slide 9): Three classes with priors $0.6$, $0.3$, $0.1$. At the measured $\mathbf{x}$ the densities are $p(\mathbf{x}\mid\omega_i)=0.3$, $0.5$, $0.6$. Compute the three posteriors and classify. Does the class with the largest likelihood win?
> Joint values $J_i=p(\mathbf{x}\mid\omega_i)P(\omega_i)$: $J_1=(0.3)(0.6)=0.18$, $J_2=(0.5)(0.3)=0.15$, $J_3=(0.6)(0.1)=0.06$.
> Evidence: $p(\mathbf{x})=0.18+0.15+0.06=0.39$.
> Posteriors: $0.18/0.39=0.4615$, $0.15/0.39=0.3846$, $0.06/0.39=0.1538$. Sum: $1$.
> Decision: $\omega_1$. The largest likelihood belongs to $\omega_3$ ($0.6$), but $\omega_3$ is rare (prior $0.1$), so it ends last.

## 4. Discrete and continuous features (slides 10-11)
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p010.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p011.jpg]]

**Notes.** Two binary features: $x_1$ = red?, $x_2$ = round?. The feature vector takes four values only.

| $\mathbf{x}$ | apple $P(\mathbf{x}\mid\omega_1)$ | orange $P(\mathbf{x}\mid\omega_2)$ |
|---|---|---|
| $[0,0]$ | 0.05 | 0.10 |
| $[0,1]$ | 0.10 | 0.70 |
| $[1,0]$ | 0.75 | 0.05 |
| $[1,1]$ | 0.10 | 0.15 |

- Discrete features: $P(\mathbf{x}\mid\omega_i)$ is a probability, assigned directly to each feature vector.
- Continuous features: $p(\mathbf{x}\mid\omega_i)$ is a density. The probability of one exact value is zero. Probabilities come from integrals: $P(a\le x\le b)=\int_a^b p(x)\,dx$.

> [!example] Classify all four vectors (priors 0.6 / 0.4 added by our notes)
> Joints $(J_1,J_2)$: $[0,0]$: $(0.03,0.04)$ orange. $[0,1]$: $(0.06,0.28)$ orange. $[1,0]$: $(0.45,0.02)$ apple. $[1,1]$: $(0.06,0.06)$ tie.
> $P(\text{correct})=0.04+0.28+0.45+0.06=0.83$, so $P(\text{error})=0.17$.

> [!warning] Watch out
> A density value $p(x)$ can be larger than 1 (normal with $\sigma=0.1$: peak 3.99). A probability cannot.

> [!question]- Check (slide 10): Read from the table: what fraction of the apples is red (round or not)? What fraction of the oranges is round (red or not)?
> "Red" means $x_1=1$. Add the two rows with $x_1=1$ in the apple column: $P(x_1=1\mid\omega_1)=P([1,0]\mid\omega_1)+P([1,1]\mid\omega_1)=0.75+0.10=0.85$.
> "Round" means $x_2=1$. Add the two rows with $x_2=1$ in the orange column: $P(x_2=1\mid\omega_2)=P([0,1]\mid\omega_2)+P([1,1]\mid\omega_2)=0.70+0.15=0.85$.
> Each column is a full distribution over the four vectors (sum $1.00$), so the probability of one feature alone is a sum over the rows that contain it.

> [!question]- Check (slide 11): The weight $x$ of a fruit is continuous. Assume a constant density $p(x\mid\omega_1)=0.1$ per gram between 150 g and 160 g, and 0 outside. Find $P(152\le x\le153.5\mid\omega_1)$ and $P(x=152.3\mid\omega_1)$.
> Check that it is a density: area $=0.1\cdot(160-150)=1$.
> Probability of an interval is the integral of the density: $P(152\le x\le153.5\mid\omega_1)=\int_{152}^{153.5}0.1\,dx=0.1\cdot(153.5-152)=0.1\cdot1.5=0.15$.
> One exact value is an interval of width 0: $P(x=152.3\mid\omega_1)=0.1\cdot0=0$.
> The density value $0.1$ is not a probability. It becomes one only after multiplying by a width (integrating). For the discrete fruit table no integral is needed.

## 5. The two-class Bayes rule (slides 12-13)
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p012.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p013.jpg]]

**Notes.** Assumed known: the priors and the class-conditional pdfs $p(\mathbf{x}\mid\omega_i)$ (the **likelihood function** of $\omega_i$ with respect to $\mathbf{x}$). Priors from training data: $P(\omega_i)\approx N_i/N$.

Bayes rule (2.1) and evidence (2.2):

$$P(\omega_i\mid\mathbf{x})=\frac{p(\mathbf{x}\mid\omega_i)P(\omega_i)}{p(\mathbf{x})},\qquad p(\mathbf{x})=\sum_{i=1}^{2}p(\mathbf{x}\mid\omega_i)P(\omega_i)$$

Classification rule (2.3): decide $\omega_1$ if $P(\omega_1\mid\mathbf{x})>P(\omega_2\mid\mathbf{x})$, else $\omega_2$. Equivalent forms:

$$\text{(2.4)}\quad p(\mathbf{x}\mid\omega_1)P(\omega_1)\gtrless p(\mathbf{x}\mid\omega_2)P(\omega_2)\qquad\text{(2.5)}\quad p(\mathbf{x}\mid\omega_1)\gtrless p(\mathbf{x}\mid\omega_2)\ \text{ if }P(\omega_1)=P(\omega_2)$$

$p(\mathbf{x})$ drops out because it is the same positive number for both classes.

> [!warning] Slide wording
> Slide 13 calls the case of equality "detrimental". It means arbitrary: either class may be chosen.

> [!question]- Check (slide 12): A training set has $N=200$ fish: $N_1=150$ sea bass and $N_2=50$ salmon. Estimate the priors. When is this estimate not good?
> $P(\omega_1)\approx N_1/N=150/200=0.75$ and $P(\omega_2)\approx N_2/N=50/200=0.25$. Sum: $1$.
> The estimate is good only if the training set was collected the way the fish arrive in real use (a random sample). If somebody chose the training fish on purpose, for example the same number of each class, then $N_i/N$ says nothing about how common the classes really are.

> [!question]- Check (slide 13): At a point $\mathbf{x}$: $p(\mathbf{x}\mid\omega_1)=0.30$, $p(\mathbf{x}\mid\omega_2)=0.50$, $P(\omega_1)=0.7$, $P(\omega_2)=0.3$. Classify with (2.4). What would (2.5) give, and why may it not be used here?
> (2.4): $p(\mathbf{x}\mid\omega_1)P(\omega_1)=(0.30)(0.7)=0.21$ and $p(\mathbf{x}\mid\omega_2)P(\omega_2)=(0.50)(0.3)=0.15$. $0.21>0.15$: decide $\omega_1$.
> Posteriors, for a check: $p(\mathbf{x})=0.21+0.15=0.36$, so $P(\omega_1\mid\mathbf{x})=0.21/0.36=0.5833$ and $P(\omega_2\mid\mathbf{x})=0.15/0.36=0.4167$. Same decision as (2.3).
> (2.5) compares the likelihoods only: $0.30<0.50$ would give $\omega_2$. It is valid only for $P(\omega_1)=P(\omega_2)$. Here the priors differ, so it gives the wrong answer.

## 6. Decision regions and the probability of error (slides 14-15)
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p014.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p015.jpg]]

**Notes.** One feature, two equiprobable classes. The threshold $x_0$ splits the axis into $R_1$ (decide $\omega_1$) and $R_2$ (decide $\omega_2$). Errors are unavoidable: an $x$ can lie in $R_2$ and still come from $\omega_1$.

$$\text{(2.6)}\qquad P_e=\frac12\int_{-\infty}^{x_0}p(x\mid\omega_2)\,dx+\frac12\int_{x_0}^{+\infty}p(x\mid\omega_1)\,dx$$

The Bayesian classifier minimises $P_e$. Moving the threshold away from $x_0$ always increases the shaded area.

> [!example] Figure 2.1 in numbers (beyond the slides)
> $N(0,1)$ against $N(2,1)$, equal priors: $x_0=1$. Each tail is $1-\Phi(1)=0.1587$, so $P_e=0.1587$. At $t=1.5$: $P_e=\tfrac12(0.0668)+\tfrac12(0.3085)=0.1877$, larger. $\Phi$ is the standard normal cdf.

> [!info] Where (2.6) comes from (beyond the slides)
> The slide says "it does not take much thought". The steps are:
> An error happens in two ways: the pattern is from $\omega_2$ but $x$ falls in $R_1$, or the pattern is from $\omega_1$ but $x$ falls in $R_2$.
> $$P_e=P(x\in R_1,\omega_2)+P(x\in R_2,\omega_1)$$
> Write each joint probability as prior times class-conditional probability, and the class-conditional probability as an integral of the pdf over the region:
> $$P_e=P(\omega_2)\int_{R_1}p(x\mid\omega_2)\,dx+P(\omega_1)\int_{R_2}p(x\mid\omega_1)\,dx$$
> With $P(\omega_1)=P(\omega_2)=\tfrac12$, $R_1=(-\infty,x_0)$ and $R_2=(x_0,+\infty)$ this is (2.6).
> In the figure the first integral is the hatched area under the $\omega_2$ curve left of $x_0$, the second is the hatched area under the $\omega_1$ curve right of $x_0$. $P_e$ is half of the total hatched area.
> Why $x_0$ is the best threshold: move it to the right, to $t>x_0$. The points between $x_0$ and $t$ now go to $\omega_1$. There $p(x\mid\omega_2)>p(x\mid\omega_1)$, so we add the larger curve to the error and remove the smaller one. The error grows. The same holds for a move to the left.

> [!question]- Check (slide 14): In the figure, what is true for the two curves at $x_0$, and why is the threshold exactly there? Which kind of error does the hatched area on the left of $x_0$ show?
> At $x_0$ the curves cross: $p(x_0\mid\omega_1)=p(x_0\mid\omega_2)$.
> The classes are equiprobable, so the rule is (2.5): compare the likelihoods. Left of $x_0$ the $\omega_1$ curve is higher (region $R_1$), right of $x_0$ the $\omega_2$ curve is higher (region $R_2$). The decision changes where the curves are equal.
> The hatched area on the left lies under $p(x\mid\omega_2)$ but inside $R_1$: patterns of $\omega_2$ that are classified as $\omega_1$. The hatched area on the right is the opposite error.

> [!question]- Check (slide 15): $\omega_1$: $N(10,2^2)$, $\omega_2$: $N(15,2^2)$, equal priors. Find $x_0$ and $P_e$ from (2.6). Then compute the error for the threshold $t=13$.
> Equal priors and equal variances: the curves cross in the middle of the means, $x_0=(10+15)/2=12.5$.
> First integral: $P(x<12.5\mid\omega_2)=\Phi\left(\frac{12.5-15}{2}\right)=\Phi(-1.25)=0.1056$.
> Second integral: $P(x>12.5\mid\omega_1)=1-\Phi\left(\frac{12.5-10}{2}\right)=1-\Phi(1.25)=0.1056$.
> $P_e=\tfrac12(0.1056)+\tfrac12(0.1056)=0.1056$.
> Threshold $t=13$: $P(x<13\mid\omega_2)=\Phi\left(\frac{13-15}{2}\right)=\Phi(-1)=0.1587$ and $P(x>13\mid\omega_1)=1-\Phi\left(\frac{13-10}{2}\right)=1-\Phi(1.5)=0.0668$.
> Error $=\tfrac12(0.1587)+\tfrac12(0.0668)=\tfrac12(0.2255)=0.1127>0.1056$. Moving the threshold away from $x_0$ made the error larger, as the slide says.

## 7. Sea bass and salmon again, minimum error (slides 16-18)
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p016.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p017.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p018.jpg]]

**Notes.** $\omega_1$ = sea bass, $\omega_2$ = salmon. With priors only: decide $\omega_1$ if $P(\omega_1)>P(\omega_2)$, the same decision for every fish, error $\min_iP(\omega_i)$. A lightness measurement $x$ improves this.

With $P(\omega_1)=2/3$, $P(\omega_2)=1/3$ the slide plots the posteriors. At $x=14$: $P(\omega_1\mid x)\approx0.92$, $P(\omega_2\mid x)\approx0.08$.

For one observed $x$, the error probability is the posterior of the class we did not choose. So decide $\omega_1$ if $P(\omega_1\mid x)>P(\omega_2\mid x)$, and then

$$P(\text{error}\mid x)=\min\left[P(\omega_1\mid x),P(\omega_2\mid x)\right],\qquad P(\text{error})=\int_{-\infty}^{\infty}P(\text{error}\mid x)\,p(x)\,dx$$

Making every $P(\text{error}\mid x)$ as small as possible makes the integral as small as possible. The result is the **Bayes error**.

> [!example] Checking slide 17
> Read from the plot: $p(14\mid\omega_1)\approx0.18$, $p(14\mid\omega_2)\approx0.03$. Joints $0.18\cdot\tfrac23=0.12$ and $0.03\cdot\tfrac13=0.01$. Posteriors $0.12/0.13=0.923$ and $0.077$.

> [!question]- Check (slide 16): No measurement is available and $P(\omega_1)=2/3$, $P(\omega_2)=1/3$. What does the rule on the slide decide, and how often is it wrong? What is unsatisfying about this rule?
> $P(\omega_1)>P(\omega_2)$, so the rule decides $\omega_1$ (sea bass).
> It is wrong every time the fish is a salmon: $P(\text{error})=P(\omega_2)=1/3$. In general the error is the smaller prior.
> The rule gives the same answer for every fish, so it never finds a salmon. The lightness $x$ helps because the two pdfs in the figure are different: a value of $x$ can be typical for one class and unusual for the other.

> [!question]- Check (slide 17): Read from the left plot at $x=11.5$: $p(x\mid\omega_1)\approx0.155$, $p(x\mid\omega_2)\approx0.36$. With $P(\omega_1)=2/3$, $P(\omega_2)=1/3$, compute the posteriors and compare with the right plot.
> Joint values: $p(x\mid\omega_1)P(\omega_1)=0.155\cdot\tfrac23=0.1033$ and $p(x\mid\omega_2)P(\omega_2)=0.36\cdot\tfrac13=0.12$.
> Evidence: $p(x)=0.1033+0.12=0.2233$.
> $P(\omega_2\mid x)=0.12/0.2233=0.537$, $P(\omega_1\mid x)=0.1033/0.2233=0.463$. Decide $\omega_2$ (salmon).
> The right plot shows the same: near $x=11.5$ the red curve ($\omega_2$) is just above $0.5$ and the black curve just below.
> The likelihood of salmon is $0.36/0.155=2.3$ times larger, but the prior of sea bass is 2 times larger. So salmon wins only narrowly.

> [!question]- Check (slide 18): At some $x$ the posteriors are $P(\omega_1\mid x)=0.3$ and $P(\omega_2\mid x)=0.7$. Give $P(\text{error}\mid x)$ for each of the two possible decisions. Why does choosing the smaller one at every $x$ also make the average error $P(\text{error})$ smallest?
> Decide $\omega_2$: we are wrong when the truth is $\omega_1$, so $P(\text{error}\mid x)=P(\omega_1\mid x)=0.3$.
> Decide $\omega_1$: we are wrong when the truth is $\omega_2$, so $P(\text{error}\mid x)=P(\omega_2\mid x)=0.7$.
> The Bayes rule takes $\omega_2$: $P(\text{error}\mid x)=\min[0.3,0.7]=0.3$.
> Average error: $P(\text{error})=\int P(\text{error}\mid x)\,p(x)\,dx$. The weight $p(x)$ is never negative and does not depend on our decision. So if the factor $P(\text{error}\mid x)$ is as small as possible at every $x$, no other rule can make the integral smaller.

## 8. Actions, loss and conditional risk (slides 19-22)
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p019.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p020.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p021.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p022.jpg]]

**Notes.** Four generalisations: a feature vector $\mathbf{x}\in\mathbb{R}^d$, $c$ classes $\omega_1,\dots,\omega_c$, $a$ actions $\alpha_1,\dots,\alpha_a$ (including rejection), and a loss function. $\lambda(\alpha_i\mid\omega_j)$ is the loss for taking action $\alpha_i$ when the true class is $\omega_j$.

**Conditional risk** (expected loss of action $\alpha_i$ at $\mathbf{x}$):

$$R(\alpha_i\mid\mathbf{x})=\sum_{j=1}^{c}\lambda(\alpha_i\mid\omega_j)P(\omega_j\mid\mathbf{x})$$

For each $\mathbf{x}$ take the action with the smallest conditional risk. A decision rule $\alpha(\mathbf{x})$ gives an action for every $\mathbf{x}$. Its **overall risk** is

$$R=\int R(\alpha(\mathbf{x})\mid\mathbf{x})\,p(\mathbf{x})\,d\mathbf{x}$$

The minimum overall risk is the **Bayes risk**.

> [!example] Red fruit with costs (beyond the slides)
> Orange called apple costs $\lambda_{12}=10$, apple called orange costs $\lambda_{21}=1$. Posteriors $0.8571$, $0.1429$.
> $R(\alpha_1\mid x)=10(0.1429)=1.429$, $R(\alpha_2\mid x)=1(0.8571)=0.857$. Decide orange, although apple is more probable.

> [!warning] Watch out
> In $\lambda_{ij}$ the first index is the action, the second the truth. $\lambda_{12}$: decided $\omega_1$, truth $\omega_2$.

> [!question]- Check (slide 19): Which of the four generalisations does each case need? (a) Iris flowers: 4 measurements, 3 species. (b) A spam filter where deleting a real mail is much worse than letting spam through. (c) A system that may answer "not sure, ask a human".
> (a) More than one feature (4 measurements form a feature vector) and more than two states of nature (3 species).
> (b) A loss function more general than the probability of error: the two kinds of mistake do not cost the same.
> (c) Actions other than deciding the state of nature: "ask a human" is a rejection. It names no class.

> [!question]- Check (slide 20): A problem has $c=3$ classes and $a=4$ actions: decide $\omega_1$, decide $\omega_2$, decide $\omega_3$, reject. How many loss values $\lambda(\alpha_i\mid\omega_j)$ must be given? What does $\lambda(\alpha_4\mid\omega_2)$ mean?
> One value for every pair (action, true class): $a\cdot c=4\cdot3=12$ values. They form a table with 4 rows (actions) and 3 columns (true classes).
> $\lambda(\alpha_4\mid\omega_2)$ is the loss for taking action $\alpha_4$ (reject) when the true state of nature is $\omega_2$.
> The numbers of actions and classes need not be equal. This is new compared with the simple rule "decide the class".

> [!question]- Check (slide 21): Three classes, three actions ($\alpha_i$ = decide $\omega_i$). Posteriors at $\mathbf{x}$: $0.5$, $0.3$, $0.2$. Losses: correct decisions cost 0, $\lambda(\alpha_1\mid\omega_2)=2$, $\lambda(\alpha_1\mid\omega_3)=4$, $\lambda(\alpha_2\mid\omega_1)=1$, $\lambda(\alpha_2\mid\omega_3)=3$, $\lambda(\alpha_3\mid\omega_1)=1$, $\lambda(\alpha_3\mid\omega_2)=1$. Compute the three conditional risks and choose the action.
> Formula: $R(\alpha_i\mid\mathbf{x})=\sum_{j=1}^{3}\lambda(\alpha_i\mid\omega_j)P(\omega_j\mid\mathbf{x})$.
> $R(\alpha_1\mid\mathbf{x})=0(0.5)+2(0.3)+4(0.2)=0+0.6+0.8=1.4$.
> $R(\alpha_2\mid\mathbf{x})=1(0.5)+0(0.3)+3(0.2)=0.5+0+0.6=1.1$.
> $R(\alpha_3\mid\mathbf{x})=1(0.5)+1(0.3)+0(0.2)=0.5+0.3+0=0.8$.
> Smallest risk: $\alpha_3$, decide $\omega_3$. This is the least probable class ($0.2$). It wins because missing $\omega_3$ is expensive (losses 4 and 3), while a wrong "$\omega_3$" costs only 1.

> [!question]- Check (slide 22): The feature takes two values: $P(x=a)=0.7$, $P(x=b)=0.3$. Conditional risks: $R(\alpha_1\mid a)=0.2$, $R(\alpha_2\mid a)=0.9$, $R(\alpha_1\mid b)=1.5$, $R(\alpha_2\mid b)=0.4$. Compute the overall risk of the rule "always $\alpha_1$" and the Bayes risk.
> For a discrete feature the integral becomes a sum: $R=\sum_xR(\alpha(x)\mid x)P(x)$.
> Rule "always $\alpha_1$": $R=R(\alpha_1\mid a)P(a)+R(\alpha_1\mid b)P(b)=(0.2)(0.7)+(1.5)(0.3)=0.14+0.45=0.59$.
> Bayes rule: take the smaller conditional risk at each $x$. At $a$: $\alpha_1$ ($0.2<0.9$). At $b$: $\alpha_2$ ($0.4<1.5$).
> Bayes risk: $R=(0.2)(0.7)+(0.4)(0.3)=0.14+0.12=0.26$.
> A decision rule is a function $\alpha(x)$. It may choose different actions at different $x$. No rule has an overall risk below $0.26$.

## 9. Two categories and the likelihood ratio (slides 23-24)
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p023.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p024.jpg]]

**Notes.** With $\lambda_{ij}=\lambda(\alpha_i\mid\omega_j)$:

$$\begin{aligned}
R(\alpha_1\mid\mathbf{x})&=\lambda_{11}P(\omega_1\mid\mathbf{x})+\lambda_{12}P(\omega_2\mid\mathbf{x})\\
R(\alpha_2\mid\mathbf{x})&=\lambda_{21}P(\omega_1\mid\mathbf{x})+\lambda_{22}P(\omega_2\mid\mathbf{x})
\end{aligned}$$

Decide $\omega_1$ if $R(\alpha_1\mid\mathbf{x})<R(\alpha_2\mid\mathbf{x})$, that is if

$$(\lambda_{21}-\lambda_{11})P(\omega_1\mid\mathbf{x})>(\lambda_{12}-\lambda_{22})P(\omega_2\mid\mathbf{x})$$

Insert Bayes' formula, multiply by $p(\mathbf{x})>0$ and divide by $(\lambda_{21}-\lambda_{11})\,p(\mathbf{x}\mid\omega_2)\,P(\omega_1)$. With $\lambda_{21}>\lambda_{11}$ the direction is kept:

$$\text{decide }\omega_1\iff\frac{p(\mathbf{x}\mid\omega_1)}{p(\mathbf{x}\mid\omega_2)}>\theta=\frac{\lambda_{12}-\lambda_{22}}{\lambda_{21}-\lambda_{11}}\cdot\frac{P(\omega_2)}{P(\omega_1)}$$

The left side is the **likelihood ratio**. The threshold $\theta$ does not depend on $\mathbf{x}$. Costs and priors move only $\theta$. Zero-one loss: $\theta=P(\omega_2)/P(\omega_1)$.

> [!example] Red fruit as a likelihood-ratio test
> Ratio $0.8/0.2=4$. Threshold $\theta=\frac{10-0}{1-0}\cdot\frac{0.4}{0.6}=6.667$. $4<6.667$: orange, as in group 8.

> [!question]- Check (slide 23): Losses $\lambda_{11}=1$, $\lambda_{12}=6$, $\lambda_{21}=3$, $\lambda_{22}=1$ (here a correct decision also costs something). At $\mathbf{x}$: $P(\omega_1\mid\mathbf{x})=0.6$. Compute both conditional risks and decide. Check with the inequality at the bottom of the slide.
> $P(\omega_2\mid\mathbf{x})=1-0.6=0.4$.
> $R(\alpha_1\mid\mathbf{x})=\lambda_{11}P(\omega_1\mid\mathbf{x})+\lambda_{12}P(\omega_2\mid\mathbf{x})=1(0.6)+6(0.4)=0.6+2.4=3.0$.
> $R(\alpha_2\mid\mathbf{x})=\lambda_{21}P(\omega_1\mid\mathbf{x})+\lambda_{22}P(\omega_2\mid\mathbf{x})=3(0.6)+1(0.4)=1.8+0.4=2.2$.
> $2.2<3.0$: decide $\omega_2$, although $\omega_1$ is more probable.
> Check: decide $\omega_1$ if $(\lambda_{21}-\lambda_{11})P(\omega_1\mid\mathbf{x})>(\lambda_{12}-\lambda_{22})P(\omega_2\mid\mathbf{x})$. Left: $(3-1)(0.6)=1.2$. Right: $(6-1)(0.4)=2.0$. $1.2>2.0$ is false, so $\omega_2$. Same result.
> The inequality is $R(\alpha_1\mid\mathbf{x})<R(\alpha_2\mid\mathbf{x})$ with the $P(\omega_1\mid\mathbf{x})$ terms collected on the left and the $P(\omega_2\mid\mathbf{x})$ terms on the right.

> [!question]- Check (slide 24): Same losses ($\lambda_{11}=1$, $\lambda_{12}=6$, $\lambda_{21}=3$, $\lambda_{22}=1$). Compute the threshold $\theta$ for equal priors and for $P(\omega_1)=0.8$. A pattern has likelihood ratio $1.5$. Classify it in both cases.
> Formula: $\theta=\frac{\lambda_{12}-\lambda_{22}}{\lambda_{21}-\lambda_{11}}\cdot\frac{P(\omega_2)}{P(\omega_1)}$. Loss factor: $\frac{6-1}{3-1}=\frac52=2.5$.
> Equal priors: $\theta=2.5\cdot\frac{0.5}{0.5}=2.5$. Ratio $1.5<2.5$: decide $\omega_2$.
> $P(\omega_1)=0.8$: $\theta=2.5\cdot\frac{0.2}{0.8}=2.5\cdot0.25=0.625$. Ratio $1.5>0.625$: decide $\omega_1$.
> The measurement did not change, so the likelihood ratio stayed $1.5$. Only the threshold moved, because $\omega_1$ became more common. This is what "the threshold is independent of the observation $\mathbf{x}$" means.

## 10. Minimum-error-rate classification (slides 25-26)
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p025.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p026.jpg]]

**Notes.** Action $\alpha_i$ = "decide $\omega_i$". **Zero-one loss** (all errors cost the same):

$$\lambda(\alpha_i\mid\omega_j)=\begin{cases}0,& i=j\\ 1,& i\neq j\end{cases}\qquad R(\alpha_i\mid\mathbf{x})=\sum_{j\neq i}P(\omega_j\mid\mathbf{x})=1-P(\omega_i\mid\mathbf{x})$$

So minimum risk = maximum posterior: decide $\omega_i$ if $P(\omega_i\mid\mathbf{x})>P(\omega_j\mid\mathbf{x})$ for all $j\neq i$. The resulting error is the Bayes error.

> [!info] Reject option (beyond the slides)
> If rejecting costs $\lambda_r$ whatever the truth, reject when $1-\max_iP(\omega_i\mid\mathbf{x})>\lambda_r$.

> [!warning] Slide error
> Slide 25: "if action $\alpha_i$ is taken and the true state of nature is $\omega_i$, the decision is correct if $i=j$". It should read $\omega_j$.

> [!question]- Check (slide 25): Zero-one loss, three classes, posteriors at $\mathbf{x}$: $0.2$, $0.5$, $0.3$. Compute the three conditional risks from the sum on the slide. What does $R(\alpha_i\mid\mathbf{x})$ mean in words for this loss?
> With zero-one loss, the term $j=i$ has loss 0 and every other term has loss 1. So $R(\alpha_i\mid\mathbf{x})=\sum_{j\neq i}P(\omega_j\mid\mathbf{x})$.
> $R(\alpha_1\mid\mathbf{x})=0.5+0.3=0.8=1-0.2$.
> $R(\alpha_2\mid\mathbf{x})=0.2+0.3=0.5=1-0.5$.
> $R(\alpha_3\mid\mathbf{x})=0.2+0.5=0.7=1-0.3$.
> In words: the conditional risk is the probability that the decision $\omega_i$ is wrong at this $\mathbf{x}$. The smallest risk ($0.5$, action $\alpha_2$) belongs to the largest posterior ($0.5$, class $\omega_2$).

> [!question]- Check (slide 26): Find the mistake: "The Bayes error is the error of one special classifier. A better classifier, trained with more data on the same features, can get below it."
> The Bayes error is the smallest error that any decision rule can reach for the given class-conditional pdfs and priors. The slide calls it "the best performance that can be achieved".
> It is a property of the problem (how much the classes overlap in the chosen features), not of a learning method. More data only helps a real classifier to come closer to it.
> To get a smaller Bayes error the problem itself must change, for example by measuring better or more features.

## 11. Likelihood-ratio pictures (slides 27-28)
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p027.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p028.jpg]]

**Notes.** Figure 2.3 plots the likelihood ratio $p(x\mid\omega_1)/p(x\mid\omega_2)$ of the fish pdfs, with $P(\omega_1)=2/3$, $P(\omega_2)=1/3$. $R_1$ is where the curve is above the horizontal line at $\theta$.

- Zero-one loss: $\theta_a=\frac{1/3}{2/3}=0.5$. Same boundaries as the posterior plot (Figure 2.2).
- If calling an $\omega_2$ pattern $\omega_1$ costs more ($\lambda_{12}>\lambda_{21}$), the threshold $\theta_b$ is larger and $R_1$ becomes smaller. Example: $\lambda_{12}=2$, $\lambda_{21}=1$ gives $\theta_b=1$.

The fish ratio is not monotone, so one threshold gives several boundaries.

> [!warning] Watch out
> - Two different pictures are both called "Figure 2.1": slides 14-15 (two equiprobable Gaussians) and slides 16, 17, 27 (the fish lightness pdfs).
> - Formula (2.6) needs equal priors and a single threshold. For several regions, integrate over each region.

> [!question]- Check (slide 27): The slide says the threshold $\theta_a$ gives the same decision boundaries as Figure 2.2 "as it must". Show why. Then test it at $x=14$, where $p(x\mid\omega_1)\approx0.18$ and $p(x\mid\omega_2)\approx0.03$.
> Zero-one loss: $\theta_a=P(\omega_2)/P(\omega_1)$. The test "decide $\omega_1$ if $\frac{p(x\mid\omega_1)}{p(x\mid\omega_2)}>\frac{P(\omega_2)}{P(\omega_1)}$" is the same as $p(x\mid\omega_1)P(\omega_1)>p(x\mid\omega_2)P(\omega_2)$ (multiply both sides by $p(x\mid\omega_2)P(\omega_1)>0$).
> Divide both sides by $p(x)$: $P(\omega_1\mid x)>P(\omega_2\mid x)$. This is the rule of Figure 2.2. So the ratio curve crosses $\theta_a$ exactly where the two posterior curves cross.
> At $x=14$: ratio $=0.18/0.03=6$, threshold $\theta_a=\frac{1/3}{2/3}=0.5$. $6>0.5$: $R_1$. Figure 2.2 agrees: $P(\omega_1\mid x)\approx0.92>0.08$.

> [!question]- Check (slide 28): Take $\lambda_{12}=1.4$, $\lambda_{21}=1$, $\lambda_{11}=\lambda_{22}=0$ and the fish priors. Compute $\theta_b$. In Figure 2.2 the posterior of $\omega_1$ has a local peak of about $0.62$ near $x=10.5$. What is the likelihood ratio there, and in which region is $x=10.5$ for $\theta_a$ and for $\theta_b$? How large must $\theta_b$ be to make the middle piece of $R_1$ disappear?
> $\theta_b=\frac{\lambda_{12}-\lambda_{22}}{\lambda_{21}-\lambda_{11}}\cdot\frac{P(\omega_2)}{P(\omega_1)}=\frac{1.4-0}{1-0}\cdot\frac{1/3}{2/3}=1.4\cdot0.5=0.7$.
> From posterior to likelihood ratio: $\frac{P(\omega_1\mid x)}{P(\omega_2\mid x)}=\frac{p(x\mid\omega_1)}{p(x\mid\omega_2)}\cdot\frac{P(\omega_1)}{P(\omega_2)}$. Posterior ratio: $0.62/0.38=1.632$. Prior ratio: $2$. Likelihood ratio: $1.632/2=0.816$.
> $\theta_a=0.5$: $0.816>0.5$, so $x=10.5$ is in $R_1$ (the middle $R_1$ piece of the figure).
> $\theta_b=0.7$: $0.816>0.7$, so $x=10.5$ is still in $R_1$. But the line at $\theta_b$ is higher than the line at $\theta_a$, so it cuts the bump nearer to its top: the middle piece of $R_1$ is narrower. The right piece of $R_1$ also starts later. This is what the slide draws: $\theta_b$ lies a little above $\theta_a$ and below the top of the bump, and $R_1$ becomes smaller. Calling a salmon "sea bass" is now more expensive, so we say "sea bass" less often.
> The middle piece disappears when $\theta_b$ is above the top of the bump: $\theta_b>0.816$, that is $\frac{\lambda_{12}}{\lambda_{21}}>\frac{0.816}{0.5}=1.63$. Example: $\lambda_{12}=2$, $\lambda_{21}=1$ gives $\theta_b=1>0.816$. Then $R_1$ is only the right piece.
> (The values $0.62$ and $0.816$ are read from the plot, so they are approximate. The slide gives no numbers for $\theta_b$.)

## 12. Discriminant functions and decision regions (slides 29-31)
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p029.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p030.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p031.jpg]]

**Notes.** A classifier is a set of **discriminant functions** $g_i(\mathbf{x})$, $i=1,\dots,c$. It assigns $\mathbf{x}$ to $\omega_i$ if $g_i(\mathbf{x})>g_j(\mathbf{x})$ for all $j\neq i$.

- With risks: $g_i(\mathbf{x})=-R(\alpha_i\mid\mathbf{x})$.
- Minimum error rate: $g_i(\mathbf{x})=P(\omega_i\mid\mathbf{x})$.

Replacing every $g_i$ by $f(g_i)$ with $f$ monotonically increasing does not change the decisions. Equivalent choices:

$$g_i(\mathbf{x})=P(\omega_i\mid\mathbf{x}),\qquad g_i(\mathbf{x})=p(\mathbf{x}\mid\omega_i)P(\omega_i),\qquad g_i(\mathbf{x})=\ln p(\mathbf{x}\mid\omega_i)+\ln P(\omega_i)$$

The rule divides feature space into **decision regions** $R_1,\dots,R_c$, separated by **decision boundaries** where the largest discriminants tie. A region need not be in one piece: on slide 31 the boundary is two hyperbolas and $R_2$ is not simply connected.

> [!warning] Slide error
> Slide 30 prints "$g_i(\mathbf{x})=-R(\lambda(\alpha_i\mid\mathbf{x})$". The intended formula is $g_i(\mathbf{x})=-R(\alpha_i\mid\mathbf{x})$.

> [!info] A region in two pieces, in one dimension (beyond the slides)
> Slide 31 only shows the picture. The same effect appears with one feature.
> $\omega_1$: $N(0,1)$, $\omega_2$: $N(0,4)$ (same mean, $\sigma_2=2$), equal priors. Use $g_i(x)=\ln p(x\mid\omega_i)+\ln P(\omega_i)$.
> $\ln p(x\mid\omega_i)=-\ln\sqrt{2\pi}-\ln\sigma_i-\frac{x^2}{2\sigma_i^2}$. Drop $-\ln\sqrt{2\pi}$ and $\ln\tfrac12$, they are the same for both classes.
> $g_1(x)=-\frac{x^2}{2}-\ln1=-\frac{x^2}{2}$ and $g_2(x)=-\frac{x^2}{8}-\ln2$.
> Boundary $g_1=g_2$: $-\frac{x^2}{2}+\frac{x^2}{8}=-\ln2$, so $\frac{3x^2}{8}=\ln2$, $x^2=\frac{8\ln2}{3}=1.848$, $x=\pm1.360$.
> At $x=0$: $g_1=0>g_2=-0.693$, so $\omega_1$ wins in the middle.
> $R_1=(-1.360,\,1.360)$ and $R_2=(-\infty,-1.360)\cup(1.360,\infty)$: two pieces. The wide class wins far away on both sides, the narrow class wins near the centre.
> On slide 31 the two classes also have different covariance matrices. Then the $g_i$ differ in their quadratic terms, the boundary is a curve of second degree (here two hyperbolas), and $R_2$ lies on both outer sides of $R_1$.
> Region and boundary: a decision region is a set of points. A boundary is where the two largest $g_i$ are equal. It has one dimension less (a point in 1-D, a curve in 2-D, a surface in 3-D).

> [!question]- Check (slide 29): For one input the network computes $g_1(\mathbf{x})=-2.1$, $g_2(\mathbf{x})=-0.4$, $g_3(\mathbf{x})=-3.0$. Which class does it output? Is it a problem that all values are negative and do not add to 1?
> The classifier selects the largest discriminant. $-0.4$ is the largest, so the output is $\omega_2$.
> It is not a problem. A discriminant function only has to give the right order of the classes. It need not be a probability. For example $g_i=-R(\alpha_i\mid\mathbf{x})$ or $g_i=\ln p(\mathbf{x}\mid\omega_i)+\ln P(\omega_i)$ are usually negative.

> [!question]- Check (slide 30): All $g_i(\mathbf{x})$ are replaced by $f(g_i(\mathbf{x}))$. For which $f$ do the decisions stay the same? (a) $f(g)=2g+5$. (b) $f(g)=\ln g$, when all $g_i>0$. (c) $f(g)=-g$. (d) $f(g)=g^2$, when some $g_i$ are negative. Test (b) with $g_1=0.02$, $g_2=0.05$.
> The decisions stay the same when $f$ is monotonically increasing: a larger $g$ always gives a larger $f(g)$, so the order is kept.
> (a) Yes. Multiply by a positive constant and add a constant: the two cases named on the slide.
> (b) Yes. $\ln$ is increasing for positive arguments. Test: $\ln0.02=-3.912$, $\ln0.05=-2.996$. Class 2 is the largest before and after.
> (c) No. $-g$ is decreasing. The largest becomes the smallest, so the classifier would pick the worst class.
> (d) No. $g^2$ is not monotone when $g$ can be negative: $g_1=-3$, $g_2=1$ gives $g_1^2=9>g_2^2=1$, the order flips.

> [!question]- Check (slide 31): $\omega_1$: $N(0,1)$, $\omega_2$: $N(0,9)$ (same mean, $\sigma_2=3$), equal priors. Find the decision boundaries and the regions. Is $R_2$ in one piece?
> Log-discriminants without the common terms: $g_1(x)=-\frac{x^2}{2}-\ln1=-\frac{x^2}{2}$ and $g_2(x)=-\frac{x^2}{2\cdot9}-\ln3=-\frac{x^2}{18}-\ln3$.
> Boundary $g_1=g_2$: $-\frac{x^2}{2}+\frac{x^2}{18}=-\ln3$. Left side: $-\frac{9x^2}{18}+\frac{x^2}{18}=-\frac{8x^2}{18}=-\frac{4x^2}{9}$.
> So $\frac{4x^2}{9}=\ln3=1.0986$, $x^2=\frac{9(1.0986)}{4}=2.472$, $x=\pm1.572$.
> Which side: at $x=0$, $g_1=0>g_2=-1.099$, so $\omega_1$ in the middle. At $x=2$: $g_1=-2$, $g_2=-\frac{4}{18}-1.099=-1.321$, so $\omega_2$ outside.
> $R_1=(-1.572,\,1.572)$, $R_2=(-\infty,-1.572)\cup(1.572,\infty)$. $R_2$ is not in one piece: it has two separate parts, one on each side of $R_1$. The picture on the slide shows the same in 2-D: $R_1$ is the band between the two hyperbola branches, and $R_2$ is the two outer parts (left and right), which do not touch. The slide calls this "not simply connected". Here it means: $R_2$ is not one connected piece.

## 13. The normal density (slides 32-35)
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p032.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p033.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p034.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p035.jpg]]

**Notes.** The Gaussian is analytically tractable, and it fits the case where the feature vectors of a class are randomly corrupted versions of one prototype vector $\boldsymbol\mu_i$ (Central Limit Theorem).

Univariate, $p(x)\sim N(\mu,\sigma^2)$, with mean $\mu$ and variance $\sigma^2$:

$$p(x)=\frac{1}{\sqrt{2\pi}\,\sigma}\exp\left[-\frac12\left(\frac{x-\mu}{\sigma}\right)^2\right]$$

Multivariate in $d$ dimensions, $p(\mathbf{x})\sim N(\boldsymbol\mu,\Sigma)$:

$$p(\mathbf{x})=\frac{1}{(2\pi)^{d/2}|\Sigma|^{1/2}}\exp\left[-\frac12(\mathbf{x}-\boldsymbol\mu)^{\mathsf T}\Sigma^{-1}(\mathbf{x}-\boldsymbol\mu)\right]$$

$\boldsymbol\mu$ is the mean vector (centre of the cloud). $\Sigma$ is the $d\times d$ covariance matrix (shape of the cloud), $|\Sigma|$ its determinant. The curves of equal density are ellipses. The quadratic form in the exponent is the **squared Mahalanobis distance**.

> [!warning] Slide rounding
> The figure on slide 33 marks 2.5% in each tail beyond $\mu\pm2\sigma$. The exact value is $1-\Phi(2)=2.28\%$. The 2.5% tails start at $\mu\pm1.96\sigma$.

> [!info] Reading the multivariate formula (beyond the slides)
> Slide 34 gives the formula without an example. How to read it:
> - $\mathcal{E}[\cdot]$ is the expected value (the average). $\Sigma$ is a matrix here, not the summation sign.
> - $\Sigma$ for $d=2$: the diagonal entries are the variances of $x_1$ and $x_2$. The off-diagonal entry is the covariance of $x_1$ and $x_2$ (the matrix is symmetric). Covariance $0$: the ellipses are parallel to the axes. Positive covariance: the cloud is tilted up to the right, as on slide 35. Negative: tilted down to the right.
> - Sizes: $(\mathbf{x}-\boldsymbol\mu)^{\mathsf T}$ is a row ($1\times d$), $\Sigma^{-1}$ is $d\times d$, $(\mathbf{x}-\boldsymbol\mu)$ is a column ($d\times1$). The product is one number, the squared Mahalanobis distance. It is the distance from $\boldsymbol\mu$ measured in units of the spread. All points on one red ellipse of slide 35 have the same Mahalanobis distance and the same density.
> - For a $2\times2$ matrix with rows $(a,b)$ and $(b,c)$: determinant $ac-b^2$. Inverse: swap $a$ and $c$, change the sign of $b$, divide every entry by the determinant.
>
> Worked example: $\boldsymbol\mu=[0,0]^{\mathsf T}$, $\Sigma$ with rows $(1,\,0.5)$ and $(0.5,\,1)$, $\mathbf{x}=[1,1]^{\mathsf T}$.
> Determinant: $\lvert\Sigma\rvert=1\cdot1-0.5^2=0.75$.
> Inverse: rows $(1,-0.5)/0.75$ and $(-0.5,1)/0.75$, that is $(1.333,-0.667)$ and $(-0.667,1.333)$.
> $\Sigma^{-1}(\mathbf{x}-\boldsymbol\mu)$: first entry $1.333(1)-0.667(1)=0.667$, second entry $-0.667(1)+1.333(1)=0.667$.
> Squared Mahalanobis distance: $1(0.667)+1(0.667)=1.333$.
> Constant ($d=2$): $\frac{1}{(2\pi)^{1}\sqrt{0.75}}=\frac{1}{2\pi(0.8660)}=0.1838$.
> $p(\mathbf{x})=0.1838\,e^{-1.333/2}=0.1838\cdot0.5134=0.0944$. At the mean the exponent is 0 and $p(\boldsymbol\mu)=0.1838$, the largest value.

> [!question]- Check (slide 32): The slide gives two reasons for using the Gaussian. Name them. Then look at $p(x\mid\omega_1)$ of the fish on slide 16: would one Gaussian be a good model for it?
> Reason 1: analytical tractability, the formulas can be worked out in closed form. Reason 2: it fits a class whose feature vectors are randomly corrupted versions of one prototype vector $\boldsymbol\mu_i$.
> The sea bass pdf on slide 16 has two peaks (near $x=10.8$ and near $x=13$). A Gaussian has one peak and is symmetric around it. So one Gaussian would be a poor model. The picture looks like two prototypes, not one.

> [!question]- Check (slide 33): $x\sim N(20,4^2)$, so $\mu=20$, $\sigma=4$. Compute $p(26)$. What fraction of the samples lies between 16 and 24? What fraction lies above 28?
> $p(26)$: $\frac{x-\mu}{\sigma}=\frac{26-20}{4}=1.5$. Exponent: $-\frac12(1.5)^2=-1.125$. Constant: $\frac{1}{\sqrt{2\pi}\cdot4}=\frac{1}{10.027}=0.09974$.
> $p(26)=0.09974\,e^{-1.125}=0.09974\cdot0.3247=0.0324$.
> Between 16 and 24: these are $\mu\pm\sigma$ ($z=\pm1$). Fraction $=\Phi(1)-\Phi(-1)=0.84134-0.15866=0.6827$.
> Above 28: $z=\frac{28-20}{4}=2$, so the fraction is $1-\Phi(2)=0.0228$. This is the red tail of the slide's figure (printed there as 2.5%, see the warning above).

> [!question]- Check (slide 34): $d=2$, $\boldsymbol\mu=[0,0]^{\mathsf T}$, $\Sigma$ with rows $(2,-1)$ and $(-1,1)$. Compute $\lvert\Sigma\rvert$, $\Sigma^{-1}$, the squared Mahalanobis distance of $\mathbf{x}=[1,1]^{\mathsf T}$ and $p(\mathbf{x})$.
> Determinant: $\lvert\Sigma\rvert=2\cdot1-(-1)^2=2-1=1$.
> Inverse: swap the diagonal entries, change the sign of the off-diagonal entries, divide by 1: rows $(1,1)$ and $(1,2)$.
> $\mathbf{x}-\boldsymbol\mu=[1,1]^{\mathsf T}$. $\Sigma^{-1}(\mathbf{x}-\boldsymbol\mu)$: first entry $1(1)+1(1)=2$, second entry $1(1)+2(1)=3$.
> Squared Mahalanobis distance: $(\mathbf{x}-\boldsymbol\mu)^{\mathsf T}[2,3]^{\mathsf T}=1(2)+1(3)=5$.
> Constant: $\frac{1}{(2\pi)^{2/2}\lvert\Sigma\rvert^{1/2}}=\frac{1}{2\pi\cdot1}=0.1592$.
> $p(\mathbf{x})=0.1592\,e^{-5/2}=0.1592\cdot0.0821=0.0131$.
> The covariance is negative, so the cloud is tilted down to the right. The point $[1,1]^{\mathsf T}$ lies across the tilt, so its Mahalanobis distance is large.

> [!question]- Check (slide 35): Point A lies on the long axis and point B on the short axis of the same red ellipse. Which point is farther from $\boldsymbol\mu$ in ordinary (Euclidean) distance? Which has the higher density? What does the tilt of the cloud say about $\Sigma$?
> A is farther from $\boldsymbol\mu$ in Euclidean distance, because the long axis is longer.
> The densities are equal. A red ellipse is a line of equal probability density, so every point on it has the same $p(\mathbf{x})$ and the same Mahalanobis distance.
> The cloud goes up to the right: when $x_1$ is above its mean, $x_2$ is usually above its mean too. So the off-diagonal entry of $\Sigma$ (the covariance of $x_1$ and $x_2$) is positive. The mean vector only moves the cloud. The covariance matrix sets its shape and direction.

## 14. Discriminant functions for the normal density (slides 36-37)
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p036.jpg]]
![[ee525_lecture_02_lecture_2_bayesian_classifiers_p037.jpg]]

**Notes.** With $g_i(\mathbf{x})=\ln p(\mathbf{x}\mid\omega_i)+\ln P(\omega_i)$ and $p(\mathbf{x}\mid\omega_i)\sim N(\boldsymbol\mu_i,\Sigma_i)$:

$$g_i(\mathbf{x})=-\frac12(\mathbf{x}-\boldsymbol\mu_i)^{\mathsf T}\Sigma_i^{-1}(\mathbf{x}-\boldsymbol\mu_i)-\frac d2\ln2\pi-\frac12\ln|\Sigma_i|+\ln P(\omega_i)$$

The term $-\frac d2\ln2\pi$ is the same for all classes and can be dropped.

If all covariances are equal and proportional to the identity ($\Sigma_i=\sigma^2I$), the classes are spherical and the boundary is a hyperplane perpendicular to the line joining the means. For two classes:

$$(\boldsymbol\mu_1-\boldsymbol\mu_2)^{\mathsf T}\mathbf{x}=\tfrac12\left(\|\boldsymbol\mu_1\|^2-\|\boldsymbol\mu_2\|^2\right)-\sigma^2\ln\frac{P(\omega_1)}{P(\omega_2)}$$

With equal priors it is the perpendicular bisector of the means (the nearest-mean classifier of [[EE525 L01 - Introduction to pattern recognition]]). The slide's pictures use $P(\omega_1)=P(\omega_2)$. The general cases follow in [[EE525 L04 - Minimum risk and discriminant functions]].

> [!info] Where the two formulas come from (beyond the slides)
> **The Gaussian discriminant (slide 36).** Take the logarithm of the multivariate normal density. The logarithm of a product is a sum, and $\ln e^{u}=u$:
> $$\ln p(\mathbf{x}\mid\omega_i)=-\frac12(\mathbf{x}-\boldsymbol\mu_i)^{\mathsf T}\Sigma_i^{-1}(\mathbf{x}-\boldsymbol\mu_i)-\ln(2\pi)^{d/2}-\ln\lvert\Sigma_i\rvert^{1/2}$$
> With $\ln(2\pi)^{d/2}=\frac d2\ln2\pi$ and $\ln\lvert\Sigma_i\rvert^{1/2}=\frac12\ln\lvert\Sigma_i\rvert$, and after adding $\ln P(\omega_i)$, this is the formula on the slide.
> For one feature ($d=1$, $\Sigma_i=\sigma_i^2$): $g_i(x)=-\frac12\left(\frac{x-\mu_i}{\sigma_i}\right)^2-\ln\sigma_i+\ln P(\omega_i)$, without the common term $-\frac12\ln2\pi$.
>
> **The hyperplane (slide 37).** Let $\Sigma_i=\sigma^2I$ for all classes. Then $\Sigma_i^{-1}=I/\sigma^2$, and $\ln\lvert\Sigma_i\rvert$ is the same for all classes and can be dropped:
> $$g_i(\mathbf{x})=-\frac{\|\mathbf{x}-\boldsymbol\mu_i\|^2}{2\sigma^2}+\ln P(\omega_i)$$
> Expand $\|\mathbf{x}-\boldsymbol\mu_i\|^2=\|\mathbf{x}\|^2-2\boldsymbol\mu_i^{\mathsf T}\mathbf{x}+\|\boldsymbol\mu_i\|^2$. The term $\|\mathbf{x}\|^2$ is the same for all classes and can be dropped too:
> $$g_i(\mathbf{x})=\frac{\boldsymbol\mu_i^{\mathsf T}\mathbf{x}}{\sigma^2}-\frac{\|\boldsymbol\mu_i\|^2}{2\sigma^2}+\ln P(\omega_i)$$
> This is linear in $\mathbf{x}$. Set $g_1(\mathbf{x})=g_2(\mathbf{x})$ and multiply by $\sigma^2$: the result is the boundary equation above. Its normal vector is $\boldsymbol\mu_1-\boldsymbol\mu_2$, so the boundary is perpendicular to the line that joins the means.
> With equal priors, $g_i$ is largest for the smallest $\|\mathbf{x}-\boldsymbol\mu_i\|$: assign $\mathbf{x}$ to the nearest mean.

> [!question]- Check (slide 36): One feature. $\omega_1$: $N(0,1)$ with $P(\omega_1)=0.6$. $\omega_2$: $N(2,2^2)$ with $P(\omega_2)=0.4$. Compute $g_1$ and $g_2$ at $x=1$ and classify. Which term of the slide's formula may be left out, and why not $\frac12\ln\lvert\Sigma_i\rvert$?
> For $d=1$: $g_i(x)=-\frac12\left(\frac{x-\mu_i}{\sigma_i}\right)^2-\ln\sigma_i+\ln P(\omega_i)$, because $\frac12\ln\lvert\Sigma_i\rvert=\frac12\ln\sigma_i^2=\ln\sigma_i$.
> $g_1(1)=-\frac12\left(\frac{1-0}{1}\right)^2-\ln1+\ln0.6=-0.5-0-0.5108=-1.0108$.
> $g_2(1)=-\frac12\left(\frac{1-2}{2}\right)^2-\ln2+\ln0.4=-0.125-0.6931-0.9163=-1.7344$.
> $g_1>g_2$: decide $\omega_1$. The point $x=1$ is nearer to $\mu_2$ in units of $\sigma$ ($0.5$ against $1$), but $\omega_2$ loses because of the terms $-\ln\sigma_2$ (a wide density is low everywhere) and $\ln P(\omega_2)$ (smaller prior).
> The term $-\frac d2\ln2\pi$ may be left out: it is the same number for every class. $\frac12\ln\lvert\Sigma_i\rvert$ must stay when the classes have different covariances, as here ($\ln1=0$ against $\ln2=0.6931$).

> [!question]- Check (slide 37): Two classes in 2-D with $\Sigma_1=\Sigma_2=2I$ ($\sigma^2=2$), $\boldsymbol\mu_1=[0,0]^{\mathsf T}$, $\boldsymbol\mu_2=[4,0]^{\mathsf T}$. Find the boundary for equal priors and for $P(\omega_1)=0.75$. Classify $\mathbf{x}=[2.3,\,7]^{\mathsf T}$ in both cases.
> Pieces: $\boldsymbol\mu_1-\boldsymbol\mu_2=[-4,0]^{\mathsf T}$, so $(\boldsymbol\mu_1-\boldsymbol\mu_2)^{\mathsf T}\mathbf{x}=-4x_1+0\cdot x_2=-4x_1$. $\|\boldsymbol\mu_1\|^2=0$, $\|\boldsymbol\mu_2\|^2=4^2+0^2=16$, so $\tfrac12(0-16)=-8$.
> Boundary: $-4x_1=-8-2\ln\frac{P(\omega_1)}{P(\omega_2)}$. Divide by $-4$: $x_1=2+\tfrac12\ln\frac{P(\omega_1)}{P(\omega_2)}$.
> Side of $\omega_1$: $\boldsymbol\mu_1$ has $x_1=0$, so $R_1$ is the side with the smaller $x_1$.
> Equal priors: $\ln1=0$, boundary $x_1=2$. This is the perpendicular bisector of the means. $2.3>2$: decide $\omega_2$.
> $P(\omega_1)=0.75$: $\frac{0.75}{0.25}=3$, $\ln3=1.0986$, boundary $x_1=2+0.5493=2.549$. $2.3<2.549$: decide $\omega_1$.
> The boundary is a vertical line, perpendicular to the line that joins the means (the $x_1$ axis). So the value $x_2=7$ plays no role. A larger prior for $\omega_1$ moves the line towards $\boldsymbol\mu_2$ and $R_1$ grows.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Total probability | $P(x)=\sum_iP(x\mid\omega_i)P(\omega_i)$ |
| Bayes' theorem | $P(\omega_i\mid\mathbf{x})=p(\mathbf{x}\mid\omega_i)P(\omega_i)/p(\mathbf{x})$ |
| Bayes classifier | largest posterior, same as largest $p(\mathbf{x}\mid\omega_i)P(\omega_i)$ |
| Priors from data | $P(\omega_i)\approx N_i/N$ |
| Error, equal priors | $P_e=\frac12\int_{-\infty}^{x_0}p(x\mid\omega_2)dx+\frac12\int_{x_0}^{\infty}p(x\mid\omega_1)dx$ |
| Conditional error | $P(\text{error}\mid x)=\min_iP(\omega_i\mid x)$ (two classes) |
| Loss | $\lambda_{ij}=\lambda(\alpha_i\mid\omega_j)$: action $i$, truth $j$ |
| Conditional risk | $R(\alpha_i\mid\mathbf{x})=\sum_j\lambda(\alpha_i\mid\omega_j)P(\omega_j\mid\mathbf{x})$ |
| Bayes risk | minimum of the overall risk $R=\int R(\alpha(\mathbf{x})\mid\mathbf{x})p(\mathbf{x})d\mathbf{x}$ |
| Likelihood-ratio test | $\frac{p(\mathbf{x}\mid\omega_1)}{p(\mathbf{x}\mid\omega_2)}>\frac{\lambda_{12}-\lambda_{22}}{\lambda_{21}-\lambda_{11}}\frac{P(\omega_2)}{P(\omega_1)}$, needs $\lambda_{21}>\lambda_{11}$ |
| Zero-one loss | $R(\alpha_i\mid\mathbf{x})=1-P(\omega_i\mid\mathbf{x})$ |
| Discriminant | decide $\omega_i$ if $g_i(\mathbf{x})>g_j(\mathbf{x})$ for all $j\neq i$ |
| Gaussian discriminant | $g_i=-\frac12(\mathbf{x}-\boldsymbol\mu_i)^{\mathsf T}\Sigma_i^{-1}(\mathbf{x}-\boldsymbol\mu_i)-\frac12\ln\lvert\Sigma_i\rvert+\ln P(\omega_i)$ |
| Bayes error and Bayes risk | they coincide only under the zero-one loss |

## Check yourself
> [!question]- 1. Basket with 30% apples, 70% oranges, $P(\text{red}\mid\text{apple})=0.8$, $P(\text{red}\mid\text{orange})=0.2$. Find $P(\text{red})$ and classify a red fruit.
> $P(\text{red})=(0.8)(0.3)+(0.2)(0.7)=0.24+0.14=0.38$.
> $P(\text{apple}\mid\text{red})=0.24/0.38=0.6316$, $P(\text{orange}\mid\text{red})=0.3684$. Decision: apple.

> [!question]- 2. Two-feature fruit table with equal priors. Which decisions change, and what is the error?
> Joints $(J_1,J_2)$: $[0,0]$: $(0.025,0.050)$ orange. $[0,1]$: $(0.050,0.350)$ orange. $[1,0]$: $(0.375,0.025)$ apple. $[1,1]$: $(0.050,0.075)$ orange.
> Only $[1,1]$ changes, from a tie to orange. $P(\text{correct})=0.050+0.350+0.375+0.075=0.85$, error $0.15$.

> [!question]- 3. $\omega_1$: $N(1,1)$, $\omega_2$: $N(4,1)$. Threshold and error for equal priors, then for $P(\omega_1)=0.75$.
> Equal priors: $x_0=2.5$, $P_e=1-\Phi(1.5)=0.0668$.
> $P(\omega_1)=0.75$: $\frac{15-6x}{2}>-\ln3$ gives $x<2.5+\frac{\ln3}{3}=2.866$. $P_e=0.75(0.0310)+0.25(0.1284)=0.0554$. The threshold moved towards the mean of $\omega_2$.

> [!question]- 4. Tumour ($\omega_1$) or healthy ($\omega_2$). $\lambda_{11}=\lambda_{22}=0$, false alarm $\lambda_{12}=1$, missed tumour $\lambda_{21}=10$, $P(\omega_1\mid\mathbf{x})=0.2$. Decide. For which posteriors do we call tumour?
> $R(\alpha_1\mid\mathbf{x})=1(0.8)=0.8$, $R(\alpha_2\mid\mathbf{x})=10(0.2)=2.0$. Call tumour.
> In general call tumour when $1-P<10P$, that is $P>1/11=0.091$.

> [!question]- 5. Fruit table, priors 0.6 / 0.4, $\lambda_{12}=3$, $\lambda_{21}=1$, $\lambda_{11}=\lambda_{22}=0$. Threshold, decisions, Bayes risk?
> $\theta=\frac{3}{1}\cdot\frac{0.4}{0.6}=2$. Ratios: $0.5$, $0.143$, $15$, $0.667$. Only $[1,0]$ is apple.
> Expected losses (say apple $1.2\,P(\mathbf{x}\mid\omega_2)$, say orange $0.6\,P(\mathbf{x}\mid\omega_1)$), minima $0.03+0.06+0.06+0.06=0.21$. Bayes risk $0.21$.

> [!question]- 6. $x\in\{1,2,3\}$, priors 0.7 / 0.3, $P(x\mid\omega_1)=0.6,0.3,0.1$, $P(x\mid\omega_2)=0.1,0.3,0.6$. Minimum-error decisions and Bayes error?
> $J_1=0.42,0.21,0.07$; $J_2=0.03,0.09,0.18$. Evidence $0.45,0.30,0.25$. $P(\omega_1\mid x)=0.933,0.700,0.280$.
> Decide $\omega_1,\omega_1,\omega_2$. Bayes error $=0.03+0.09+0.07=0.19$.

## Links
- Course index: [[EE525 Home]]
- Previous: [[EE525 L01 - Introduction to pattern recognition]]. Next: [[EE525 L03 - Iris example analysis]] (this theory as a MATLAB script).
- Full step-by-step notes (PDF): [EE525_Lecture_02_Bayesian_Classifiers.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/ee_525/lecture_notes/lecture_02/EE525_Lecture_02_Bayesian_Classifiers.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lecture_notes/lecture_02/EE525_Lecture_02_Bayesian_Classifiers.pdf`
- Original slides (PDF): [Lecture 2- Bayesian Classifiers.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_02/Lecture%202-%20Bayesian%20Classifiers.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_02/Lecture 2- Bayesian Classifiers.pdf`
- Quiz: [[EE525 L02 - Bayesian classifiers - quiz]]

Slides not embedded: 1 (title).
