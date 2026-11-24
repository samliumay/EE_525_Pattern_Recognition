---
course: EE 525 Pattern Recognition
lecture: 4
title: Minimum risk and discriminant functions
tags: [ee525, lecture]
slides: lectures/lecture_04/Lecture 4-1_Minimum-risk classification vs Minimum-error-rate classification.pdf
slides_2: lectures/lecture_04/Lecture 4-2_Classifiers, Discriminant Functions and Decision Surfaces.pdf
full_notes: lecture_notes/lecture_04/EE525_Lecture_04_Risk_and_Discriminants.pdf
---
# EE 525 · Lecture 4 · Minimum risk and discriminant functions

> [!summary] In short
> - Lecture 4-1: a full loss matrix for the three Iris classes. In the script, rows = true class, columns = predicted class, and `risk = posterior * Loss` gives all conditional risks at once.
> - Minimum risk takes `min(risk,[],2)`. Minimum error takes `max(posterior,[],2)`. They agree when all mistakes cost the same.
> - Lecture run: the loss matrix changed 1 of 45 decisions. Error 2.22% to 4.44%, mean realized loss 0.044 to 0.089. One split only.
> - Lecture 4-2: every classifier is "one score $g_i(\mathbf{x})$ per class, take the largest".
> - Gaussian score = Mahalanobis term + spread term + prior term. Shared $\Sigma$ gives linear boundaries (LDA). Own $\Sigma_i$ gives quadratic boundaries (QDA). Diagonal $\Sigma_i$ is Gaussian naive Bayes.
> - k-NN, SVM and decision trees fit the same framework with other scores.

Both handouts are documents, not slides. "Page" means a page of the handout. Theory from [[EE525 L02 - Bayesian classifiers]], script from [[EE525 L03 - Iris example analysis]].

## Part A · Lecture 4-1: minimum risk against minimum error rate

## 1. Loss, conditional risk, zero-one loss (4-1, pages 1-2)
![[ee525_lecture_04_lecture_4_1_minimum_risk_classification_vs_minimum_error_rate_classification_p001.jpg]]
![[ee525_lecture_04_lecture_4_1_minimum_risk_classification_vs_minimum_error_rate_classification_p002.jpg]]

**Notes.** Classes $\omega_1,\dots,\omega_c$, actions $\alpha_1,\dots,\alpha_a$, loss $\lambda(\alpha_i\mid\omega_j)$ for taking $\alpha_i$ when the truth is $\omega_j$. The **conditional risk** is the expected loss of an action for this $\mathbf{x}$:

$$R(\alpha_i\mid\mathbf{x})=\sum_{j=1}^{c}\lambda(\alpha_i\mid\omega_j)P(\omega_j\mid\mathbf{x})$$

Choose the action with the smallest one. The minimum overall risk is the **Bayes risk**. With the **zero-one loss**, $R(\alpha_i\mid\mathbf{x})=1-P(\omega_i\mid\mathbf{x})$, so minimum risk = largest posterior.

Nursery example (page 2): a wrong species label costs complaints and replacement work. Illustrative loss units, rows = true species, columns = predicted species:

| true \ predicted | setosa | versicolor | virginica |
|---|---|---|---|
| setosa | 0 | 2 | 5 |
| versicolor | 3 | 0 | 2 |
| virginica | 6 | 3 | 0 |

The matrix is not symmetric. The worst mistake is a virginica sold as setosa (6).

> [!warning] Handout slips (page 1)
> - "$R(\alpha_i\mid\omega_j)$ is called the conditional risk" should read $R(\alpha_i\mid\mathbf{x})$.
> - "For $i=1,\dots,a$ and select the action..." is unfinished. It means: compute $R(\alpha_i\mid\mathbf{x})$ for $i=1,\dots,a$ and select the action with the smallest value.

> [!question]- Check (4-1, slide 1): Two classes, two actions. $\lambda(\alpha_1\mid\omega_1)=0$, $\lambda(\alpha_1\mid\omega_2)=10$, $\lambda(\alpha_2\mid\omega_1)=1$, $\lambda(\alpha_2\mid\omega_2)=0$, and $P(\omega_1\mid\mathbf{x})=0.85$. Compute both conditional risks. Which action is chosen?
> $P(\omega_2\mid\mathbf{x})=1-0.85=0.15$.
> $R(\alpha_1\mid\mathbf{x})=\lambda(\alpha_1\mid\omega_1)P(\omega_1\mid\mathbf{x})+\lambda(\alpha_1\mid\omega_2)P(\omega_2\mid\mathbf{x})=0(0.85)+10(0.15)=1.5$.
> $R(\alpha_2\mid\mathbf{x})=1(0.85)+0(0.15)=0.85$.
> $0.85<1.5$, so choose $\alpha_2$. The class $\omega_1$ is much more probable, but being wrong with $\alpha_1$ is ten times more expensive, so the rule decides $\omega_2$.

> [!question]- Check (4-1, slide 2): Use the nursery table. (a) What does it cost to call a true versicolor "setosa"? (b) A flower is surely a virginica, $\mathbf{p}=[0,\ 0,\ 1]$. What are the three conditional risks, and which row of the table do you read them from?
> (a) Row versicolor, column setosa: 3 loss units.
> (b) $R(\alpha_j\mid\mathbf{x})=\sum_t P(\omega_t\mid\mathbf{x})L_{tj}$. Only $t=3$ has a non-zero posterior, so $R(\alpha_j\mid\mathbf{x})=1\cdot L_{3j}$.
> The risks are the virginica row: predict setosa 6, predict versicolor 3, predict virginica 0. Choose virginica.
> When the class is certain, the risk of an action is just the loss in the row of the true class. With doubt, the risk is a mixture of the rows, weighted by the posteriors.

## 2. The loss matrix in MATLAB (4-1, pages 3-4)
![[ee525_lecture_04_lecture_4_1_minimum_risk_classification_vs_minimum_error_rate_classification_p003.jpg]]
![[ee525_lecture_04_lecture_4_1_minimum_risk_classification_vs_minimum_error_rate_classification_p004.jpg]]

```matlab
Loss = [0 2 5;      % rows = true class
        3 0 2;      % columns = predicted class
        6 3 0];     % order: setosa, versicolor, virginica
[~,idxError] = max(posterior,[],2);      % minimum-error-rate decision
risk = posterior * Loss;                 % 45x3 * 3x3 -> 45x3
[~,idxRisk] = min(risk,[],2);            % minimum-risk decision
```

**Notes.** Let $\mathbf{p}=[p_1,\dots,p_c]$ be the posterior row vector of one flower and $L_{tj}$ the loss when the true class is $\omega_t$ and we predict $\omega_j$. Then all risks are one product:

$$\mathbf{r}=\mathbf{p}\,L,\qquad r_j=\sum_{t=1}^{c}P(\omega_t\mid\mathbf{x})\,L_{tj}=R(\alpha_j\mid\mathbf{x})$$

Entry $j$ is "row $\mathbf{p}$ times column $j$ of $L$". With the zero-one matrix (page 3), $\mathbf{r}=\mathbf{1}-\mathbf{p}$.

> [!example] One flower, two answers (pages 4-5)
> $\mathbf{p}=[0.45,\ 0.40,\ 0.15]$ with the nursery matrix:
> $r_1=0.45(0)+0.40(3)+0.15(6)=2.10$, $r_2=0.45(2)+0.40(0)+0.15(3)=1.35$, $r_3=0.45(5)+0.40(2)+0.15(0)=3.05$.
> Minimum error: setosa (posterior 0.45). Minimum risk: versicolor (risk 1.35).

> [!warning] Watch out
> - Orientation: the script stores rows = true class. Lecture 2 wrote $\lambda(\alpha_i\mid\omega_j)$ with rows = action. The script matrix is the transpose. With the Lecture 2 orientation you must write `posterior * Loss'`.
> - `max` for posteriors, `min` for risks.
> - On page 4 the handout explains `predRisk = classNames(idxRisk)` with the words "index stored in idxError" and says `predRisk(:)` "converts predError". The names are mixed up. The paragraph describes the `predError` lines.

> [!question]- Check (4-1, slide 3): Take the zero-one matrix of page 3 and the posteriors $\mathbf{p}=[0.1,\ 0.6,\ 0.3]$. Compute the three risks $\mathbf{r}=\mathbf{p}\,\lambda$. Which index does `min` return, and which index does `max(posterior,[],2)` return?
> $r_1=0.1(0)+0.6(1)+0.3(1)=0.9$.
> $r_2=0.1(1)+0.6(0)+0.3(1)=0.4$.
> $r_3=0.1(1)+0.6(1)+0.3(0)=0.7$.
> This is $\mathbf{1}-\mathbf{p}=[0.9,\ 0.4,\ 0.7]$: each risk is the sum of the other two posteriors.
> `min` of the risks returns index 2. `max` of the posteriors returns index 2 too (0.6). With the zero-one matrix the two rules are the same rule.

> [!question]- Check (4-1, slide 4): Page 4 shows a posterior matrix with three samples. Take sample 3, $\mathbf{p}=[0.25,\ 0.30,\ 0.45]$, and the nursery matrix. Compute the risk row. What do `idxError(3)` and `idxRisk(3)` contain?
> $r_1=0.25(0)+0.30(3)+0.45(6)=0+0.90+2.70=3.60$.
> $r_2=0.25(2)+0.30(0)+0.45(3)=0.50+0+1.35=1.85$.
> $r_3=0.25(5)+0.30(2)+0.45(0)=1.25+0.60+0=1.85$.
> `idxError(3) = 3`: the largest posterior is 0.45, virginica.
> The risks of versicolor and virginica are equal (1.85). The minimum-risk rule is indifferent between them. `min` returns the first position of the smallest value, so `idxRisk(3) = 2`, versicolor.
> So the two rules can give different labels even when both labels have the same expected loss.

## 3. Comparing the two rules on the test set (4-1, pages 5-7)
![[ee525_lecture_04_lecture_4_1_minimum_risk_classification_vs_minimum_error_rate_classification_p005.jpg]]
![[ee525_lecture_04_lecture_4_1_minimum_risk_classification_vs_minimum_error_rate_classification_p006.jpg]]
![[ee525_lecture_04_lecture_4_1_minimum_risk_classification_vs_minimum_error_rate_classification_p007.jpg]]

**Notes.**
- `different = ~strcmp(predError,predRisk)` marks the flowers where the rules disagree. Output: `Decisions changed by the loss matrix: 1 of 45`.
- **Error rate**: fraction of wrong answers, every mistake counts 1.
- **Realized loss**: the price actually paid on the test set. `sub2ind(size(Loss),trueIdx,predIdx)` turns (row, column) pairs into linear indices, `Loss(...)` picks one loss per flower, `mean` averages.

| Rule | Error rate | Mean realized loss | Versicolor called virginica |
|---|---|---|---|
| Minimum error | $1/45=2.22\%$ | $2/45=0.044$ | 1 |
| Minimum risk | $2/45=4.44\%$ | $4/45=0.089$ | 2 |

Page 7 shows the two confusion charts (rows true, columns predicted): versicolor row $[0,14,1]$ for minimum error, $[0,13,2]$ for minimum risk. All setosa and virginica are correct in both.

> [!info] Why minimum risk "lost" here (beyond the slides)
> With no setosa doubt ($p_1=0$) the nursery rule says virginica when $p_3>0.4$, because a virginica called versicolor costs 3 and the reverse costs 2. In our Python stand-in for the lecture run, the changed flower was a versicolor (petal 5.1 by 1.6 cm) with posteriors $[0,\ 0.5695,\ 0.4305]$. The rule minimises the expected loss; this flower fell on the unlucky side. Over 200 splits the two rules differ by a few thousandths of a loss unit.

> [!warning] Watch out
> Do not conclude that minimum risk is worse. One flower decides the comparison on 45 test flowers.

> [!question]- Check (4-1, slide 5): Suppose `predError = {'setosa';'versicolor';'virginica';'versicolor'}` and `predRisk = {'setosa';'virginica';'virginica';'versicolor'}`. What does `different` hold, and what does the `fprintf` line print?
> `strcmp(predError,predRisk)` compares the two lists element by element: $[1;\ 0;\ 1;\ 1]$ (1 = same text).
> `~` inverts it: `different` $=[0;\ 1;\ 0;\ 0]$, a logical column vector with 1 where the rules disagree.
> `sum(different)` $=1$ and `numel(different)` $=4$.
> Output: `Decisions changed by the loss matrix: 1 of 4`.

> [!question]- Check (4-1, slide 6): A test flower is a true virginica and the rule predicts versicolor. What does `sub2ind(size(Loss),3,2)` return, and which loss does `Loss(...)` pick with it?
> MATLAB counts the elements of a matrix column by column. For a matrix with 3 rows: linear index $=\text{row}+(\text{column}-1)\cdot 3$.
> Here: $3+(2-1)\cdot 3=3+3=6$.
> Column by column, `Loss` is $0,3,6,\ 2,0,3,\ 5,2,0$. The 6th element is 3.
> This is $L_{32}=3$: virginica called versicolor. The script collects one such number per test flower and `mean` averages them.

> [!question]- Check (4-1, slide 7): Imagine a third rule whose confusion chart has the rows setosa $[15,0,0]$, versicolor $[0,14,1]$, virginica $[0,1,14]$. Compute its error rate and its mean realized loss with the nursery matrix. Compare with the minimum-risk chart of page 7.
> Wrong flowers: $1+1=2$. Error rate $=2/45=0.0444=4.44\%$. The same as the minimum-risk rule.
> Loss: one versicolor called virginica costs $L_{23}=2$. One virginica called versicolor costs $L_{32}=3$. Total $2+3=5$.
> Mean realized loss $=5/45=0.111$.
> The minimum-risk chart has two versicolor called virginica: $2+2=4$, mean $4/45=0.089$.
> Same error rate, different loss. The error rate counts mistakes. The realized loss also looks at which mistakes they are.

## Part B · Lecture 4-2: classifiers, discriminant functions, decision surfaces

## 4. Discriminant functions and decision surfaces (4-2, pages 1-2)
![[ee525_lecture_04_lecture_4_2_classifiers_discriminant_functions_and_decision_surfaces_p001.jpg]]
![[ee525_lecture_04_lecture_4_2_classifiers_discriminant_functions_and_decision_surfaces_p002.jpg]]

**Notes.** A classifier is a set of discriminant functions $g_i(\mathbf{x})$, $i=1,\dots,c$. It assigns $\mathbf{x}$ to $\omega_i$ if $g_i(\mathbf{x})>g_j(\mathbf{x})$ for all $j\neq i$. A discriminant is a score: one number per class, the largest wins.

- With risks: $g_i(\mathbf{x})=-R(\alpha_i\mid\mathbf{x})$. The minus sign turns "smallest risk" into "largest score".
- Minimum error: $g_i(\mathbf{x})=P(\omega_i\mid\mathbf{x})$, or $p(\mathbf{x}\mid\omega_i)P(\omega_i)$, or $\ln p(\mathbf{x}\mid\omega_i)+\ln P(\omega_i)$. Any monotonically increasing $f(g_i)$, the same for all classes, keeps the decisions.

The rule divides feature space into **decision regions** $R_1,\dots,R_c$, separated by **decision boundaries** where the two largest scores are equal. In the figure the boundary is two hyperbolas and $R_2$ comes in two pieces.

> [!warning] Wording
> The handout says $R_2$ is "not simply connected" (wording of the textbook). Strictly, a region in two pieces is not connected. Also, the handout has two sections numbered "1."; the second is Section 2.

> [!info] Why the boundary is a hyperbola (beyond the slides)
> The slide shows the figure without numbers. Here is a case you can do by hand. It uses the Gaussian score of section 5. For a diagonal covariance $\mathrm{diag}(s_1,s_2)$ and mean $(m_1,m_2)$ the score is $g=-\frac12\left(\frac{(x-m_1)^2}{s_1}+\frac{(y-m_2)^2}{s_2}\right)-\frac12\ln(s_1s_2)+\ln P$.
> - $\omega_1$: mean $(0,0)$, $\Sigma_1=\mathrm{diag}(0.5,\ 2)$, a tall cloud. $\omega_2$: mean $(1.5,0)$, $\Sigma_2=\mathrm{diag}(2,\ 0.5)$, a wide cloud. Equal priors.
> - Both determinants are $0.5\cdot2=1$, so $\ln\lvert\Sigma_i\rvert=0$. The priors are equal. Only the distance terms are left.
> - $g_1=-x^2-\frac{y^2}{4}$ and $g_2=-\frac{(x-1.5)^2}{4}-y^2$.
> - Set $g_1=g_2$ and multiply by $-4$: $4x^2+y^2=(x-1.5)^2+4y^2$. Then $3y^2=4x^2-(x^2-3x+2.25)=3x^2+3x-2.25$, so $y^2=x^2+x-0.75$.
> - Complete the square, $x^2+x=(x+0.5)^2-0.25$: the boundary is $(x+0.5)^2-y^2=1$. This is a hyperbola with two branches. They cross the $x$ axis at $x=0.5$ and $x=-1.5$.
> - $R_1$ is the band between the two branches (it contains the mean of $\omega_1$). $R_2$ is the rest: one piece on the right around its mean, one piece far on the left.
>
> The left piece exists because $\omega_2$ is wide in the $x$ direction. Far to the left both densities are tiny, but the wide one falls more slowly, so it is still the larger one there.

> [!question]- Check (4-2, slide 1): Three scores $g_1,g_2,g_3$ are replaced by new scores. Which replacements always keep the decision? (a) $2g_i+3$, (b) $\ln g_i$ when all $g_i>0$, (c) $-g_i$, (d) $g_i^2$.
> The decision stays the same when the same monotonically increasing function is applied to every score. Then the order of the scores does not change.
> (a) Yes. Multiplying by a positive constant and adding a constant is increasing.
> (b) Yes. $\ln$ is increasing on positive numbers. This is how $p(\mathbf{x}\mid\omega_i)P(\omega_i)$ becomes $\ln p(\mathbf{x}\mid\omega_i)+\ln P(\omega_i)$.
> (c) No. $-g_i$ is decreasing: the largest score becomes the smallest. This is why risks get a minus sign, $g_i=-R(\alpha_i\mid\mathbf{x})$.
> (d) Not always. $g^2$ is increasing only for $g\ge0$. With log scores $-3$ and $-1$ the winner is $-1$, but the squares are 9 and 1 and the winner changes.

> [!question]- Check (4-2, slide 2): In the figure, the red ellipse (class $\omega_2$) is on the right, but a second region $R_2$ lies far on the left, on the other side of $\omega_1$. (a) What holds for $g_1$ and $g_2$ exactly on the boundary? (b) How can a point far from the mean of $\omega_2$ still belong to $R_2$?
> (a) On the boundary the two scores are equal: $g_1(\mathbf{x})=g_2(\mathbf{x})$, here $p(\mathbf{x}\mid\omega_1)P(\omega_1)=p(\mathbf{x}\mid\omega_2)P(\omega_2)$.
> (b) The region is decided by which score is larger, not by which mean is nearer. The two Gaussians have different covariances. In the left-right direction $\omega_2$ is wider, so its density falls more slowly. Far to the left the narrow density of $\omega_1$ is already almost zero and the wide one is larger.
> With equal covariances this cannot happen: the boundary is one straight line and each region is one piece.

## 5. Scores for one flower, Bayesian and Gaussian discriminants (4-2, pages 3-5)
![[ee525_lecture_04_lecture_4_2_classifiers_discriminant_functions_and_decision_surfaces_p003.jpg]]
![[ee525_lecture_04_lecture_4_2_classifiers_discriminant_functions_and_decision_surfaces_p004.jpg]]
![[ee525_lecture_04_lecture_4_2_classifiers_discriminant_functions_and_decision_surfaces_p005.jpg]]

**Notes.** Each flower is $\mathbf{x}=[x_1,x_2,x_3,x_4]^{\mathsf T}$ (sepal length, sepal width, petal length, petal width). Each class gets a score $g_1,g_2,g_3$. Scores need not be probabilities.

**Bayesian discriminant.** The evidence is the same for every class, so maximise

$$g_i(\mathbf{x})=p(\mathbf{x}\mid\omega_i)P(\omega_i)\qquad\text{or}\qquad g_i(\mathbf{x})=\ln p(\mathbf{x}\mid\omega_i)+\ln P(\omega_i)$$

To use it we must estimate $p(\mathbf{x}\mid\omega_i)$: (1) Gaussian model, (2) no assumed distribution, (3) conditionally independent features, (4) other density estimation.

**Gaussian classifier** ($d=4$, mean $\boldsymbol\mu_i$ and covariance $\Sigma_i$ estimated from the training flowers of species $i$):

$$g_i(\mathbf{x})=-\tfrac12(\mathbf{x}-\boldsymbol\mu_i)^{\mathsf T}\Sigma_i^{-1}(\mathbf{x}-\boldsymbol\mu_i)-\tfrac12\ln|\Sigma_i|+\ln P(\omega_i)$$

Three terms: (1) squared **Mahalanobis distance** (how well the flower fits the class), (2) covariance determinant (spread of the class), (3) prior. The common term $-\frac d2\ln2\pi$ was dropped. The Gaussian classifier is a Bayesian classifier with Gaussian class-conditional densities.

> [!example] Illustrative score table (page 3)
> $p(\mathbf{x}\mid\omega_i)=0.80,0.20,0.10$ and $P(\omega_i)=0.33,0.33,0.34$. Scores $0.264,\ 0.066,\ 0.034$: setosa. Evidence $0.364$, posteriors $0.7253,\ 0.1813,\ 0.0934$.

> [!warning] Watch out
> - The example flower $[5.1,3.5,1.4,0.2]^{\mathsf T}$ is flower 1 of the data, a setosa. The scores $0.05/0.75/0.20$ (versicolor) on page 3 only illustrate the max rule.
> - Page 4 writes "estimate $p(x\mid\omega_1)$ for each species". It means $p(\mathbf{x}\mid\omega_i)$. The handout also writes $w_i$ for $\omega_i$.

> [!info] Reading the Mahalanobis term (beyond the slides)
> The slide calls the first term "distance-like" and stops there. With one feature, $\Sigma_i$ is just the variance $\sigma_i^2$ and the term is
> $$(\mathbf{x}-\boldsymbol\mu_i)^{\mathsf T}\Sigma_i^{-1}(\mathbf{x}-\boldsymbol\mu_i)=\frac{(x-\mu_i)^2}{\sigma_i^2}=\left(\frac{x-\mu_i}{\sigma_i}\right)^2$$
> So it is the squared distance from the class mean, counted in standard deviations of that class. A flower 2 cm from the mean is far for a class with $\sigma=0.5$ (4 standard deviations) and near for a class with $\sigma=2$ (1 standard deviation).
> With several features $\Sigma_i^{-1}$ does the same job in every direction and also takes correlations into account. For a diagonal $\Sigma_i=\mathrm{diag}(s_1,\dots,s_d)$ the term is $\sum_j (x_j-\mu_{ij})^2/s_j$ and $\lvert\Sigma_i\rvert=s_1s_2\cdots s_d$.
> The second term $-\frac12\ln\lvert\Sigma_i\rvert$ is a penalty for a wide class: a wide Gaussian spreads its probability over a large volume, so its density is lower everywhere.

> [!question]- Check (4-2, slide 3): For one flower, $p(\mathbf{x}\mid\omega_i)=0.30,\ 0.60,\ 0.50$ and $P(\omega_i)=0.6,\ 0.2,\ 0.2$ (setosa, versicolor, virginica). Compute the scores $g_i=p(\mathbf{x}\mid\omega_i)P(\omega_i)$, the decision, and the posteriors. Does the class with the largest likelihood win?
> $g_1=0.30(0.6)=0.18$, $g_2=0.60(0.2)=0.12$, $g_3=0.50(0.2)=0.10$.
> Largest score: $g_1$, so setosa.
> Evidence $p(\mathbf{x})=0.18+0.12+0.10=0.40$.
> Posteriors: $0.18/0.40=0.45$, $0.12/0.40=0.30$, $0.10/0.40=0.25$. Same order as the scores, because every score is divided by the same number.
> The largest likelihood is versicolor (0.60), but it does not win. The large prior of setosa (0.6) decides. The score needs both the likelihood and the prior.

> [!question]- Check (4-2, slide 4): Two features, $\mathbf{x}=[2,\ 1]^{\mathsf T}$, equal priors $0.5$. Class 1: $\boldsymbol\mu_1=[0,0]^{\mathsf T}$, $\Sigma_1=I$. Class 2: $\boldsymbol\mu_2=[3,1]^{\mathsf T}$, $\Sigma_2=\mathrm{diag}(4,\ 1)$. Compute the three terms of each Gaussian score and decide.
> Class 1. Distance term: $-\frac12\left(\frac{(2-0)^2}{1}+\frac{(1-0)^2}{1}\right)=-\frac12(4+1)=-2.5$. Determinant term: $\lvert\Sigma_1\rvert=1$, $-\frac12\ln1=0$. Prior term: $\ln0.5=-0.6931$.
> $g_1=-2.5+0-0.6931=-3.1931$.
> Class 2. Distance term: $-\frac12\left(\frac{(2-3)^2}{4}+\frac{(1-1)^2}{1}\right)=-\frac12(0.25+0)=-0.125$. Determinant term: $\lvert\Sigma_2\rvert=4\cdot1=4$, $-\frac12\ln4=-0.6931$. Prior term: $\ln0.5=-0.6931$.
> $g_2=-0.125-0.6931-0.6931=-1.5113$.
> $g_2>g_1$, so class 2. The point fits class 2 much better (distance term $-0.125$ against $-2.5$). Class 2 pays $0.6931$ for its larger spread, but that is not enough to change the result.

> [!question]- Check (4-2, slide 5): Two features. $g_1(\mathbf{x})=x_1+2x_2-3$ and $g_2(\mathbf{x})=2x_1-x_2+1$. (a) Write the boundary between the two classes. (b) Classify $\mathbf{x}=[1,\ 2]^{\mathsf T}$.
> (a) $\mathbf{w}_1=[1,2]^{\mathsf T}$, $w_{10}=-3$, $\mathbf{w}_2=[2,-1]^{\mathsf T}$, $w_{20}=1$.
> $\mathbf{w}_1-\mathbf{w}_2=[1-2,\ 2-(-1)]^{\mathsf T}=[-1,\ 3]^{\mathsf T}$ and $w_{10}-w_{20}=-3-1=-4$.
> Boundary: $-x_1+3x_2-4=0$. One linear equation in two features: a straight line.
> (b) $g_1=1+2(2)-3=2$ and $g_2=2(1)-2+1=1$. $g_1>g_2$, so class 1.
> Check with the boundary expression: $-1+3(2)-4=1>0$. Positive means $g_1-g_2>0$, the class 1 side.

## 6. Linear classifier, LDA and QDA (4-2, pages 6-8)
![[ee525_lecture_04_lecture_4_2_classifiers_discriminant_functions_and_decision_surfaces_p006.jpg]]
![[ee525_lecture_04_lecture_4_2_classifiers_discriminant_functions_and_decision_surfaces_p007.jpg]]
![[ee525_lecture_04_lecture_4_2_classifiers_discriminant_functions_and_decision_surfaces_p008.jpg]]

**Notes.** A **linear classifier** uses $g_i(\mathbf{x})=\mathbf{w}_i^{\mathsf T}\mathbf{x}+w_{i0}$ (weight vector $\mathbf{w}_i$, bias $w_{i0}$). The boundary between classes 1 and 2 is

$$(\mathbf{w}_1-\mathbf{w}_2)^{\mathsf T}\mathbf{x}+(w_{10}-w_{20})=0$$

a hyperplane (a line for two features). Where the weights come from depends on the method: LDA, linear SVM, perceptron, logistic regression.

**LDA: one shared covariance** $\Sigma_1=\Sigma_2=\Sigma_3=\Sigma$. Expand the quadratic term:

$$g_i(\mathbf{x})=-\tfrac12\mathbf{x}^{\mathsf T}\Sigma^{-1}\mathbf{x}+\boldsymbol\mu_i^{\mathsf T}\Sigma^{-1}\mathbf{x}-\tfrac12\boldsymbol\mu_i^{\mathsf T}\Sigma^{-1}\boldsymbol\mu_i-\tfrac12\ln|\Sigma|+\ln P(\omega_i)$$

The first term and the determinant term are the same for all classes. Drop them:

$$g_i(\mathbf{x})=\mathbf{w}_i^{\mathsf T}\mathbf{x}+w_{i0},\qquad \mathbf{w}_i=\Sigma^{-1}\boldsymbol\mu_i,\qquad w_{i0}=-\tfrac12\boldsymbol\mu_i^{\mathsf T}\Sigma^{-1}\boldsymbol\mu_i+\ln P(\omega_i)$$

**QDA: each class its own** $\Sigma_i$. The term $-\frac12\mathbf{x}^{\mathsf T}\Sigma_i^{-1}\mathbf{x}$ depends on the class and does not cancel. The boundary can be curved.

| | Covariance | Boundary | Covariance parameters ($d=4$, $c=3$) |
|---|---|---|---|
| nearest mean | $\sigma^2I$ for all | lines (perpendicular bisectors) | 1 |
| LDA | one shared $\Sigma$ | lines (tilted) | 10 |
| QDA | own $\Sigma_i$ | curves | 30 |

> [!warning] Watch out
> - In QDA the term $-\frac12\ln|\Sigma_i|$ is not common and must be kept.
> - The handout writes $\Sigma_1\neq\Sigma_2\neq\Sigma_3$. Read it as "not all equal".
> - The figures on pages 6 and 8 come from other software and other training sets. They show shapes, not the boundaries of the course script.

> [!question]- Check (4-2, slide 6): Look at the coloured figure at the top of page 6. Which species does this linear classifier give to a flower with petal length 6 cm and petal width 0.5 cm? Are there training flowers near this point? What does that tell you?
> The point $(6,\ 0.5)$ lies in the orange region, so the answer is versicolor.
> There are no flowers near it. The versicolor flowers are around lengths 3 to 5 with widths 1 to 1.8, and the virginica flowers have widths above about 1.4.
> A classifier gives an answer for every point of the feature space, because some score is always the largest. Far from the training data this answer only continues the straight lines. It is not supported by any flower.

> [!question]- Check (4-2, slide 7): Shared covariance $\Sigma=\mathrm{diag}(1,\ 0.5)$, means $\boldsymbol\mu_1=[1,1]^{\mathsf T}$ and $\boldsymbol\mu_2=[3,2]^{\mathsf T}$, equal priors $0.5$. Compute $\mathbf{w}_i$, $w_{i0}$ and the boundary line. Does the midpoint of the two means lie on it?
> $\Sigma^{-1}=\mathrm{diag}(1,\ 2)$.
> $\mathbf{w}_1=\Sigma^{-1}\boldsymbol\mu_1=[1(1),\ 2(1)]^{\mathsf T}=[1,\ 2]^{\mathsf T}$. $\boldsymbol\mu_1^{\mathsf T}\Sigma^{-1}\boldsymbol\mu_1=1(1)+1(2)=3$, so $w_{10}=-\frac12(3)+\ln0.5=-1.5-0.6931=-2.1931$.
> $\mathbf{w}_2=\Sigma^{-1}\boldsymbol\mu_2=[1(3),\ 2(2)]^{\mathsf T}=[3,\ 4]^{\mathsf T}$. $\boldsymbol\mu_2^{\mathsf T}\Sigma^{-1}\boldsymbol\mu_2=3(3)+2(4)=17$, so $w_{20}=-\frac12(17)+\ln0.5=-8.5-0.6931=-9.1931$.
> Boundary: $(\mathbf{w}_1-\mathbf{w}_2)^{\mathsf T}\mathbf{x}+(w_{10}-w_{20})=0$, that is $-2x_1-2x_2+7=0$, or $x_1+x_2=3.5$. The $\ln0.5$ terms cancel.
> Midpoint: $(2,\ 1.5)$, and $2+1.5=3.5$. Yes. With equal priors the LDA boundary always passes through the midpoint of the two means.

> [!question]- Check (4-2, slides 7-8): In the right picture of page 8 (QDA) the boundary between versicolor and virginica is curved. In the left picture (LDA) it is straight. Which term of the Gaussian score makes the difference? What happens to QDA if the three estimated $\Sigma_i$ are all equal?
> On a boundary $g_i-g_j=0$. The difference contains $-\frac12\mathbf{x}^{\mathsf T}\left(\Sigma_i^{-1}-\Sigma_j^{-1}\right)\mathbf{x}$.
> In QDA the two inverse covariances are different, so this term stays. The boundary equation has squares and products of the features: a curve (ellipse, parabola or hyperbola).
> In LDA $\Sigma_i=\Sigma_j$, the bracket is zero and only terms of first degree in $\mathbf{x}$ are left: a line.
> If all $\Sigma_i$ are equal, the quadratic terms cancel and the terms $-\frac12\ln\lvert\Sigma_i\rvert$ cancel too. QDA then gives straight boundaries of the LDA form.

> [!question]- Check (4-2, slide 8): Naive Bayes with two features and equal priors $0.5$. Class A: $p(x_1\mid A)=0.4$, $p(x_2\mid A)=0.1$. Class B: $p(x_1\mid B)=0.2$, $p(x_2\mid B)=0.3$. Compute both scores $g_i=\ln P(\omega_i)+\sum_j\ln p(x_j\mid\omega_i)$ and decide.
> $g_A=\ln0.5+\ln0.4+\ln0.1=-0.6931-0.9163-2.3026=-3.9120$.
> $g_B=\ln0.5+\ln0.2+\ln0.3=-0.6931-1.6094-1.2040=-3.5066$.
> $g_B>g_A$, so class B.
> Check without logarithms: $0.5(0.4)(0.1)=0.02$ and $0.5(0.2)(0.3)=0.03$, and $\ln0.02=-3.9120$, $\ln0.03=-3.5066$.
> Feature 1 alone prefers A ($0.4>0.2$). Feature 2 prefers B more strongly ($0.3$ against $0.1$). Naive Bayes adds the log evidence of the features, one feature at a time.

## 7. Naive Bayes, k-NN, SVM, decision tree (4-2, pages 8-9)
![[ee525_lecture_04_lecture_4_2_classifiers_discriminant_functions_and_decision_surfaces_p009.jpg]]

**Notes.** (Page 8 is embedded in group 6.)

- **Naive Bayes**: features conditionally independent given the class, $p(\mathbf{x}\mid\omega_i)=\prod_{j=1}^{4}p(x_j\mid\omega_i)$, so
$$g_i(\mathbf{x})=\ln P(\omega_i)+\sum_{j=1}^{4}\ln p(x_j\mid\omega_i)$$
With Gaussian factors it is Gaussian naive Bayes: QDA with every $\Sigma_i$ diagonal. It ignores within-class correlations.
- **k-NN**: vote count among the $k$ nearest training samples $N_k(\mathbf{x})$, with labels $y_j$:
$$g_i(\mathbf{x})=\sum_{j\in N_k(\mathbf{x})}\mathbf{1}(y_j=i)$$
Example $k=5$: 3 versicolor, 2 virginica, 0 setosa give scores $0,3,2$: versicolor. No distribution is assumed.
- **SVM**: binary linear SVM $g(\mathbf{x})=\mathbf{w}^{\mathsf T}\mathbf{x}+b$, classify by the sign. Large margin. For three species MATLAB combines binary SVMs with error-correcting output codes (ECOC). A Gaussian (RBF) kernel gives curved boundaries.
- **Decision tree**: successive questions on single features. $g_i(\mathbf{x})=1$ if the tree predicts class $i$, else 0. Boundaries are piecewise linear and axis-aligned.

> [!warning] Handout gaps (page 9)
> The SVM paragraph stops at "Its score" and the formula is missing. The tree paragraph has a doubled "is is".

> [!info] The missing kernel SVM score and ECOC (beyond the slides)
> The handout stops at "Its score". The standard score of a kernel SVM is
> $$g(\mathbf{x})=\sum_{n}\alpha_n y_n K(\mathbf{x}_n,\mathbf{x})+b$$
> - The sum runs over the **support vectors** $\mathbf{x}_n$: the training samples near the boundary. All other training samples have weight $\alpha_n=0$.
> - $y_n=\pm1$ is the label of support vector $n$, $\alpha_n\ge0$ is its learned weight, $b$ is the bias.
> - $K$ is the kernel, a similarity between two points. Gaussian kernel: $K(\mathbf{x}_n,\mathbf{x})=\exp(-\|\mathbf{x}_n-\mathbf{x}\|^2/s^2)$. It is 1 when the two points are equal and goes to 0 when they are far apart. $s$ is MATLAB's `KernelScale`.
> - So each support vector votes for its own label, and near support vectors count more. The decision is again the sign of $g(\mathbf{x})$. Because $K$ is not linear in $\mathbf{x}$, the boundary $g(\mathbf{x})=0$ can be curved.
>
> A binary SVM gives one score for two classes. The other methods give one score per class. ECOC is the bridge: `fitcecoc` trains several binary SVMs (by default one for each pair: setosa/versicolor, setosa/virginica, versicolor/virginica) and combines their answers into one of three classes.

> [!question]- Check (4-2, slide 9): k-NN. The labels of the 7 nearest training flowers, from nearest to farthest, are: versicolor, virginica, virginica, versicolor, versicolor, virginica, virginica. Give the scores and the decision for $k=1$, $k=3$, $k=5$ and $k=7$.
> Score = number of neighbours of that class among the first $k$. No setosa appears, so $g_1=0$ every time.
> $k=1$: versicolor 1, virginica 0. Versicolor.
> $k=3$: versicolor 1, virginica 2. Virginica.
> $k=5$: versicolor 3, virginica 2. Versicolor.
> $k=7$: versicolor 3, virginica 4. Virginica.
> Near a boundary the answer can change with $k$. A small $k$ follows single flowers. A large $k$ averages over a wider area.

> [!question]- Check (4-2, slide 9): (a) A binary linear SVM has $\mathbf{w}=[1,\ -2]^{\mathsf T}$ and $b=0.5$. Which side is $\mathbf{x}=[3,\ 1]^{\mathsf T}$ on? (b) A decision tree sends a flower to a leaf labelled virginica. What are $g_1,g_2,g_3$? Why are tree boundaries parallel to the axes?
> (a) $g(\mathbf{x})=\mathbf{w}^{\mathsf T}\mathbf{x}+b=1(3)+(-2)(1)+0.5=3-2+0.5=1.5$. The sign is positive, so the positive class. Here there is one score for two classes, and its sign decides.
> (b) Indicator scores: $g_1=0$, $g_2=0$, $g_3=1$. The largest is $g_3$, virginica.
> Each question of the tree compares one single feature with a threshold, for example "petal length $<2.5$?". The border of such a question is the line "feature = threshold", which is parallel to the other axis. Many such pieces give a boundary that looks like stairs.

## 8. Comparison table and the six decision-region pictures (4-2, pages 10-12)
![[ee525_lecture_04_lecture_4_2_classifiers_discriminant_functions_and_decision_surfaces_p010.jpg]]
![[ee525_lecture_04_lecture_4_2_classifiers_discriminant_functions_and_decision_surfaces_p011.jpg]]
![[ee525_lecture_04_lecture_4_2_classifiers_discriminant_functions_and_decision_surfaces_p012.jpg]]

**Notes.**

| Classifier | Discriminant function or rule | Typical boundary |
|---|---|---|
| Bayesian, general | $\ln p(\mathbf{x}\mid\omega_i)+\ln P(\omega_i)$ | depends on density model |
| Gaussian, shared $\Sigma$ (LDA) | $\mathbf{w}_i^{\mathsf T}\mathbf{x}+b_i$ | linear |
| Gaussian, own $\Sigma_i$ (QDA) | quadratic function of $\mathbf{x}$ | quadratic |
| Naive Bayes | $\ln P(\omega_i)+\sum_j\ln p(x_j\mid\omega_i)$ | depends on feature distributions |
| Linear SVM | $\mathbf{w}^{\mathsf T}\mathbf{x}+b$ (binary) | linear |
| Kernel SVM | kernel-based score | often nonlinear |
| k-NN | neighbour-vote score | often nonlinear |
| Decision tree | rule-based class score | piecewise, axis-aligned |

MATLAB commands:
- `fitcdiscr(X,Y,'DiscrimType','linear')`: LDA. Gaussian classes, one shared covariance, straight boundaries.
- `fitcdiscr(X,Y,'DiscrimType','quadratic')`: QDA. One covariance per class, curved boundaries.
- `fitcnb(X,Y,'DistributionNames','normal')`: Gaussian naive Bayes. Diagonal covariance, own variances per class, so axis-aligned ellipses and still a curved boundary.

Page 12 shows six panels (petal length against petal width): LDA and linear SVM have straight boundaries, QDA, naive Bayes, k-NN and the Gaussian-kernel SVM have curved ones.

> [!warning] Watch out
> The script fits every model on all 150 flowers. There is no test set, so the pictures show shapes and training behaviour only. Page 10 writes `fitcdiscr(Xtrain,Ytrain,...)` for LDA but `X,Y` for QDA; the script uses `X,Y` for all.

> [!question]- Check (4-2, slide 10): Find the two mistakes. "`fitcdiscr(X,Y,'DiscrimType','linear')` assumes nothing about the data, it only searches for the best straight lines. With `'quadratic'` the Gaussian assumption is dropped, so the boundaries can be curved."
> Mistake 1: `'linear'` (LDA) does make assumptions. Every class is Gaussian and all classes share one covariance matrix. The straight lines are a consequence of these two assumptions, they are not searched for directly.
> Mistake 2: `'quadratic'` (QDA) keeps the Gaussian assumption. It only drops the shared covariance: each class gets its own $\Sigma_i$. Then the quadratic terms do not cancel and the boundaries can be curved.
> Both are Gaussian Bayesian classifiers. They differ in one assumption only.

> [!question]- Check (4-2, slide 11): Two features, three classes. How many mean and covariance numbers do LDA, QDA and Gaussian naive Bayes estimate? Under which condition would the naive Bayes boundaries be straight?
> Means: $3\times2=6$ in every case.
> A full $2\times2$ covariance is symmetric: 2 variances + 1 covariance = 3 numbers. A diagonal one has 2 numbers.
> LDA: one shared full covariance, $6+3=9$.
> QDA: three full covariances, $6+3(3)=6+9=15$.
> Gaussian naive Bayes: three diagonal covariances, $6+3(2)=6+6=12$.
> Naive Bayes is curved because each class has its own variances, so the terms in $x_j^2$ differ between classes and do not cancel. If every feature had the same variance in all three classes, the diagonal covariance would be shared, the squared terms would cancel and the boundaries would be straight lines.

> [!question]- Check (4-2, slide 12): Look at the six panels. Take a point at the bottom right: petal length 6.5 cm, petal width 0.2 cm. Which class does each of the six classifiers give? Why can they disagree so much there?
> Versicolor (orange): LDA, k-NN and linear SVM.
> Virginica (green): QDA, Gaussian naive Bayes and Gaussian-kernel SVM.
> No flower lies near this point. All six models agree fairly well where the flowers are. Away from the flowers each model continues its own shape: straight lines for LDA and the linear SVM, ellipse-like curves for QDA and naive Bayes, a closed "island" for versicolor in the Gaussian-kernel SVM.
> So the pictures are reliable only near the data. The difference in the empty corners comes from the model assumptions, not from the flowers.

## 9. MATLAB script printout: Experiment1_BayesianIris_RiskVsMinumumError.m (pages 1-5)
![[ee525_lecture_04_experiment1_bayesianiris_riskvsminumumerror_m_p001.jpg]]
![[ee525_lecture_04_experiment1_bayesianiris_riskvsminumumerror_m_p002.jpg]]
![[ee525_lecture_04_experiment1_bayesianiris_riskvsminumumerror_m_p003.jpg]]
![[ee525_lecture_04_experiment1_bayesianiris_riskvsminumumerror_m_p004.jpg]]
![[ee525_lecture_04_experiment1_bayesianiris_riskvsminumumerror_m_p005.jpg]]

**What the script does.** It is the Lecture 3 Iris script (split, Gaussian fit, posteriors, largest posterior, plots) plus section `%% 3B` at the end of the file: loss matrix, `risk = posterior * Loss`, minimum-risk decisions, comparison table, error rates, realized losses, two confusion charts.
This copy is identical to the one in the Lecture 3 folder; only the print time stamp differs. The handout says to insert 3B "immediately after" section 3; in the file it sits after section 8, which still works.

> [!info] The script block by block (beyond the slides)
> The printout has no explanation, only code. What each block computes, with the sizes of the variables:
> - **Block 1 (page 1), data and split.** `X = meas(:,3:4)` keeps columns 3 and 4: petal length and width, size $150\times2$. `rng(10)` fixes the random number generator, so the split is the same in every run. `cvpartition(Y,'HoldOut',0.30)` puts 30% of the flowers in the test set and keeps the class proportions: 105 training flowers (35 per species), 45 test flowers (15 per species). `classNames = unique(Ytrain)` is the sorted list setosa, versicolor, virginica, so $K=3$.
> - **Block 2 (page 1), estimation.** For each class $k$, `Xi` holds the training rows of that class. `priors(k)` $=N_k/N$, `mu(k,:)` is the mean row ($1\times2$), `Sigma(:,:,k) = cov(Xi)` is the $2\times2$ sample covariance (division by $N_k-1$). Sizes: `priors` $3\times1$, `mu` $3\times2$, `Sigma` $2\times2\times3$.
> - **Block 3 (pages 1-2), Bayes' rule.** `likelihood(:,k) = mvnpdf(Xtest,mu(k,:),Sigma(:,:,k))` is the Gaussian density of class $k$ at every test flower, so `likelihood` is $45\times3$. `joint = likelihood .* priors'` multiplies column $k$ by `priors(k)`: this is $p(\mathbf{x}\mid\omega_k)P(\omega_k)$. `sum(joint,2)` is the evidence $p(\mathbf{x})$ of each flower ($45\times1$). `posterior = joint ./ sum(joint,2)` divides each row by its own sum, so every row of `posterior` adds up to 1.
> - **Block 4 (page 2), decision.** `max(posterior,[],2)` gives the column index of the largest posterior in each row. `errorRate` is the fraction of test flowers where the predicted name differs from `Ytest`.
> - **Block 7 (pages 2-3), decision regions.** `meshgrid` with `linspace(...,250)` builds a $250\times250$ grid. `G = [x1(:),x2(:)]` lists the grid points as rows. `scoreGrid(:,k)` is prior times density at every grid point. `gridClass` is the winning class number (1, 2 or 3), reshaped back to $250\times250$. `contourf` with the levels `0.5:1:3.5` paints the three regions and draws black lines between them.
> - **Block 3B (pages 3-5), risk.** Explained in groups 2 and 3 above.

> [!question]- Check (Experiment1 script, slide 1): The script uses `cvpartition(Y,'HoldOut',0.30)` on 150 flowers, 50 per species. How many training and test flowers are there, in total and per species, and what is `priors`? What changes with `'HoldOut',0.20`? What does `rng(10)` do?
> With $0.30$: test $=0.30\times150=45$, training $=150-45=105$. The split keeps the class proportions: $45/3=15$ test and $105/3=35$ training flowers per species.
> `priors(k)` $=35/105=0.333$ for each species.
> With $0.20$: test $=0.20\times150=30$ (10 per species), training $=120$ (40 per species). `priors(k)` $=40/120=0.333$, unchanged. The means and covariances change a little, because other flowers are used.
> `rng(10)` sets the seed of the random generator. The same flowers go to the test set in every run, so the printed numbers can be reproduced. Another seed gives another split and other error rates.

> [!question]- Check (Experiment1 script, slide 2): Suppose `priors = [0.5; 0.25; 0.25]` and one row of `likelihood` is $[0.1,\ 0.6,\ 0.2]$. Compute this row of `joint` and of `posterior`. Why is `priors` transposed in `likelihood .* priors'`?
> `joint` $=[0.1(0.5),\ 0.6(0.25),\ 0.2(0.25)]=[0.05,\ 0.15,\ 0.05]$.
> `sum(joint,2)` $=0.05+0.15+0.05=0.25$. This is the evidence $p(\mathbf{x})$.
> `posterior` $=[0.05/0.25,\ 0.15/0.25,\ 0.05/0.25]=[0.2,\ 0.6,\ 0.2]$. The row adds up to 1.
> `priors` is a $3\times1$ column. `likelihood` has one column per class. The transpose `priors'` is a $1\times3$ row, so MATLAB multiplies column $k$ of `likelihood` by `priors(k)` in every row. Without the transpose the sizes $45\times3$ and $3\times1$ do not fit and MATLAB stops with an error.

> [!question]- Check (Experiment1 script, slides 2-3): In block 7, how many rows do `G` and `scoreGrid` have? Which values does `gridClass` contain? `scoreGrid` is `priors(k) * mvnpdf(...)`, without division by the evidence. Why is the picture still correct?
> The grid is $250\times250=62500$ points. `G` is $62500\times2$ (one point per row), `scoreGrid` is $62500\times3$ (one score per class).
> `gridClass` contains the class numbers 1, 2 or 3, first as a $62500\times1$ column, after `reshape` as a $250\times250$ matrix like `x1`.
> The evidence $p(\mathbf{x})$ is the same for all three classes at one point. Dividing three scores by the same positive number does not change which one is largest. So $p(\mathbf{x}\mid\omega_k)P(\omega_k)$ is already a valid discriminant, as on page 3 of Lecture 4-2.
> The contour levels $0.5,\ 1.5,\ 2.5,\ 3.5$ lie between the class numbers, so each class number gets its own band and its own colour.

> [!question]- Check (Experiment1 script, slide 4): A student has the loss matrix with rows = true class but writes `risk = posterior * Loss'`. Take the flower $\mathbf{p}=[0.45,\ 0.40,\ 0.15]$ and the nursery matrix. Which risks does this line give, and which decision? Compare with the correct line.
> `Loss'` is the transpose: its column $j$ is row $j$ of `Loss`. So entry $j$ is $\sum_t p_t L_{jt}$.
> Entry 1: $0.45(0)+0.40(2)+0.15(5)=0+0.80+0.75=1.55$.
> Entry 2: $0.45(3)+0.40(0)+0.15(2)=1.35+0+0.30=1.65$.
> Entry 3: $0.45(6)+0.40(3)+0.15(0)=2.70+1.20+0=3.90$.
> `min` picks entry 1: setosa.
> The correct line `posterior * Loss` gives $[2.10,\ 1.35,\ 3.05]$ and versicolor. MATLAB gives no error, because both products are $1\times3$ times $3\times3$. The wrong line uses the price of "true $j$, predicted $t$" where the price of "true $t$, predicted $j$" is needed. Always check the orientation of the loss matrix.

> [!question]- Check (Experiment1 script, slide 5): Replace the loss matrix by `Loss = ones(3) - eye(3)` and run block 3B again on the same split. What do the three output blocks print?
> `ones(3) - eye(3)` is the zero-one matrix: 0 on the diagonal, 1 everywhere else.
> Then `risk = posterior * Loss` $=1-$ `posterior` in every row. The smallest risk is at the largest posterior, so `idxRisk` equals `idxError` for every flower.
> `Decisions changed by the loss matrix: 0 of 45`.
> Both rules make the same single mistake of the lecture run: `Error rate = 2.22%` twice ($1/45=0.0222$).
> Every mistake now costs 1, so the realized loss is the number of mistakes divided by 45: `Mean realized loss = 0.022 units/sample` twice. With the zero-one loss, mean realized loss and error rate are the same number.
> The two confusion charts are identical.

## 10. MATLAB script printout: Discrimiant_Functions_Comparison_TwoFeatures_Figures.m (pages 1-3)
![[ee525_lecture_04_discrimiant_functions_comparison_twofeatures_figures_m_p001.jpg]]
![[ee525_lecture_04_discrimiant_functions_comparison_twofeatures_figures_m_p002.jpg]]
![[ee525_lecture_04_discrimiant_functions_comparison_twofeatures_figures_m_p003.jpg]]

**What the script does.** It fits six classifiers on petal length and width of all 150 flowers: LDA, QDA, Gaussian naive Bayes, 5-NN (standardised features), linear SVM and Gaussian-kernel SVM (both multiclass through `fitcecoc`).
Then it predicts the class of every point of a $300\times300$ grid, paints the regions with `imagesc` and overlays the flowers: the six panels of page 12. In this script virginica is green (yellow in the Lecture 3 script).

> [!info] The script block by block (beyond the slides)
> The printout has no explanation, only code. What each block does:
> - **Fitting (page 1).** Six `fit...` calls, all on the same `X` ($150\times2$) and `Y`. `fitcdiscr` with `'linear'` or `'quadratic'` is LDA or QDA. `fitcnb` with `'normal'` is Gaussian naive Bayes. `fitcknn` with `'NumNeighbors',5` is 5-NN. `templateSVM` only describes one binary SVM (kernel `'linear'` or `'gaussian'`); `fitcecoc` trains several of them and combines them for three classes.
> - **`'Standardize'`.** For k-NN and both SVMs each feature is first changed to (value minus mean) divided by standard deviation. These methods use Euclidean distances, and without this step the feature with the larger numbers (petal length) would count more.
> - **Grid (page 2).** `xMin`, `xMax`, `yMin`, `yMax` are the data limits plus a margin (0.5 cm in length, 0.3 cm in width). `meshgrid` with two `linspace(...,300)` gives a $300\times300$ grid. `XGrid = [xGrid(:), yGrid(:)]` lists the $90000$ grid points as rows, in the same two-column format as `X`.
> - **Loop (pages 2-3), once per model.** `predict(models{k}, XGrid)` returns a species name for every grid point. The inner loop turns the names into numbers: `Z` is 1 for setosa, 2 for versicolor, 3 for virginica. `reshape` brings `Z` back to $300\times300$.
> - **Painting.** `imagesc` draws `Z` as an image, one coloured cell per grid point. An image is drawn from the top down, so `set(gca,'YDir','normal')` turns the $y$ axis back: small widths at the bottom. `colormap(gca, classColors)` with `clim([0.5 3.5])` splits the range into three equal parts, so the values 1, 2, 3 get the colours blue, orange, green.
> - **Overlay (page 3).** For each class, `idx = strcmp(Y, classNames{c})` selects its 50 flowers and `scatter` draws them in the class colour with a black edge. The legend is added only in the first panel (`if k == 1`).

> [!question]- Check (Figures script, slide 1): In the Iris data the standard deviation of petal length is 1.765 cm and of petal width 0.762 cm. Flowers A and B differ by 1 cm in length only. Flowers A and C differ by 1 cm in width only. Compute both distances after `'Standardize'`. Why does the script standardise for k-NN but not for LDA?
> Standardising divides each feature by its standard deviation (the subtraction of the mean cancels in a difference).
> A to B: $1/1.765=0.567$. A to C: $1/0.762=1.312$.
> Without standardising both distances are 1 cm. After it, 1 cm in width counts more than twice as much as 1 cm in length, because 1 cm is a large step for the width (range 0.1 to 2.5 cm) and a small step for the length (range 1.0 to 6.9 cm).
> k-NN decides by Euclidean distance, so the units of the features change its neighbours. LDA and QDA use the Mahalanobis term with $\Sigma^{-1}$, which already rescales every direction. Standardising would change their weights but not their decisions.

> [!question]- Check (Figures script, slide 2): Petal length runs from 1.0 to 6.9 cm. Compute `xMin`, `xMax` and the distance between two neighbouring grid points in the length direction. How many rows has `XGrid`, and how many class predictions does the whole loop make? What would a 0 in `Z` mean?
> `xMin` $=1.0-0.5=0.5$ and `xMax` $=6.9+0.5=7.4$.
> `linspace(0.5, 7.4, 300)` has 300 points and 299 gaps: step $=(7.4-0.5)/299=6.9/299=0.0231$ cm.
> `XGrid` has $300\times300=90000$ rows and 2 columns.
> The loop calls `predict` once per model: $6\times90000=540000$ predictions.
> `Z` starts as zeros and is filled with 1, 2 or 3 where the predicted name equals a name in `classNames`. A remaining 0 would be a grid point whose predicted label matches none of the three names, for example after a spelling mistake in `classNames`. With `clim([0.5 3.5])` it would be painted in the first colour, blue, so the mistake would be hard to see.

> [!question]- Check (Figures script, slide 3): What does `idx = strcmp(Y, classNames{c})` hold, and how many times is `scatter` called in the whole script? In a panel, an orange dot lies on the green region. What does it mean, and is it a test error?
> `idx` is a $150\times1$ logical vector: 1 for the flowers of class `c`, 0 for the others. Each species has 50 flowers, so 50 entries are 1.
> `scatter` runs once per class and per panel: $3\times6=18$ calls.
> The dot colour is the true species (orange = versicolor). The background colour is the prediction of the model at that place (green = virginica). So this versicolor flower is classified as virginica: a mistake.
> It is a training error, not a test error. All 150 flowers were used to fit the models, and the same flowers are drawn. The pictures say nothing sure about new flowers.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Conditional risk | $R(\alpha_i\mid\mathbf{x})=\sum_j\lambda(\alpha_i\mid\omega_j)P(\omega_j\mid\mathbf{x})$ |
| Loss matrix in the script | $L_{tj}$: true class $t$, predicted class $j$ |
| All risks at once | $\mathbf{r}=\mathbf{p}L$; MATLAB `risk = posterior * Loss` |
| Minimum risk / minimum error | `min(risk,[],2)` / `max(posterior,[],2)` |
| Zero-one loss | $\mathbf{p}L=\mathbf{1}-\mathbf{p}$: largest posterior |
| Realized loss | mean of `Loss(sub2ind(size(Loss),trueIdx,predIdx))` |
| Decision rule | $\mathbf{x}\to\omega_k$ if $g_k(\mathbf{x})>g_i(\mathbf{x})$ for all $i\neq k$ |
| Gaussian discriminant | $-\frac12(\mathbf{x}-\boldsymbol\mu_i)^{\mathsf T}\Sigma_i^{-1}(\mathbf{x}-\boldsymbol\mu_i)-\frac12\ln\lvert\Sigma_i\rvert+\ln P(\omega_i)$ |
| LDA (shared $\Sigma$) | $\mathbf{w}_i=\Sigma^{-1}\boldsymbol\mu_i$, $w_{i0}=-\frac12\boldsymbol\mu_i^{\mathsf T}\Sigma^{-1}\boldsymbol\mu_i+\ln P(\omega_i)$ |
| QDA (own $\Sigma_i$) | quadratic term stays, curved boundaries |
| Naive Bayes | $\ln P(\omega_i)+\sum_j\ln p(x_j\mid\omega_i)$ (diagonal $\Sigma_i$) |
| k-NN | $g_i$ = number of the $k$ nearest neighbours in class $i$ |
| SVM / tree | sign of $\mathbf{w}^{\mathsf T}\mathbf{x}+b$ (ECOC for 3 classes) / indicator of the leaf |

## Check yourself
> [!question]- 1. Nursery matrix. Compute the three risks and both decisions for $\mathbf{p}=[0.2,\ 0.5,\ 0.3]$.
> $r_1=0.5(3)+0.3(6)=3.3$, $r_2=0.2(2)+0.3(3)=1.3$, $r_3=0.2(5)+0.5(2)=2.0$. Minimum risk: versicolor. Minimum error: versicolor too (posterior 0.5).

> [!question]- 2. Loss matrix (rows true A, B, C) $[0,1,4]$, $[2,0,1]$, $[5,2,0]$, posteriors $[0.5,0.3,0.2]$. Risks, both decisions, error probability of each decision?
> Predict A: $0.3(2)+0.2(5)=1.6$. Predict B: $0.5(1)+0.2(2)=0.9$. Predict C: $0.5(4)+0.3(1)=2.3$.
> Minimum risk: B. Minimum error: A. Error probability: $0.5$ for A, $0.7$ for B. The risk rule accepts more errors to avoid the expensive ones.

> [!question]- 3. Confusion matrices on 45 flowers: A has rows $[15,0,0]$, $[0,13,2]$, $[0,1,14]$; B has rows $[15,0,0]$, $[0,12,3]$, $[0,0,15]$. Error rate and mean realized loss with the nursery matrix?
> Both have 3 errors: $6.67\%$. Loss of A: $2(2)+1(3)=7$, mean $7/45=0.156$. Loss of B: $3(2)=6$, mean $0.133$. The nursery prefers B. The error rate cannot see the difference.

> [!question]- 4. Show that a shared covariance gives a linear discriminant. Which terms are dropped?
> Expand: $-\frac12\mathbf{x}^{\mathsf T}\Sigma^{-1}\mathbf{x}+\boldsymbol\mu_i^{\mathsf T}\Sigma^{-1}\mathbf{x}-\frac12\boldsymbol\mu_i^{\mathsf T}\Sigma^{-1}\boldsymbol\mu_i-\frac12\ln|\Sigma|+\ln P(\omega_i)$. The two cross terms are equal because $\Sigma^{-1}$ is symmetric.
> Drop $-\frac12\mathbf{x}^{\mathsf T}\Sigma^{-1}\mathbf{x}$ and $-\frac12\ln|\Sigma|$: they are the same for every class. Left: $\mathbf{w}_i=\Sigma^{-1}\boldsymbol\mu_i$, $w_{i0}=-\frac12\boldsymbol\mu_i^{\mathsf T}\Sigma^{-1}\boldsymbol\mu_i+\ln P(\omega_i)$. With own $\Sigma_i$ nothing cancels (QDA).

> [!question]- 5. One feature, equal priors, $\omega_1\sim N(0,1)$, $\omega_2\sim N(1,4)$. Find the decision regions.
> $g_1=-\frac{x^2}{2}$, $g_2=-\frac{(x-1)^2}{8}-\ln2$. Setting $g_1=g_2$: $3x^2+2x-(1+8\ln2)=0$, so $x=1.1809$ or $x=-1.8475$.
> $R_1=(-1.8475,\ 1.1809)$. $R_2$ is the rest: two pieces, not connected.

> [!question]- 6. For $d=4$, $c=3$: how many mean and covariance parameters do LDA, QDA and Gaussian naive Bayes estimate?
> Means: 12 in every case. One full covariance: 10. LDA $12+10=22$, QDA $12+30=42$, naive Bayes $12+12=24$. QDA needs the most training data.

## Links
- Course index: [[EE525 Home]]
- Previous: [[EE525 L03 - Iris example analysis]]. Next: [[EE525 L05 - MLE, Bayesian estimation and EM]].
- Full step-by-step notes (PDF): [EE525_Lecture_04_Risk_and_Discriminants.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/ee_525/lecture_notes/lecture_04/EE525_Lecture_04_Risk_and_Discriminants.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lecture_notes/lecture_04/EE525_Lecture_04_Risk_and_Discriminants.pdf`
- Handout 4-1 (PDF): [Lecture 4-1](file:///home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_04/Lecture%204-1_Minimum-risk%20classification%20vs%20Minimum-error-rate%20classification.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_04/Lecture 4-1_Minimum-risk classification vs Minimum-error-rate classification.pdf`
- Handout 4-2 (PDF): [Lecture 4-2](file:///home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_04/Lecture%204-2_Classifiers,%20Discriminant%20Functions%20and%20Decision%20Surfaces.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_04/Lecture 4-2_Classifiers, Discriminant Functions and Decision Surfaces.pdf`
- Script printout, risk against minimum error (PDF): [Experiment1 printout](file:///home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_04/lms.tedu.edu.tr_pluginfile.php_82482_mod_resource_content_1_Experiment1_BayesianIris_RiskVsMinumumError.m.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_04/lms.tedu.edu.tr_pluginfile.php_82482_mod_resource_content_1_Experiment1_BayesianIris_RiskVsMinumumError.m.pdf`
- Script printout, six classifiers (PDF): [Discriminant functions comparison printout](file:///home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_04/lms.tedu.edu.tr_pluginfile.php_82486_mod_resource_content_1_Discrimiant_Functions_Comparison_TwoFeatures_Figures.m.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_04/lms.tedu.edu.tr_pluginfile.php_82486_mod_resource_content_1_Discrimiant_Functions_Comparison_TwoFeatures_Figures.m.pdf`

Slides not embedded: none.
