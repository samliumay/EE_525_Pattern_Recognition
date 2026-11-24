---
course: EE 525 Pattern Recognition
lecture: 3
title: Iris example analysis
tags: [ee525, lecture]
slides: lectures/lecture_03/Lecture3-Bayesian Classifier Example Analysis.pdf
full_notes: lecture_notes/lecture_03/EE525_Lecture_03_Iris_Example_Analysis.pdf
---
# EE 525 · Lecture 3 · Iris example analysis

> [!summary] In short
> - The lecture reads one MATLAB script line by line: a Gaussian Bayes classifier for Iris with two features (petal length, petal width) and three classes.
> - Pipeline: stratified split, estimate prior, mean and covariance per class on the training set only, likelihoods of the test points, posteriors, largest posterior, error rate.
> - `mvnpdf` evaluates one 2-D Gaussian per class. Own $\Sigma_k$ per class = QDA. Shared $\Sigma$ = LDA. Diagonal $\Sigma_k$ = naive Bayes.
> - `joint = likelihood .* priors'`, `posterior = joint ./ sum(joint,2)`: Bayes' rule, row by row.
> - Lecture run (`rng(10)`): 105 training and 45 test flowers, 1 error, test error 2.22%.

The handout is a 12-page document, not slides. "Page" below means a page of the handout. Theory: [[EE525 L02 - Bayesian classifiers]].

## 1. The Iris data (pages 1-2)
![[ee525_lecture_03_lecture3_bayesian_classifier_example_analysis_p001.jpg]]
![[ee525_lecture_03_lecture3_bayesian_classifier_example_analysis_p002.jpg]]

**Notes.** Fisher's Iris data (1936): 150 flowers, 50 per class, three species (setosa, versicolor, virginica). Four measurements in cm: sepal length, sepal width, petal length, petal width. `meas` is $150\times4$; rows 1-50 are setosa, 51-100 versicolor, 101-150 virginica.

The script keeps columns 3 and 4, so each flower is a point $\mathbf{x}=[x_1,x_2]^{\mathsf T}$ (petal length, petal width). Class names: $\omega_1$ = setosa, $\omega_2$ = versicolor, $\omega_3$ = virginica.

> [!warning] Handout spelling
> The handout writes "Sentosa" and "Virginic". The names are setosa and virginica, as MATLAB prints them.

> [!question]- Check (slide 1): `meas` is $150\times4$ with columns sepal length, sepal width, petal length, petal width. Write the MATLAB expression for the sepal widths of all versicolor flowers. What size is the result?
> Versicolor is the second block of 50 rows: rows 51 to 100. Sepal width is column 2.
> `meas(51:100,2)` is a $50\times1$ column.
> The script itself does not use it: it keeps only columns 3 and 4 (the petal features).

> [!question]- Check (slide 2): Suppose the script kept all four features: `X = meas(:,1:4)`. What sizes do `X`, one class mean and one class covariance get? How many numbers are then estimated per class (mean plus covariance)? Which later lines of the script must change?
> `X` is $150\times4$. One mean is $1\times4$. One covariance is $4\times4$.
> A covariance matrix is symmetric, so it has $d(d+1)/2$ different entries. With $d=4$: $4\cdot5/2=10$.
> Per class: $4+10=14$ numbers. With two features it was $2+3=5$.
> `mu = zeros(K,2)` and `Sigma = zeros(2,2,K)` must become `zeros(K,4)` and `zeros(4,4,K)`, otherwise the assignment `mu(k,:) = mean(Xi,1)` fails (a $1\times4$ row does not fit in a $1\times2$ row). The two scatter and region plots only work for two features.

## 2. Step 1: choose the features and split the data (page 3)
![[ee525_lecture_03_lecture3_bayesian_classifier_example_analysis_p003.jpg]]

```matlab
X = meas(:,3:4);               % petal length, petal width -> 150x2
Y = species;                   % 150x1 cell of class names
rng(10);                       % fix the random numbers
cv = cvpartition(Y,'HoldOut',0.30);
Xtrain = X(training(cv),:);   Ytrain = Y(training(cv));
Xtest  = X(test(cv),:);        Ytest  = Y(test(cv));
classNames = unique(Ytrain);   K = numel(classNames);   % K = 3
```

**Notes.**
- `rng(10)` seeds the random generator, so every run gives the same split and the same printed results.
- `cvpartition(Y,'HoldOut',0.30)` keeps 30% for testing. Because `Y` holds class labels, the split is **stratified**: each class keeps its proportion in both sets.
- `training(cv)` and `test(cv)` are logical vectors ($150\times1$, entries 1/0) and are opposites. `X(training(cv),:)` keeps the rows marked 1.
- Sizes: test $0.30\times150=45$ (15 per class), training $105$ (35 per class).

> [!warning] Watch out
> Never estimate anything from the test set. The test flowers play the role of new, unseen patterns. (The handout comment says `test(c)`; it means `test(cv)`.)

> [!question]- Check (slide 3): A small data set has 10 flowers and `training(cv)` returns $[1,1,0,1,0,1,1,1,0,1]^{\mathsf T}$. What is `test(cv)`? What sizes do `Xtrain` and `Xtest` have (two features)? Which rows of `X` end up in `Xtest`?
> `test(cv)` is the logical opposite: $[0,0,1,0,1,0,0,0,1,0]^{\mathsf T}$.
> The training vector has seven 1s, so `Xtrain` is $7\times2$. The test vector has three 1s, so `Xtest` is $3\times2$. Check: $7+3=10$.
> `Xtest = X(test(cv),:)` keeps the rows marked 1: rows 3, 5 and 9 of `X`, in that order.
> Every flower is in exactly one of the two sets, never in both.

## 3. Step 2: priors, means and covariances (page 4)
![[ee525_lecture_03_lecture3_bayesian_classifier_example_analysis_p004.jpg]]

```matlab
for k = 1:K
    Xi = Xtrain(strcmp(Ytrain,classNames{k}),:);  % rows of class k
    priors(k)    = size(Xi,1)/size(Xtrain,1);     % N_k / N
    mu(k,:)      = mean(Xi,1);                    % column means
    Sigma(:,:,k) = cov(Xi);                       % 2x2 covariance
end
```

**Notes.** $N$ = number of training flowers (105), $N_k$ = number in class $k$ (35). A hat means "estimated from the training data".

$$\hat P(\omega_k)=\frac{N_k}{N},\qquad \hat{\boldsymbol\mu}_k=\frac1{N_k}\sum_n\mathbf{x}_n,\qquad \hat\Sigma_k=\frac1{N_k-1}\sum_n(\mathbf{x}_n-\hat{\boldsymbol\mu}_k)(\mathbf{x}_n-\hat{\boldsymbol\mu}_k)^{\mathsf T}$$

The diagonal of $\Sigma$ holds the variance of each feature. The off-diagonal entry is the covariance: positive if long petals tend to be wide. Here every prior is $35/105=1/3$.

> [!example] Mean and covariance by hand
> Flowers $[1.4,0.2]$, $[1.5,0.3]$, $[1.3,0.2]$, $[1.6,0.3]$: $\hat{\boldsymbol\mu}=[1.45,\ 0.25]$.
> Sums of squared deviations $0.05$ and $0.01$, sum of products $0.02$. Divide by $N-1=3$:
> $$\hat\Sigma=\begin{bmatrix}0.01667&0.00667\\0.00667&0.00333\end{bmatrix}$$

> [!warning] Watch out
> `cov` divides by $N_k-1$ (unbiased). The maximum-likelihood estimate divides by $N_k$ (see [[EE525 L05 - MLE, Bayesian estimation and EM]]). With 35 flowers the ratio is $35/34=1.029$.

> [!info] The second half of page 4: what the loop was for
> Page 4 ends the training loop and starts code section 3. It repeats Bayes' rule, $P(\omega_i\mid\mathbf{x})=p(\mathbf{x}\mid\omega_i)P(\omega_i)/p(\mathbf{x})$, and says what is still missing: the class-conditional density $p(\mathbf{x}\mid\omega_i)$. The handout assumes it is Gaussian and first shows the one-feature (univariate) form:
> $$p(x)=\frac{1}{\sqrt{2\pi}\,\sigma}\exp\left[-\frac12\left(\frac{x-\mu}{\sigma}\right)^2\right]$$
> - $\mu$ = mean (centre of the bell), $\sigma$ = standard deviation (width), $\sigma^2$ = variance.
> - $(x-\mu)/\sigma$ = distance from the centre, counted in standard deviations.
> - The factor $1/(\sqrt{2\pi}\,\sigma)$ is the height of the peak. It makes the area under the curve equal to 1.
>
> The priors, means and covariances of the loop are exactly the numbers this formula (in its 2-D form, next section) needs.

> [!question]- Check (slide 4): One feature, $\mu=4.3$, $\sigma=0.5$. Evaluate the univariate Gaussian density at $x=4.8$. Then give the peak height for $\sigma=0.5$ and for $\sigma=0.2$. What does the second peak value tell you?
> Formula: $p(x)=\dfrac{1}{\sqrt{2\pi}\,\sigma}\exp\left[-\dfrac12\left(\dfrac{x-\mu}{\sigma}\right)^2\right]$.
> Distance in standard deviations: $(4.8-4.3)/0.5=1$. Exponent: $-\tfrac12\cdot1^2=-0.5$, and $e^{-0.5}=0.6065$.
> Front factor: $\dfrac{1}{\sqrt{2\pi}\cdot0.5}=\dfrac{1}{1.2533}=0.7979$.
> $p(4.8)=0.7979\times0.6065=0.4839$.
> Peak ($x=\mu$, exponent 0): $0.7979$ for $\sigma=0.5$. For $\sigma=0.2$: $\dfrac{1}{\sqrt{2\pi}\cdot0.2}=\dfrac{1}{0.5013}=1.9947$.
> A density can be larger than 1. Only the area under the curve must be 1: a narrow bell must be tall.

## 4. Step 3: one 2-D Gaussian per class (pages 5-6)
![[ee525_lecture_03_lecture3_bayesian_classifier_example_analysis_p005.jpg]]
![[ee525_lecture_03_lecture3_bayesian_classifier_example_analysis_p006.jpg]]

```matlab
likelihood = zeros(size(Xtest,1),K);           % 45x3
for k = 1:K
    likelihood(:,k) = mvnpdf(Xtest,mu(k,:),Sigma(:,:,k));
end
```

**Notes.** Each class-conditional density is one two-dimensional Gaussian ($d=2$), not two separate one-dimensional ones:

$$p(\mathbf{x}\mid\omega_k)=\frac{1}{(2\pi)^{d/2}|\Sigma_k|^{1/2}}\exp\left(-\tfrac12(\mathbf{x}-\boldsymbol\mu_k)^{\mathsf T}\Sigma_k^{-1}(\mathbf{x}-\boldsymbol\mu_k)\right)$$

After training, $\boldsymbol\mu_k$ and $\Sigma_k$ are fixed. A test flower near $\boldsymbol\mu_k$ gets a high density, a flower far away a low one. Column $k$ of `likelihood` says how typical each test flower is for class $k$.

For a $2\times2$ matrix with entries $a,b,b,c$: $|\Sigma|=ac-b^2$ and

$$\Sigma^{-1}=\frac{1}{ac-b^2}\begin{bmatrix}c&-b\\-b&a\end{bmatrix}$$

If $\Sigma$ is diagonal, the 2-D Gaussian is the product of two 1-D Gaussians. Forcing every $\Sigma_k$ to be diagonal gives **naive Bayes** (features independent within a class). In the script: `Sigma(:,:,k) = diag(var(Xi));`.

> [!warning] Handout wording
> - The handout says the full-covariance model "is performing Linear or Quadratic Discriminant Analysis". Each class has its own $\Sigma_k$, so it is quadratic: QDA. LDA needs one shared $\Sigma$.
> - $p(\mathbf{x}\mid\omega_k)$ is a density and can be larger than 1.
> - The Gaussian figure marks 2.5% tails beyond $\pm2\sigma$. The exact value is 2.28% (same figure as in Lecture 2).

> [!question]- Check (slide 5): A class has a $2\times2$ covariance matrix $\Sigma_k$ with first row $[0.25,\ 0.06]$ and second row $[0.06,\ 0.04]$ (feature 1 = petal length, feature 2 = petal width). Read off the two variances and standard deviations. What does the entry $0.06$ say? Compute the correlation coefficient $\rho=\dfrac{\text{covariance}}{\sigma_1\sigma_2}$.
> Diagonal: variance of petal length $0.25$, so $\sigma_1=\sqrt{0.25}=0.5$ cm. Variance of petal width $0.04$, so $\sigma_2=\sqrt{0.04}=0.2$ cm.
> Off-diagonal: covariance $0.06>0$. Inside this class, flowers with longer petals tend to have wider petals. The cloud is tilted upwards ("the slant" of the handout).
> $\rho=\dfrac{0.06}{0.5\times0.2}=\dfrac{0.06}{0.10}=0.6$.
> $\rho$ always lies between $-1$ and $1$. The value $0.6$ is a clear positive relation, but not a perfect line. (The formula for $\rho$ is beyond the slides.)

> [!question]- Check (slide 6): Naive Bayes case. $\boldsymbol\mu=[4.0,\ 1.3]$, and $\Sigma$ is diagonal: first row $[0.25,\ 0]$, second row $[0,\ 0.04]$. Compute $p(\mathbf{x})$ at $\mathbf{x}=[4.5,\ 1.5]$ with the 2-D formula. Then check the handout's claim: the product of two 1-D Gaussians gives the same number.
> 2-D formula with $d=2$ (the handout writes $n$ for $d$): $p(\mathbf{x})=\dfrac{1}{2\pi\lvert\Sigma\rvert^{1/2}}\exp\left(-\tfrac12(\mathbf{x}-\boldsymbol\mu)^{\mathsf T}\Sigma^{-1}(\mathbf{x}-\boldsymbol\mu)\right)$.
> Difference: $\mathbf{x}-\boldsymbol\mu=[0.5,\ 0.2]$.
> Determinant: $\lvert\Sigma\rvert=0.25\times0.04-0^2=0.01$, so $\lvert\Sigma\rvert^{1/2}=0.1$.
> Inverse of a diagonal matrix: $\Sigma^{-1}=\begin{bmatrix}4&0\\0&25\end{bmatrix}$.
> Quadratic form: $4(0.5)^2+25(0.2)^2=1+1=2$. Exponent: $-\tfrac12\cdot2=-1$, and $e^{-1}=0.3679$.
> $p(\mathbf{x})=\dfrac{1}{2\pi\times0.1}\times0.3679=1.5915\times0.3679=0.5855$.
> 1-D check. Feature 1 ($\sigma=0.5$, one standard deviation away): $\dfrac{1}{\sqrt{2\pi}\cdot0.5}e^{-0.5}=0.7979\times0.6065=0.4839$.
> Feature 2 ($\sigma=0.2$, one standard deviation away): $\dfrac{1}{\sqrt{2\pi}\cdot0.2}e^{-0.5}=1.9947\times0.6065=1.2099$.
> Product: $0.4839\times1.2099=0.5855$. Same number, so a diagonal $\Sigma$ means "two independent 1-D Gaussians".

## 5. Step 4: from likelihoods to posteriors (page 7)
![[ee525_lecture_03_lecture3_bayesian_classifier_example_analysis_p007.jpg]]

```matlab
joint     = likelihood .* priors';       % (45x3) .* (1x3) -> 45x3
posterior = joint ./ sum(joint,2);       % (45x3) ./ (45x1) -> 45x3
```

**Notes.** For test flower $n$ and class $k$:

$$P(\omega_k\mid\mathbf{x}_n)=\frac{p(\mathbf{x}_n\mid\omega_k)P(\omega_k)}{\sum_{j=1}^{3}p(\mathbf{x}_n\mid\omega_j)P(\omega_j)}$$

- `priors'` is a $1\times3$ row. `.*` multiplies column $k$ of the likelihood by prior $k$ (entry by entry, not a matrix product).
- `sum(joint,2)` adds along each row: the evidence $p(\mathbf{x}_n)$, one number per flower ($45\times1$).
- `./` divides every entry of row $n$ by the $n$-th evidence. Each row of `posterior` adds to 1.

> [!example] Two test flowers, priors $[0.2,0.3,0.5]$
> Likelihood rows $[0.50,0.20,0.10]$ and $[0.02,0.40,0.30]$. Joint rows $[0.100,0.060,0.050]$ and $[0.004,0.120,0.150]$, evidences $0.210$ and $0.274$.
> Posteriors $[0.476,0.286,0.238]$ and $[0.015,0.438,0.547]$. For flower 2, class 2 has the larger likelihood but class 3 wins the posterior because of its prior. With equal priors (Iris) this cannot happen.

> [!question]- Check (slide 7): A student types `sum(joint,1)` instead of `sum(joint,2)`. Take a `joint` with two rows (two flowers): $[0.10,\ 0.06,\ 0.04]$ and $[0.02,\ 0.03,\ 0.15]$. Compute the correct posteriors, then the student's result. How do you see that the student's result is wrong?
> Correct: `sum(joint,2)` adds along each row. Row 1: $0.10+0.06+0.04=0.20$. Row 2: $0.02+0.03+0.15=0.20$. These are the evidences $p(\mathbf{x}_1)$ and $p(\mathbf{x}_2)$.
> Posteriors: row 1 $[0.10,0.06,0.04]/0.20=[0.50,\ 0.30,\ 0.20]$. Row 2 $[0.02,0.03,0.15]/0.20=[0.10,\ 0.15,\ 0.75]$. Each row adds to 1.
> Student: `sum(joint,1)` adds down each column: $[0.12,\ 0.09,\ 0.19]$, a $1\times3$ row. `./` then divides column $k$ by the $k$-th sum.
> Row 1 becomes $[0.10/0.12,\ 0.06/0.09,\ 0.04/0.19]=[0.833,\ 0.667,\ 0.211]$, which adds to $1.711$, not 1.
> The student normalised over the flowers (each column adds to 1) instead of over the classes. MATLAB gives no error, because the sizes still fit. In the real script the result is still $45\times3$, so only the row sums show the mistake.

## 6. Step 5: decide and count the errors (pages 8-9)
![[ee525_lecture_03_lecture3_bayesian_classifier_example_analysis_p008.jpg]]
![[ee525_lecture_03_lecture3_bayesian_classifier_example_analysis_p009.jpg]]

```matlab
[~,idx]   = max(posterior,[],2);         % 45x1, values 1, 2 or 3
predicted = classNames(idx);             % numbers -> names
predicted = predicted(:);                % force a 45x1 column
errorRate = mean(~strcmp(predicted,Ytest));
```

**Notes.** `max(posterior,[],2)` looks along each row. `~` in the output list discards the value and keeps only the column index. This is the minimum-error-rate rule: largest posterior. `strcmp` gives 1 for a correct flower, `~` flips it, and `mean` of the 0/1 vector is errors/45.

Error rate = wrong/total. Accuracy = 1 − error rate.

Lecture run: one of 45 test flowers is misclassified, error rate $1/45=2.22\%$, accuracy $97.78\%$. Test flower 1 (petal length 1.30 cm, width 0.20 cm): posteriors setosa 1, versicolor $1.7609\times10^{-10}$, virginica $4.6551\times10^{-18}$. Actual and predicted class: setosa.

> [!warning] Watch out
> - The "1" for setosa is rounded on display. The true value is $1-1.7609\times10^{-10}-4.6551\times10^{-18}$.
> - One split of 45 flowers is noisy: each mistake changes the result by 2.22%. Our notes checked 200 random splits in Python: average test error QDA 2.84%, naive Bayes 4.04%, LDA 4.21%.

> [!question]- Check (slide 8): Four test flowers have `posterior` rows $[0.90,0.10,0.00]$, $[0.05,0.60,0.35]$, $[0.00,0.45,0.55]$, $[0.00,0.20,0.80]$. The true classes are setosa, versicolor, versicolor, virginica. Follow the four code lines: give `idx`, `predicted`, the `strcmp` vector, and `errorRate`.
> `[~,idx] = max(posterior,[],2)`: position of the largest entry in each row. `idx` $=[1,2,3,3]^{\mathsf T}$.
> `classNames(idx)`: 1 = setosa, 2 = versicolor, 3 = virginica. `predicted` = setosa, versicolor, virginica, virginica.
> `strcmp(predicted,Ytest)` $=[1,1,0,1]^{\mathsf T}$ (flower 3 is a versicolor called virginica).
> `~` flips it: $[0,0,1,0]^{\mathsf T}$. `mean` $=(0+0+1+0)/4=0.25$.
> Error rate $25\%$, accuracy $1-0.25=0.75=75\%$. Flower 3 was a close call ($0.55$ against $0.45$), but the rule only looks at which posterior is largest.

> [!question]- Check (slide 9): In the printed table for test flower 1 the posteriors are setosa 1, versicolor $1.7609\times10^{-10}$. How many times more probable is setosa than versicolor? What does this ratio say about the two likelihoods $p(\mathbf{x}\mid\omega_1)$ and $p(\mathbf{x}\mid\omega_2)$ at this flower?
> Ratio of posteriors: $\dfrac{1}{1.7609\times10^{-10}}=5.68\times10^{9}$. Setosa is about 5.7 billion times more probable.
> Posterior ratio $=\dfrac{p(\mathbf{x}\mid\omega_1)P(\omega_1)}{p(\mathbf{x}\mid\omega_2)P(\omega_2)}$: the evidence cancels. The priors are equal ($1/3$ each), so they cancel too.
> So the likelihood ratio is the same number, $5.68\times10^{9}$. The flower $[1.30,\ 0.20]$ lies in the middle of the setosa cloud and many standard deviations away from the versicolor cloud, where the Gaussian has almost no height.

## 7. Steps 6-8: the three pictures (pages 10-12)
![[ee525_lecture_03_lecture3_bayesian_classifier_example_analysis_p010.jpg]]
![[ee525_lecture_03_lecture3_bayesian_classifier_example_analysis_p011.jpg]]
![[ee525_lecture_03_lecture3_bayesian_classifier_example_analysis_p012.jpg]]

**Notes.**
- **Training data**: scatter plot, one colour per class. Setosa sits alone, versicolor and virginica touch near petal length 5 cm.
- **Decision regions**: lay a $250\times250$ grid over the plane (`meshgrid`), flatten it to `G` ($62500\times2$), score every point with $P(\omega_k)\,p(\mathbf{x}\mid\omega_k)$, take `max`, `reshape` back, colour with `contourf`. No division by the evidence is needed: it is the same positive number for all classes at a point.
- **Confusion matrix** (`confusionchart(Ytest,predicted)`): rows = true class, columns = predicted class. Diagonal = correct, off-diagonal = errors.

| true \ predicted | setosa | versicolor | virginica |
|---|---|---|---|
| setosa | 15 | 0 | 0 |
| versicolor | 0 | 14 | 1 |
| virginica | 0 | 0 | 15 |

Error rate = off-diagonal / total = $1/45=2.22\%$. The one error: a versicolor called virginica. Per-class rates divide by the row total: versicolor $14/15=93.3\%$.

> [!warning] Handout details
> The code has the heading `%% 7` twice. The legend of the decision-region figure shows an extra entry "data1": it is the `contourf` object, which has no `DisplayName`.

> [!info] Reading the grid code and the curved boundaries (beyond the slides)
> The handout prints the plotting code without comments. What each line does:
> - `linspace(a,b,250)` gives 250 equally spaced values from `a` to `b`. The limits are the smallest and largest feature value of all 150 flowers, with a margin ($0.3$ cm in length, $0.15$ cm in width).
> - `meshgrid` makes two $250\times250$ matrices: `x1` holds the petal length of every grid point, `x2` the petal width.
> - `x1(:)` stacks a matrix into one long column. `G = [x1(:),x2(:)]` is a list of all $250\times250=62500$ grid points, one point per row, the same layout as `Xtest`. So `mvnpdf` can score the grid like test flowers.
> - `gridClass` first is $62500\times1$ (values 1, 2, 3). `reshape(gridClass,size(x1))` puts it back into the $250\times250$ picture shape.
> - `contourf(...,0.5:1:(K+0.5),...)`: the levels are $0.5,\ 1.5,\ 2.5,\ 3.5$. Class 1 lies between the first two levels, class 2 between the next two, class 3 between the last two. `colormap` and `clim` give each band its class colour.
>
> Why the boundaries are curved: on a boundary two scores are equal, $P(\omega_i)p(\mathbf{x}\mid\omega_i)=P(\omega_j)p(\mathbf{x}\mid\omega_j)$. Take the logarithm. Each side then contains $-\tfrac12(\mathbf{x}-\boldsymbol\mu)^{\mathsf T}\Sigma^{-1}(\mathbf{x}-\boldsymbol\mu)$, a second-degree expression in $x_1,x_2$. Because $\Sigma_i\neq\Sigma_j$, the squared terms do not cancel. The boundary is a second-degree curve (ellipse, parabola or hyperbola). With one shared $\Sigma$ they would cancel and the boundary would be a straight line (LDA). Details: [[EE525 L04 - Minimum risk and discriminant functions]].

> [!question]- Check (slide 10): Petal length in the data runs from $1.0$ to $6.9$ cm. (a) What are the first and last grid values in the length direction, and the spacing between neighbours? (b) The number 250 is changed to 100 in both `linspace` calls. What sizes do `x1`, `G`, `scoreGrid` and the final `gridClass` get?
> (a) Limits: $1.0-0.3=0.7$ and $6.9+0.3=7.2$. 250 points have 249 gaps: spacing $=\dfrac{7.2-0.7}{249}=\dfrac{6.5}{249}=0.0261$ cm.
> (b) `x1` and `x2`: $100\times100$. `G`: $100\times100=10000$ rows, so $10000\times2$. `scoreGrid`: one column per class, $10000\times3$. `gridClass`: $10000\times1$ after `max`, then $100\times100$ after `reshape`.
> New spacing: $6.5/99=0.0657$ cm. The picture is coarser, so the boundaries look less smooth. The classifier itself does not change: the grid is only for drawing.

> [!question]- Check (slide 11): Read the decision-region figure. Which class does the classifier give to a point at petal length $1.0$ cm, petal width $2.0$ cm (top left corner)? There is no training flower near it. Explain the colour, and say if you would trust this decision.
> The top left corner is yellow: virginica.
> Reason: the point is far from all three means, so all three densities are tiny, and the rule still picks the largest one. Virginica has the widest Gaussian (largest variances), so its density falls most slowly with distance. Far away from the data the widest class wins. The same thing happens below the orange region, for example at $[4.0,\ 0.2]$.
> Do not trust it. The posterior can be close to 1 there, but only because the other two densities are even smaller. No iris looks like this. A large posterior does not mean that the point is typical for the class. To see that, look at the evidence $p(\mathbf{x})$: it is almost zero there.

> [!question]- Check (slide 12): Use the confusion matrix of the lecture run. How many test flowers were predicted virginica? Which fraction of them really is virginica? Compare with the fraction of true versicolor flowers that were found.
> Predicted virginica = column 3: $0+1+15=16$ flowers.
> Really virginica among them: $15$. Fraction $15/16=0.9375=93.75\%$.
> True versicolor = row 2: $0+14+1=15$ flowers, $14$ found. Fraction $14/15=93.3\%$.
> A column answers "if the classifier says virginica, how often is it right?". A row answers "how many of the true versicolor flowers are caught?". The single error lowers one column value (virginica) and one row value (versicolor). Setosa has $15/15$ in both views.

## 8. MATLAB script printout: Experiment1_BayesianIris_RiskVsMinumumError.m (pages 1-5)
![[ee525_lecture_03_experiment1_bayesianiris_riskvsminumumerror_m_p001.jpg]]
![[ee525_lecture_03_experiment1_bayesianiris_riskvsminumumerror_m_p002.jpg]]
![[ee525_lecture_03_experiment1_bayesianiris_riskvsminumumerror_m_p003.jpg]]
![[ee525_lecture_03_experiment1_bayesianiris_riskvsminumumerror_m_p004.jpg]]
![[ee525_lecture_03_experiment1_bayesianiris_riskvsminumumerror_m_p005.jpg]]

**What the script does.** Sections 1 to 8 are exactly the code analysed above: split, estimate, `mvnpdf`, posteriors, largest posterior, three plots.
The added section `%% 3B` multiplies the posteriors by a loss matrix (`risk = posterior * Loss`), takes the smallest risk per row, and compares the minimum-risk decisions with the minimum-error ones. That part is explained in [[EE525 L04 - Minimum risk and discriminant functions]].

> [!info] Section 3B in short (pages 3-5 of the printout)
> - `Loss` is $3\times3$. Rows = true class, columns = predicted class, order setosa, versicolor, virginica. `Loss(i,j)` is the price of calling a class-$i$ flower class $j$. The diagonal is 0: a correct decision costs nothing. Here `Loss = [0 2 5; 3 0 2; 6 3 0]` (a `;` starts a new row).
> - `risk = posterior * Loss` is a real matrix product, $(45\times3)(3\times3)\to45\times3$. Entry $(n,j)$ is $\sum_i P(\omega_i\mid\mathbf{x}_n)\,\texttt{Loss}(i,j)$: the expected loss if flower $n$ is called class $j$ (the conditional risk of Lecture 2).
> - `[~,idxRisk] = min(risk,[],2)`: smallest risk per row. This is the minimum-risk decision. `idxError` (largest posterior) is the minimum-error decision from before.
> - `different = ~strcmp(predError,predRisk)` marks the flowers where the two rules disagree. `sum(different)` counts them.
> - `find(strcmp(classNames,Ytest{i}))` turns a class name into its number 1, 2 or 3. So `trueIdx`, `predErrorIdx`, `predRiskIdx` are $45\times1$ vectors of class numbers.
> - `sub2ind(size(Loss),r,c)` turns (row, column) pairs into single positions inside `Loss`, counted down the columns: position $=(c-1)\cdot3+r$. `Loss(linearIdxError)` then picks one loss per flower (0 if correct), and `mean` gives the average loss actually paid on the test set.
> - The last block converts the labels to `categorical` with a fixed class order, so both confusion charts list the classes in the same order.

> [!question]- Check (script page 1): `priors` is created as a column, `zeros(K,1)`. A student changes this line to `priors = zeros(1,K)` and leaves `joint = likelihood .* priors'` as it is. What happens, and what is the smallest fix?
> Now `priors` is $1\times3$, so `priors'` is $3\times1$.
> `.*` needs sizes that are equal or 1 in each dimension. Rows: 45 against 3. They do not match, so MATLAB stops with the error `Arrays have incompatible sizes for this operation.`
> Fix: remove the transpose, `joint = likelihood .* priors`. Then $(45\times3)$ `.*` $(1\times3)$ again multiplies column $k$ by prior $k$.
> Rule to remember: the prior vector must lie along the class direction (a row of 3), because the classes are the columns of `likelihood`.

> [!question]- Check (script page 2): In another run 3 of the 45 test flowers are wrong. What exactly do the two `fprintf` lines of section 4 print? Why is `%%` written in the format string?
> `errorRate` $=3/45=0.0667$. `100*errorRate` $=6.67$ with two decimals (`%.2f`).
> Accuracy: $100\times(1-0.0667)=93.33$.
> Output: an empty line (the format string starts with `\n`), then `Test error rate: 6.67%`, then `Test accuracy:   93.33%` on the next line.
> `%` starts a format code such as `%.2f`. To print a real percent sign you write it twice: `%%`. `\n` ends the line.

> [!question]- Check (script page 3): A student reads the loss matrix and says: "`Loss(2,3) = 2` is the price of calling a virginica flower versicolor." Find the mistake and give the correct price of that error. Which single error is the most expensive one?
> Rows are the true class, columns the predicted class. `Loss(2,3)` is row 2 (true versicolor), column 3 (predicted virginica): a versicolor called virginica costs 2.
> A virginica called versicolor is row 3, column 2: `Loss(3,2) = 3`.
> Largest entry: `Loss(3,1) = 6`, a virginica called setosa.
> The matrix is not symmetric (`Loss(1,3) = 5` but `Loss(3,1) = 6`), so the direction of an error matters. With the 0/1 loss of the minimum-error rule every error would cost the same.

> [!question]- Check (script page 4): One test flower has the posterior row $[0,\ 0.55,\ 0.45]$. Compute its row of `risk = posterior * Loss`. What does the minimum-error rule decide, what does the minimum-risk rule decide, and why do they differ?
> Row times matrix: entry $j$ = posterior row times column $j$ of `Loss`.
> Call it setosa: $0(0)+0.55(3)+0.45(6)=1.65+2.70=4.35$.
> Call it versicolor: $0(2)+0.55(0)+0.45(3)=1.35$.
> Call it virginica: $0(5)+0.55(2)+0.45(0)=1.10$.
> `risk` row $=[4.35,\ 1.35,\ 1.10]$.
> Minimum error: largest posterior is $0.55$: versicolor. Minimum risk: smallest risk is $1.10$: virginica.
> Reason: the two classes are almost equally probable, but the errors do not cost the same. Calling a virginica versicolor costs 3, calling a versicolor virginica costs only 2. Saying virginica is the cheaper bet. This flower would be counted by `sum(different)`.

> [!question]- Check (script page 5): Use the lecture result for the minimum-error rule: 44 flowers correct, one versicolor called virginica. What do the lines `Error rate` and `Mean realized loss` print? What would they print if the one error were a virginica called setosa instead?
> Error rate: $1/45=0.0222$, printed as `Error rate = 2.22%`.
> Realized loss: 44 correct flowers pick a diagonal entry, loss 0. The wrong one picks `Loss(2,3) = 2`. Mean $=\dfrac{44\cdot0+2}{45}=0.0444$, printed as `Mean realized loss = 0.044 units/sample`.
> Other case: the error picks `Loss(3,1) = 6`. Mean $=6/45=0.1333$, printed as `0.133`. The error rate is still $2.22\%$.
> The error rate only counts mistakes. The realized loss also weighs how expensive each mistake is. Here the same error rate gives three times the loss.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Stratified hold-out split | random test part, each class keeps its proportion; `cvpartition(Y,'HoldOut',0.30)` |
| Prior estimate | $\hat P(\omega_k)=N_k/N$ |
| Mean estimate | `mean(Xi,1)`: column averages |
| Covariance estimate | `cov(Xi)`: divides by $N_k-1$; diagonal = variances, off-diagonal = covariance |
| Likelihood | `mvnpdf(Xtest,mu(k,:),Sigma(:,:,k))` $=p(\mathbf{x}\mid\omega_k)$ |
| Joint | `likelihood .* priors'` $=p(\mathbf{x}\mid\omega_k)P(\omega_k)$ |
| Evidence | `sum(joint,2)` $=p(\mathbf{x})$ |
| Posterior | `joint ./ sum(joint,2)`, rows add to 1 |
| Decision | `[~,idx] = max(posterior,[],2)` |
| Error rate | `mean(~strcmp(predicted,Ytest))` |
| QDA / LDA / naive Bayes | own $\Sigma_k$ / one shared $\Sigma$ / diagonal $\Sigma_k$ |
| Confusion matrix | rows true, columns predicted; error = off-diagonal / total |

Variable sizes after the run: `X` $150\times2$, `Xtrain` $105\times2$, `Xtest` $45\times2$, `priors` $3\times1$, `mu` $3\times2$, `Sigma` $2\times2\times3$, `likelihood`, `joint`, `posterior` $45\times3$, `idx` $45\times1$, `G` $62500\times2$, `gridClass` $250\times250$.

## Check yourself
> [!question]- 1. The split is changed to `cvpartition(Y,'HoldOut',0.20)`. How many flowers go to testing and training, and what priors are estimated?
> Test: $0.20\times150=30$, 10 per class. Training: 120, 40 per class. Priors $40/120=1/3$ each, unchanged.

> [!question]- 2. Three training flowers of one class: $[4.0,1.2]$, $[4.5,1.5]$, $[5.0,1.5]$. What do `mean(Xi,1)` and `cov(Xi)` return?
> Mean $[4.5,\ 1.4]$. Deviations $[-0.5,-0.2]$, $[0,0.1]$, $[0.5,0.1]$. Divide the sums by $N-1=2$: variances $0.25$ and $0.03$, covariance $0.15/2=0.075$.
> $$\hat\Sigma=\begin{bmatrix}0.25&0.075\\0.075&0.03\end{bmatrix}$$
> The covariance is positive: longer petals are also wider.

> [!question]- 3. A test flower has likelihoods $[0.8,0.4,0.1]$. Decide with priors $[1/3,1/3,1/3]$ and with priors $[0.1,0.3,0.6]$.
> Equal priors: posteriors $[0.8,0.4,0.1]/1.3=[0.615,0.308,0.077]$: setosa.
> Priors $[0.1,0.3,0.6]$: joints $[0.08,0.12,0.06]$, evidence $0.26$, posteriors $[0.308,0.462,0.231]$: versicolor.

> [!question]- 4. Confusion matrix (rows true A, B, C): $[20,0,0]$, $[0,17,3]$, $[0,1,19]$. Error rate, per-class rates, which classes overlap?
> Errors $3+1=4$ of 60: error rate $6.67\%$, accuracy $93.33\%$. A: $100\%$, B: $17/20=85\%$, C: $19/20=95\%$. All errors are between B and C, so their clouds overlap. A is never confused.

> [!question]- 5. One feature. Class 1: $1,2,3$. Class 2: $5,6,7,8$. Classify $x=4$ with the Gaussian Bayes classifier (variances divided by $N_k-1$).
> Priors $3/7$ and $4/7$. $\hat\mu_1=2$, $\hat\sigma_1^2=1$. $\hat\mu_2=6.5$, $\hat\sigma_2^2=5/3$.
> $p(4\mid\omega_1)=0.05399$, $p(4\mid\omega_2)=0.04739$. Joints $0.02314$ and $0.02708$.
> Posteriors $0.461$ and $0.539$: decide $\omega_2$, although $x=4$ is closer to $\mu_1$.

> [!question]- 6. Why is the score `priors(k)*mvnpdf(...)` enough to draw the decision regions?
> The evidence $p(\mathbf{x})$ is the same positive number for every class at a given point. Dividing all scores by it cannot change which one is largest.

## Links
- Course index: [[EE525 Home]]
- Previous: [[EE525 L02 - Bayesian classifiers]]. Next: [[EE525 L04 - Minimum risk and discriminant functions]].
- Full step-by-step notes (PDF): [EE525_Lecture_03_Iris_Example_Analysis.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/ee_525/lecture_notes/lecture_03/EE525_Lecture_03_Iris_Example_Analysis.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lecture_notes/lecture_03/EE525_Lecture_03_Iris_Example_Analysis.pdf`
- Original handout (PDF): [Lecture3-Bayesian Classifier Example Analysis.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_03/Lecture3-Bayesian%20Classifier%20Example%20Analysis.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_03/Lecture3-Bayesian Classifier Example Analysis.pdf`
- MATLAB script printout (PDF): [Experiment1_BayesianIris_RiskVsMinumumError.m.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_03/lms.tedu.edu.tr_pluginfile.php_82482_mod_resource_content_1_Experiment1_BayesianIris_RiskVsMinumumError.m.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_03/lms.tedu.edu.tr_pluginfile.php_82482_mod_resource_content_1_Experiment1_BayesianIris_RiskVsMinumumError.m.pdf`

Slides not embedded: none.
