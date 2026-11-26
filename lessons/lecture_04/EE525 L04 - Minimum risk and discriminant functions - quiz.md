---
course: EE 525 Pattern Recognition
lesson: EE525 L04 - Minimum risk and discriminant functions
type: quiz
questions: 29
---

# EE525 L04 - Minimum risk and discriminant functions - quiz

Lesson note: [[EE525 L04 - Minimum risk and discriminant functions]]. One question per check question of the lesson. Pick an option, then open the answer.

Several questions use the nursery loss matrix (loss units, rows = true species, columns = predicted species):

| true \ predicted | setosa | versicolor | virginica |
|---|---|---|---|
| setosa | 0 | 2 | 5 |
| versicolor | 3 | 0 | 2 |
| virginica | 6 | 3 | 0 |

## 1. Loss, conditional risk, zero-one loss (4-1, pages 1-2)

### Q1 (4-1, slide 1)

Two classes, two actions. The losses are $\lambda(\alpha_1\mid\omega_1)=0$, $\lambda(\alpha_1\mid\omega_2)=10$, $\lambda(\alpha_2\mid\omega_1)=1$, $\lambda(\alpha_2\mid\omega_2)=0$. The posterior is $P(\omega_1\mid\mathbf{x})=0.85$. The conditional risk is $R(\alpha_i\mid\mathbf{x})=\sum_j\lambda(\alpha_i\mid\omega_j)P(\omega_j\mid\mathbf{x})$. What are the two risks, and which action does the minimum-risk rule choose?

- A) $R(\alpha_1\mid\mathbf{x})=0.85$, $R(\alpha_2\mid\mathbf{x})=1.5$, choose $\alpha_1$.
- B) $R(\alpha_1\mid\mathbf{x})=1.5$, $R(\alpha_2\mid\mathbf{x})=0.85$, choose $\alpha_1$.
- C) $R(\alpha_1\mid\mathbf{x})=1.5$, $R(\alpha_2\mid\mathbf{x})=0.85$, choose $\alpha_2$.
- D) $R(\alpha_1\mid\mathbf{x})=8.5$, $R(\alpha_2\mid\mathbf{x})=0.15$, choose $\alpha_2$.

> [!success]- Answer
> **C.** $P(\omega_2\mid\mathbf{x})=1-0.85=0.15$.
> $R(\alpha_1\mid\mathbf{x})=0(0.85)+10(0.15)=1.5$ and $R(\alpha_2\mid\mathbf{x})=1(0.85)+0(0.15)=0.85$.
> $0.85<1.5$, so $\alpha_2$ is chosen. The class $\omega_1$ is more probable, but a mistake with $\alpha_1$ is ten times more expensive.
> - A: The two risks are swapped. The loss 10 belongs to $\alpha_1$, so the 1.5 is the risk of $\alpha_1$.
> - B: The risks are right, but the rule takes the smallest risk. This option takes the action of the more probable class.
> - D: The posteriors are swapped: $10(0.85)=8.5$ and $1(0.15)=0.15$. The loss 10 is paid when the truth is $\omega_2$, so it is multiplied by 0.15.

### Q2 (4-1, slide 2)

Use the nursery matrix above. (a) What does it cost to call a true versicolor "setosa"? (b) A flower is surely a virginica, $\mathbf{p}=[0,\ 0,\ 1]$. What are the conditional risks of predicting setosa, versicolor, virginica?

- A) (a) 3. (b) $[6,\ 3,\ 0]$, the virginica row of the table.
- B) (a) 2. (b) $[6,\ 3,\ 0]$, the virginica row of the table.
- C) (a) 3. (b) $[5,\ 2,\ 0]$, the virginica column of the table.
- D) (a) 2. (b) $[5,\ 2,\ 0]$, the virginica column of the table.

> [!success]- Answer
> **A.** (a) Row versicolor (true), column setosa (predicted): 3.
> (b) $R(\alpha_j\mid\mathbf{x})=\sum_t P(\omega_t\mid\mathbf{x})L_{tj}$. Only $t=3$ has a non-zero posterior, so $R(\alpha_j\mid\mathbf{x})=1\cdot L_{3j}$. This is the row of the true class: $[6,\ 3,\ 0]$.
> - B: The 2 is row setosa, column versicolor: a true setosa called versicolor. Row and column are swapped in (a).
> - C: The column of virginica holds the costs of predicting virginica for the three possible true classes. Here the true class is fixed, so we need the row.
> - D: Both parts read the table transposed (rows as predicted class).

## 2. The loss matrix in MATLAB (4-1, pages 3-4)

### Q3 (4-1, slide 3)

The zero-one loss matrix has 0 on the diagonal and 1 everywhere else. The posterior row of one flower is $\mathbf{p}=[0.1,\ 0.6,\ 0.3]$. The script computes `risk = posterior * Loss`. What is the risk row, which index does `min` of the risks return, and which index does `max(posterior,[],2)` return?

- A) Risks $[0.1,\ 0.6,\ 0.3]$. `min` returns 1, `max` returns 2.
- B) Risks $[0.9,\ 0.4,\ 0.7]$. `min` returns 2, `max` returns 1.
- C) Risks $[0.45,\ 0.2,\ 0.35]$. `min` returns 2, `max` returns 2.
- D) Risks $[0.9,\ 0.4,\ 0.7]$. `min` returns 2, `max` returns 2.

> [!success]- Answer
> **D.** $r_1=0.1(0)+0.6(1)+0.3(1)=0.9$, $r_2=0.1(1)+0.6(0)+0.3(1)=0.4$, $r_3=0.1(1)+0.6(1)+0.3(0)=0.7$.
> This is $\mathbf{1}-\mathbf{p}$. The smallest risk is at index 2 and the largest posterior (0.6) is at index 2 too.
> With the zero-one matrix the two rules are the same rule.
> - A: These are the posteriors, not the risks. Each risk is the sum of the other two posteriors, $1-p_j$.
> - B: The risks are right. But `max` works on the posteriors, and the largest posterior is at index 2. Index 1 is the place of the largest risk.
> - C: These are the risks divided by their sum 2. The product `posterior * Loss` does not normalise. The indices are right, the values are not.

### Q4 (4-1, slide 4)

Sample 3 of a posterior matrix is $\mathbf{p}=[0.25,\ 0.30,\ 0.45]$. Use the nursery matrix above and `risk = posterior * Loss`. `idxError` comes from `max(posterior,[],2)` and `idxRisk` from `min(risk,[],2)`. What are the risk row, `idxError(3)` and `idxRisk(3)`?

- A) Risks $[3.60,\ 1.85,\ 1.85]$, `idxError(3) = 3`, `idxRisk(3) = 3`.
- B) Risks $[3.60,\ 1.85,\ 1.85]$, `idxError(3) = 3`, `idxRisk(3) = 2`.
- C) Risks $[2.85,\ 1.65,\ 2.40]$, `idxError(3) = 3`, `idxRisk(3) = 2`.
- D) Risks $[3.60,\ 1.85,\ 1.85]$, `idxError(3) = 2`, `idxRisk(3) = 3`.

> [!success]- Answer
> **B.** $r_1=0.25(0)+0.30(3)+0.45(6)=3.60$.
> $r_2=0.25(2)+0.30(0)+0.45(3)=1.85$.
> $r_3=0.25(5)+0.30(2)+0.45(0)=1.85$.
> The largest posterior is 0.45, so `idxError(3) = 3` (virginica). The risks of versicolor and virginica are equal. `min` returns the first position of the smallest value, so `idxRisk(3) = 2` (versicolor).
> - A: In a tie `min` does not take the last position or the class with the larger posterior. It takes the first position.
> - C: These numbers come from the rows of the matrix ($\mathbf{p}$ times `Loss'`), for example $0.25(0)+0.30(2)+0.45(5)=2.85$. The risk of predicting class $j$ uses column $j$.
> - D: The two variables are swapped. `idxError` is the place of the largest posterior, which is 3.

## 3. Comparing the two rules on the test set (4-1, pages 5-7)

### Q5 (4-1, slide 5)

The script runs `different = ~strcmp(predError,predRisk)` and then prints `sum(different)` "of" `numel(different)`. Suppose `predError = {'setosa';'versicolor';'virginica';'versicolor'}` and `predRisk = {'setosa';'virginica';'virginica';'versicolor'}`. What does `different` hold, and what is printed?

- A) `different` $=[1;\ 0;\ 1;\ 1]$. Printed: `Decisions changed by the loss matrix: 3 of 4`.
- B) `different` $=[0;\ 1;\ 0;\ 0]$. Printed: `Decisions changed by the loss matrix: 1 of 4`.
- C) `different` $=[0;\ 1;\ 0;\ 0]$. Printed: `Decisions changed by the loss matrix: 1 of 45`.
- D) `different` $=[1;\ 0;\ 1;\ 1]$. Printed: `Decisions changed by the loss matrix: 1 of 4`.

> [!success]- Answer
> **B.** `strcmp` compares the lists element by element: $[1;\ 0;\ 1;\ 1]$ (1 = same text). `~` inverts it: $[0;\ 1;\ 0;\ 0]$.
> `sum(different)` $=1$ and `numel(different)` $=4$, so the line says "1 of 4".
> - A: The `~` is forgotten. This vector marks the flowers where the rules agree.
> - C: The second number is `numel(different)`, the length of this list (4). The 45 is the size of the test set in the lecture run.
> - D: The vector is the result of `strcmp` without `~`. With this vector the sum would be 3, not 1.

### Q6 (4-1, slide 6)

`Loss` is the nursery matrix above, a $3\times3$ MATLAB matrix. A test flower is a true virginica (row 3) and the rule predicts versicolor (column 2). What does `sub2ind(size(Loss),3,2)` return, and which loss does `Loss(...)` pick with this index?

- A) Index 8, loss 2.
- B) Index 6, loss 2.
- C) Index 8, loss 3.
- D) Index 6, loss 3.

> [!success]- Answer
> **D.** MATLAB counts the elements column by column. For 3 rows: index $=\text{row}+(\text{column}-1)\cdot3=3+(2-1)\cdot3=6$.
> Column by column, `Loss` is $0,3,6,\ 2,0,3,\ 5,2,0$. The 6th element is 3.
> This is $L_{32}=3$: a virginica called versicolor.
> - A: Index 8 comes from counting row by row, $(3-1)\cdot3+2=8$. In MATLAB's order the 8th element is $L_{23}=2$.
> - B: The index is right. But the 6th element was read row by row ($0,2,5,\ 3,0,2$), which gives $L_{23}=2$.
> - C: The loss is right, but the index is counted row by row. MATLAB counts column by column, so the index is 6.

### Q7 (4-1, slide 7)

A test set has 45 flowers, 15 per species. A rule has the confusion chart (rows true, columns predicted) setosa $[15,0,0]$, versicolor $[0,14,1]$, virginica $[0,1,14]$. The minimum-risk rule of the lecture has versicolor $[0,13,2]$ and no other mistake: error rate $4.44\%$, mean realized loss $4/45=0.089$. Use the nursery matrix above. What are the error rate and the mean realized loss of the new rule?

- A) Error rate $4.44\%$, mean realized loss $0.111$. Same error rate as minimum risk, higher loss.
- B) Error rate $4.44\%$, mean realized loss $0.089$. Same error rate and same loss as minimum risk.
- C) Error rate $4.44\%$, mean realized loss $0.044$. Same error rate as minimum risk, lower loss.
- D) Error rate $2.22\%$, mean realized loss $0.111$. Lower error rate than minimum risk, higher loss.

> [!success]- Answer
> **A.** Wrong flowers: $1+1=2$, error rate $2/45=4.44\%$.
> A versicolor called virginica costs $L_{23}=2$. A virginica called versicolor costs $L_{32}=3$. Total $5$, mean $5/45=0.111$.
> The error rate counts mistakes. The realized loss also looks at which mistakes they are.
> - B: Both mistakes were priced with 2: $4/45=0.089$. The virginica called versicolor costs 3.
> - C: Every mistake was priced with 1: $2/45=0.044$. This is the error rate again, not the loss.
> - D: Only one of the two wrong flowers was counted: $1/45=2.22\%$.

## 4. Discriminant functions and decision surfaces (4-2, pages 1-2)

### Q8 (4-2, slide 1)

A classifier picks the class with the largest score $g_i$. Scores can be negative (for example log scores). The three scores are replaced by new scores: (a) $2g_i+3$, (b) $\ln g_i$, used only when all $g_i>0$, (c) $-g_i$, (d) $g_i^2$. Which replacements always keep the decision?

- A) (a), (b) and (d).
- B) Only (a).
- C) (a) and (b).
- D) All four.

> [!success]- Answer
> **C.** The decision stays when the same monotonically increasing function is applied to every score. Then the order of the scores does not change.
> (a) is increasing (positive factor, then a constant). (b) $\ln$ is increasing on positive numbers.
> - A: $g^2$ is increasing only for $g\ge0$. With the scores $-3$ and $-1$ the winner is $-1$, but the squares are 9 and 1 and the winner changes.
> - B: $\ln$ also keeps the order. This is how $p(\mathbf{x}\mid\omega_i)P(\omega_i)$ becomes $\ln p(\mathbf{x}\mid\omega_i)+\ln P(\omega_i)$.
> - D: $-g_i$ is decreasing: the largest score becomes the smallest. And (d) fails for negative scores.

### Q9 (4-2, slide 2)

![[ee525_lecture_04_lecture_4_2_classifiers_discriminant_functions_and_decision_surfaces_p002.jpg]]

In the figure the scores are $g_i(\mathbf{x})=p(\mathbf{x}\mid\omega_i)P(\omega_i)$ with Gaussian densities. The red ellipse (class $\omega_2$) is on the right, but a second piece of $R_2$ lies far on the left, on the other side of $\omega_1$. Which statement is correct?

- A) On the boundary $g_1=g_2$. The left piece is in $R_2$ because its points are nearer to the mean of $\omega_2$ than to the mean of $\omega_1$.
- B) On the boundary $g_1=g_2$. The left piece is in $R_2$ because the density of $\omega_2$ is wider and falls more slowly there, so $g_2>g_1$.
- C) On the boundary $g_1=g_2=0$. The left piece is in $R_2$ because both densities are zero there and the class with the larger index wins.
- D) On the boundary $g_1=g_2$. The left piece is in $R_2$ because $\omega_2$ has the larger prior, and the prior decides far from both means.

> [!success]- Answer
> **B.** A boundary is the place where the two largest scores are equal. A region is decided by which score is larger, not by which mean is nearer.
> The two Gaussians have different covariances. In the left-right direction $\omega_2$ is wider. Far to the left the narrow density of $\omega_1$ is almost zero and the wide one is larger.
> - A: The left piece is nearer to the mean of $\omega_1$ (the grey ellipse lies between it and the red one). Distance to the mean alone does not decide.
> - C: The scores are equal on the boundary, but they are not zero. A Gaussian density is positive everywhere.
> - D: A larger prior only shifts a boundary. With equal covariances the boundary is one straight line and each region is one piece. The second piece needs different covariances.

## 5. Scores for one flower, Bayesian and Gaussian discriminants (4-2, pages 3-5)

### Q10 (4-2, slide 3)

For one flower the likelihoods are $p(\mathbf{x}\mid\omega_i)=0.30,\ 0.60,\ 0.50$ and the priors are $P(\omega_i)=0.6,\ 0.2,\ 0.2$ (setosa, versicolor, virginica). The Bayesian score is $g_i=p(\mathbf{x}\mid\omega_i)P(\omega_i)$. What are the scores, the decision and the posteriors?

- A) Scores $0.30,\ 0.60,\ 0.50$. Versicolor. Posteriors $0.214,\ 0.429,\ 0.357$.
- B) Scores $0.18,\ 0.12,\ 0.10$. Setosa. Posteriors $0.18,\ 0.12,\ 0.10$.
- C) Scores $0.90,\ 0.80,\ 0.70$. Setosa. Posteriors $0.375,\ 0.333,\ 0.292$.
- D) Scores $0.18,\ 0.12,\ 0.10$. Setosa. Posteriors $0.45,\ 0.30,\ 0.25$.

> [!success]- Answer
> **D.** $g_1=0.30(0.6)=0.18$, $g_2=0.60(0.2)=0.12$, $g_3=0.50(0.2)=0.10$. The largest is $g_1$: setosa.
> Evidence $=0.18+0.12+0.10=0.40$. Posteriors: $0.18/0.40=0.45$, $0.12/0.40=0.30$, $0.10/0.40=0.25$.
> Versicolor has the largest likelihood but does not win. The large prior of setosa decides.
> - A: The priors are ignored. The score needs the likelihood and the prior.
> - B: The scores are not divided by the evidence $0.40$. Posteriors must add up to 1, these add up to 0.40.
> - C: Likelihood and prior were added, not multiplied. The decision is the same by chance, the numbers are wrong.

### Q11 (4-2, slide 4)

The Gaussian score is $g_i(\mathbf{x})=-\frac12(\mathbf{x}-\boldsymbol\mu_i)^{\mathsf T}\Sigma_i^{-1}(\mathbf{x}-\boldsymbol\mu_i)-\frac12\ln\lvert\Sigma_i\rvert+\ln P(\omega_i)$. Two features, $\mathbf{x}=[2,\ 1]^{\mathsf T}$, equal priors $0.5$. Class 1: $\boldsymbol\mu_1=[0,0]^{\mathsf T}$, $\Sigma_1=I$. Class 2: $\boldsymbol\mu_2=[3,1]^{\mathsf T}$, $\Sigma_2=\mathrm{diag}(4,\ 1)$. What are $g_1$ and $g_2$? (In every option the point goes to class 2. Only the values differ.)

- A) $g_1=-3.1931$ and $g_2=-1.5113$.
- B) $g_1=-3.1931$ and $g_2=-0.8181$.
- C) $g_1=-3.1931$ and $g_2=-1.8863$.
- D) $g_1=-5.6931$ and $g_2=-1.6363$.

> [!success]- Answer
> **A.** Class 1: distance term $-\frac12\left(\frac{2^2}{1}+\frac{1^2}{1}\right)=-2.5$, determinant term $-\frac12\ln1=0$, prior term $\ln0.5=-0.6931$. $g_1=-3.1931$.
> Class 2: distance term $-\frac12\left(\frac{(2-3)^2}{4}+\frac{(1-1)^2}{1}\right)=-0.125$, determinant term $-\frac12\ln4=-0.6931$, prior term $-0.6931$. $g_2=-1.5113$.
> $g_2>g_1$: class 2. It pays $0.6931$ for its larger spread, but the point fits it much better.
> - B: The determinant term of class 2 is missing: $-0.125-0.6931=-0.8181$. Here $\lvert\Sigma_2\rvert=4$, not 1.
> - C: The difference in feature 1 was not divided by the variance 4: $-\frac12(1+0)=-0.5$, so $g_2=-0.5-0.6931-0.6931$.
> - D: The factor $\frac12$ of the distance term is missing: $-5-0.6931$ and $-0.25-0.6931-0.6931$.

### Q12 (4-2, slide 5)

Two features and two linear scores: $g_1(\mathbf{x})=x_1+2x_2-3$ and $g_2(\mathbf{x})=2x_1-x_2+1$. What is the boundary between the two classes, and which class gets $\mathbf{x}=[1,\ 2]^{\mathsf T}$?

- A) Boundary $3x_1+x_2-2=0$. The point goes to class 1.
- B) Boundary $-x_1+3x_2-4=0$. The point goes to class 2.
- C) Boundary $-x_1+3x_2-4=0$. The point goes to class 1.
- D) Boundary $-x_1+x_2-2=0$. The point goes to class 1.

> [!success]- Answer
> **C.** Boundary: $g_1-g_2=0$. $(1-2)x_1+(2-(-1))x_2+(-3-1)=-x_1+3x_2-4=0$, a straight line.
> Point: $g_1=1+2(2)-3=2$ and $g_2=2(1)-2+1=1$. $g_1>g_2$, so class 1.
> Check: $-1+3(2)-4=1>0$, the side where $g_1-g_2>0$.
> - A: The two scores were added. The boundary is where they are equal, so they are subtracted.
> - B: The line is right. The class is wrong: $g_2=2-2+1=1$, smaller than $g_1=2$. A typical slip is $+x_2$ in $g_2$, which gives 5.
> - D: Two sign slips: $2-(-1)=3$, not 1, and $-3-1=-4$, not $-2$.

## 6. Linear classifier, LDA and QDA (4-2, pages 6-8)

### Q13 (4-2, slide 6)

![[ee525_lecture_04_lecture_4_2_classifiers_discriminant_functions_and_decision_surfaces_p006.jpg]]

Look at the coloured figure at the top (blue = setosa, orange = versicolor, green = virginica). A new flower has petal length 6 cm and petal width 0.5 cm. What does this linear classifier answer, and how well is the answer supported?

- A) Virginica. Petal length 6 cm is typical for virginica, and many virginica flowers lie near the point.
- B) Versicolor. Many versicolor training flowers lie near the point, so the answer is well supported.
- C) No class. The point lies outside the training data, so no score is the largest there.
- D) Versicolor. No training flower lies near the point, so the answer only continues the straight lines.

> [!success]- Answer
> **D.** The point $(6,\ 0.5)$ lies in the orange region: versicolor. The versicolor flowers are around lengths 3 to 5 with widths 1 to 1.8. The virginica flowers have widths above about 1.4. Nothing is near $(6,\ 0.5)$.
> A classifier answers for every point, because some score is always the largest. Far from the data the answer is not supported by any flower.
> - A: The classifier uses both features. At width 0.5 the point is below the line between orange and green, so it is in the orange region.
> - B: The class is right, the support is not. The bottom right corner of the figure is empty.
> - C: The regions cover the whole plane. There is always a largest score, also where there is no data.

### Q14 (4-2, slide 7)

LDA with a shared covariance uses $g_i(\mathbf{x})=\mathbf{w}_i^{\mathsf T}\mathbf{x}+w_{i0}$ with $\mathbf{w}_i=\Sigma^{-1}\boldsymbol\mu_i$ and $w_{i0}=-\frac12\boldsymbol\mu_i^{\mathsf T}\Sigma^{-1}\boldsymbol\mu_i+\ln P(\omega_i)$. Here $\Sigma=\mathrm{diag}(1,\ 0.5)$, $\boldsymbol\mu_1=[1,1]^{\mathsf T}$, $\boldsymbol\mu_2=[3,2]^{\mathsf T}$, equal priors. The midpoint of the means is $(2,\ 1.5)$. What are the weight vectors and the boundary, and does the midpoint lie on it?

- A) $\mathbf{w}_1=[1,\ 0.5]^{\mathsf T}$, $\mathbf{w}_2=[3,\ 1]^{\mathsf T}$. Boundary $4x_1+x_2=9.5$. The midpoint lies on it.
- B) $\mathbf{w}_1=[1,\ 2]^{\mathsf T}$, $\mathbf{w}_2=[3,\ 4]^{\mathsf T}$. Boundary $x_1+x_2=3.5$. The midpoint lies on it.
- C) $\mathbf{w}_1=[1,\ 2]^{\mathsf T}$, $\mathbf{w}_2=[3,\ 4]^{\mathsf T}$. Boundary $x_1+x_2=7$. The midpoint does not lie on it.
- D) $\mathbf{w}_1=[1,\ 1]^{\mathsf T}$, $\mathbf{w}_2=[3,\ 2]^{\mathsf T}$. Boundary $2x_1+x_2=5.5$. The midpoint lies on it.

> [!success]- Answer
> **B.** $\Sigma^{-1}=\mathrm{diag}(1,\ 2)$, so $\mathbf{w}_1=[1,\ 2]^{\mathsf T}$ and $\mathbf{w}_2=[3,\ 4]^{\mathsf T}$.
> $\boldsymbol\mu_1^{\mathsf T}\Sigma^{-1}\boldsymbol\mu_1=1+2=3$ and $\boldsymbol\mu_2^{\mathsf T}\Sigma^{-1}\boldsymbol\mu_2=9+8=17$, so $w_{10}=-1.5+\ln0.5$ and $w_{20}=-8.5+\ln0.5$.
> Boundary: $(\mathbf{w}_1-\mathbf{w}_2)^{\mathsf T}\mathbf{x}+(w_{10}-w_{20})=-2x_1-2x_2+7=0$, that is $x_1+x_2=3.5$. The $\ln0.5$ terms cancel.
> Midpoint: $2+1.5=3.5$. With equal priors the LDA boundary always passes through the midpoint of the means.
> - A: $\Sigma$ was used where $\Sigma^{-1}$ is needed. The inverse of $\mathrm{diag}(1,\ 0.5)$ is $\mathrm{diag}(1,\ 2)$.
> - C: The factor $\frac12$ in $w_{i0}$ is missing: $-3$ and $-17$ give $-2x_1-2x_2+14=0$.
> - D: The covariance was ignored ($\Sigma^{-1}=I$). This is the nearest-mean boundary, the perpendicular bisector of the two means.

### Q15 (4-2, slides 7-8)

![[ee525_lecture_04_lecture_4_2_classifiers_discriminant_functions_and_decision_surfaces_p008.jpg]]

In the right picture (QDA) the boundary between versicolor and virginica is curved. In the left picture (LDA) it is straight. The Gaussian score is $g_i(\mathbf{x})=-\frac12(\mathbf{x}-\boldsymbol\mu_i)^{\mathsf T}\Sigma_i^{-1}(\mathbf{x}-\boldsymbol\mu_i)-\frac12\ln\lvert\Sigma_i\rvert+\ln P(\omega_i)$. Which term makes the difference, and what happens to QDA if the three estimated $\Sigma_i$ are all equal?

- A) The term $-\frac12\mathbf{x}^{\mathsf T}\Sigma_i^{-1}\mathbf{x}$, which differs between classes in QDA. With equal $\Sigma_i$ it cancels and the boundaries are straight.
- B) The term $-\frac12\ln\lvert\Sigma_i\rvert$, which differs between classes in QDA. With equal $\Sigma_i$ it cancels and the boundaries are straight.
- C) The term $\ln P(\omega_i)$, which QDA estimates per class. With equal $\Sigma_i$ the priors still differ and the boundaries stay curved.
- D) The term $-\frac12\mathbf{x}^{\mathsf T}\Sigma_i^{-1}\mathbf{x}$, which differs between classes in QDA. With equal $\Sigma_i$ the boundaries stay curved, because QDA is a quadratic method.

> [!success]- Answer
> **A.** On a boundary $g_i-g_j=0$. The difference contains $-\frac12\mathbf{x}^{\mathsf T}\left(\Sigma_i^{-1}-\Sigma_j^{-1}\right)\mathbf{x}$.
> In QDA the bracket is not zero, so the equation has squares and products of the features: a curve.
> If all $\Sigma_i$ are equal, the bracket is zero. Only terms of first degree in $\mathbf{x}$ are left: a line, as in LDA. The terms $-\frac12\ln\lvert\Sigma_i\rvert$ cancel too.
> - B: This term does not contain $\mathbf{x}$. It is a constant per class, so it shifts a boundary but cannot bend it.
> - C: The prior term is also a constant per class. LDA has priors too and its boundaries are straight.
> - D: The first half is right. The second is false: the curve comes from the different $\Sigma_i$, not from the name of the method.

### Q16 (4-2, slide 8)

Naive Bayes with two features and equal priors $0.5$. Class A: $p(x_1\mid A)=0.4$, $p(x_2\mid A)=0.1$. Class B: $p(x_1\mid B)=0.2$, $p(x_2\mid B)=0.3$. The score is $g_i=\ln P(\omega_i)+\sum_j\ln p(x_j\mid\omega_i)$ with the natural logarithm. What are $g_A$ and $g_B$, and what is the decision?

- A) $g_A=-3.2189$ and $g_B=-2.8134$. Class B.
- B) $g_A=-3.9120$ and $g_B=-3.5066$. Class A.
- C) $g_A=-3.9120$ and $g_B=-3.5066$. Class B.
- D) $g_A=0.02$ and $g_B=0.03$. Class B.

> [!success]- Answer
> **C.** $g_A=\ln0.5+\ln0.4+\ln0.1=-0.6931-0.9163-2.3026=-3.9120$.
> $g_B=\ln0.5+\ln0.2+\ln0.3=-0.6931-1.6094-1.2040=-3.5066$.
> $g_B>g_A$, so class B. Feature 1 alone prefers A. Feature 2 prefers B more strongly.
> - A: The prior term $\ln0.5=-0.6931$ is missing in both scores. The decision is the same, the scores are not.
> - B: The values are right. But $-3.5066$ is the larger number. The score nearer to zero wins, not the one with the larger absolute value.
> - D: These are the products $0.5(0.4)(0.1)$ and $0.5(0.2)(0.3)$ without the logarithm. They give the same decision, but they are not the log scores of the formula.

## 7. Naive Bayes, k-NN, SVM, decision tree (4-2, pages 8-9)

### Q17 (4-2, slide 9)

k-NN: the score of a class is the number of its flowers among the $k$ nearest training flowers. The labels of the 7 nearest training flowers, from nearest to farthest, are: versicolor, virginica, virginica, versicolor, versicolor, virginica, virginica. What are the decisions for $k=1$, $k=3$, $k=5$ and $k=7$?

- A) Virginica, virginica, virginica, virginica.
- B) Versicolor, virginica, virginica, virginica.
- C) Virginica, versicolor, virginica, versicolor.
- D) Versicolor, virginica, versicolor, virginica.

> [!success]- Answer
> **D.** Count the labels among the first $k$.
> $k=1$: versicolor 1, virginica 0. $k=3$: 1 against 2. $k=5$: 3 against 2. $k=7$: 3 against 4.
> So versicolor, virginica, versicolor, virginica. Near a boundary the answer can change with $k$.
> - A: This uses the majority of all 7 neighbours (4 virginica) for every $k$. Only the first $k$ count.
> - B: Miscount at $k=5$. The first five labels hold 3 versicolor and 2 virginica.
> - C: This takes the class with the smaller count every time. The largest score wins.

### Q18 (4-2, slide 9)

(a) A binary linear SVM has the score $g(\mathbf{x})=\mathbf{w}^{\mathsf T}\mathbf{x}+b$ with $\mathbf{w}=[1,\ -2]^{\mathsf T}$ and $b=0.5$. Compute $g$ for $\mathbf{x}=[3,\ 1]^{\mathsf T}$. (b) A decision tree sends a flower to a leaf labelled virginica (class 3). What are $(g_1,g_2,g_3)$, and why are tree boundaries parallel to the axes?

- A) $g=1.5$, positive class. $(0,0,1)$. Each question of the tree compares one single feature with a threshold.
- B) $g=1.0$, positive class. $(0,0,1)$. Each question of the tree compares one single feature with a threshold.
- C) $g=-4.5$, negative class. $(0,0,1)$. Each question of the tree compares one single feature with a threshold.
- D) $g=1.5$, positive class. $(0,0,1)$. The tree assumes independent features, as naive Bayes does.

> [!success]- Answer
> **A.** (a) $g=1(3)+(-2)(1)+0.5=1.5>0$: the positive class. One score for two classes, the sign decides.
> (b) Indicator scores: 1 for the predicted class, 0 for the others, so $(0,0,1)$. A question such as "petal length $<2.5$?" has the border "feature = threshold", a line parallel to the other axis. Many such pieces look like stairs.
> - B: The bias $b=0.5$ is missing: $3-2=1.0$.
> - C: The components of $\mathbf{x}$ are swapped: $1(1)+(-2)(3)+0.5=-4.5$.
> - D: A tree makes no independence assumption and estimates no densities. The axis-parallel pieces come from the one-feature questions.

## 8. Comparison table and the six decision-region pictures (4-2, pages 10-12)

### Q19 (4-2, slide 10)

A student writes: "`fitcdiscr(X,Y,'DiscrimType','linear')` assumes nothing about the data, it only searches for the best straight lines. With `'quadratic'` the Gaussian assumption is dropped, so the boundaries can be curved." Which correction is right?

- A) `'linear'` assumes nothing about the data. `'quadratic'` keeps the Gaussian assumption and gives each class its own covariance.
- B) `'linear'` assumes Gaussian classes with one shared covariance. `'quadratic'` drops the Gaussian assumption and fits free curves.
- C) `'linear'` assumes Gaussian classes with one shared covariance. `'quadratic'` keeps the Gaussian assumption and gives each class its own covariance.
- D) `'linear'` assumes Gaussian classes with one covariance per class. `'quadratic'` keeps the Gaussian assumption and uses one shared covariance.

> [!success]- Answer
> **C.** Both sentences of the student are wrong. LDA assumes Gaussian classes and one shared covariance matrix. The straight lines follow from these assumptions. QDA keeps the Gaussian assumption and drops only the shared covariance. Then the quadratic terms do not cancel.
> - A: Only the second mistake is corrected. LDA does make assumptions.
> - B: Only the first mistake is corrected. QDA is still a Gaussian classifier.
> - D: The two methods are swapped. Shared covariance is LDA, own covariances are QDA.

### Q20 (4-2, slide 11)

Two features, three classes. Count the mean and covariance numbers that LDA, QDA and Gaussian naive Bayes estimate (priors not counted). Under which condition are the naive Bayes boundaries straight?

- A) LDA 9, QDA 15, naive Bayes 12. Straight if the features are uncorrelated inside every class.
- B) LDA 9, QDA 15, naive Bayes 12. Straight if every feature has the same variance in all three classes.
- C) LDA 10, QDA 18, naive Bayes 12. Straight if every feature has the same variance in all three classes.
- D) LDA 3, QDA 9, naive Bayes 6. Straight if every feature has the same variance in all three classes.

> [!success]- Answer
> **B.** Means: $3\times2=6$ in every case. A full $2\times2$ covariance is symmetric: 2 variances + 1 covariance = 3 numbers. A diagonal one has 2.
> LDA: $6+3=9$. QDA: $6+3(3)=15$. Naive Bayes: $6+3(2)=12$.
> Naive Bayes is curved because each class has its own variances, so the terms in $x_j^2$ do not cancel. With the same variances in all classes the diagonal covariance is shared and the boundaries are straight.
> - A: The counts are right. But uncorrelated features are the assumption naive Bayes always makes, and its boundaries are still curved.
> - C: A full covariance was counted with 4 numbers. The two off-diagonal entries are equal, so it has 3.
> - D: The 6 mean numbers are missing. These are the covariance numbers only.

### Q21 (4-2, slide 12)

![[ee525_lecture_04_lecture_4_2_classifiers_discriminant_functions_and_decision_surfaces_p012.jpg]]

Look at the six panels (blue = setosa, orange = versicolor, green = virginica). Take a point at the bottom right of each panel: petal length 6.5 cm, petal width 0.2 cm. Which class does each classifier give?

- A) Versicolor: LDA and linear SVM. Virginica: QDA, naive Bayes, k-NN and Gaussian-kernel SVM.
- B) Virginica in all six panels.
- C) Versicolor: LDA, k-NN and linear SVM. Virginica: QDA, naive Bayes and Gaussian-kernel SVM.
- D) Virginica: LDA, k-NN and linear SVM. Versicolor: QDA, naive Bayes and Gaussian-kernel SVM.

> [!success]- Answer
> **C.** At the bottom right the panels LDA, k-NN and linear SVM are orange. The panels QDA, Gaussian naive Bayes and Gaussian-kernel SVM are green.
> No flower lies near this point. Away from the data each model continues its own shape: straight lines, ellipse-like curves, or a closed orange "island". The difference comes from the model assumptions, not from the flowers.
> - A: This sorts the panels into straight and curved boundaries. But the k-NN panel is orange in this corner.
> - B: A petal length of 6.5 cm looks like virginica, but three panels are orange there. The classifiers use both features.
> - D: The two groups are right, the colours are swapped. Orange is versicolor.

## 9. MATLAB script printout: Experiment1_BayesianIris_RiskVsMinumumError.m (pages 1-5)

### Q22 (Experiment1 script, slide 1)

The script uses `cvpartition(Y,'HoldOut',0.30)` on 150 flowers, 50 per species. The split keeps the class proportions. `priors(k)` is the number of training flowers of class $k$ divided by the number of all training flowers. Which statement is correct?

- A) 105 training and 45 test flowers, `priors` $=0.333$ each. With `'HoldOut',0.20`: 120 training, 30 test, `priors` unchanged.
- B) 45 training and 105 test flowers, `priors` $=0.333$ each. With `'HoldOut',0.20`: 30 training, 120 test, `priors` unchanged.
- C) 105 training and 45 test flowers, `priors` $=0.233$ each. With `'HoldOut',0.20`: 120 training, 30 test, `priors` $=0.267$ each.
- D) 105 training and 45 test flowers, `priors` $=0.333$ each. With `'HoldOut',0.20`: 30 training, 120 test, `priors` unchanged.

> [!success]- Answer
> **A.** With $0.30$: test $=0.30\times150=45$ (15 per species), training $=105$ (35 per species). `priors(k)` $=35/105=0.333$.
> With $0.20$: test $=30$ (10 per species), training $=120$ (40 per species). `priors(k)` $=40/120=0.333$, unchanged.
> `rng(10)` before the split fixes the random seed, so the same flowers go to the test set in every run.
> - B: The hold-out fraction is the test part, not the training part.
> - C: The class counts were divided by all 150 flowers: $35/150$ and $40/150$. The priors use the training flowers only, and they add up to 1.
> - D: The first split is right. In the second one the two parts are swapped: 20% of 150 is the test set, 30 flowers.

### Q23 (Experiment1 script, slide 2)

The script computes `joint = likelihood .* priors'` and `posterior = joint ./ sum(joint,2)`. Suppose `priors = [0.5; 0.25; 0.25]` and one row of `likelihood` is $[0.1,\ 0.6,\ 0.2]$. What are this row of `joint` and this row of `posterior`?

- A) `joint` $=[0.05,\ 0.15,\ 0.05]$, `posterior` $=[0.05,\ 0.15,\ 0.05]$.
- B) `joint` $=[0.60,\ 0.85,\ 0.45]$, `posterior` $=[0.316,\ 0.447,\ 0.237]$.
- C) `joint` $=[0.05,\ 0.15,\ 0.05]$, `posterior` $=[0.111,\ 0.667,\ 0.222]$.
- D) `joint` $=[0.05,\ 0.15,\ 0.05]$, `posterior` $=[0.2,\ 0.6,\ 0.2]$.

> [!success]- Answer
> **D.** `joint` $=[0.1(0.5),\ 0.6(0.25),\ 0.2(0.25)]=[0.05,\ 0.15,\ 0.05]$.
> `sum(joint,2)` $=0.25$, the evidence. `posterior` $=[0.05/0.25,\ 0.15/0.25,\ 0.05/0.25]=[0.2,\ 0.6,\ 0.2]$. The row adds up to 1.
> `priors` is a $3\times1$ column. The transpose makes it a $1\times3$ row, so column $k$ of `likelihood` is multiplied by `priors(k)`.
> - A: The division by the evidence is missing. This row adds up to 0.25, not to 1.
> - B: Likelihood and prior were added. `.*` multiplies element by element.
> - C: The likelihood row was divided by its own sum $0.9$. The priors are missing in the posterior.

### Q24 (Experiment1 script, slides 2-3)

Block 7 builds a $250\times250$ grid with `meshgrid`. `G = [x1(:),x2(:)]` lists the grid points. `scoreGrid(:,k)` is `priors(k) * mvnpdf(G,...)` for the three classes, without division by the evidence. `gridClass` is the index of the largest score in each row. Which statement is correct?

- A) `G` is $250\times2$ and `scoreGrid` is $250\times3$. `gridClass` holds 1, 2 or 3. The picture is correct because the evidence is the same for all classes at one point.
- B) `G` is $62500\times2$ and `scoreGrid` is $62500\times3$. `gridClass` holds 1, 2 or 3. The picture is correct because the evidence is the same for all classes at one point.
- C) `G` is $62500\times2$ and `scoreGrid` is $62500\times3$. `gridClass` holds posteriors between 0 and 1. The picture is correct because the evidence is the same for all classes at one point.
- D) `G` is $62500\times2$ and `scoreGrid` is $62500\times3$. `gridClass` holds 1, 2 or 3. The picture is correct because the priors are equal, so the evidence is 1 at every point.

> [!success]- Answer
> **B.** The grid has $250\times250=62500$ points. `G` has one point per row (2 columns), `scoreGrid` one score per class (3 columns).
> `gridClass` is the number of the winning class: 1, 2 or 3.
> The evidence $p(\mathbf{x})$ is the same for the three classes at one point. Dividing three scores by the same positive number does not change which one is largest.
> - A: 250 is the number of points on one axis. The grid has $250^2$ points.
> - C: `gridClass` comes from the index output of `max`, so it holds class numbers. The contour levels $0.5, 1.5, 2.5, 3.5$ separate these numbers.
> - D: The evidence is $\sum_k p(\mathbf{x}\mid\omega_k)P(\omega_k)$. It changes from point to point and is not 1. It may be left out because it is common to the classes.

### Q25 (Experiment1 script, slide 4)

The loss matrix in the script has rows = true class (the nursery matrix above). The correct line `risk = posterior * Loss` gives $[2.10,\ 1.35,\ 3.05]$ for the flower $\mathbf{p}=[0.45,\ 0.40,\ 0.15]$: versicolor. A student writes `risk = posterior * Loss'` (with a transpose). What does this line give for the same flower?

- A) $[2.10,\ 1.35,\ 3.05]$ and versicolor. The transpose does not change the product.
- B) No result. MATLAB stops with an error, because the sizes do not fit.
- C) $[1.55,\ 1.65,\ 3.90]$ and setosa. MATLAB gives no error.
- D) $[1.55,\ 1.65,\ 3.90]$ and versicolor. MATLAB gives no error.

> [!success]- Answer
> **C.** Column $j$ of `Loss'` is row $j$ of `Loss`.
> Entry 1: $0.45(0)+0.40(2)+0.15(5)=1.55$. Entry 2: $0.45(3)+0.40(0)+0.15(2)=1.65$. Entry 3: $0.45(6)+0.40(3)+0.15(0)=3.90$.
> `min` picks entry 1: setosa. Both products are $1\times3$ times $3\times3$, so there is no error message. Always check the orientation of the loss matrix.
> - A: The nursery matrix is not symmetric, so the transpose changes the product.
> - B: A $3\times3$ matrix stays $3\times3$ after the transpose. The sizes fit.
> - D: The numbers are right, but the smallest one is the first (1.55), so the decision is setosa.

### Q26 (Experiment1 script, slide 5)

In the lecture run (45 test flowers, nursery matrix) the minimum-error rule made one mistake: a versicolor called virginica. Now the loss matrix is replaced by `Loss = ones(3) - eye(3)` and the risk block runs again on the same split. What do the outputs show for the two rules (minimum error, minimum risk)?

- A) Decisions changed: 1 of 45. Error rates $2.22\%$ and $4.44\%$. Mean realized losses $0.044$ and $0.089$.
- B) Decisions changed: 0 of 45. Error rates $2.22\%$ and $2.22\%$. Mean realized losses $0.044$ and $0.044$.
- C) Decisions changed: 0 of 45. Error rates $0\%$ and $0\%$. Mean realized losses $0$ and $0$.
- D) Decisions changed: 0 of 45. Error rates $2.22\%$ and $2.22\%$. Mean realized losses $0.022$ and $0.022$.

> [!success]- Answer
> **D.** `ones(3) - eye(3)` is the zero-one matrix. Then `risk` $=1-$ `posterior` in every row. The smallest risk is at the largest posterior, so both rules decide the same: 0 of 45 changed.
> Both make the same single mistake: $1/45=2.22\%$.
> Every mistake costs 1, so the mean realized loss is $1/45=0.022$. With the zero-one loss it equals the error rate.
> - A: These are the outputs of the lecture run with the nursery matrix. The new matrix changes the minimum-risk rule.
> - B: The mistake was still priced with the nursery value 2: $2/45=0.044$. In the new matrix it costs 1.
> - C: The zero-one loss does not remove mistakes. It only gives every mistake the price 1.

## 10. MATLAB script printout: Discrimiant_Functions_Comparison_TwoFeatures_Figures.m (pages 1-3)

### Q27 (Figures script, slide 1)

`'Standardize'` changes each feature to (value minus mean) divided by its standard deviation. In the Iris data the standard deviation of petal length is 1.765 cm and of petal width 0.762 cm. Flowers A and B differ by 1 cm in length only. Flowers A and C differ by 1 cm in width only. What are the two distances after standardising?

- A) A to B: $0.567$. A to C: $1.312$.
- B) A to B: $1.765$. A to C: $0.762$.
- C) A to B: $1.312$. A to C: $0.567$.
- D) A to B: $0.321$. A to C: $1.722$.

> [!success]- Answer
> **A.** The mean cancels in a difference. A to B: $1/1.765=0.567$. A to C: $1/0.762=1.312$.
> After standardising, 1 cm in width counts more than twice as much as 1 cm in length.
> The script standardises for k-NN because k-NN uses Euclidean distances, so the units change its neighbours. LDA and QDA use $\Sigma^{-1}$ in the Mahalanobis term, which already rescales every direction.
> - B: The difference was multiplied by the standard deviation. Standardising divides.
> - C: The two features are swapped. The length has the larger standard deviation, so its 1 cm becomes the smaller distance.
> - D: The difference was divided by the variance: $1/1.765^2$ and $1/0.762^2$. Standardising divides by the standard deviation.

### Q28 (Figures script, slide 2)

Petal length runs from 1.0 to 6.9 cm. The script sets `xMin` and `xMax` to these limits with a margin of 0.5 cm on each side and uses `linspace(xMin, xMax, 300)`. The width axis also gets 300 points. `XGrid` lists all grid points as rows, and `predict` runs on `XGrid` once for each of the 6 models. What are the grid step in the length direction, the number of rows of `XGrid` and the total number of class predictions?

- A) Step $6.9/300=0.0230$ cm. 90000 rows. 540000 predictions.
- B) Step $6.9/299=0.0231$ cm. 300 rows. 1800 predictions.
- C) Step $6.9/299=0.0231$ cm. 90000 rows. 540000 predictions.
- D) Step $5.9/299=0.0197$ cm. 90000 rows. 540000 predictions.

> [!success]- Answer
> **C.** `xMin` $=1.0-0.5=0.5$ and `xMax` $=6.9+0.5=7.4$. 300 points have 299 gaps: step $=(7.4-0.5)/299=0.0231$ cm.
> `XGrid` has $300\times300=90000$ rows. Six models: $6\times90000=540000$ predictions.
> In the script `Z` starts as zeros and gets 1, 2 or 3. A remaining 0 would be a grid point whose predicted name matches none of the three class names.
> - A: 300 points have 299 gaps, not 300.
> - B: 300 is the number of points on one axis. The grid has $300^2$ points.
> - D: The margin is missing: $6.9-1.0=5.9$. With the margin the range is $7.4-0.5=6.9$.

### Q29 (Figures script, slide 3)

The script fits six models on all 150 flowers (50 per species) and draws one panel per model. In every panel a loop over the three classes runs `idx = strcmp(Y, classNames{c})` and one `scatter` call. The dot colour is the true species (orange = versicolor). The background colour is the prediction of the model (green = virginica). In one panel an orange dot lies on the green region. Which statement is correct?

- A) `idx` is a $150\times1$ logical vector with 50 ones. `scatter` is called 18 times. The dot is a versicolor classified as virginica, a test error.
- B) `idx` is a $150\times1$ logical vector with 50 ones. `scatter` is called 18 times. The dot is a versicolor classified as virginica, a training error.
- C) `idx` is a $150\times1$ logical vector with 50 ones. `scatter` is called 3 times. The dot is a versicolor classified as virginica, a training error.
- D) `idx` is a $150\times1$ logical vector with 50 ones. `scatter` is called 18 times. The dot is a virginica classified as versicolor, a training error.

> [!success]- Answer
> **B.** `strcmp` gives 1 for the flowers of class `c` and 0 for the others: 150 entries, 50 ones.
> `scatter` runs once per class and per panel: $3\times6=18$ calls.
> Orange dot = true versicolor. Green background = predicted virginica. So the flower is misclassified.
> All 150 flowers were used to fit the models and the same flowers are drawn. So it is a training error. The pictures say nothing sure about new flowers.
> - A: There is no test set in this script. Every drawn flower was used for the fit.
> - C: 3 is the number of calls in one panel. There are six panels.
> - D: The colours are read the wrong way round. The dot shows the truth, the background shows the prediction.
