# Learning brief: EE525 L06 - Hidden Markov models

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of EE 525 Pattern Recognition so that I understand every slide and can answer its questions without help.
Exams mix derivations and numeric problems (Bayes rule, risk, discriminants, estimation, EM, HMM). I must be able to do the computations by hand, step by step.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 24 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/EE_525_Pattern_Recognition/lessons/lecture_06/EE525 L06 - Hidden Markov models.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/EE_525_Pattern_Recognition/others/slides/ee525_lecture_06_lecture_6_hidden_markov_models`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/Lecture_06/Lecture 6 Hidden Markov Models.pdf`

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Why time matters, and the parameters of an HMM (pages 1-2)
- 2. Links to earlier methods, and the Markov property (page 3)
- 3. First-order Markov models (pages 4-5)
- 4. Weather example: transition matrix, counting, sequence probability (pages 6-7)
- 5. From Markov models to hidden Markov models (pages 8-9)
- 6. The activity HMM: A, B, pi and the joint probability (pages 10-12)
- 7. Continuous observations and one HMM per class (pages 13-14)
- 8. Evaluation: the forward and backward algorithms (beyond the slides)
- 9. Decoding: the Viterbi algorithm (beyond the slides)
- 10. Learning: the Baum-Welch algorithm (beyond the slides)

## Known errors and traps in the sources (do not teach the errors)

- **Watch out:** - Page 2 says the test pattern is classified by the model with "the highest posterior probability", and then compares likelihoods $P(X\mid\lambda_c)$. The two agree only when the class priors are equal. The handout assumes this ("assuming equal class priors"),...
- **Watch out (page 5): the start factor is left out:** The product $a_{13}a_{32}a_{22}a_{21}a_{13}$ has no factor for the first state. Strictly it is the probability of the sequence given that it starts in $\omega_1$. The full probability is $P(\omega(1)=\omega_1)\,a_{13}a_{32}a_{22}a_{21}a_{13}$. Page 7 does incl...
- **Watch out:** On page 6 the line "$A=[\dots]\ A=[\dots]$" writes $A$ twice, side by side: first with the symbols $P(S\mid S),\dots$, then with the numbers. It is one matrix, not a product of two.
- **Watch out:** - Assumption 2 does not say that the observations are independent of each other. They are independent only when the hidden states are given. Without the states, $v(1)$ tells something about $\omega(1)$, which tells something about $\omega(2)$ and so about $v(2...
- **Slide error (page 12):** The page says "We want to calculate $P(Q\mid O,\lambda)$" and then computes the joint probability $P(Q,O\mid\lambda)=0.037632$. These are different. The conditional probability of the path given the observations is $$P(Q\mid O,\lambda)=\frac{P(Q,O\mid\lambda)}...
- **Slide error (page 11):** - "Suppose $\pi_i=[0.6\ 0.4]$": the vector is $\pi$; $\pi_1=0.6$ and $\pi_2=0.4$ are its entries. - "$w(t)=w_i$, $i=1,2,3$": the example has only two states, so $i=1,2$. The range $1,2,3$ fits the time index $t$ ($T=3$), not the state index. - Notation changes...
- **Watch out (page 13): the figure does not match the table:** The figure does not show the example's densities, and the handout does not say so. Its legend says $\mu_1=-3$, $\sigma_1=1$ and $\mu_2=2$, $\sigma_2=2$, a general picture of two Gaussians. The table above it says $\mu_W=0.4$, $\sigma_W=0.2$ and $\mu_R=1.0$, $\...
- **Underflow: use scaling or logarithms:** Every step multiplies by numbers below 1, so $\alpha_t$ shrinks fast. If each step gives a factor of about $0.3$, then after $T=1000$ steps $\alpha\approx0.3^{1000}\approx10^{-523}$. The smallest normal double-precision number is about $10^{-308}$, and nothing...
- **Watch out:** - $P^*=\max_QP(Q,O\mid\lambda)$ is not $P(O\mid\lambda)$. Here $0.037632$ against $0.116$. Viterbi keeps one path, the forward algorithm adds all of them. - The path must be read backwards with $\psi$. Taking the larger $\delta_t$ at every time separately can ...
- **Watch out:** - The example trains on one sequence of three observations only to show the arithmetic. A real model is trained on many sequences: add the numerators and the denominators over all training sequences. With one short sequence the model only learns that sequence ...

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
