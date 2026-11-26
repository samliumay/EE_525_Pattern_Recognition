---
course: EE 525 Pattern Recognition
lesson: EE525 L03 - Iris example analysis
type: quiz
questions: 17
---

# EE525 L03 - Iris example analysis - quiz

Lesson note: [[EE525 L03 - Iris example analysis]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. The Iris data (pages 1-2)

### Q1 (slide 1)

`meas` is $150\times4$. The columns are sepal length, sepal width, petal length, petal width. Rows 1-50 are setosa, rows 51-100 versicolor, rows 101-150 virginica. Which MATLAB expression gives the sepal widths of all versicolor flowers, and what size is the result?

- A) `meas(51:100,4)`, a $50\times1$ column
- B) `meas(2,51:100)`, a $1\times50$ row
- C) `meas(51:100,2)`, a $50\times1$ column
- D) `meas(101:150,2)`, a $50\times1$ column

> [!success]- Answer
> **C.** Versicolor is the second block of 50 rows: rows 51 to 100. Sepal width is column 2. Fifty rows and one column give a $50\times1$ column.
> - A: The rows are right, but column 4 is petal width, not sepal width.
> - B: Rows and columns are swapped. The first index is the row (flower), the second is the column (feature). `meas` has only 4 columns, so MATLAB gives an error.
> - D: Rows 101 to 150 are virginica, the third block.

### Q2 (slide 2)

Suppose the script kept all four features: `X = meas(:,1:4)`. For each class it estimates one mean vector and one covariance matrix. A covariance matrix is symmetric, so equal entries are counted once. How many different numbers are estimated per class?

- A) 10
- B) 14
- C) 20
- D) 8

> [!success]- Answer
> **B.** The mean has $d=4$ numbers. A symmetric $4\times4$ matrix has $d(d+1)/2=4\cdot5/2=10$ different entries. Total: $4+10=14$. With two features it was $2+3=5$.
> - A: This counts only the covariance matrix. The 4 numbers of the mean are missing.
> - C: This is $4+16$. It counts all 16 entries of the matrix and forgets the symmetry.
> - D: This is $4+4$: the mean and only the four variances. That is the count for a diagonal covariance (naive Bayes), not for a full one.

## 2. Step 1: choose the features and split the data (page 3)

### Q3 (slide 3)

A small data set has 10 flowers with two features, so `X` is $10\times2$. `training(cv)` returns $[1,1,0,1,0,1,1,1,0,1]^{\mathsf T}$. The script runs `Xtest = X(test(cv),:)`. What is `Xtest`?

- A) A $7\times2$ matrix with rows 1, 2, 4, 6, 7, 8 and 10 of `X`
- B) A $3\times2$ matrix with rows 8, 9 and 10 of `X`
- C) A $3\times1$ column with rows 3, 5 and 9 of `X`
- D) A $3\times2$ matrix with rows 3, 5 and 9 of `X`

> [!success]- Answer
> **D.** `test(cv)` is the logical opposite of `training(cv)`: $[0,0,1,0,1,0,0,0,1,0]^{\mathsf T}$. It has three 1s, at positions 3, 5 and 9. The `:` keeps both feature columns, so `Xtest` is $3\times2$. `Xtrain` is $7\times2$, and $7+3=10$.
> - A: These are the rows marked 1 in `training(cv)`. This is `Xtrain`.
> - B: The size is right, but the split is random. The test set is not the last 30% of the rows.
> - C: The rows are right, but `:` in the second index keeps all columns, so there are 2 columns.

## 3. Step 2: priors, means and covariances (page 4)

### Q4 (slide 4)

One feature with a Gaussian density, mean $\mu=4.3$ and standard deviation $\sigma=0.5$:

$$p(x)=\frac{1}{\sqrt{2\pi}\,\sigma}\exp\left[-\frac12\left(\frac{x-\mu}{\sigma}\right)^2\right]$$

What is $p(4.8)$?

- A) $0.4839$
- B) $0.2420$
- C) $0.7041$
- D) $0.2935$

> [!success]- Answer
> **A.** Distance in standard deviations: $(4.8-4.3)/0.5=1$. Exponent: $-\tfrac12\cdot1^2=-0.5$, and $e^{-0.5}=0.6065$.
> Front factor: $\dfrac{1}{\sqrt{2\pi}\cdot0.5}=0.7979$. So $p(4.8)=0.7979\times0.6065=0.4839$.
> The front factor is also the peak height. For $\sigma=0.2$ it is $1.9947$: a density can be larger than 1.
> - B: The $\sigma$ in the front factor is missing: $\dfrac{1}{\sqrt{2\pi}}\times0.6065=0.2420$.
> - C: The distance was not divided by $\sigma$: exponent $-\tfrac12(0.5)^2=-0.125$, and $0.7979\times e^{-0.125}=0.7041$.
> - D: The factor $\tfrac12$ in the exponent is missing: $0.7979\times e^{-1}=0.2935$.

## 4. Step 3: one 2-D Gaussian per class (pages 5-6)

### Q5 (slide 5)

A class has the covariance matrix

$$\Sigma_k=\begin{bmatrix}0.25&0.06\\0.06&0.04\end{bmatrix}$$

Feature 1 is petal length, feature 2 is petal width. The correlation coefficient is $\rho=\dfrac{\text{covariance}}{\sigma_1\sigma_2}$, where $\sigma_1,\sigma_2$ are the two standard deviations. What is $\rho$?

- A) $0.06$
- B) $0.12$
- C) $0.6$
- D) $6$

> [!success]- Answer
> **C.** The diagonal holds the variances: $\sigma_1=\sqrt{0.25}=0.5$ cm and $\sigma_2=\sqrt{0.04}=0.2$ cm. The off-diagonal entry is the covariance $0.06$.
> $\rho=\dfrac{0.06}{0.5\times0.2}=\dfrac{0.06}{0.10}=0.6$. Positive: inside this class longer petals tend to be wider.
> - A: This is the covariance itself. It was not divided by $\sigma_1\sigma_2$.
> - B: This divides by $\sigma_1$ only: $0.06/0.5=0.12$.
> - D: This divides by the variances instead of the standard deviations: $0.06/(0.25\times0.04)=6$. A correlation can never be larger than 1.

### Q6 (slide 6)

Naive Bayes case with two features ($d=2$). The mean is $\boldsymbol\mu=[4.0,\ 1.3]$ and the covariance is diagonal:

$$\Sigma=\begin{bmatrix}0.25&0\\0&0.04\end{bmatrix},\qquad p(\mathbf{x})=\frac{1}{2\pi\lvert\Sigma\rvert^{1/2}}\exp\left(-\tfrac12(\mathbf{x}-\boldsymbol\mu)^{\mathsf T}\Sigma^{-1}(\mathbf{x}-\boldsymbol\mu)\right)$$

What is $p(\mathbf{x})$ at $\mathbf{x}=[4.5,\ 1.5]$?

- A) $0.2154$
- B) $5.8550$
- C) $1.6938$
- D) $0.5855$

> [!success]- Answer
> **D.** Difference: $\mathbf{x}-\boldsymbol\mu=[0.5,\ 0.2]$. Determinant: $0.25\times0.04=0.01$, so $\lvert\Sigma\rvert^{1/2}=0.1$.
> $\Sigma^{-1}$ has the diagonal $4,\ 25$. Quadratic form: $4(0.5)^2+25(0.2)^2=1+1=2$. Exponent: $-1$, and $e^{-1}=0.3679$.
> $p(\mathbf{x})=\dfrac{1}{2\pi\times0.1}\times0.3679=1.5915\times0.3679=0.5855$.
> Check: the product of the two 1-D Gaussians is $0.4839\times1.2099=0.5855$, the same number.
> - A: The factor $\tfrac12$ in the exponent is missing: $1.5915\times e^{-2}=0.2154$.
> - B: The square root of the determinant is missing: $\dfrac{1}{2\pi\times0.01}\times0.3679=5.8550$.
> - C: This adds the two 1-D densities: $0.4839+1.2099=1.6938$. Independent features are multiplied, not added.

## 5. Step 4: from likelihoods to posteriors (page 7)

### Q7 (slide 7)

The script computes `posterior = joint ./ sum(joint,2)`. A student types `sum(joint,1)` instead. Take a `joint` with two rows (two flowers, three classes): $[0.10,\ 0.06,\ 0.04]$ and $[0.02,\ 0.03,\ 0.15]$. What does the student get for row 1, and how does the mistake show?

- A) Row 1 is $[0.50,\ 0.30,\ 0.20]$. It adds to 1, so the mistake does not show in this example.
- B) Row 1 is $[0.833,\ 0.667,\ 0.211]$. It adds to $1.711$, not to 1.
- C) No row is computed. MATLAB stops, because a $1\times3$ row cannot divide a $2\times3$ matrix.
- D) Row 1 is $[0.25,\ 0.15,\ 0.10]$. It adds to $0.5$, not to 1.

> [!success]- Answer
> **B.** `sum(joint,1)` adds down each column: $[0.12,\ 0.09,\ 0.19]$. `./` divides column $k$ by the $k$-th sum. Row 1: $[0.10/0.12,\ 0.06/0.09,\ 0.04/0.19]=[0.833,\ 0.667,\ 0.211]$, sum $1.711$.
> The student normalised over the flowers (each column adds to 1) instead of over the classes. Only the row sums show it.
> - A: This is the correct posterior, from `sum(joint,2)`: row sum $0.20$, and $[0.10,0.06,0.04]/0.20$.
> - C: The sizes fit. MATLAB expands the $1\times3$ row over both rows and gives no error.
> - D: This divides by the sum of all six entries, $0.40$. Neither `sum(joint,1)` nor `sum(joint,2)` does that.

## 6. Step 5: decide and count the errors (pages 8-9)

### Q8 (slide 8)

Four test flowers have the `posterior` rows $[0.90,0.10,0.00]$, $[0.05,0.60,0.35]$, $[0.00,0.45,0.55]$, $[0.00,0.20,0.80]$. Class 1 is setosa, 2 versicolor, 3 virginica. The true classes are setosa, versicolor, versicolor, virginica. The code is

```matlab
[~,idx]   = max(posterior,[],2);
predicted = classNames(idx);
errorRate = mean(~strcmp(predicted,Ytest));
```

What are `idx` and `errorRate`?

- A) `idx` $=[1,2,3,3]^{\mathsf T}$ and `errorRate` $=0.25$
- B) `idx` $=[1,2,3,3]^{\mathsf T}$ and `errorRate` $=0.75$
- C) `idx` $=[1,2,2,3]^{\mathsf T}$ and `errorRate` $=0$
- D) `idx` $=[0.90,0.60,0.55,0.80]^{\mathsf T}$ and `errorRate` $=0.25$

> [!success]- Answer
> **A.** `idx` is the position of the largest entry in each row: $[1,2,3,3]^{\mathsf T}$. So `predicted` is setosa, versicolor, virginica, virginica.
> `strcmp` gives $[1,1,0,1]^{\mathsf T}$ (flower 3 is a versicolor called virginica). `~` flips it to $[0,0,1,0]^{\mathsf T}$. `mean` $=1/4=0.25$.
> - B: The `~` was forgotten. $0.75$ is the mean of the `strcmp` vector, which is the accuracy.
> - C: These are the true classes. Flower 3 has $0.55$ for class 3 against $0.45$ for class 2, so the rule says 3, even if it is a close call.
> - D: These are the largest values. The `~` in `[~,idx]` discards the values and keeps the positions.

### Q9 (slide 9)

The priors are equal ($1/3$ each). For test flower 1 the printed posteriors are setosa $1$ and versicolor $1.7609\times10^{-10}$. How many times more probable is setosa than versicolor, and what follows for the ratio of the likelihoods $p(\mathbf{x}\mid\omega_1)/p(\mathbf{x}\mid\omega_2)$ at this flower?

- A) About $5.7\times10^{9}$ times. The likelihood ratio is 3 times smaller, $1.9\times10^{9}$, because of the prior $1/3$.
- B) About $1.8\times10^{10}$ times. The likelihood ratio is the same number, because the priors are equal.
- C) About $5.7\times10^{9}$ times. The likelihood ratio cannot be found, because the evidence is not printed.
- D) About $5.7\times10^{9}$ times. The likelihood ratio is the same number, because the priors are equal.

> [!success]- Answer
> **D.** Ratio of posteriors: $\dfrac{1}{1.7609\times10^{-10}}=5.68\times10^{9}$.
> Posterior ratio $=\dfrac{p(\mathbf{x}\mid\omega_1)P(\omega_1)}{p(\mathbf{x}\mid\omega_2)P(\omega_2)}$. The evidence cancels. The equal priors cancel too. So the likelihood ratio is also $5.68\times10^{9}$.
> - A: The prior $1/3$ is in the numerator and in the denominator, so it cancels. It does not scale the ratio.
> - B: Only the sign of the exponent was flipped. $1/1.7609$ is $0.568$, so the result is $0.568\times10^{10}=5.68\times10^{9}$.
> - C: Both posteriors are divided by the same evidence $p(\mathbf{x})$, so it cancels in the ratio. It is not needed.

## 7. Steps 6-8: the three pictures (pages 10-12)

### Q10 (slide 10)

The decision-region code makes two grid matrices `x1` and `x2` with `meshgrid` from two `linspace` calls with 250 values each. Then `G = [x1(:),x2(:)]`, every row of `G` gets one score per class, `gridClass` is the class with the largest score in each row, and at the end `gridClass = reshape(gridClass,size(x1))`. The number 250 is changed to 100 in both `linspace` calls. What sizes do `G` and the final `gridClass` have?

- A) `G` is $100\times2$, the final `gridClass` is $100\times1$
- B) `G` is $100\times100$, the final `gridClass` is $100\times100$
- C) `G` is $10000\times2$, the final `gridClass` is $100\times100$
- D) `G` is $10000\times2$, the final `gridClass` is $10000\times3$

> [!success]- Answer
> **C.** `x1` and `x2` are $100\times100$. `x1(:)` stacks a matrix into one column of $100\times100=10000$ entries, so `G` is $10000\times2$: one grid point per row. `gridClass` is $10000\times1$ after `max`, and `reshape` puts it back into the picture shape $100\times100$.
> The grid is only for drawing. A coarser grid gives less smooth boundaries, but the classifier does not change.
> - A: A grid of 100 by 100 values has 10000 points, not 100.
> - B: $100\times100$ is the size of `x1`. `G` is a list of points with two columns (length, width).
> - D: $10000\times3$ is the size of the score matrix (one column per class). `gridClass` holds one class number per point.

### Q11 (slide 11)

![[ee525_lecture_03_lecture3_bayesian_classifier_example_analysis_p011.jpg]]

Read the decision-region figure (blue = setosa, orange = versicolor, yellow = virginica). Look at the point with petal length $1.0$ cm and petal width $2.0$ cm, in the top left corner. No training flower is near it. Which statement is correct?

- A) It is classified as virginica, because the virginica Gaussian is the widest and its density falls most slowly far from the data. The decision should not be trusted.
- B) It is classified as setosa, because the setosa mean is the nearest of the three means. The decision should not be trusted.
- C) It is classified as virginica, because many virginica training flowers have this petal length and width. The decision can be trusted.
- D) It is classified as virginica, and the posterior is close to 1 there, so the point is a typical virginica flower. The decision can be trusted.

> [!success]- Answer
> **A.** The top left corner is yellow: virginica. The point is far from all three means, so all three densities are tiny. The rule still picks the largest one. Virginica has the largest variances, so its density falls most slowly with distance.
> No iris looks like this point. The evidence $p(\mathbf{x})$ is almost zero there, so the decision means little.
> - B: The figure shows yellow at this point, not blue. The blue region ends at a petal width of about $0.9$ cm. The rule compares densities, not plain distances to the means.
> - C: The figure shows no training flower near this corner. The virginica flowers have petal lengths of about 4.5 to 7 cm.
> - D: A posterior near 1 only says that the other two densities are even smaller. It does not say that the point is typical for the class.

### Q12 (slide 12)

![[ee525_lecture_03_lecture3_bayesian_classifier_example_analysis_p012.jpg]]

Use the confusion matrix of the lecture run (rows = true class, columns = predicted class, empty cells are 0). Which fraction of the test flowers that were predicted virginica really is virginica?

- A) $15/15=100\%$
- B) $15/16=93.75\%$
- C) $14/15=93.33\%$
- D) $1/16=6.25\%$

> [!success]- Answer
> **B.** Predicted virginica is column 3: $0+1+15=16$ flowers. Of these, $15$ really are virginica. Fraction $15/16=93.75\%$.
> - A: This uses row 3 (true virginica): all 15 true virginica flowers were found. The question asks about the column.
> - C: This is row 2: the fraction of true versicolor flowers that were found.
> - D: This is the wrong part of column 3: the one versicolor among the 16 flowers predicted virginica.

## 8. MATLAB script printout: Experiment1_BayesianIris_RiskVsMinumumError.m (pages 1-5)

### Q13 (script page 1)

In the script `priors` is a column, `zeros(K,1)` with $K=3$, and `likelihood` is $45\times3$. A student changes the line to `priors = zeros(1,K)` and leaves `joint = likelihood .* priors'` as it is. What happens, and what is the smallest fix?

- A) Nothing changes, because `.*` ignores the orientation of a vector. No fix is needed.
- B) The script runs, but it multiplies row $k$ by prior $k$. Fix: change `priors` back to a column.
- C) MATLAB stops with a size error. Fix: write `*` instead of `.*`.
- D) MATLAB stops with a size error. Fix: remove the transpose `'`.

> [!success]- Answer
> **D.** Now `priors` is $1\times3$, so `priors'` is $3\times1$. `.*` needs sizes that are equal or 1 in each dimension. Rows: 45 against 3. MATLAB stops with `Arrays have incompatible sizes for this operation.`
> Without the transpose, $(45\times3)$ `.*` $(1\times3)$ again multiplies column $k$ by prior $k$.
> - A: The orientation matters. A $1\times3$ row fits the 3 class columns, a $3\times1$ column does not fit 45 rows.
> - B: There is no silent wrong result. The sizes 45 and 3 do not fit, so the script stops.
> - C: The error is right, the fix is not. `likelihood * priors'` is a matrix product, $(45\times3)(3\times1)\to45\times1$. It runs, but it gives the evidence, not the $45\times3$ joint.

### Q14 (script page 2)

In another run 3 of the 45 test flowers are wrong. The script prints the error rate with `fprintf('\nTest error rate: %.2f%%\n',100*errorRate);`. The first `\n` prints an empty line. Which text line appears after it?

- A) `Test error rate: 0.07%`
- B) `Test error rate: 6.67%%`
- C) `Test error rate: 6.67%`
- D) `Test error rate: 15.00%`

> [!success]- Answer
> **C.** `errorRate` $=3/45=0.0667$. `100*errorRate` $=6.67$ with two decimals (`%.2f`). `%%` prints one percent sign, because a single `%` starts a format code. The last `\n` ends the line. The accuracy line prints $100\times(1-0.0667)=93.33$.
> - A: The factor 100 was forgotten: $0.0667$ with two decimals is $0.07$.
> - B: `%%` in the format string prints one `%`, not two.
> - D: The division is upside down: $45/3=15$.

### Q15 (script page 3)

The loss matrix is `Loss = [0 2 5; 3 0 2; 6 3 0]`. Rows are the true class, columns the predicted class, in the order setosa, versicolor, virginica. Which statement is correct?

- A) Calling a virginica versicolor costs `Loss(3,2) = 3`. The most expensive error is a virginica called setosa.
- B) Calling a virginica versicolor costs `Loss(2,3) = 2`. The most expensive error is a virginica called setosa.
- C) Calling a virginica versicolor costs `Loss(3,2) = 3`. The most expensive error is a setosa called virginica.
- D) Calling a virginica versicolor costs `Loss(2,3) = 2`. The most expensive error is a setosa called virginica.

> [!success]- Answer
> **A.** True virginica is row 3, predicted versicolor is column 2: `Loss(3,2) = 3`. The largest entry is `Loss(3,1) = 6`: row 3 (true virginica), column 1 (predicted setosa).
> The matrix is not symmetric, so the direction of an error matters.
> - B: `Loss(2,3) = 2` is row 2, column 3: a versicolor called virginica. Rows and columns are swapped.
> - C: The first half is right. A setosa called virginica is `Loss(1,3) = 5`, not the largest entry.
> - D: Both halves have rows and columns swapped.

### Q16 (script page 4)

`Loss = [0 2 5; 3 0 2; 6 3 0]` (rows = true class, columns = predicted class; order setosa, versicolor, virginica). One test flower has the posterior row $[0,\ 0.55,\ 0.45]$. The script computes `risk = posterior * Loss`. What is the `risk` row of this flower, and what do the two rules decide?

- A) `risk` $=[3.35,\ 0.90,\ 1.65]$. Minimum error: versicolor. Minimum risk: versicolor.
- B) `risk` $=[4.35,\ 1.35,\ 1.10]$. Minimum error: versicolor. Minimum risk: virginica.
- C) `risk` $=[4.35,\ 1.35,\ 1.10]$. Minimum error: versicolor. Minimum risk: versicolor.
- D) `risk` $=[4.35,\ 1.35,\ 1.10]$. Minimum error: versicolor. Minimum risk: setosa.

> [!success]- Answer
> **B.** Entry $j$ is the posterior row times column $j$ of `Loss`.
> Setosa: $0(0)+0.55(3)+0.45(6)=4.35$. Versicolor: $0(2)+0.55(0)+0.45(3)=1.35$. Virginica: $0(5)+0.55(2)+0.45(0)=1.10$.
> Minimum error takes the largest posterior, $0.55$: versicolor. Minimum risk takes the smallest risk, $1.10$: virginica. A virginica called versicolor costs 3, the opposite error only 2.
> - A: This uses the rows of `Loss` instead of the columns (`Loss * posterior'`): $2(0.55)+5(0.45)=3.35$, $2(0.45)=0.90$, $3(0.55)=1.65$.
> - C: The risk row is right, but its smallest entry is the third one. The largest posterior does not always have the smallest risk.
> - D: $4.35$ is the largest risk. The rule uses `min`, not `max`.

### Q17 (script page 5)

`Loss = [0 2 5; 3 0 2; 6 3 0]` (rows = true class, columns = predicted class; order setosa, versicolor, virginica). In the lecture run the minimum-error rule gets 44 of 45 test flowers right and calls one versicolor virginica. The script prints the mean of the losses paid on the 45 test flowers. What does the line `Mean realized loss` show?

- A) `0.022 units/sample`
- B) `0.067 units/sample`
- C) `0.044 units/sample`
- D) `0.133 units/sample`

> [!success]- Answer
> **C.** The 44 correct flowers pick a diagonal entry, loss 0. The wrong one picks `Loss(2,3) = 2` (true versicolor, predicted virginica). Mean $=\dfrac{44\cdot0+2}{45}=0.0444$, printed as `0.044`. The error rate is $1/45=2.22\%$.
> - A: This is $1/45$, the error rate as a fraction. It counts the error but ignores its price 2.
> - B: This is $3/45$. It uses `Loss(3,2) = 3`, the price of a virginica called versicolor.
> - D: This is $6/45$. It uses `Loss(3,1) = 6`, a virginica called setosa. The error rate would be the same, the loss three times larger.
