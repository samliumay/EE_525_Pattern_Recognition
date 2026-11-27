# Learning brief: EE525 L05 - MLE, Bayesian estimation and EM

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of EE 525 Pattern Recognition so that I understand every slide and can answer its questions without help.
Exams mix derivations and numeric problems (Bayes rule, risk, discriminants, estimation, EM, HMM). I must be able to do the computations by hand, step by step.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 21 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/EE_525_Pattern_Recognition/lessons/lecture_05/EE525 L05 - MLE, Bayesian estimation and EM.md`
2. **Quiz note** (21 multiple-choice questions, one per check question, each with a folded answer that explains every option): `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/EE_525_Pattern_Recognition/lessons/lecture_05/EE525 L05 - MLE, Bayesian estimation and EM - quiz.md`
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/EE_525_Pattern_Recognition/others/slides/ee525_lecture_05_expectation_maximization_em_and_mixture_density_estimation`, `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/EE_525_Pattern_Recognition/others/slides/ee525_lecture_05_maximum_likelihood_and_bayesian_parameter_estimation`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_05/Maximum Likelihood and Bayesian Parameter Estimation.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lecture_notes/lecture_05/EE525_Lecture_05_MLE_Bayes_EM.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lecture_notes/lecture_05/body_5.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Why the parameters must be estimated (deck A, pages 1-2)
- 2. Likelihood and the MLE of a Gaussian (deck A, pages 3-5)
- 3. Bayesian estimation of a Gaussian mean (deck A, pages 6-8)
- 4. MLE against Bayes, histogram against fitted Gaussian (deck A, pages 9-10)
- 5. Gaussian mixture models and the latent-variable problem (deck B, pages 1-2)
- 6. E-step and M-step (deck B, page 3)
- 7. MATLAB Examples 1 and 2: synthetic data and EM from scratch (deck B, pages 4-7)
- 8. MATLAB Examples 3 and 4: fitgmdist in 1-D and 2-D (deck B, pages 8-10)

## Known errors and traps in the sources (do not teach the errors)

- **Watch out:** - MATLAB `var(X)` and `cov(X)` divide by $N-1$, so they are not the MLE. Use `var(X,1)` or `cov(X,1)`, or multiply by $(N-1)/N$. The ML variance is biased: its average is $\frac{N-1}{N}\sigma^2$. - The likelihood is not "the probability that $\theta$ is the tr...
- **Watch out:** "Bayes approaches MLE" needs $\tau_0^2>0$ and large $N$. With $\tau_0^2=0$ no amount of data moves the estimate from $\mu_0$.
- **Slide error (deck B, page 3):** The slide's table lists the observations $2.0$, $3.5$, $5.0$ with responsibilities $0.9997/0.0003$, $0.5/0.5$, $0.0003/0.9997$. Those values belong to $x=2,4,6$. For the printed observations the correct rows are $x=3.5$: $0.8808/0.1192$ and $x=5.0$: $0.0180/0....
- **Watch out:** - A log-likelihood that goes down between iterations always means a bug. - Label switching: nothing ties "component 1" to "population 1". Compare components by their means, not by their index.

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
