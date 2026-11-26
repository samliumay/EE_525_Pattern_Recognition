# Learning brief: EE525 L03 - Iris example analysis

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of EE 525 Pattern Recognition so that I understand every slide and can answer its questions without help.
Exams mix derivations and numeric problems (Bayes rule, risk, discriminants, estimation, EM, HMM). I must be able to do the computations by hand, step by step.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 17 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/EE_525_Pattern_Recognition/lessons/lecture_03/EE525 L03 - Iris example analysis.md`
2. **Quiz note** (17 multiple-choice questions, one per check question, each with a folded answer that explains every option): `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/EE_525_Pattern_Recognition/lessons/lecture_03/EE525 L03 - Iris example analysis - quiz.md`
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/EE_525_Pattern_Recognition/others/slides/ee525_lecture_03_experiment1_bayesianiris_riskvsminumumerror_m`, `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/EE_525_Pattern_Recognition/others/slides/ee525_lecture_03_lecture3_bayesian_classifier_example_analysis`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_03/Lecture3-Bayesian Classifier Example Analysis.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lecture_notes/lecture_03/EE525_Lecture_03_Iris_Example_Analysis.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lecture_notes/lecture_03/body_3.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. The Iris data (pages 1-2)
- 2. Step 1: choose the features and split the data (page 3)
- 3. Step 2: priors, means and covariances (page 4)
- 4. Step 3: one 2-D Gaussian per class (pages 5-6)
- 5. Step 4: from likelihoods to posteriors (page 7)
- 6. Step 5: decide and count the errors (pages 8-9)
- 7. Steps 6-8: the three pictures (pages 10-12)
- 8. MATLAB script printout: Experiment1_BayesianIris_RiskVsMinumumError.m (pages 1-5)

## Known errors and traps in the sources (do not teach the errors)

- **Handout spelling:** The handout writes "Sentosa" and "Virginic". The names are setosa and virginica, as MATLAB prints them.
- **Watch out:** Never estimate anything from the test set. The test flowers play the role of new, unseen patterns. (The handout comment says `test(c)`; it means `test(cv)`.)
- **Watch out:** `cov` divides by $N_k-1$ (unbiased). The maximum-likelihood estimate divides by $N_k$ (see [[EE525 L05 - MLE, Bayesian estimation and EM]]). With 35 flowers the ratio is $35/34=1.029$.
- **Handout wording:** - The handout says the full-covariance model "is performing Linear or Quadratic Discriminant Analysis". Each class has its own $\Sigma_k$, so it is quadratic: QDA. LDA needs one shared $\Sigma$. - $p(\mathbf{x}\mid\omega_k)$ is a density and can be larger than...
- **Watch out:** - The "1" for setosa is rounded on display. The true value is $1-1.7609\times10^{-10}-4.6551\times10^{-18}$. - One split of 45 flowers is noisy: each mistake changes the result by 2.22%. Our notes checked 200 random splits in Python: average test error QDA 2.8...
- **Handout details:** The code has the heading `%% 7` twice. The legend of the decision-region figure shows an extra entry "data1": it is the `contourf` object, which has no `DisplayName`.

## How to run a session

1. Ask which part I want, or start from section 1. Ask two or three short questions to see what I already know.
2. Go section by section. Explain one slide or one idea, then ask the **check question** of that slide from the lesson note (the callouts `> [!question]- Check (slide N): ...`). Do not show the answer. Wait for mine, then compare it with the answer in the note and tell me what is missing.
3. At the end of a section, or in Practice mode, ask the **multiple-choice questions** of that section from the quiz note. Show the four options, wait for my letter, then explain why the right option is right and why my choice was wrong, using the answer callout.
4. Make new questions of the same kind (new numbers, a changed condition) for the points I miss.
5. Finish with a short list: what I know, what I should repeat, and which slides to read again.

## About me

MS student at TED University (CS/CMPE). I study alone, often on a phone. English is not my first language.

- Probe my starting point first.
- One idea at a time, short explanations, a check question after each step. Never skip steps.
- Give the Turkish equivalent of a key term at its first use.
- Ask me to think out loud. When I am wrong, show me which step went wrong.
- For graded homework or labs: hints and explanations only, never the submission.
