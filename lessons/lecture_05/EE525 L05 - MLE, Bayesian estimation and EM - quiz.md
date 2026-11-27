---
course: EE 525 Pattern Recognition
lesson: EE525 L05 - MLE, Bayesian estimation and EM
type: quiz
questions: 21
---

# EE525 L05 - MLE, Bayesian estimation and EM - quiz

Lesson note: [[EE525 L05 - MLE, Bayesian estimation and EM]]. One question per check question of the lesson. Pick an option, then open the answer.

Useful constants: $\sqrt{2\pi}=2.5066$, $\frac{1}{\sqrt{2\pi}}=0.3989$. "ML" means maximum likelihood.

## 1. Why the parameters must be estimated (deck A, pages 1-2)

### Q1 (deck A, slide 1)

Each sample has $d=4$ features. We assume a Gaussian density $\mathcal N(\boldsymbol\mu_i,\Sigma_i)$ for one class, with a full covariance matrix. How many different numbers must be estimated for this class?

- A) $8$
- B) $14$
- C) $20$
- D) $10$

> [!success]- Answer
> **B.** Mean vector: $d=4$ numbers. The covariance matrix is symmetric, so it has $\frac{d(d+1)}{2}=\frac{4\cdot5}{2}=10$ different entries. Total $4+10=14$.
> Without the Gaussian assumption the unknown is a whole function on a 4-dimensional space. With it, the samples only have to fix 14 numbers.
> - A: $4+4=8$ counts only the 4 variances (a diagonal covariance). A full covariance also has the 6 covariances.
> - C: $4+16=20$ counts all $4\times4$ entries. The symmetric pairs $\Sigma_{jk}=\Sigma_{kj}$ are counted twice.
> - D: $10$ is the covariance alone. The 4 entries of the mean are forgotten.

### Q2 (deck A, slide 2)

The Gaussian density is $p(x\mid\mu,\sigma^2)=\frac{1}{\sqrt{2\pi\sigma^2}}\exp\left(-\frac{(x-\mu)^2}{2\sigma^2}\right)$. Evaluate it at $x=4.8$ (petal length in cm) for $\mu=4.3$, $\sigma^2=0.25$. What is the value, and what does it mean?

- A) $0.4839$. It is the probability that a flower has petal length exactly $4.8$ cm, so about half of the flowers have it.
- B) $0.9679$. It is a density value (probability per cm), not a probability.
- C) $0.2935$. It is a density value (probability per cm), not a probability.
- D) $0.4839$. It is a density value; the probability of a small interval of width $0.1$ around $4.8$ is about $0.048$.

> [!success]- Answer
> **D.** Front factor $\frac{1}{\sqrt{2\pi(0.25)}}=\frac{1}{0.5\times2.5066}=0.7979$. Exponent $-\frac{(0.5)^2}{2(0.25)}=-0.5$, $e^{-0.5}=0.6065$.
> Density $0.7979\times0.6065=0.4839$. A probability is an area: $0.4839\times0.1\approx0.048$.
> - A: The number is right, but a density is not a probability. The probability of one exact value is 0.
> - B: The front factor uses $\sigma^2=0.25$ in place of $\sigma=0.5$: $\frac{1}{0.25\times2.5066}\times0.6065=0.9679$.
> - C: The 2 in the exponent is forgotten: $0.7979\times e^{-1}=0.2935$.

## 2. Likelihood and the MLE of a Gaussian (deck A, pages 3-5)

### Q3 (deck A, slide 3)

![[ee525_lecture_05_maximum_likelihood_and_bayesian_parameter_estimation_p003.jpg]]

In Figure 3.1 the bottom curve $p(\mathcal D\mid\theta)$ has its peak near $\theta=3.1$, with a height of about $1.5\times10^{-7}$. What is the MLE, why is the height so small, and is the area under this curve equal to 1?

- A) $\hat\theta\approx3.1$. The height is small because it is a product of about a dozen density values below 1. The area is not 1.
- B) $\hat\theta\approx3.1$. The height is small because it is the probability that $\theta$ equals $3.1$. The area is 1.
- C) $\hat\theta\approx1.5\times10^{-7}$, the height of the peak. The height is a product of densities. The area is not 1.
- D) $\hat\theta\approx3.1$. The height is small because it is a product of about a dozen density values below 1. The area is 1, because each factor has area 1.

> [!success]- Answer
> **A.** The MLE is the position of the peak, $\hat\theta\approx3.1$ (the sample mean of the dots). The height does not matter.
> The likelihood multiplies one density value per point; for example $0.27^{12}\approx1.5\times10^{-7}$. This is why we use the log-likelihood.
> The curve is a function of $\theta$ with the data fixed. It is not a density of $\theta$, so its area need not be 1.
> - B: The likelihood is not a probability of $\theta$. MLE gives no probability for $\theta$.
> - C: The MLE is a value of $\theta$ (where the peak is), not the height of the peak.
> - D: Each factor has area 1 as a function of $x$. The curve is a function of $\theta$, and a product of such factors has no reason to have area 1.

### Q4 (deck A, slide 4)

For $N=50$ flowers the script prints the ML variance $0.2164$ from `mean((X-mu_ML).^2)` (divisor $N$). A student replaces this by `var(X)`, called with MATLAB's default normalisation (no second argument). What does the script print now, and is it still the MLE?

- A) $0.2164$. Yes, `var` also divides by $N$.
- B) $0.2121$. No, `var` is the unbiased variance.
- C) $0.2208$. No, `var` is the unbiased variance.
- D) $0.2208$. Yes, both values maximise the likelihood.

> [!success]- Answer
> **C.** Sum of squared deviations $50\times0.2164=10.82$. `var(X)` divides by $N-1$: $\frac{10.82}{49}=0.2208$.
> This is the unbiased sample variance, not the MLE. The MLE divides by $N$.
> - A: MATLAB `var(X)` divides by $N-1$. Only `var(X,1)` divides by $N$.
> - B: The factor is applied the wrong way: $0.2164\times\frac{49}{50}=0.2121$. Dividing by $N-1$ gives a larger value.
> - D: The number is right, but the likelihood has its maximum only at the value with divisor $N$.

### Q5 (deck A, slide 5)

![[ee525_lecture_05_maximum_likelihood_and_bayesian_parameter_estimation_p005.jpg]]

The histogram is pdf-normalised (total area 1) and uses all $N=50$ flowers. The bar from $4.5$ to $5$ cm has height $0.76$. How many flowers are in this bin?

- A) $38$
- B) $19$
- C) $76$
- D) $0.38$

> [!success]- Answer
> **B.** Height $=\frac{\text{count}}{N\times\text{width}}$, so count $=0.76\times50\times0.5=19$.
> Check with all bars: heights $0.12,0.32,0.72,0.76,0.08$ give $3+8+18+19+2=50$.
> - A: The bin width is forgotten: $0.76\times50=38$.
> - C: Divided by the width instead of multiplying: $0.76\times50/0.5=76$.
> - D: $0.76\times0.5=0.38$ is the probability of the bin, not the number of flowers. $N$ is forgotten.

## 3. Bayesian estimation of a Gaussian mean (deck A, pages 6-8)

### Q6 (deck A, slide 6)

Known variance $\sigma^2=0.25$, prior $\mu\sim\mathcal N(\mu_0,\tau_0^2)$ with $\mu_0=4.0$, data $N=5$, $\bar x=4.2$. The posterior mean is $\mu_{Bayes}=\dfrac{\frac{N}{\sigma^2}\bar x+\frac{1}{\tau_0^2}\mu_0}{\frac{N}{\sigma^2}+\frac{1}{\tau_0^2}}$. What is $\mu_{Bayes}$ for $\tau_0^2=2.5$ and for $\tau_0^2=0.025$ (in this order)?

- A) $4.1961$ and $4.0667$
- B) $4.0667$ and $4.1961$
- C) $4.1778$ and $4.1998$
- D) $4.0039$ and $4.1333$

> [!success]- Answer
> **A.** Data precision $\frac{5}{0.25}=20$, $20\times4.2=84$.
> $\tau_0^2=2.5$: prior precision $0.4$, $\mu_{Bayes}=\frac{84+0.4(4.0)}{20.4}=\frac{85.6}{20.4}=4.1961$ (weak prior, near $\bar x$).
> $\tau_0^2=0.025$: prior precision $40$, $\mu_{Bayes}=\frac{84+160}{60}=4.0667$ (strong prior, near $\mu_0$).
> - B: The two results are swapped. A large prior variance means a weak prior, so the estimate moves toward $\bar x$.
> - C: $\tau_0^2$ itself is used as the prior precision: $\frac{84+2.5(4)}{22.5}=4.1778$ and $\frac{84+0.025(4)}{20.025}=4.1998$.
> - D: The weights are put on the wrong means: $\frac{20(4.0)+0.4(4.2)}{20.4}=4.0039$ and $\frac{20(4.0)+40(4.2)}{60}=4.1333$.

### Q7 (deck A, slide 7)

The code has `idx = randperm(length(X)); data = X(idx(1:N)); mu_ML = mean(data);` and `posteriorVariance = 1/(N/sigma2 + 1/tau2)` with $N=5$, $\sigma^2=0.25$, $\tau_0^2=0.25$. Two runs pick two different random sets of 5 flowers. What happens to the posterior variance, and which line decides which flowers are used?

- A) The posterior variance differs between the two sets, because each set has its own spread. The flowers are chosen by `randperm` and `X(idx(1:N))`.
- B) Both sets give the same posterior variance $\sigma^2/N=0.05$; only the posterior mean changes. The flowers are chosen by `randperm` and `X(idx(1:N))`.
- C) Both sets give the same posterior variance $1/24=0.0417$; only the posterior mean changes. The flowers are chosen by `mu_ML = mean(data)`.
- D) Both sets give the same posterior variance $1/24=0.0417$; only the posterior mean changes. The flowers are chosen by `randperm` and `X(idx(1:N))`.

> [!success]- Answer
> **D.** The posterior variance uses only $N$, $\sigma^2$ and $\tau_0^2$: $\frac{1}{20+4}=0.0417$, SD $\sqrt{0.0417}=0.2041$. $\sigma^2$ is assumed known, so the data are not needed for the spread.
> Only the posterior mean changes, through `mu_ML`. `randperm` shuffles the indices and `X(idx(1:N))` takes the first $N$.
> - A: The formula does not contain `data`, so the spread of the set cannot enter.
> - B: $0.25/5=0.05$ forgets the prior precision $1/\tau_0^2=4$ in the denominator.
> - C: `mean(data)` uses the flowers that were already chosen. It does not choose them.

### Q8 (deck A, slide 8)

Here $\tau_0^2=\sigma^2=0.25$. When the prior mean changes by $\Delta=1.0$ (from $4.0$ to $5.0$), the Bayesian mean moves by $0.1667$ for $N=5$ and by $0.0196$ for $N=50$. Which factor times $\Delta$ gives these moves, and what is the smallest $N$ for which the move is below $0.01$?

- A) Factor $\frac{1}{N+1}$; smallest $N=99$.
- B) Factor $\frac{1}{N}$; smallest $N=101$.
- C) Factor $\frac{1}{N+1}$; smallest $N=100$.
- D) Factor $\frac{1}{\sqrt{N+1}}$; smallest $N=10000$.

> [!success]- Answer
> **C.** Shrinkage form $\mu_N=w_N\bar x+(1-w_N)\mu_0$ with $w_N=\frac{N\tau_0^2}{N\tau_0^2+\sigma^2}=\frac{N}{N+1}$. A change $\Delta$ of $\mu_0$ moves $\mu_N$ by $\frac{\Delta}{N+1}$.
> Check: $\frac16=0.1667$, $\frac{1}{51}=0.0196$. Below $0.01$: $N+1>100$, so $N=100$ ($\frac{1}{101}=0.0099$).
> - A: $N=99$ gives $\frac{1}{100}=0.01$, which is not below $0.01$.
> - B: $\frac{1}{N}$ gives $\frac15=0.2$ for $N=5$, not $0.1667$. It does not fit the numbers.
> - D: $\frac{1}{\sqrt6}=0.408$ for $N=5$, not $0.1667$. This is how a standard deviation shrinks, not the weight of the prior.

## 4. MLE against Bayes, histogram against fitted Gaussian (deck A, pages 9-10)

### Q9 (deck A, slide 9)

Find the mistake: "A pdf-normalised histogram bar of height $1.6$ is impossible, because a probability cannot be larger than 1." Use $N=5$ samples, bin width $0.5$, 4 samples in the bin.

- A) The height is a density, not a probability. Height $\frac{4}{5\times0.5}=1.6$ is possible; the bin probability is $1.6\times0.5=0.8$.
- B) The statement is right: with pdf normalisation each height is count$/N$, so the bar must be $\frac45=0.8$.
- C) The height is a density, but the bin probability is height$/N=1.6/5=0.32$.
- D) The number is wrong: the height is $\frac{4\times0.5}{5}=0.4$, so it stays below 1.

> [!success]- Answer
> **A.** Height $=\frac{\text{count}}{N\times\text{width}}=\frac{4}{2.5}=1.6$. It is a density.
> The probability of the bin is an area: $1.6\times0.5=0.8=\frac45$, below 1. Only areas must stay below 1.
> - B: count$/N$ is the bin probability. The pdf height also divides by the width.
> - C: The probability is height $\times$ width, not height$/N$. $0.32$ is not $\frac45$.
> - D: The width is multiplied instead of divided.

### Q10 (deck A, slide 10)

![[ee525_lecture_05_maximum_likelihood_and_bayesian_parameter_estimation_p010.jpg]]

Page 10 shows two panels. Red curve: Gaussian with the ML mean and ML standard deviation of the panel's samples. Blue curve: Gaussian centred at $\mu_{Bayes}$. Peak heights: $N=5$ panel, red $1.57$, blue $0.8$; $N=50$ panel, red $0.86$, blue $0.8$. A Gaussian has peak $\frac{1}{\sigma\sqrt{2\pi}}$. Which standard deviations do the curves use (blue, red $N=5$, red $N=50$), and why does only the red peak change?

- A) $1.25$, $0.637$, $1.163$. The red $\sigma$ is estimated from the samples of each panel.
- B) $0.5$, $0.254$, $0.464$. Blue uses the assumed known $\sigma$; the red $\sigma$ is estimated from the samples of each panel.
- C) $0.5$, $0.254$, $0.464$. The red $\sigma$ is the standard error $\sigma/\sqrt N$, which changes with $N$.
- D) $0.199$, $0.101$, $0.185$. Blue uses the assumed known $\sigma$; the red $\sigma$ is estimated from the samples of each panel.

> [!success]- Answer
> **B.** $\sigma=\frac{1}{\text{peak}\times2.5066}$. Blue: $\frac{1}{0.8\times2.5066}\approx0.5=\sqrt{0.25}$, the known $\sigma$, the same in both panels.
> Red: $\frac{1}{1.57\times2.5066}=0.254$ and $\frac{1}{0.86\times2.5066}=0.464\approx\hat\sigma_{ML}=0.4652$. The five flowers lie close together, so their ML $\sigma$ is too small.
> - A: $\sigma=\frac{1}{\text{peak}}$ forgets the factor $\sqrt{2\pi}$.
> - C: The standard error would be $0.5/\sqrt5=0.224$ and $0.5/\sqrt{50}=0.071$, not $0.254$ and $0.464$. The red curve uses the sample SD.
> - D: $\sigma=\frac{1}{\text{peak}\times2\pi}$ forgets the square root.

## 5. Gaussian mixture models and the latent-variable problem (deck B, pages 1-2)

### Q11 (deck B, slide 1)

Find the mistake: "$p(x)=0.7\,\mathcal N(x\mid1,1)+0.5\,\mathcal N(x\mid3,1)$ is a Gaussian mixture model with $K=2$." Which repair makes $p(x)$ a valid mixture density and keeps the ratio of the two weights?

- A) Nothing is wrong: the weights need not sum to 1, because each Gaussian already has area 1.
- B) The weights sum to $1.2$. Subtract $0.1$ from each: $\pi=(0.6,\ 0.4)$.
- C) The weights sum to $1.2$. Multiply each by $1.2$: $\pi=(0.84,\ 0.6)$.
- D) The weights sum to $1.2$. Divide each by $1.2$: $\pi=(0.5833,\ 0.4167)$.

> [!success]- Answer
> **D.** The mixing coefficients must sum to 1. Here $0.7+0.5=1.2$, so the area under $p(x)$ is $1.2$.
> Dividing by $1.2$ gives $0.5833+0.4167=1$ and keeps the ratio $0.7:0.5$.
> - A: The area is $0.7\times1+0.5\times1=1.2$, so $p(x)$ is not a density.
> - B: The sum is 1, but the ratio changes from $1.4$ to $1.5$.
> - C: The ratio is kept, but the sum gets worse: $0.84+0.6=1.44$, not 1.

### Q12 (deck B, slide 2)

Suppose the source of each value is known: $1.8,\ 2.1,\ 2.4$ come from condition 1 and $5.7,\ 6.2,\ 6.5$ from condition 2. What are the maximum-likelihood estimates of $\pi_k$, $\mu_k$, $\sigma_k^2$?

- A) $\pi=(0.5,\ 0.5)$, $\mu=(2.1,\ 6.1333)$, $\sigma^2=(0.09,\ 0.1633)$
- B) $\pi=(0.5,\ 0.5)$, $\mu=(2.1,\ 6.1333)$, $\sigma^2=(0.18,\ 0.3267)$
- C) $\pi=(0.5,\ 0.5)$, $\mu=(2.1,\ 6.1333)$, $\sigma^2=(0.06,\ 0.1089)$
- D) $\pi=(0.5,\ 0.5)$, $\mu=(2.1,\ 6.1333)$, $\sigma^2=(0.2449,\ 0.3300)$

> [!success]- Answer
> **C.** With known sources each group is an ordinary MLE problem, so EM is not needed. $\pi_k=\frac36=0.5$.
> Group 1: mean $2.1$, squared deviations $0.09+0+0.09=0.18$, $\sigma_1^2=\frac{0.18}{3}=0.06$.
> Group 2: mean $\frac{18.4}{3}=6.1333$, sum of squared deviations $0.3267$, $\sigma_2^2=\frac{0.3267}{3}=0.1089$.
> - A: Divided by $N-1=2$. That is the unbiased variance, not the MLE.
> - B: The sums of squared deviations, not divided by the group size.
> - D: These are standard deviations $\sqrt{0.06}$ and $\sqrt{0.1089}$, not variances.

## 6. E-step and M-step (deck B, page 3)

### Q13 (deck B, slide 3, E-step)

Components $\mu_1=2$, $\mu_2=6$, $\sigma_1=\sigma_2=1$, $\pi_1=\pi_2=0.5$. (a) What are the responsibilities $(\gamma_1,\gamma_2)$ of $x=2.5$? (b) What are the responsibilities of $x=4$ if the weights change to $\pi=(0.9,\ 0.1)$?

- A) (a) $(0.9975,\ 0.0025)$. (b) $(0.5,\ 0.5)$.
- B) (a) $(0.9975,\ 0.0025)$. (b) $(0.9,\ 0.1)$.
- C) (a) $(0.3521,\ 0.0009)$. (b) $(0.0486,\ 0.0054)$.
- D) (a) $(0.9975,\ 0.0025)$. (b) $(0.1,\ 0.9)$.

> [!success]- Answer
> **B.** (a) $\mathcal N(2.5\mid2,1)=0.3989\,e^{-0.125}=0.35207$, $\mathcal N(2.5\mid6,1)=0.3989\,e^{-6.125}=0.00087$. Numerators $0.17603$, $0.00044$, sum $0.17647$. $\gamma=(0.9975,\ 0.0025)$.
> (b) At $x=4$ both densities are $0.05399$ and cancel: $\gamma_1=\frac{0.9}{0.9+0.1}=0.9$.
> - A: The weights are ignored in (b). They only cancel when they are equal.
> - C: No division by the sum: these are densities in (a) and numerators $0.9(0.05399)$, $0.1(0.05399)$ in (b). They do not sum to 1.
> - D: The direction is wrong. The larger weight pulls the shared point toward its own component.

### Q14 (deck B, slide 3, M-step)

A student runs one M-step on $N=1000$ observations with $K=2$ and gets $N_1=412.3$ and $N_2=601.2$, where $N_k=\sum_i\gamma_{ik}$. What is wrong, and where is the bug most likely?

- A) $N_1+N_2$ must be $N=1000$, but it is $1013.5$. The rows of $\gamma$ do not sum to 1: the E-step did not divide by the right sum.
- B) $N_k$ must be whole numbers. The responsibilities were not rounded to 0 or 1.
- C) $N_1+N_2$ must be $N=1000$, but it is $1013.5$. The M-step mean formula divides by $N$ instead of $N_k$.
- D) Nothing is wrong: effective counts can sum to more than $N$ when the components overlap.

> [!success]- Answer
> **A.** Every observation has $\sum_k\gamma_{ik}=1$. Adding over all observations: $\sum_kN_k=N$.
> $412.3+601.2=1013.5\ne1000$, so the E-step normalisation is missing or wrong. Then $\pi_k=N_k/N$ would sum to $1.0135$.
> - B: $N_k$ is an effective count. Shared observations give non-integer values; that is correct.
> - C: The mean formula does not change $N_k$. $N_k$ comes from $\gamma$ only.
> - D: Overlap shares one observation between components, but its total is still 1.

## 7. MATLAB Examples 1 and 2: synthetic data and EM from scratch (deck B, pages 4-7)

### Q15 (deck B, slide 4)

![[ee525_lecture_05_expectation_maximization_em_and_mixture_density_estimation_p004.jpg]]

The data are 400 values from $\mathcal N(2,0.5^2)$ and 600 from $\mathcal N(6,1^2)$, so the mixture is $0.4\,\mathcal N(x\mid2,0.5^2)+0.6\,\mathcal N(x\mid6,1^2)$. In the histogram the peak near $x=2$ is higher than the peak near $x=6$. What are the peak heights $\frac{\pi_k}{\sigma_k\sqrt{2\pi}}$ of the two weighted components?

- A) $0.6383$ and $0.2394$
- B) $0.1596$ and $0.2394$
- C) $0.7979$ and $0.3989$
- D) $0.3192$ and $0.2394$

> [!success]- Answer
> **D.** Component 1: $\frac{0.4}{0.5\times2.5066}=0.3192$. Component 2: $\frac{0.6}{1.0\times2.5066}=0.2394$.
> Population 1 has fewer values, but they lie in a range half as wide, so its density is higher. The area (share) is still $0.4$ against $0.6$.
> - A: The variance $0.25$ is used in place of $\sigma=0.5$: $\frac{0.4}{0.25\times2.5066}=0.6383$.
> - B: $\sigma_1$ is left out: $\frac{0.4}{2.5066}=0.1596$. Then the left peak would be lower, against the histogram.
> - C: The weights are left out: $\frac{1}{0.5\times2.5066}=0.7979$ and $\frac{1}{2.5066}=0.3989$.

### Q16 (deck B, slide 5)

Same data as Q15. The EM script starts with `mu = [1.5; 5.5]`. What happens if it starts with `mu = [3.5; 3.5]` instead (same `sigma2 = [1; 1]`, `pi_k = [0.5; 0.5]`)? Give the values the two components reach (about, from the true mixture).

- A) EM separates the components in a few iterations and reaches means near $2$ and $6$.
- B) Both means become about $4.4$, both variances about $0.7$, the weights stay $0.5$. EM is stuck.
- C) Both means become about $4.4$, both variances about $4.54$, the weights stay $0.5$. EM is stuck.
- D) Both means become about $4.4$, both variances about $4.54$. Then the weights move to $0.4$ and $0.6$ and the components separate.

> [!success]- Answer
> **C.** Identical components give $\gamma_{i1}=\gamma_{i2}=0.5$ for every point, so $N_1=N_2=500$.
> Both means become the overall mean $0.4(2)+0.6(6)=4.4$. Both variances become the overall variance $0.4(0.25+5.76)+0.6(1+2.56)=4.54$.
> The components are identical again, so nothing changes any more. The start must use different means.
> - A: Nothing in the E-step can tell two identical components apart.
> - B: $0.4(0.25)+0.6(1)=0.7$ forgets the spread of the two means around $4.4$.
> - D: The weights stay $N_k/N=0.5$, because the responsibilities stay equal.

### Q17 (deck B, slide 6)

`gamma` is an $N\times K$ matrix ($N=1000$, $K=2$) of numerators $\pi_k\,\mathcal N(x_i\mid\mu_k,\sigma_k^2)$. The line `gamma = gamma ./ sum(gamma,2);` normalises it. What is `sum(gamma,2)`, and what happens if the student writes `sum(gamma,1)`?

- A) A $1\times K$ row of column totals (the counts $N_k$). With `sum(gamma,1)` the result is the same.
- B) An $N\times1$ column with the mixture density $p(x_i)$. With `sum(gamma,1)` each column sums to 1, so $N_k=(1,1)$ and $\pi_k=0.001$ for both.
- C) An $N\times1$ column with the mixture density $p(x_i)$. With `sum(gamma,1)` the rows still sum to 1; only the scale changes.
- D) An $N\times1$ column with the largest numerator of each row. With `sum(gamma,1)` each column sums to 1, so $N_k=(1,1)$ and $\pi_k=0.001$ for both.

> [!success]- Answer
> **B.** Dimension 2 adds along each row: $\sum_k\pi_k\mathcal N(x_i\mid\mu_k,\sigma_k^2)=p(x_i)$, the E-step denominator.
> Dimension 1 adds along each column. After that division every column sums to 1, not every row. Then `Nk = sum(gamma,1)'` is $(1,1)$ and $\pi_k=1/1000=0.001$; the weights sum to $0.002$.
> - A: Dimension 2 is the row sum, and the two choices give different results.
> - C: Dividing by column sums makes the columns sum to 1. The rows no longer do.
> - D: `sum` adds the numerators. It does not take the largest one (that is `max`).

### Q18 (deck B, slide 7)

The plot uses `pdf1 = pi_k(1)*normpdf(...)`, `pdf2 = pi_k(2)*normpdf(...)` and `pdfTotal = pdf1 + pdf2`, with $\pi\approx(0.4,\ 0.6)$. What are the areas under `pdf1` and `pdfTotal`, and how must the log-likelihood curve against iteration look if the code is correct?

- A) About $0.4$ and $1$. The log-likelihood never goes down: it rises fast, then becomes flat.
- B) $1$ and $2$. The log-likelihood never goes down: it rises fast, then becomes flat.
- C) About $0.4$ and $1$. The log-likelihood goes down steadily to a minimum.
- D) About $0.4$ and $1$. The log-likelihood may go up and down, but it ends at its maximum.

> [!success]- Answer
> **A.** `normpdf` has area 1, so `pdf1` has area $\pi_1\approx0.4$. `pdfTotal` has area $\pi_1+\pi_2=1$: it is the mixture density.
> EM never decreases the log-likelihood. It rises fast and then flattens; the loop stops when the change is below $10^{-6}$.
> - B: The weight multiplies the curve, so the area of `pdf1` is $\pi_1$, not 1.
> - C: EM maximises the log-likelihood. A falling curve means a bug.
> - D: A single step down already means a bug.

## 8. MATLAB Examples 3 and 4: fitgmdist in 1-D and 2-D (deck B, pages 8-10)

### Q19 (deck B, slide 8)

The data are made with `2 + 0.5*randn(400,1)` and `6 + 1.0*randn(600,1)`. About which values should `GMModel.mu`, `GMModel.Sigma` and `GMModel.ComponentProportion` show? `fitgmdist` may print the two components in either order (label switching). Give every pair in this fixed order: first the component with mean near 2, then the component with mean near 6.

- A) mu $(2,\ 6)$, Sigma $(0.5,\ 1)$, proportions $(0.4,\ 0.6)$
- B) mu $(2,\ 6)$, Sigma $(0.25,\ 1)$, proportions $(0.5,\ 0.5)$
- C) mu $(0.8,\ 3.6)$, Sigma $(0.25,\ 1)$, proportions $(0.4,\ 0.6)$
- D) mu $(2,\ 6)$, Sigma $(0.25,\ 1)$, proportions $(0.4,\ 0.6)$

> [!success]- Answer
> **D.** The means are $2$ and $6$. `Sigma` holds variances: $0.5^2=0.25$ and $1^2=1$. The proportions are $\frac{400}{1000}=0.4$ and $\frac{600}{1000}=0.6$.
> The fitted values are close to these, not equal, because the data are random.
> - A: $0.5$ is the standard deviation in the code. `Sigma` stores the variance.
> - B: The proportions are estimated from the data. They are not equal here.
> - C: The means are multiplied by the weights: $0.4\times2=0.8$, $0.6\times6=3.6$. `mu` is the mean of each component itself.

### Q20 (deck B, slide 9)

True parameters: $\pi=(0.4,\ 0.6)$, $\mathcal N(2,0.5^2)$, $\mathcal N(6,1^2)$. Which row does `posterior` return for $x=3.5$, and which label does `max(P,[],2)` give?

- A) $(0.3358,\ 0.6642)$, label 2
- B) $(0.7618,\ 0.2382)$, label 1
- C) $(0.2521,\ 0.7479)$, label 2
- D) $(0.2521,\ 0.7479)$, label 1, because $3.5$ is nearer to the mean $2$

> [!success]- Answer
> **C.** $\mathcal N(3.5\mid2,0.5^2)=0.7979\,e^{-4.5}=0.008864$. $\mathcal N(3.5\mid6,1)=0.3989\,e^{-3.125}=0.017528$.
> Numerators $0.4(0.008864)=0.003545$ and $0.6(0.017528)=0.010517$, sum $0.014062$. Row $(0.2521,\ 0.7479)$, label 2.
> In standard deviations the point is farther from component 1: $1.5/0.5=3$ against $2.5/1=2.5$.
> - A: The weights are left out: $\frac{0.008864}{0.008864+0.017528}=0.3358$.
> - B: The exponent of component 1 uses $2\sigma$ instead of $2\sigma^2$: $0.7979\,e^{-2.25}=0.0841$, which gives $0.7618$.
> - D: The row is right, but `max` picks the larger posterior, not the nearer mean in plain distance.

### Q21 (deck B, slide 10)

Example 4 fits `fitgmdist(X,2,...)` to data with two features: $K=2$ components, $d=2$, full covariance matrices. How many free parameters are estimated?

- A) $12$
- B) $11$
- C) $13$
- D) $9$

> [!success]- Answer
> **B.** Means $Kd=4$. Covariances $K\frac{d(d+1)}{2}=2\times3=6$. Weights $K-1=1$. Total $q=11$.
> With $600$ points of 2 values each, 11 parameters are estimated well.
> - A: Both weights are counted. The second weight is $1-\pi_1$, so it is not free.
> - C: Each $2\times2$ covariance is counted with 4 entries. The two off-diagonal entries are equal.
> - D: Only the 2 variances of each matrix are counted (diagonal covariance). Full matrices also have a covariance.
