---
course: EE 525 Pattern Recognition
lecture: 5
title: MLE, Bayesian estimation and EM
tags: [ee525, lecture]
slides: lectures/lecture_05/Maximum Likelihood and Bayesian Parameter Estimation.pdf
slides_2: lectures/lecture_05/Expectation-Maximization (EM) and Mixture Density Estimation.pdf
full_notes: lecture_notes/lecture_05/EE525_Lecture_05_MLE_Bayes_EM.pdf
---
# EE 525 · Lecture 5 · MLE, Bayesian estimation and EM

> [!summary] In short
> - Assume the shape of the class density (Gaussian). Then only its parameters are unknown and must be estimated from training data.
> - Maximum likelihood (MLE): the parameter is fixed but unknown. Pick the value that makes the observed samples most likely. For a Gaussian: sample mean, and variance divided by $N$.
> - Bayesian estimation: the parameter is random with a prior. Data turn the prior into a posterior. For a Gaussian mean with known variance the posterior mean is a weighted average of the sample mean and the prior mean (shrinkage).
> - More data: the posterior gets narrower and the Bayesian estimate approaches the MLE.
> - Gaussian mixture model (GMM): a weighted sum of $K$ Gaussians, for densities with several peaks.
> - EM fits a GMM when the source of each sample is unknown: E-step (responsibilities), M-step (weighted MLE formulas), repeat until the log-likelihood stops changing.

Two handouts (documents, 10 pages each). Deck A: "Maximum Likelihood and Bayesian Parameter Estimation". Deck B: "Expectation-Maximization (EM) and Mixture Density Estimation". The MATLAB code is printed inside the handouts; this lecture has no separate script printout.

## Part A · Maximum likelihood and Bayesian parameter estimation

## 1. Why the parameters must be estimated (deck A, pages 1-2)
![[ee525_lecture_05_maximum_likelihood_and_bayesian_parameter_estimation_p001.jpg]]
![[ee525_lecture_05_maximum_likelihood_and_bayesian_parameter_estimation_p002.jpg]]

**Notes.** The Bayes classifier needs the priors $P(\omega_i)$ and the densities $p(\mathbf{x}\mid\omega_i)$. In practice we only have training samples. Priors are easy (count: $\hat P(\omega_i)=N_i/N$). A whole density is hard, above all with many features.

If we assume a **parametric form**, for example $p(\mathbf{x}\mid\omega_i)\sim\mathcal N(\boldsymbol\mu_i,\Sigma_i)$, we only need the parameters. $\theta$ is the list of unknown parameters; for a 1-D Gaussian $\theta=(\mu,\sigma^2)$:

$$p(x\mid\mu,\sigma^2)=\frac{1}{\sqrt{2\pi\sigma^2}}\exp\left(-\frac{(x-\mu)^2}{2\sigma^2}\right)$$

- **MLE**: parameters are fixed but unknown numbers.
- **Bayesian estimation**: parameters are random variables with a prior. More samples sharpen the posterior (**Bayesian learning**).
- **Supervised** learning: the class label of every sample is known. **Unsupervised**: it is not.

Samples of one class are **i.i.d.** (independent, identically distributed). Samples of class $i$ say nothing about the parameters of class $j$, so each class is a separate problem.

> [!question]- Check (deck A, slide 1): Each sample has $d=4$ features. How many numbers must be estimated for one class if we assume a Gaussian density? Why is this easier than estimating the density with no assumption?
> Mean vector $\boldsymbol\mu_i$: $d=4$ numbers.
> Covariance matrix $\Sigma_i$: it is symmetric, so it has $\frac{d(d+1)}{2}=\frac{4\cdot5}{2}=10$ different entries.
> Total: $4+10=14$ numbers per class.
> Without the assumption the unknown is a whole function: one value for every point $\mathbf{x}$ of a 4-dimensional space. The samples are never enough for that. With the Gaussian form the same samples only have to fix 14 numbers.

> [!question]- Check (deck A, slide 2): Evaluate the Gaussian density of the slide at $x=4.8$ for $\mu=4.3$, $\sigma^2=0.25$. Is the result the probability that a flower has petal length $4.8$?
> Formula: $p(x\mid\mu,\sigma^2)=\frac{1}{\sqrt{2\pi\sigma^2}}\exp\left(-\frac{(x-\mu)^2}{2\sigma^2}\right)$.
> Front factor: $\frac{1}{\sqrt{2\pi(0.25)}}=\frac{1}{0.5\times2.5066}=0.7979$.
> Exponent: $-\frac{(4.8-4.3)^2}{2(0.25)}=-\frac{0.25}{0.5}=-0.5$, and $e^{-0.5}=0.6065$.
> Density: $0.7979\times0.6065=0.4839$.
> It is not a probability. It is a density (probability per cm). A probability is an area: for a small interval of width $0.1$ around $4.8$ it is about $0.4839\times0.1=0.048$.

## 2. Likelihood and the MLE of a Gaussian (deck A, pages 3-5)
![[ee525_lecture_05_maximum_likelihood_and_bayesian_parameter_estimation_p003.jpg]]
![[ee525_lecture_05_maximum_likelihood_and_bayesian_parameter_estimation_p004.jpg]]
![[ee525_lecture_05_maximum_likelihood_and_bayesian_parameter_estimation_p005.jpg]]

**Notes.** For i.i.d. samples $\mathcal D=\{x_1,\dots,x_N\}$ the **likelihood** and **log-likelihood** are

$$p(\mathcal D\mid\theta)=\prod_{k=1}^{N}p(x_k\mid\theta),\qquad \ell(\theta)=\ln p(\mathcal D\mid\theta)=\sum_{k=1}^{N}\ln p(x_k\mid\theta)$$

The data are fixed and $\theta$ varies. The MLE $\hat\theta$ is the $\theta$ where the likelihood is largest. The log is increasing, so $\ell$ has its maximum at the same $\theta$.

Recipe: write $\ell(\theta)$, set its derivatives to zero, solve. For $\mathcal N(\mu,\sigma^2)$ with both unknown:

$$\hat\mu_{ML}=\frac1N\sum_{k=1}^{N}x_k,\qquad \hat\sigma^2_{ML}=\frac1N\sum_{k=1}^{N}(x_k-\hat\mu_{ML})^2$$

MATLAB example (page 4): petal length of the 50 versicolor flowers. `mu_ML = mean(X)`, `sigma2_ML = mean((X-mu_ML).^2)`. Output: mean $4.2600$, variance $0.2164$, standard deviation $0.4652$.

Page 5 starts the Bayesian part: with only 5 flowers we would not fully trust the sample mean.

> [!example] MLE by hand
> Petal lengths $4.0,4.5,4.7,3.9,4.4$: $\hat\mu=21.5/5=4.3$. Squared deviations $0.09,0.04,0.16,0.16,0.01$, sum $0.46$. $\hat\sigma^2_{ML}=0.46/5=0.092$. Unbiased: $0.46/4=0.115$.

> [!info] Several features (beyond the slides)
> $$\hat{\boldsymbol\mu}_{ML}=\frac1N\sum_{k=1}^{N}\mathbf{x}_k,\qquad \hat\Sigma_{ML}=\frac1N\sum_{k=1}^{N}(\mathbf{x}_k-\hat{\boldsymbol\mu}_{ML})(\mathbf{x}_k-\hat{\boldsymbol\mu}_{ML})^{\mathsf T}$$

> [!warning] Watch out
> - MATLAB `var(X)` and `cov(X)` divide by $N-1$, so they are not the MLE. Use `var(X,1)` or `cov(X,1)`, or multiply by $(N-1)/N$. The ML variance is biased: its average is $\frac{N-1}{N}\sigma^2$.
> - The likelihood is not "the probability that $\theta$ is the true value". MLE has no probability for $\theta$.

> [!info] Reading Figure 3.1 on page 3
> Top graph: the black dots are the training points on the $x$ axis. Each dashed curve is one candidate Gaussian. All have the same known variance, only the mean $\theta$ differs.
> Bottom graph: the horizontal axis is now $\theta$, not $x$. For every candidate mean we multiply the densities of all dots. That product is $p(\mathcal D\mid\theta)$. A candidate whose curve sits over the dots gives a large product. A candidate far from the dots gives a product near 0.
> $\hat\theta$ is the position of the peak of the bottom curve. With more training points the bottom curve becomes narrower: fewer values of $\theta$ still explain all the points.

> [!info] Where the two MLE formulas come from (beyond the slides)
> The slide prints the result only. The steps, with $v=\sigma^2$:
> 1. Log of one Gaussian term: $\ln p(x_k\mid\mu,v)=-\frac12\ln(2\pi)-\frac12\ln v-\frac{(x_k-\mu)^2}{2v}$.
> 2. Sum over the $N$ samples: $\ell(\mu,v)=-\frac N2\ln(2\pi)-\frac N2\ln v-\frac{1}{2v}\sum_{k=1}^{N}(x_k-\mu)^2$.
> 3. Derivative in $\mu$. Chain rule: $\frac{\partial}{\partial\mu}(x_k-\mu)^2=-2(x_k-\mu)$. So $\frac{\partial\ell}{\partial\mu}=\frac1v\sum_{k=1}^{N}(x_k-\mu)$.
> 4. Set it to 0: $\sum_k x_k-N\mu=0$, so $\hat\mu=\frac1N\sum_k x_k$.
> 5. Derivative in $v$. Use $\frac{d}{dv}\ln v=\frac1v$ and $\frac{d}{dv}\frac1v=-\frac{1}{v^2}$: $\frac{\partial\ell}{\partial v}=-\frac{N}{2v}+\frac{1}{2v^2}\sum_{k=1}^{N}(x_k-\mu)^2$.
> 6. Set it to 0 and multiply by $2v^2$: $-Nv+\sum_k(x_k-\mu)^2=0$, so $\hat v=\frac1N\sum_k(x_k-\hat\mu)^2$.
>
> The division by $N$ (not $N-1$) is a result of step 6. It is not a choice.

> [!question]- Check (deck A, slide 3): In Figure 3.1 the bottom curve has its peak near $\theta=3.1$, with a height of about $1.5\times10^{-7}$ (a little above the top label $1.2\times10^{-7}$). What is the MLE? Why is the height so small? Is the area under this curve equal to 1?
> The MLE is the position of the peak: $\hat\theta\approx3.1$. This is the sample mean of the dots in the top graph. The height does not matter, only where the maximum is.
> The likelihood is a product of one density value per training point. Each factor is well below 1, and there are about a dozen points. To see the size (the numbers are an illustration, not read from the figure): 13 factors of $0.3$ give $0.3^{13}\approx1.6\times10^{-7}$. This is why we work with the log-likelihood (a sum) in practice.
> The area is not 1. The curve is a function of $\theta$ with the data fixed. It is not a density of $\theta$.

> [!question]- Check (deck A, slide 4): The script prints the variance $0.2164$ for $N=50$. A student replaces `mean((X-mu_ML).^2)` by `var(X)`. What does the script print now, and is it still the MLE?
> `var(X)` divides by $N-1$ instead of $N$.
> Sum of squared deviations: $N\,\hat\sigma^2_{ML}=50\times0.2164=10.82$.
> `var(X)` $=\frac{10.82}{49}=0.2208$.
> It is not the MLE any more. It is the unbiased sample variance. The factor between them is $\frac{N}{N-1}=\frac{50}{49}=1.0204$, small here but not zero.

> [!question]- Check (deck A, slide 5): In the histogram the bar from $4.5$ to $5$ cm has height $0.76$. How many of the 50 flowers are in this bin?
> Formula: height $=\frac{\text{count}}{N\times\text{bin width}}$, so count $=$ height $\times N\times$ width.
> Bin width: $5-4.5=0.5$.
> Count: $0.76\times50\times0.5=19$ flowers.
> Check with all bars: heights $0.12,\ 0.32,\ 0.72,\ 0.76,\ 0.08$ give counts $3,\ 8,\ 18,\ 19,\ 2$, and $3+8+18+19+2=50$.

## 3. Bayesian estimation of a Gaussian mean (deck A, pages 6-8)
![[ee525_lecture_05_maximum_likelihood_and_bayesian_parameter_estimation_p006.jpg]]
![[ee525_lecture_05_maximum_likelihood_and_bayesian_parameter_estimation_p007.jpg]]
![[ee525_lecture_05_maximum_likelihood_and_bayesian_parameter_estimation_p008.jpg]]

**Notes.** Bayes' theorem for a parameter: posterior = likelihood × prior / evidence.

$$p(\theta\mid\mathcal D)=\frac{p(\mathcal D\mid\theta)\,p(\theta)}{p(\mathcal D)}$$

MLE returns one value. Bayesian estimation returns a distribution over the possible values.

Simple case of the slides: the measurement variance $\sigma^2$ is known, the unknown is $\mu$, and the prior is Gaussian, $\mu\sim\mathcal N(\mu_0,\tau_0^2)$. $\mu_0$ is the guess before the data, $\tau_0^2$ says how unsure it is. The posterior is Gaussian, $\mathcal N(\mu_N,\sigma_N^2)$, with sample mean $\bar x$:

$$\mu_N=\frac{\frac{N}{\sigma^2}\bar x+\frac{1}{\tau_0^2}\mu_0}{\frac{N}{\sigma^2}+\frac{1}{\tau_0^2}},\qquad \sigma_N^2=\frac{1}{\frac{N}{\sigma^2}+\frac{1}{\tau_0^2}}$$

Equivalent **shrinkage** form: $\mu_N=w_N\bar x+(1-w_N)\mu_0$ with $w_N=\dfrac{N\tau_0^2}{N\tau_0^2+\sigma^2}$. The estimate always lies between $\mu_0$ and $\bar x$. As $N\to\infty$: $w_N\to1$, $\mu_N\to\bar x$, $\sigma_N^2\to0$.

Three different variances: $\sigma^2$ (measurements), $\tau_0^2$ (belief about $\mu$ before the data), $\sigma_N^2$ (belief about $\mu$ after $N$ samples).

> [!example] The slide's numbers
> $\mu_0=4.0$, $\tau_0^2=0.25$, $\sigma^2=0.25$. Prior precision $1/0.25=4$.
> $N=5$, $\bar x=4.2$: $\mu_5=\frac{20(4.2)+4(4.0)}{24}=4.1667$, $\sigma_5=\sqrt{1/24}=0.2041$.
> $N=50$, $\bar x=4.26$: $\mu_{50}=\frac{200(4.26)+4(4.0)}{204}=4.2549$, $\sigma_{50}=\sqrt{1/204}=0.0700$.
> Prior mean $5.0$ instead: $\mu_5=104/24=4.3333$, $\mu_{50}=872/204=4.2745$. The prior matters a lot for $N=5$ and hardly for $N=50$. The posterior SD does not change.

> [!warning] Watch out
> "Bayes approaches MLE" needs $\tau_0^2>0$ and large $N$. With $\tau_0^2=0$ no amount of data moves the estimate from $\mu_0$.

> [!info] Where the posterior formula comes from (beyond the slides)
> The slide says "the posterior is also Gaussian" and prints its mean. The steps:
> 1. Posterior is proportional to likelihood times prior. Drop every factor without $\mu$:
> $$p(\mu\mid\mathcal D)\propto\exp\left(-\frac12 Q(\mu)\right),\qquad Q(\mu)=\frac{1}{\sigma^2}\sum_{k=1}^{N}(x_k-\mu)^2+\frac{1}{\tau_0^2}(\mu-\mu_0)^2$$
> 2. Expand the squares. $\sum_k(x_k-\mu)^2=\sum_k x_k^2-2\mu N\bar x+N\mu^2$ and $(\mu-\mu_0)^2=\mu^2-2\mu\mu_0+\mu_0^2$.
> 3. Collect the terms with $\mu^2$ and with $\mu$:
> $$Q(\mu)=\mu^2\left(\frac{N}{\sigma^2}+\frac{1}{\tau_0^2}\right)-2\mu\left(\frac{N\bar x}{\sigma^2}+\frac{\mu_0}{\tau_0^2}\right)+\text{terms without }\mu$$
> 4. A Gaussian $\mathcal N(\mu_N,\sigma_N^2)$ in $\mu$ has the exponent $-\frac12\frac{(\mu-\mu_N)^2}{\sigma_N^2}$, and $\frac{(\mu-\mu_N)^2}{\sigma_N^2}=\mu^2\frac{1}{\sigma_N^2}-2\mu\frac{\mu_N}{\sigma_N^2}+\text{const}$. $Q$ has the same form, so the posterior is Gaussian.
> 5. Match the $\mu^2$ terms: $\frac{1}{\sigma_N^2}=\frac{N}{\sigma^2}+\frac{1}{\tau_0^2}$.
> 6. Match the $\mu$ terms: $\frac{\mu_N}{\sigma_N^2}=\frac{N\bar x}{\sigma^2}+\frac{\mu_0}{\tau_0^2}$. Divide by step 5 to get the slide's $\mu_{Bayes}$.
>
> Intuition: $1/\text{variance}$ is called **precision** (how sure a source is). The data have precision $N/\sigma^2$, the prior has precision $1/\tau_0^2$. Precisions add (step 5), and the posterior mean is the average of $\bar x$ and $\mu_0$ weighted by these precisions (step 6). The slide's $\mu_{Bayes}$ is the $\mu_N$ of this note.

> [!question]- Check (deck A, slide 6): Keep the slide's $N=5$, $\bar x=4.2$, $\mu_0=4.0$, $\sigma^2=0.25$. Predict where $\mu_{Bayes}$ moves if the prior variance is made 10 times larger ($\tau_0^2=2.5$) or 10 times smaller ($\tau_0^2=0.025$). Then compute both.
> Prediction: a large $\tau_0^2$ means a weak prior, so the estimate moves toward $\bar x=4.2$. A small $\tau_0^2$ means a strong prior, so it moves toward $\mu_0=4.0$.
> Data precision in both cases: $\frac{N}{\sigma^2}=\frac{5}{0.25}=20$, and $20\times4.2=84$.
> $\tau_0^2=2.5$: prior precision $\frac{1}{2.5}=0.4$. $\mu_{Bayes}=\frac{84+0.4(4.0)}{20+0.4}=\frac{85.6}{20.4}=4.1961$.
> $\tau_0^2=0.025$: prior precision $\frac{1}{0.025}=40$. $\mu_{Bayes}=\frac{84+40(4.0)}{20+40}=\frac{244}{60}=4.0667$.
> Compare with the slide's value $4.1667$ for $\tau_0^2=0.25$: $4.0667<4.1667<4.1961$, as predicted.

> [!question]- Check (deck A, slide 7): The line `posteriorVariance = 1/(N/sigma2 + 1/tau2)` does not use `data`. What does this mean for two different random sets of 5 flowers? Which line decides which flowers are used?
> The posterior variance depends only on $N$, $\sigma^2$ and $\tau_0^2$. Any set of 5 flowers gives the same value $\frac{1}{20+4}=\frac{1}{24}=0.04167$, so the same posterior SD $\sqrt{0.04167}=0.2041$. Only the posterior mean changes with the data, through `mu_ML`.
> Reason: $\sigma^2$ is assumed known, so the data are not needed to learn the spread.
> `idx = randperm(length(X))` (page 6) shuffles the 50 indices once, and `data = X(idx(1:N))` takes the first $N$ of them. For $N=50$ this is the whole data set.

> [!question]- Check (deck A, slide 8): When the prior mean changes from $4.0$ to $5.0$, the Bayesian mean moves by $0.1667$ for $N=5$ and by $0.0196$ for $N=50$. Explain these two numbers. How many samples are needed so that the move is below $0.01$?
> From the shrinkage form $\mu_N=w_N\bar x+(1-w_N)\mu_0$: if $\mu_0$ changes by $\Delta$, then $\mu_N$ changes by $(1-w_N)\Delta$.
> Here $\tau_0^2=\sigma^2$, so $w_N=\frac{N}{N+1}$ and $1-w_N=\frac{1}{N+1}$.
> $N=5$: $\frac16\times1.0=0.1667$. Check: $4.3333-4.1667=0.1667$.
> $N=50$: $\frac{1}{51}\times1.0=0.0196$. Check: $4.2745-4.2549=0.0196$.
> Below $0.01$: $\frac{1}{N+1}<0.01$ needs $N+1>100$, so $N\ge100$ ($\frac{1}{101}=0.0099$).

## 4. MLE against Bayes, histogram against fitted Gaussian (deck A, pages 9-10)
![[ee525_lecture_05_maximum_likelihood_and_bayesian_parameter_estimation_p009.jpg]]
![[ee525_lecture_05_maximum_likelihood_and_bayesian_parameter_estimation_p010.jpg]]

**Notes.**

| | MLE | Bayes |
|---|---|---|
| $\theta$ is | fixed, unknown | random, has a prior |
| returns | one point | the whole $p(\theta\mid\mathcal D)$ |
| uses prior knowledge | no | yes |
| small data set | can be unreliable | prior stabilises |
| large data set | reliable | approaches MLE |
| effort | low | higher |

`histogram(data,'Normalization','pdf')` scales the bars so that the total area is 1:

$$\text{bin height}=\frac{\text{number of samples in the bin}}{N\times\text{bin width}},\qquad P(\text{bin})\approx\text{height}\times\text{bin width}$$

Slide example: $N=50$, width $0.2$, 10 samples: height $10/(50\times0.2)=1$, probability $1\times0.2=0.2$.

- Data distribution: what we observe (the histogram).
- Probability density model: what we assume ($\mathcal N(\mu,\sigma^2)$).
- Parameter estimation: how the model's numbers come from the data. `normpdf(x,mu,sigma)` then draws the curve.

The histogram is used to compare the fitted curve with the data, not to fit it.

> [!info] Beyond the slides: MAP and the predictive density
> MAP is the peak of the posterior; here $\hat\mu_{MAP}=\mu_N$. The Bayesian density for a new $x$ is $p(x\mid\mathcal D)=\mathcal N(\mu_N,\ \sigma^2+\sigma_N^2)$: wider than the plug-in Gaussian, because $\mu$ is not exactly known.

> [!info] Reading the figure on page 10
> Each panel shows the same histogram as page 8 and two Gaussian curves.
> - Red, "MLE Gaussian PDF": `normpdf` with the ML mean and the ML standard deviation of the samples in that panel.
> - Blue, "Bayesian Plug-in Gaussian PDF": a Gaussian centred at the Bayesian mean $\mu_{Bayes}$. "Plug-in" means that one estimated value is put into the Gaussian formula. The code of this figure is not printed. The peak height of the blue curve (about $0.8$ in both panels) fits the assumed $\sigma=\sqrt{0.25}=0.5$.
> - Vertical lines: MLE mean, Bayesian mean, prior mean $4.0$.
>
> Useful fact for reading such plots: the peak of a Gaussian is $\frac{1}{\sigma\sqrt{2\pi}}=\frac{0.3989}{\sigma}$. A narrow Gaussian is tall, a wide one is low, because the area is always 1.

> [!question]- Check (deck A, slide 9): Find the mistake: "A pdf-normalised histogram bar of height $1.6$ is impossible, because a probability cannot be larger than 1." Use $N=5$ samples, bin width $0.5$, 4 samples in the bin.
> The bar height is a density, not a probability.
> Height: $\frac{4}{5\times0.5}=\frac{4}{2.5}=1.6$. So the bar is possible. It is the tall bar of the $N=5$ panel on page 8.
> Probability of the bin: height $\times$ width $=1.6\times0.5=0.8$, which is $4/5$ and is below 1.
> Only areas must stay below 1. A height can be any positive number if the bin is narrow.

> [!question]- Check (deck A, slide 10): In the $N=5$ panel the red curve peaks at about $1.57$ and the blue curve at about $0.8$. In the $N=50$ panel red peaks at about $0.86$ and blue again at about $0.8$. What standard deviation does each curve use? Why does only the red peak change?
> Peak of a Gaussian: $\frac{1}{\sigma\sqrt{2\pi}}$, so $\sigma=\frac{1}{\text{peak}\times2.5066}$.
> Blue: $\sigma=\frac{1}{0.8\times2.5066}\approx0.5$. This is the assumed known $\sigma=\sqrt{0.25}$. Exact peak: $\frac{1}{0.5\times2.5066}=0.798$. It does not depend on the data, so it is the same in both panels. Only the centre $\mu_{Bayes}$ moves.
> Red, $N=50$: $\sigma=\hat\sigma_{ML}=0.4652$, peak $\frac{1}{0.4652\times2.5066}=0.858$.
> Red, $N=5$: $\sigma\approx\frac{1}{1.57\times2.5066}=0.254$. The five flowers happen to lie close together, so the ML standard deviation is much too small (the 50 flowers give $0.4652$).
> The red curve uses a variance estimated from the samples of the panel, so it changes with the sample. With few samples the ML variance is unreliable.

## Part B · Gaussian mixtures and the EM algorithm

## 5. Gaussian mixture models and the latent-variable problem (deck B, pages 1-2)
![[ee525_lecture_05_expectation_maximization_em_and_mixture_density_estimation_p001.jpg]]
![[ee525_lecture_05_expectation_maximization_em_and_mixture_density_estimation_p002.jpg]]

**Notes.** Sensor amplitudes from two operating conditions give a histogram with two peaks. One Gaussian represents it poorly. A **Gaussian mixture model** is a weighted sum of $K$ Gaussians:

$$p(x)=\sum_{k=1}^{K}\pi_k\,\mathcal N(x\mid\mu_k,\sigma_k^2),\qquad \pi_k\ge0,\quad\sum_{k=1}^{K}\pi_k=1$$

$\pi_k$ is the **mixing coefficient** of component $k$ (a weight, not 3.14). With several features: $\mathcal N(\mathbf{x}\mid\boldsymbol\mu_k,\Sigma_k)$. Slide example: $p(x)=0.4\,\mathcal N(x\mid2,0.5^2)+0.6\,\mathcal N(x\mid6,1^2)$.

If we knew the source of every measurement, the mean and variance of each Gaussian would be the ordinary MLE of each group. We do not: the source is a **latent variable**. The mixture log-likelihood

$$\ell(\theta)=\sum_{i=1}^{N}\ln\left(\sum_{k=1}^{K}\pi_k\,\mathcal N(x_i\mid\mu_k,\sigma_k^2)\right)$$

has the sum inside the logarithm, so there is no closed-form maximum. **EM** alternates: initialise, E-step, M-step, check convergence of the log-likelihood.

> [!example] The mixture at one point
> $p(3)=0.4(0.1080)+0.6(0.004432)=0.04585$. Component 1 supplies 94% of the density at $x=3$.

> [!info] How to picture a mixture, and why its MLE has no formula (beyond the slides)
> A mixture describes data that are made in two stages. First a component is picked: number $k$ with probability $\pi_k$. Then $x$ is drawn from the Gaussian of that component. The picked number is the latent variable (often written $z_i$). On the slide it is the column "Actual source".
> Because the weights sum to 1, the area under $p(x)$ is 1: $\int p(x)\,dx=\sum_k\pi_k\cdot1=1$.
> For one Gaussian the log removes the exponential: $\ln e^{-a}=-a$. This is why the MLE of section 2 has a formula. For a mixture we need $\ln(a+b)$, and $\ln(a+b)\ne\ln a+\ln b$. The exponentials stay inside the log. The derivative set to zero then contains the unknown parameters on both sides, so it cannot be solved in one step.
> Do not mix up: $\pi_k$ weights a component inside one density. A class prior $P(\omega_i)$ weights a class. A class density can itself be a mixture.

> [!question]- Check (deck B, slide 1): Find the mistake: "$p(x)=0.7\,\mathcal N(x\mid1,1)+0.5\,\mathcal N(x\mid3,1)$ is a Gaussian mixture model with $K=2$." How can it be repaired?
> The mixing coefficients must sum to 1 (the condition is printed at the top of page 2). Here $0.7+0.5=1.2$.
> The area under this $p(x)$ would be $0.7\times1+0.5\times1=1.2$, so it is not a probability density.
> Repair that keeps the ratio of the two components: divide both weights by $1.2$. $\pi_1=\frac{0.7}{1.2}=0.5833$, $\pi_2=\frac{0.5}{1.2}=0.4167$. Check: $0.5833+0.4167=1$.

> [!question]- Check (deck B, slide 2): Suppose the column "Actual source" of the slide's table were known: $1.8,\ 2.1,\ 2.4$ come from condition 1 and $5.7,\ 6.2,\ 6.5$ from condition 2. Estimate $\pi_k$, $\mu_k$, $\sigma_k^2$ of both components. Why is EM not needed here?
> With known sources each group is an ordinary MLE problem (section 2).
> Weights: $\pi_1=\frac36=0.5$, $\pi_2=\frac36=0.5$.
> Component 1: $\mu_1=\frac{1.8+2.1+2.4}{3}=\frac{6.3}{3}=2.1$. Squared deviations $0.09,\ 0,\ 0.09$, sum $0.18$. $\sigma_1^2=\frac{0.18}{3}=0.06$.
> Component 2: $\mu_2=\frac{5.7+6.2+6.5}{3}=\frac{18.4}{3}=6.1333$. Squared deviations $0.1878,\ 0.0044,\ 0.1344$, sum $0.3267$. $\sigma_2^2=\frac{0.3267}{3}=0.1089$.
> EM is needed only because the sources are unknown. Then we cannot split the data into groups, and the E-step replaces the known labels by probabilities.

## 6. E-step and M-step (deck B, page 3)
![[ee525_lecture_05_expectation_maximization_em_and_mixture_density_estimation_p003.jpg]]

**Notes.** **E-step.** The **responsibility** $\gamma_{ik}$ is the probability that observation $x_i$ came from component $k$, with the current parameters. It is Bayes' rule with "component" in the place of "class":

$$\gamma_{ik}=\frac{\pi_k\,\mathcal N(x_i\mid\mu_k,\sigma_k^2)}{\sum_{j=1}^{K}\pi_j\,\mathcal N(x_i\mid\mu_j,\sigma_j^2)},\qquad\sum_{k}\gamma_{ik}=1$$

EM does not assign a point to one component. It gives a probability of membership (soft assignment).

**M-step.** The MLE formulas, with every sample weighted by its responsibility:

$$N_k=\sum_{i=1}^{N}\gamma_{ik},\qquad \mu_k^{new}=\frac{1}{N_k}\sum_{i=1}^{N}\gamma_{ik}\,x_i$$

$$(\sigma_k^2)^{new}=\frac{1}{N_k}\sum_{i=1}^{N}\gamma_{ik}\,(x_i-\mu_k^{new})^2,\qquad \pi_k^{new}=\frac{N_k}{N}$$

$N_k$ is the effective number of observations of component $k$. The variance uses the new mean. After every full iteration the log-likelihood does not decrease, so EM converges to a local maximum that depends on the start.

> [!example] Responsibilities
> $\mu_1=2$, $\mu_2=6$, $\sigma_1=\sigma_2=1$, $\pi_1=\pi_2=0.5$. For $x=2$: $\gamma=(0.9997,\ 0.0003)$. For $x=4$: $(0.5,\ 0.5)$. For $x=6$: $(0.0003,\ 0.9997)$.

> [!warning] Slide error (deck B, page 3)
> The slide's table lists the observations $2.0$, $3.5$, $5.0$ with responsibilities $0.9997/0.0003$, $0.5/0.5$, $0.0003/0.9997$. Those values belong to $x=2,4,6$. For the printed observations the correct rows are $x=3.5$: $0.8808/0.1192$ and $x=5.0$: $0.0180/0.9820$. The equal-responsibility point is the midpoint $x=4$.

> [!info] Why EM must repeat, and one full iteration by hand (beyond the slides)
> The M-step formulas look like a final answer, but they are not. The responsibilities $\gamma_{ik}$ on the right side were computed with the old parameters. With the new parameters the responsibilities change. So: freeze $\gamma$ (E-step), update the parameters (M-step), compute $\gamma$ again, and repeat.
> With hard labels ($\gamma_{ik}$ is 0 or 1) the M-step formulas are exactly the ordinary MLE of each group.
>
> Tiny example. Data $x=(1,2,3,4,7)$, $N=5$, $K=2$. Start: $\mu=(2,6)$, $\sigma^2=(1,1)$, $\pi=(0.5,0.5)$.
>
> | $x_i$ | $\gamma_{i1}$ | $\gamma_{i2}$ | $\gamma_{i1}x_i$ | $\gamma_{i2}x_i$ |
> |---|---|---|---|---|
> | 1 | 1.0000 | 0.0000 | 1.0000 | 0.0000 |
> | 2 | 0.9997 | 0.0003 | 1.9993 | 0.0007 |
> | 3 | 0.9820 | 0.0180 | 2.9460 | 0.0540 |
> | 4 | 0.5000 | 0.5000 | 2.0000 | 2.0000 |
> | 7 | 0.0000 | 1.0000 | 0.0000 | 7.0000 |
> | sum | $N_1=3.4817$ | $N_2=1.5183$ | 7.9454 | 9.0546 |
>
> M-step, weights: $\pi_1=\frac{3.4817}{5}=0.6963$, $\pi_2=\frac{1.5183}{5}=0.3037$.
> M-step, means: $\mu_1=\frac{7.9454}{3.4817}=2.2821$, $\mu_2=\frac{9.0546}{1.5183}=5.9636$.
> M-step, variances around the new means: $\sum_i\gamma_{i1}(x_i-2.2821)^2=3.7052$, so $\sigma_1^2=\frac{3.7052}{3.4817}=1.0642$. $\sum_i\gamma_{i2}(x_i-5.9636)^2=3.1654$, so $\sigma_2^2=\frac{3.1654}{1.5183}=2.0848$.
> Log-likelihood: $-10.8488$ before, $-9.9637$ after. It went up by $0.8851$.
> Reading: $x=4$ is halfway between the starting means, so it is shared $0.5/0.5$. Component 2 owns "one and a half" points ($7$ and half of $4$), so its mean $5.96$ lies between them and its variance is large.

> [!question]- Check (deck B, slide 3, E-step): Use the slide's components ($\mu_1=2$, $\mu_2=6$, $\sigma_1=\sigma_2=1$, $\pi_1=\pi_2=0.5$). Compute the responsibilities of $x=2.5$. Then predict the responsibilities of $x=4$ if the weights change to $\pi=(0.9,\ 0.1)$.
> Densities, with $\mathcal N(x\mid\mu,1)=0.3989\,e^{-(x-\mu)^2/2}$:
> $\mathcal N(2.5\mid2,1)=0.3989\,e^{-0.125}=0.3989\times0.8825=0.35207$.
> $\mathcal N(2.5\mid6,1)=0.3989\,e^{-6.125}=0.3989\times0.0021875=0.00087$.
> Numerators: $0.5\times0.35207=0.17603$ and $0.5\times0.00087=0.00044$. Sum $0.17647$.
> $\gamma_1=\frac{0.17603}{0.17647}=0.9975$, $\gamma_2=\frac{0.00044}{0.17647}=0.0025$. Check: sum $=1$.
> $x=4$ with $\pi=(0.9,0.1)$: both densities are equal ($0.05399$), so they cancel. $\gamma_1=\frac{0.9}{0.9+0.1}=0.9$, $\gamma_2=0.1$. At the midpoint the responsibilities are the weights themselves. A larger weight pulls shared points toward its component.

> [!question]- Check (deck B, slide 3, M-step): A student runs one M-step on $N=1000$ observations with $K=2$ and gets $N_1=412.3$ and $N_2=601.2$. Something is wrong. What, and where is the bug most likely?
> Every observation gives a total responsibility of 1: $\sum_k\gamma_{ik}=1$. Adding over all observations gives $\sum_kN_k=N$.
> Here $412.3+601.2=1013.5\ne1000$. So the rows of $\gamma$ do not sum to 1.
> Most likely the E-step forgot to divide by the denominator (or divided by the wrong sum).
> A non-integer $N_k$ is not the mistake. $N_k$ is an effective number, because observations are shared between components.
> Result of the bug: $\pi_k=N_k/N$ would sum to $1.0135$, not to 1.

## 7. MATLAB Examples 1 and 2: synthetic data and EM from scratch (deck B, pages 4-7)
![[ee525_lecture_05_expectation_maximization_em_and_mixture_density_estimation_p004.jpg]]
![[ee525_lecture_05_expectation_maximization_em_and_mixture_density_estimation_p005.jpg]]
![[ee525_lecture_05_expectation_maximization_em_and_mixture_density_estimation_p006.jpg]]
![[ee525_lecture_05_expectation_maximization_em_and_mixture_density_estimation_p007.jpg]]

**Notes.**
- **Example 1** draws 400 values from $\mathcal N(2,0.5^2)$ and 600 from $\mathcal N(6,1^2)$, shuffles them and plots a 35-bin pdf histogram. Two peaks. The true labels are kept only for the picture.
- **Example 2** is EM by hand for $K=2$: start $\mu=(1.5,5.5)$, $\sigma^2=(1,1)$, $\pi=(0.5,0.5)$. It stops when the log-likelihood changes by less than $10^{-6}$ or after 200 iterations. It plots the two weighted components, their sum, and the log-likelihood against the iteration.

The loop, line by line:

```matlab
gamma(:,k) = pi_k(k)*normpdf(X,mu(k),sqrt(sigma2(k)));   % E-step numerators
gamma = gamma ./ sum(gamma,2);                            % each row sums to 1
Nk = sum(gamma,1)';                                       % effective counts
mu(k) = sum(gamma(:,k).*X)/Nk(k);                         % weighted mean
sigma2(k) = sum(gamma(:,k).*(X-mu(k)).^2)/Nk(k);          % weighted variance, new mean
pi_k(k) = Nk(k)/N;                                        % new weight
```

Answers to the three questions on page 5: (1) no, one Gaussian would put its peak near the overall mean 4.4, where the data are sparse; (2) two, one per peak; (3) with EM.

> [!warning] Watch out
> - A log-likelihood that goes down between iterations always means a bug.
> - Label switching: nothing ties "component 1" to "population 1". Compare components by their means, not by their index.

> [!question]- Check (deck B, slide 4): Population 1 has only 400 of the 1000 values, but in the histogram its peak (near $x=2$) is higher than the peak of population 2 (near $x=6$). Explain this with a computation.
> The peak of a weighted component is its weight times the peak of its Gaussian: $\frac{\pi_k}{\sigma_k\sqrt{2\pi}}$, with $\sqrt{2\pi}=2.5066$.
> Component 1: $\frac{0.4}{0.5\times2.5066}=\frac{0.4}{1.2533}=0.3192$.
> Component 2: $\frac{0.6}{1.0\times2.5066}=0.2394$.
> Population 1 has fewer values, but they are packed into a range that is half as wide ($\sigma=0.5$ against $1$), so the density there is higher. The histogram bars (about $0.36$ and $0.25$) agree up to sampling noise.
> The area decides the share, not the height: the area of the left hump is $0.4$, of the right hump $0.6$.

> [!question]- Check (deck B, slide 5): The script starts with `mu = [1.5; 5.5]`. Predict what EM does if the start is `mu = [3.5; 3.5]` instead (same `sigma2 = [1; 1]`, `pi_k = [0.5; 0.5]`).
> Both components are identical at the start. So for every observation the two numerators are equal and $\gamma_{i1}=\gamma_{i2}=0.5$.
> M-step: $N_1=N_2=500$. Both means become the same weighted mean, which is the overall sample mean. Both variances become the overall sample variance. The weights stay $0.5$.
> Expected values, from the true mixture $0.4\,\mathcal N(2,0.5^2)+0.6\,\mathcal N(6,1^2)$: mean $0.4(2)+0.6(6)=4.4$. Variance $=\sum_k\pi_k\left(\sigma_k^2+(\mu_k-4.4)^2\right)=0.4(0.25+5.76)+0.6(1+2.56)=2.404+2.136=4.54$. The random sample gives values near these, not exactly these.
> The components are identical again, so the next iteration changes nothing. EM is stuck: the model is one wide Gaussian written twice, and the log-likelihood stops changing after the first iteration.
> Lesson: the starting means must be different. EM only finds a local solution that depends on the start.

> [!question]- Check (deck B, slide 6): The line `gamma = gamma ./ sum(gamma,2);` normalises the responsibilities. What is `sum(gamma,2)` before the division? What goes wrong if the student writes `sum(gamma,1)`?
> `gamma` is an $N\times K$ matrix of numerators $\pi_k\,\mathcal N(x_i\mid\mu_k,\sigma_k^2)$. `sum(gamma,2)` adds along each row. It is an $N\times1$ column with $\sum_k\pi_k\,\mathcal N(x_i\mid\mu_k,\sigma_k^2)=p(x_i)$, the mixture density at each observation. This is the denominator of the E-step.
> `sum(gamma,1)` adds along each column and gives a $1\times K$ row. After the division every column sums to 1, but the rows do not.
> Then `Nk = sum(gamma,1)'` is $(1,\ 1)$ and `pi_k(k) = Nk(k)/N` is $1/1000=0.001$ for both components. The weights sum to $0.002$, not to 1. The values in `gamma` are no longer probabilities of membership.

> [!question]- Check (deck B, slide 7): The plot uses `pdf1 = pi_k(1)*normpdf(...)`, not `normpdf(...)` alone. What is the area under `pdf1`, and under `pdfTotal`? What must the second figure (log-likelihood against iteration) look like if the code is correct?
> `normpdf` alone has area 1. Multiplied by the weight, `pdf1` has area $\pi_1$ (about $0.4$) and `pdf2` has area $\pi_2$ (about $0.6$).
> `pdfTotal = pdf1 + pdf2` has area $\pi_1+\pi_2=1$. It is the mixture density $p(x)$, so it can be compared with the pdf-normalised histogram. Without the weights the two curves would not add up to the histogram.
> The log-likelihood curve must never go down. It rises fast in the first iterations and then becomes flat. The loop stops when the change is below `tol` $=10^{-6}$. A curve that goes down means a bug.

## 8. MATLAB Examples 3 and 4: fitgmdist in 1-D and 2-D (deck B, pages 8-10)
![[ee525_lecture_05_expectation_maximization_em_and_mixture_density_estimation_p008.jpg]]
![[ee525_lecture_05_expectation_maximization_em_and_mixture_density_estimation_p009.jpg]]
![[ee525_lecture_05_expectation_maximization_em_and_mixture_density_estimation_p010.jpg]]

**Notes.**
- **Example 3**: the same fit with `fitgmdist(X,2,'RegularizationValue',1e-5,'Replicates',10)`. `GMModel.mu`, `GMModel.Sigma`, `GMModel.ComponentProportion` are the estimates. `posterior(GMModel,X)` returns the responsibilities and `max(P,[],2)` a hard label.
- **Example 4**: two features (object width and length), $300+300$ points from two correlated Gaussians (`mvnrnd`). Same `fitgmdist` call, scatter plot of the hard labels, density contours from `pdf(GMModel,gridPoints)`.
- `'Replicates',10`: ten starts, best result kept, because EM finds only a local maximum.
- `'RegularizationValue',1e-5`: a small constant added to the variances. It prevents a component from collapsing onto one data point (variance to 0, likelihood to infinity).

> [!info] Beyond the slides: number of parameters and BIC
> A one-feature GMM has $q=3K-1$ free parameters ($K$ means, $K$ variances, $K-1$ weights). With $d$ features and full covariances $q=Kd+K\frac{d(d+1)}{2}+(K-1)$.
> More components always fit the training data at least as well. The Bayesian information criterion adds a price per parameter: $\mathrm{BIC}=-2\ln\hat L+q\ln n$, with $\hat L$ the maximised likelihood and $n$ the number of observations. The smallest BIC wins.
> Example on the 1000 synthetic points of our notes: $K=1$: $-2(-2188.910)+2\ln1000=4391.64$. $K=2$: $-2(-1790.287)+5\ln1000=3615.11$. BIC prefers $K=2$.

> [!question]- Check (deck B, slide 8): The data are generated with `2 + 0.5*randn(400,1)` and `6 + 1.0*randn(600,1)`. About which values should `GMModel.mu`, `GMModel.Sigma` and `GMModel.ComponentProportion` show?
> `GMModel.mu`: about $2$ and $6$.
> `GMModel.Sigma`: about $0.25$ and $1$. `Sigma` holds variances (covariance matrices, here $1\times1$), not standard deviations. The factor $0.5$ in the code is $\sigma$, so $\sigma^2=0.5^2=0.25$.
> `GMModel.ComponentProportion`: about $0.4$ and $0.6$, because $\frac{400}{1000}=0.4$ and $\frac{600}{1000}=0.6$.
> The values are close to these numbers, not equal, because the data are random. The order can be swapped (label switching): the component with mean near $6$ may be printed first.

> [!question]- Check (deck B, slide 9): Use the true parameters $\pi=(0.4,\ 0.6)$, $\mathcal N(2,0.5^2)$, $\mathcal N(6,1^2)$. Which row does `posterior` return for $x=3.5$, and which label does `max(P,[],2)` give? The point is nearer to the mean $2$. Is the label a surprise?
> Component 1: $\mathcal N(3.5\mid2,0.5^2)=\frac{1}{0.5\times2.5066}\exp\left(-\frac{(3.5-2)^2}{2(0.25)}\right)=0.7979\,e^{-4.5}=0.7979\times0.011109=0.008864$.
> Component 2: $\mathcal N(3.5\mid6,1)=\frac{1}{2.5066}\exp\left(-\frac{(3.5-6)^2}{2}\right)=0.3989\,e^{-3.125}=0.3989\times0.043937=0.017528$.
> Numerators: $0.4\times0.008864=0.003545$ and $0.6\times0.017528=0.010517$. Sum $0.014062$.
> Row of `P`: $\left(\frac{0.003545}{0.014062},\ \frac{0.010517}{0.014062}\right)=(0.2521,\ 0.7479)$. Hard label: 2.
> In plain distance the point is nearer to mean 1 ($1.5$ against $2.5$). In standard deviations it is farther: $\frac{1.5}{0.5}=3$ against $\frac{2.5}{1}=2.5$. Component 2 is also wider and has the larger weight. So label 2 is correct.
> The hard label hides that the point is not certain: about 25% of the responsibility belongs to component 1.

> [!question]- Check (deck B, slide 10): How many free parameters does `fitgmdist(X,2,...)` estimate in Example 4 (two features, two components, full covariance matrices)? Compare with the number of data points.
> Formula from the info box above with $K=2$, $d=2$: $q=Kd+K\frac{d(d+1)}{2}+(K-1)$.
> Means: $Kd=2\times2=4$ (two mean vectors with 2 entries each).
> Covariances: $K\frac{d(d+1)}{2}=2\times\frac{2\times3}{2}=6$ (each symmetric $2\times2$ matrix has 3 different entries: two variances and one covariance).
> Weights: $K-1=1$ (the second weight is $1-\pi_1$).
> Total: $q=4+6+1=11$.
> The data are $300+300=600$ points with 2 values each, so 11 parameters are estimated well. In the code the true values are the mean vectors $[2\ 3]$, $[6\ 7]$ and the two matrices given to `mvnrnd`.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Log-likelihood | $\ell(\theta)=\sum_k\ln p(x_k\mid\theta)$ |
| Gaussian MLE | $\hat\mu=\frac1N\sum x_k$, $\hat\sigma^2=\frac1N\sum(x_k-\hat\mu)^2$ |
| MLE or unbiased | divide by $N$ (MLE) or by $N-1$ (`var`, `cov`) |
| Posterior of a parameter | $p(\theta\mid\mathcal D)=p(\mathcal D\mid\theta)p(\theta)/p(\mathcal D)$ |
| Posterior of the mean | $\frac{1}{\sigma_N^2}=\frac{N}{\sigma^2}+\frac{1}{\tau_0^2}$, $\mu_N=\sigma_N^2\left(\frac{N}{\sigma^2}\bar x+\frac{1}{\tau_0^2}\mu_0\right)$ |
| Shrinkage form | $\mu_N=w_N\bar x+(1-w_N)\mu_0$, $w_N=\frac{N\tau_0^2}{N\tau_0^2+\sigma^2}$ |
| Histogram height | count / ($N\times$ bin width) |
| GMM | $p(x)=\sum_k\pi_k\mathcal N(x\mid\mu_k,\sigma_k^2)$, $\pi_k\ge0$, $\sum_k\pi_k=1$ |
| Latent variable | the unknown source (component) of each sample |
| E-step | $\gamma_{ik}=\pi_k\mathcal N(x_i\mid\mu_k,\sigma_k^2)\big/\sum_j\pi_j\mathcal N(x_i\mid\mu_j,\sigma_j^2)$ |
| M-step | $N_k=\sum_i\gamma_{ik}$, $\mu_k=\frac{1}{N_k}\sum_i\gamma_{ik}x_i$, $\sigma_k^2=\frac{1}{N_k}\sum_i\gamma_{ik}(x_i-\mu_k)^2$, $\pi_k=\frac{N_k}{N}$ |
| EM property | the log-likelihood never decreases; local maximum only |
| BIC (beyond slides) | $-2\ln\hat L+q\ln n$, smallest wins |

## Check yourself
> [!question]- 1. Samples $2,4,6,8$. Compute $\hat\mu_{ML}$, $\hat\sigma^2_{ML}$ and the unbiased variance.
> Mean $20/4=5$. Squares $9+1+1+9=20$. $\hat\sigma^2_{ML}=20/4=5$. Unbiased $20/3=6.667$. Ratio $0.75=\frac{N-1}{N}$.

> [!question]- 2. $\mathcal D=\{4.0,4.4,4.5\}$, Gaussian with known $\sigma=0.5$. Which explains the data better, $\mu=4.0$ or $\mu=4.3$?
> $\ell(\mu)=-3\ln(0.5\sqrt{2\pi})-2\sum_k(x_k-\mu)^2=-0.6774-2\sum_k(x_k-\mu)^2$.
> $\mu=4.0$: sum $0.41$, $\ell=-1.4974$. $\mu=4.3$: sum $0.14$, $\ell=-0.9574$. So $\mu=4.3$ (the sample mean) is better.

> [!question]- 3. $\sigma^2=1$, prior $\mu\sim\mathcal N(10,\ 0.5)$, $N=4$ samples with $\bar x=12$. Find $\mu_N$, $\sigma_N^2$, $w_N$.
> Data precision $4/1=4$, prior precision $1/0.5=2$. $\mu_4=\frac{4(12)+2(10)}{6}=11.333$. $\sigma_4^2=1/6=0.1667$. $w_4=\frac{4(0.5)}{4(0.5)+1}=\frac23$. Check: $\frac23(12)+\frac13(10)=11.333$.

> [!question]- 4. Histogram with $N=200$ samples, bin width $0.5$, 30 samples in a bin. Height and probability of the bin?
> Height $30/(200\times0.5)=0.3$. Probability $0.3\times0.5=0.15$, which is $30/200$.

> [!question]- 5. $\pi=(0.3,0.7)$, $\mu=(2,5)$, $\sigma_1=\sigma_2=1$. Find the responsibilities of $x=3$.
> $\mathcal N(3\mid2,1)=0.24197$, $\mathcal N(3\mid5,1)=0.05399$. Numerators $0.07259$ and $0.03779$, sum $0.11038$. $\gamma_1=0.6576$, $\gamma_2=0.3424$.

> [!question]- 6. Observations $x=(0,2,4)$ with $\gamma_{i1}=(1,0.5,0)$. Compute $N_1$, $\mu_1^{new}$, $(\sigma_1^2)^{new}$, $\pi_1^{new}$.
> $N_1=1.5$. $\mu_1=\frac{0+1+0}{1.5}=0.6667$. Variance $\frac{1(0.4444)+0.5(1.7778)}{1.5}=0.8889$. $\pi_1=1.5/3=0.5$.

## Links
- Course index: [[EE525 Home]] (Homework 1 uses this lecture; its note is listed there).
- Previous: [[EE525 L04 - Minimum risk and discriminant functions]]. Bayes' rule and posteriors: [[EE525 L02 - Bayesian classifiers]].
- Full step-by-step notes (PDF): [EE525_Lecture_05_MLE_Bayes_EM.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/ee_525/lecture_notes/lecture_05/EE525_Lecture_05_MLE_Bayes_EM.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lecture_notes/lecture_05/EE525_Lecture_05_MLE_Bayes_EM.pdf`
- Deck A (PDF): [Maximum Likelihood and Bayesian Parameter Estimation.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_05/Maximum%20Likelihood%20and%20Bayesian%20Parameter%20Estimation.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_05/Maximum Likelihood and Bayesian Parameter Estimation.pdf`
- Deck B (PDF): [Expectation-Maximization (EM) and Mixture Density Estimation.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_05/Expectation-Maximization%20(EM)%20and%20Mixture%20Density%20Estimation.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_05/Expectation-Maximization (EM) and Mixture Density Estimation.pdf`

Slides not embedded: none.
