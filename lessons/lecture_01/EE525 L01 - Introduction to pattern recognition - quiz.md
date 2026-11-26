---
course: EE 525 Pattern Recognition
lesson: EE525 L01 - Introduction to pattern recognition
type: quiz
questions: 46
---

# EE525 L01 - Introduction to pattern recognition - quiz

Lesson note: [[EE525 L01 - Introduction to pattern recognition]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Patterns and classes (slides 2-6)

### Q1 (slide 2)

A classmate says: "Pattern recognition began in the 1960s, when computers appeared." Which statement about the history of the field agrees with the slide?

- A) It began in the 1960s, because no classification is possible without a computer.
- B) Before the 1960s it was mostly practical engineering work, and the theory came with computers.
- C) Before the 1960s it was mostly theoretical research in statistics, and computers raised the demand for applications.
- D) Before the 1960s it was mostly theoretical research in statistics, and computers made new theory unnecessary.

> [!success]- Answer
> **C.** The field is older than computers. Before the 1960s it was mostly theory inside statistics. Computers raised the demand for practical applications.
> - A: This is the classmate's mistake. The field existed before computers.
> - B: The order is reversed. The theory came first, the practical applications came with computers.
> - D: The first half is right. The second half is false: the new applications asked for new theory.

### Q2 (slide 3)

A machine must distinguish fresh food from food that is not fresh by its smell. A gas sensor measures the smell. Which assignment is correct?

- A) Pattern: the smell of one food sample. Feature: the concentration of one gas. Classes: fresh, not fresh.
- B) Pattern: the concentration of one gas. Feature: the smell of one food sample. Classes: fresh, not fresh.
- C) Pattern: the smell of one food sample. Feature: fresh or not fresh. Classes: the gases in the smell.
- D) Pattern: the gas sensor of the machine. Feature: the smell of one food sample. Classes: fresh, not fresh.

> [!success]- Answer
> **A.** The pattern is the object that comes in (the smell of one sample). A feature is a number measured on it (one gas concentration). The classes are the boxes: fresh, not fresh.
> - B: Pattern and feature are swapped. The concentration is one number measured on the pattern.
> - C: "Fresh or not fresh" is the class, not a feature. The gases are what we measure, not the categories.
> - D: The sensor is the measuring device. It is not the object that is classified.

### Q3 (slide 4)

A phone unlocks with a fingerprint. The slide uses three verbs: observe, learn, decide. Which action of the phone is "learn"?

- A) The sensor scans the finger that touches it and gives an image.
- B) The phone puts a new scan into the category "owner" or "not owner".
- C) The phone opens the screen after the category "owner" was chosen.
- D) During set-up the phone stores several scans of the owner's finger.

> [!success]- Answer
> **D.** During set-up the phone learns what "owner" looks like from several example scans. This is learning to distinguish the pattern of interest.
> - A: This is "observe": the sensor measures the environment.
> - B: This is "decide": a category is chosen for a new pattern.
> - C: This is the action that follows the decision. It is not one of the three verbs.

### Q4 (slide 5)

"Every pattern recognition algorithm copies the way a living system recognises patterns." Is this true?

- A) True. Nature is the source of all the algorithms, and numerical techniques only implement them.
- B) False. Nature influences many algorithms, but many techniques are purely numerical with no natural counterpart.
- C) False. Nature has no influence on the algorithms, because all of them are purely numerical techniques.
- D) True. Even a threshold on a measured length is a copy of the way a brain makes its decisions.

> [!success]- Answer
> **B.** The slide says both things: nature inspires many algorithms, and we also use many purely numerical techniques that have no counterpart in natural systems.
> - A: Too strong. A rule such as the sign of $g(\mathbf{x})=\mathbf{w}^{\mathsf T}\mathbf{x}+w_0$ is arithmetic, not a model of a living system.
> - C: Too strong in the other direction. The slide says that nature influences many algorithms.
> - D: A threshold is an arithmetic rule. It was not copied from a brain.

### Q5 (slide 6)

Row "Industrial monitoring" of the slide's table: a machine listens to the vibration signal of a motor and decides between "normal operation" and "mechanical fault". Which item is a feature?

- A) One recording of the vibration signal of the motor.
- B) The amplitude of the vibration at one frequency.
- C) The category "mechanical fault" of the motor.
- D) The alarm that stops the motor after a decision.

> [!success]- Answer
> **B.** A feature is one number measured on the pattern. The amplitude at one frequency is such a number.
> - A: This is the pattern: the whole object that comes in.
> - C: This is a class: one of the two categories.
> - D: This is the action that follows the decision. It is not measured on the pattern.

## 2. Applications, part 1 (slides 7-13)

### Q6 (slide 7)

In machine vision the slide says the classes are "known a priori" (inspection: defect or non-defect; assembly line: one class per object type). What does this mean?

- A) The class of each object is known before the camera takes an image of it.
- B) The system discovers new classes by itself while it runs on the line.
- C) The number of classes is always two, as in the inspection example.
- D) The list of possible classes is fixed before the system starts to run.

> [!success]- Answer
> **D.** "A priori" means before the system runs. The list of classes is fixed in advance. For each object the system chooses one class from this list.
> - A: If the class of each object were known, no classifier would be needed. Only the list of classes is known.
> - B: This is the opposite. The system does not invent new classes.
> - C: Inspection has two classes, but the assembly line has several (screwdriver class, German key class, ...).

### Q7 (slide 8)

In optical character recognition, line and character segmentation come before the recognition step. Why?

- A) The classes are single characters, so each pattern for the classifier must be one character.
- B) Segmentation removes the scanner noise, so the classifier gets a cleaner image of the page.
- C) Segmentation decides which letter each character is, so the classifier only checks the result.
- D) The classifier reads the whole page first, and segmentation then cuts its output into lines.

> [!success]- Answer
> **A.** A classifier puts one pattern into one class. The classes are letters, digits and punctuation marks. A whole page holds thousands of characters and belongs to none of these classes.
> - B: Noise removal is pre-processing. It is useful, but it is not the reason for segmentation.
> - C: Segmentation only cuts the image. Naming the letter is the job of the recognition step.
> - D: The order is reversed. Segmentation works on the image before recognition.

### Q8 (slide 9)

2000 women with breast cancer have a mammogram. Assume 20% get a negative result. In about two thirds of these missed cases the cancer was visible in hindsight. In about how many cases was it visible in hindsight?

- A) 133
- B) 400
- C) 267
- D) 1333

> [!success]- Answer
> **C.** Missed: $0.20\cdot2000=400$. Visible in hindsight: $\tfrac23\cdot400\approx267$.
> These cancers were on the image but were not seen. A second reading by a machine can catch a part of them.
> - A: $\tfrac13\cdot400\approx133$ is the part that was not visible in hindsight.
> - B: $400$ is the number of missed cancers. The factor two thirds was forgotten.
> - D: $\tfrac23\cdot2000\approx1333$ takes two thirds of all women, not of the missed cases.

### Q9 (slide 10)

A skilled typist types 60 words per minute. The slide says that entering text by talking is about twice as fast. How long does a 900-word text take by talking?

- A) 15 minutes
- B) 7.5 minutes
- C) 30 minutes
- D) 450 minutes

> [!success]- Answer
> **B.** Talking speed: $2\cdot60=120$ words per minute. Time: $900/120=7.5$ minutes.
> - A: $900/60=15$ minutes is the typing time. The factor 2 was forgotten.
> - C: $900/30=30$ minutes uses half the speed. "Twice as fast" multiplies the speed, it does not divide it.
> - D: $900/2=450$ divides the number of words by the factor 2 and never uses the speed.

### Q10 (slide 11)

A database holds 5 million images. A person needs 30 seconds to write keywords for one image. How many working days of 8 hours does the manual annotation take?

- A) About 1736 days
- B) About 41 667 days
- C) About 312 500 days
- D) About 5208 days

> [!success]- Answer
> **D.** Total: $5\,000\,000\cdot30=150\,000\,000$ s. In hours: $150\,000\,000/3600\approx41\,667$ h. In working days: $41\,667/8\approx5208$ days.
> This is about 20 years for one person. Keyword search does not scale, so content-based retrieval is needed.
> - A: $41\,667/24\approx1736$ uses days of 24 hours, not working days of 8 hours.
> - B: $41\,667$ is the number of hours. The division by 8 was forgotten.
> - C: $150\,000\,000/60/8=312\,500$ converts seconds to minutes only, then divides by 8 hours.

### Q11 (slide 12)

In a content-based music retrieval system, a short extract is played to the system and it returns "similar" pieces. What does "similar" mean here?

- A) The signatures measured on the content (meter, tempo, repeated patterns) are close to each other.
- B) The keywords that a person wrote for the two pieces in the database are the same.
- C) The two pieces have the same title, the same artist and the same genre in the database.
- D) The two recordings are equal to each other, sample by sample, as audio waveforms.

> [!success]- Answer
> **A.** The system measures cues on the content itself. Together they are the signature (a feature vector). Two pieces are similar when their signatures are close.
> - B: This is keyword search. It compares words written by a human, not the content.
> - C: Title, artist and genre are also annotations written by a human.
> - D: Too strict. The system compares measured features, not raw samples. Two different recordings of one song are similar but not equal.

### Q12 (slide 13)

A DNA sequence uses the four letters A, C, G, T. How many different sequences of length 10 exist?

- A) 40
- B) 10 000
- C) 1 048 576
- D) 262 144

> [!success]- Answer
> **C.** Each position has 4 choices, so length $n$ gives $4^n$ sequences. $4^{10}=1\,048\,576$.
> Genes are much longer, so the comparison cannot be done by hand.
> - A: $4\cdot10=40$ multiplies. The choices of the positions must be multiplied with each other, which gives a power.
> - B: $10^4=10\,000$ swaps the base and the exponent.
> - D: $4^9=262\,144$ counts only 9 positions.

## 3. Applications, part 2 (slides 14-18)

### Q13 (slide 14)

The figure shows eight fingerprint types. One of them is "Tented Arch". In the words of this lecture, what is "Tented Arch"?

![[ee525_lecture_01_lecture_1_introduction_p014.jpg]]

- A) A pattern: it is the object that the classifier receives at its input.
- B) A feature: it is a number that is measured on one fingerprint image.
- C) A class: it is one of the 8 categories, and one fingerprint image is the pattern.
- D) A feature vector: it is the list of all measurements of one fingerprint.

> [!success]- Answer
> **C.** "Tented Arch" is the name of a category. The figure shows 8 such classes. The pattern is one fingerprint image, and the classifier puts it into exactly one class.
> - A: The pattern is a single fingerprint image, not the name of a type.
> - B: A feature is a number measured on the image. "Tented Arch" is a name of a group.
> - D: A feature vector is a list of numbers for one image. It is still not a category.

### Q14 (slide 15)

A tablet records six kinds of measurement of a signature: position $x,y$, time stamp, pressure, pen inclination, curvature, acceleration. Which of them can you still get from a signature that was scanned from paper?

- A) Position and curvature, because both come from the shape of the line alone.
- B) Position and time stamp, because the order of the strokes is visible on paper.
- C) Curvature and acceleration, because both are computed from the shape of the line.
- D) All six of them, because the tablet data can be computed back from the image.

> [!success]- Answer
> **A.** A scan gives only the shape: the positions $x,y$, and the curvature that is computed from them.
> The other four need a tablet that records the pen while the person writes. A forger cannot see them, so they help to separate genuine from forged.
> - B: Paper does not store when each point was written. The time stamp is lost.
> - C: Acceleration needs the time of each point, so it is lost too.
> - D: Time, pressure, pen inclination and acceleration are not in the image. This is why they are hard to forge.

### Q15 (slide 16)

In part (b) of the figure the query is a video, and the system returns three sentences numbered 1, 2, 3. What does this order mean?

![[ee525_lecture_01_lecture_1_introduction_p016.jpg]]

- A) The order in which the sentences were found during the search of the database.
- B) The time order of the three events that are shown one after the other in the video.
- C) Three equally good descriptions of the video, where the numbers are only labels.
- D) A ranking by similarity, where sentence 1 is the most similar to the query video.

> [!success]- Answer
> **D.** The list is ranked. Sentence 1 is the one the system finds most similar to the video, so it is the answer. Sentences 2 and 3 are less similar.
> Part (a) is the same idea in the other direction: the query is a sentence and the ranked list holds videos.
> - A: The order comes from a similarity score, not from the search order.
> - B: The three sentences are three candidate descriptions of the same video, not three events.
> - C: The numbers carry information: a lower number means more similar.

### Q16 (slide 17)

In part (a) of the figure the system writes "HOME" on the screen. Part (b) shows the 11 hand shapes the system knows. Which two steps does the system do to produce "HOME"?

![[ee525_lecture_01_lecture_1_introduction_p017.jpg]]

- A) It first classifies the hand shape into one of the 11 classes, then it finds the face and the hand in the image.
- B) It first finds the face and the hand in the image, then it classifies the hand region into one of the 11 classes.
- C) It first finds the face in the image, then it classifies the face region into one of the 11 stored classes.
- D) It first finds the face and the hand in the image, then it clusters the hand region without any known classes.

> [!success]- Answer
> **B.** Step 1 is detection: find where the face and the hand are (the two boxes). This isolates the pattern from the background. Step 2 is classification: assign the hand region to one of the 11 gesture classes, here "Home".
> - A: The order is reversed. The hand must be found before its shape can be classified.
> - C: The 11 classes are hand shapes. The gesture is read from the hand, not from the face.
> - D: The classes are known (the 11 shapes of part (b)), so this is classification, not clustering.

### Q17 (slide 18)

An MPEG-7 query is "find all video scenes showing person X laughing". Which two recognition decisions are hidden in this query?

- A) Who is in the scene (person X or not), and what the person does (laughing or not).
- B) Where the scene is stored in the library, and how the result is shown on the screen.
- C) Who is in the scene (person X or not), and in which video file the scene is stored.
- D) Which words are spoken in the scene, and which of the persons speaks these words.

> [!success]- Answer
> **A.** Decision 1 is face recognition: person X or another person. Decision 2 is expression recognition: laughing or not laughing.
> - B: Storage and display are needed too, but they are the work of database design and computer graphics. This is why pattern recognition alone is not enough.
> - C: The first half is right. Finding the file is a database task, not a recognition decision.
> - D: The query asks for a person and an expression. Speech is not part of it.

## 4. Features, feature vectors and classifiers (slides 19-22)

### Q18 (slide 19)

Look at the two image regions of Figure 1.1, region (a) of class A and region (b) of class B. Compare the mean intensity $\mu$ and the standard deviation $\sigma$ of their pixels.

![[ee525_lecture_01_lecture_1_introduction_p019.jpg]]

- A) Region (a) has the larger $\mu$ and also the larger $\sigma$.
- B) Region (b) has the larger $\mu$ and also the larger $\sigma$.
- C) Region (a) has the larger $\mu$, and region (b) has the larger $\sigma$.
- D) Region (b) has the larger $\mu$, and region (a) has the larger $\sigma$.

> [!success]- Answer
> **C.** Region (a) is light grey almost everywhere: bright pixels give a large $\mu$, and similar pixels give a small $\sigma$. Region (b) mixes very dark and very bright pixels: it is darker on average (smaller $\mu$) and its values are far from the mean (large $\sigma$).
> - A: The $\mu$ part is right. But the pixels of (a) are almost equal, so its $\sigma$ is small.
> - B: The $\sigma$ part is right. But (b) is darker on average, so its $\mu$ is smaller.
> - D: Both parts are reversed. Brightness gives $\mu$, and the spread of the values gives $\sigma$.

### Q19 (slide 20)

Two regions have the pixels $R_1=\{2,2,10,10\}$ and $R_2=\{5,6,6,7\}$. Use $\mu=\frac1N\sum z_k$ and $\sigma=\sqrt{\frac1N\sum(z_k-\mu)^2}$. Which result is correct?

- A) $\mu=6$ for both, $\sigma_1=16$ and $\sigma_2=0.5$
- B) $\mu=6$ for both, $\sigma_1\approx4.62$ and $\sigma_2\approx0.82$
- C) $\mu=6$ for both, $\sigma_1=4$ and $\sigma_2=0.5$
- D) $\mu=6$ for both, $\sigma_1=4$ and $\sigma_2\approx0.71$

> [!success]- Answer
> **D.** $R_1$: $\mu=24/4=6$. Squared deviations $16,16,16,16$, sum $64$. $\sigma^2=64/4=16$, $\sigma=4$.
> $R_2$: $\mu=24/4=6$. Squared deviations $1,0,0,1$, sum $2$. $\sigma^2=2/4=0.5$, $\sigma=\sqrt{0.5}\approx0.71$.
> Both regions have $\mu=6$, so $\mu$ alone cannot separate them. With $\sigma$ they are far apart. This is why the example uses two features.
> - A: $16$ and $0.5$ are the variances $\sigma^2$. The square root was forgotten.
> - B: Division by $N-1=3$: $\sqrt{64/3}\approx4.62$ and $\sqrt{2/3}\approx0.82$. The formula here divides by $N$.
> - C: These are the means of the absolute deviations: $(4+4+4+4)/4=4$ and $(1+0+0+1)/4=0.5$. For $R_1$ this happens to equal $\sigma$, for $R_2$ it does not. The deviations must be squared, averaged, and then the root is taken.

### Q20 (slide 21)

A team measures four numbers on each image region: mean, standard deviation, area and perimeter. The same lesion is imaged twice and gives $\mu=3.0$ the first time and $\mu=3.2$ the second time. Which statement is correct? ($l$ is the number of features, $\mathbf{x}$ the feature vector.)

- A) $l=4$ and $\mathbf{x}\in\mathbb{R}^4$. The device is broken, because one lesion must always give one value.
- B) $l=4$ and $\mathbf{x}\in\mathbb{R}^4$. Nothing is broken, because features are treated as random variables.
- C) $l=2$ and $\mathbf{x}\in\mathbb{R}^2$. Nothing is broken, because features are treated as random variables.
- D) $l=8$ and $\mathbf{x}\in\mathbb{R}^8$. Nothing is broken, because the two images give four numbers each.

> [!success]- Answer
> **B.** Four features give $l=4$ and $\mathbf{x}=[x_1,x_2,x_3,x_4]^{\mathsf T}\in\mathbb{R}^4$. Features vary randomly because of measurement noise and of differences between patterns. So the points of one class form a cloud, not a single point.
> - A: The dimension is right. But a small change between two measurements is normal, not a defect.
> - C: $l=2$ is the toy example with $\mu$ and $\sigma$ only. Here four numbers are measured.
> - D: Each image gives its own feature vector with 4 entries. The two images are two points in $\mathbb{R}^4$, not one point in $\mathbb{R}^8$.

### Q21 (slide 22)

A pattern is the pair $(\sigma,\mu)$. The decision line is $g=\mu-\sigma-2=0$: class A if $g>0$, class B if $g<0$. Training patterns $(\sigma,\mu)$: class A $(1,5)$, $(2,6)$, $(3,4.5)$ and class B $(4,3)$, $(5,4)$. How many of the five patterns does the line misclassify?

- A) 1
- B) 0
- C) 3
- D) 4

> [!success]- Answer
> **A.** Class A needs $g>0$: $(1,5)$: $5-1-2=2$, correct. $(2,6)$: $6-2-2=2$, correct. $(3,4.5)$: $4.5-3-2=-0.5<0$, wrong.
> Class B needs $g<0$: $(4,3)$: $3-4-2=-3$, correct. $(5,4)$: $4-5-2=-3$, correct.
> One misclassification out of five, an error rate of $20\%$. We can count it because the true classes of training patterns are known.
> - B: Without the offset, $g=\mu-\sigma$ gives $4.5-3=1.5>0$ for the third point and no error. The $-2$ was forgotten.
> - C: With the coordinates swapped, $g=\sigma-\mu-2$ gives $-6,-6,-3.5$ for class A (3 errors) and $-1,-1$ for class B (0 errors).
> - D: With the sides swapped (A if $g<0$), the four correct points become wrong and the wrong one becomes correct.

## 5. The four design stages (slides 23-25)

### Q22 (slide 23)

A team invents 10 candidate features (feature generation) and then keeps the best 3 (feature selection). How many different groups of 3 features can be chosen from the 10?

- A) 30
- B) 720
- C) 1000
- D) 120

> [!success]- Answer
> **D.** $\binom{10}{3}=\dfrac{10\cdot9\cdot8}{3\cdot2\cdot1}=\dfrac{720}{6}=120$. Even a small selection problem has many options to compare.
> - A: $10\cdot3=30$ multiplies the two numbers. This does not count groups.
> - B: $10\cdot9\cdot8=720$ counts ordered choices. The order of the 3 features does not matter, so divide by $3!=6$.
> - C: $10^3=1000$ allows the same feature to be chosen again and also counts the order.

### Q23 (slide 24)

Someone says: "The line in Figure 1.2 separates the two classes, so a line drawn by eye is a good classifier design. And for most real problems a straight line is enough." Which statement agrees with the slide?

- A) Every line that separates the training points is an equally good design of the classifier.
- B) A line drawn by eye is a good design, because most of the real problems are linear.
- C) The boundary must be placed optimally for a criterion, and in general it is nonlinear.
- D) The boundary must be placed optimally for a criterion, and it is a line for most problems.

> [!success]- Answer
> **C.** The slide says two things. The boundary is not drawn "to please the eye": it is placed optimally with respect to an optimality criterion. And problems where a line (or hyperplane) works well are not the rule: in general the boundary is nonlinear.
> - A: Many lines separate the training points. A criterion is needed to choose one of them.
> - B: Both mistakes of the sentence are repeated here.
> - D: The first half is right. The second half is the second mistake: linear problems are not the rule.

### Q24 (slide 25)

A finished classifier is tested on 200 patterns and gets 26 wrong. The customer accepts an error rate of at most 5%. What is the error rate, and what happens next according to Figure 1.3?

- A) 7.7%. It is above 5%, so the team goes back and redesigns an earlier stage.
- B) 13%. It is above 5%, so the team goes back and redesigns an earlier stage.
- C) 13%. It is above 5%, so the team repeats the system evaluation stage only.
- D) 1.3%. It is below 5%, so the design is finished and the system is delivered.

> [!success]- Answer
> **B.** $26/200=0.13=13\%$, and $13\%>5\%$. The stages are a loop: the feedback arrows lead from system evaluation back to classifier design, feature selection, feature generation and the sensor. After the redesign the system is evaluated again.
> - A: $200/26\approx7.7$ divides the wrong way. The error rate is errors divided by patterns.
> - C: The rate is right. But a new evaluation of the same system gives the same kind of result. An earlier stage must change.
> - D: $0.13$ was read as $1.3\%$. It is $13\%$.

## 6. Fish sorting: one feature and a threshold (slides 26-30)

### Q25 (slide 26)

The slide assumes two kinds of fish, sea bass and salmon. Suppose a third species, trout, also arrives on the belt. Is one threshold on one feature still enough?

- A) Yes. One threshold is enough when the chosen feature is good, for example the lightness.
- B) No. Three classes need at least three thresholds on the feature, one for each class.
- C) No. Three classes always need three different features, one feature for each class.
- D) No. One threshold gives only 2 regions, so two thresholds or more features are needed.

> [!success]- Answer
> **D.** One threshold cuts the feature axis into 2 regions, so it can name only 2 classes. Three classes need at least 3 regions: two thresholds on one feature, or a division of a feature space with more features.
> - A: Even a perfect feature cannot give 3 answers with one cut.
> - B: Two thresholds already give 3 regions. Three thresholds would give 4.
> - C: The number of features does not have to equal the number of classes. One feature with two thresholds can be enough.

### Q26 (slide 27)

The slide's steps are: capture image, isolate fish, take measurements, make decision. The lamp above the belt becomes weaker during the day. Which feature is damaged more, and at which step does the problem enter?

- A) Lightness is damaged more. The problem enters at "capture image".
- B) Length is damaged more. The problem enters at "capture image".
- C) Lightness is damaged more. The problem enters at "make decision".
- D) Length is damaged more. The problem enters at "take measurements".

> [!success]- Answer
> **A.** A weaker lamp makes every fish look darker, so all lightness values go down and a bright sea bass can fall below the threshold. The length in the image stays almost the same. Lighting is a sensing problem, so it enters at "capture image". It can be repaired by constant lighting or by a brightness correction before the measurements.
> - B: The step is right, but the size of the fish in the image does not depend on the lamp.
> - C: The feature is right. The decision rule did not change: it gets wrong input because the image is darker.
> - D: Neither part is right. The measurement step works as before on a darker image.

### Q27 (slide 28)

The rule is "sea bass if length $>l^\ast$, else salmon". Suppose the two species had exactly the same length distribution. What could the best threshold $l^\ast$ achieve?

- A) Zero errors, when $l^\ast$ is placed in the middle of the common length range.
- B) A small error, because a sea bass is generally longer than a salmon.
- C) Nothing better than a guess, because the length tells nothing about the species.
- D) Zero errors on the training fish, but some errors on the new fish of tomorrow.

> [!success]- Answer
> **C.** With identical distributions, the two species appear on each side of any threshold in the same proportion as on the whole belt. The length carries no information about the species. A feature is useful only if its values differ between the classes.
> - A: On both sides of the middle there are fish of both species, so there are many errors.
> - B: "Generally longer" is the real situation. The question assumes equal distributions, so this advantage is gone.
> - D: This describes overfitting. Here even the training fish cannot be separated by a threshold.

### Q28 (slide 29)

In the length histogram (black: salmon, red: sea bass) the rule is "sea bass if length $>l^\ast$". You move the threshold $l^\ast$ from 11 to 16. What happens to the two kinds of error?

![[ee525_lecture_01_lecture_1_introduction_p029.jpg]]

- A) "Salmon called sea bass" and "sea bass called salmon" both become rarer.
- B) "Salmon called sea bass" becomes rarer, "sea bass called salmon" more frequent.
- C) "Salmon called sea bass" becomes more frequent, "sea bass called salmon" rarer.
- D) "Salmon called sea bass" and "sea bass called salmon" both become more frequent.

> [!success]- Answer
> **B.** A salmon is called sea bass when it is longer than $l^\ast$. With a higher threshold fewer salmon are above it. A sea bass is called salmon when it is not longer than $l^\ast$. With a higher threshold more sea bass are below it.
> No position gives zero errors, because the two histograms overlap over almost the whole axis.
> - A: Moving a threshold trades one error for the other. Both cannot become rarer when the histograms overlap.
> - C: This is the result of moving the threshold down, from 16 to 11.
> - D: Fewer salmon lie above 16 than above 11, so the first error cannot grow.

### Q29 (slide 30)

Lightness of training fish: salmon $1,2,3,4,7$ and sea bass $5,6,8,9,10$. Rule: sea bass if lightness $>t$. Among $t=3.5,\ 4.5,\ 5.5,\ 7.5$, which threshold is best, and how many errors does it make?

- A) $t=4.5$ with 1 error
- B) $t=5.5$ with 1 error
- C) $t=7.5$ with 0 errors
- D) $t=3.5$ with 0 errors

> [!success]- Answer
> **A.** Errors $=(\text{salmon}>t)+(\text{sea bass}\le t)$.
> $t=3.5$: $2+0=2$. $t=4.5$: $1+0=1$. $t=5.5$: $1+1=2$. $t=7.5$: $0+2=2$.
> Best: $t=4.5$, 1 error out of 10 ($10\%$). No threshold is perfect: the salmon with lightness 7 is brighter than the sea bass 5 and 6.
> - B: At $t=5.5$ the salmon 7 is above and the sea bass 5 is below: 2 errors. One of them was forgotten.
> - C: At $t=7.5$ no salmon is above, but the sea bass 5 and 6 are below: 2 errors. Only one kind of error was counted.
> - D: At $t=3.5$ no sea bass is below, but the salmon 4 and 7 are above: 2 errors. Only one kind of error was counted.

## 7. Cost of error (slide 31)

### Q30 (slide 31)

Rule: sea bass if lightness $>x^\ast$. A salmon called sea bass costs $c_1=1$. A sea bass called salmon costs $c_2=4$, because salmon buyers object strongly to sea bass in their cans. Threshold P: 4 salmon above, 6 sea bass below. Threshold Q: 10 salmon above, 1 sea bass below. Which threshold has the lower total cost?

- A) P has cost 10 and Q has cost 11, so P is the better choice.
- B) P has cost 22 and Q has cost 41, so P is the better choice.
- C) P has cost 28 and Q has cost 14, so Q is the better choice.
- D) P has cost 40 and Q has cost 44, so P is the better choice.

> [!success]- Answer
> **C.** Cost $=c_1\cdot(\text{salmon above})+c_2\cdot(\text{sea bass below})$.
> P: $1\cdot4+4\cdot6=4+24=28$. Q: $1\cdot10+4\cdot1=10+4=14$.
> Q makes one more error ($11$ against $10$) but has half the cost. Q is the lower threshold: fewer sea bass fall below it, so the expensive mistake is rarer.
> - A: $4+6=10$ and $10+1=11$ are the numbers of errors. The costs were not used.
> - B: The costs are swapped: $4\cdot4+1\cdot6=22$ and $4\cdot10+1\cdot1=41$.
> - D: $4\cdot10=40$ and $4\cdot11=44$ give every error the cost 4. Only a sea bass called salmon costs 4.

## 8. Two features, complexity and generalisation (slides 32-36)

### Q31 (slide 32)

The slide defines $x_1$ as lightness and $x_2$ as width. A fish has width 18 and lightness 6.5. How is this fish described?

- A) By $[18,\ 6.5]^{\mathsf T}$, one point in $\mathbb{R}^2$. The order of the entries is free.
- B) By $[6.5,\ 18]^{\mathsf T}$, one point in $\mathbb{R}^2$. The order of the entries is fixed.
- C) By the values $6.5$ and $18$, two points in $\mathbb{R}^1$. Each feature gives one point.
- D) By $[6.5,\ 18]^{\mathsf T}$, one point in $\mathbb{R}^3$. A fish is a three-dimensional object.

> [!success]- Answer
> **B.** The order is fixed by the definition: $x_1$ lightness, $x_2$ width. So $\mathbf{x}=[6.5,\ 18]^{\mathsf T}$. Two features give a two-dimensional feature space, and each fish is one point in the plane.
> - A: $[18,\ 6.5]^{\mathsf T}$ describes a different fish, with lightness 18 and width 6.5.
> - C: The features of one pattern are put together into one vector. One fish is one point.
> - D: The dimension is the number of features, not the dimension of the object. With the length as a third feature it would be $\mathbb{R}^3$.

### Q32 (slide 33)

Feature space: $x_1$ lightness, $x_2$ width. The straight boundary passes through the points $(4,21)$ and $(7,15)$. Sea bass are on the side of large lightness, where $g(\mathbf{x})>0$. Which line is correct, and how are the fish $[5,20]^{\mathsf T}$ and $[6,16]^{\mathsf T}$ classified?

- A) $g=2x_1-x_2+13$. The first is sea bass, the second is sea bass.
- B) $g=x_1-5.5$. The first is salmon, the second is sea bass.
- C) $g=x_1+2x_2-46$. The first is salmon, the second is salmon.
- D) $g=2x_1+x_2-29$. The first is sea bass, the second is salmon.

> [!success]- Answer
> **D.** Slope: $\dfrac{15-21}{7-4}=-2$. Line: $x_2-21=-2(x_1-4)$, so $2x_1+x_2-29=0$. Check: $8+21-29=0$ and $14+15-29=0$.
> $[5,20]^{\mathsf T}$: $g=10+20-29=1>0$, sea bass. $[6,16]^{\mathsf T}$: $g=12+16-29=-1<0$, salmon.
> The tilted line uses the width too: a wide fish is sea bass even when it is a little dark.
> - A: Slope $+2$, a sign error. This line does not pass through $(7,15)$: $14-15+13=12\ne0$. It gives $g=3$ and $g=9$.
> - B: This is the lightness-only rule "sea bass if $x_1>5.5$", a vertical line. It passes through neither point, and both answers are reversed.
> - C: Slope $-\tfrac12$, the fraction was inverted ($3/(-6)$). This line does not pass through $(7,15)$: $7+30-46=-9\ne0$. It gives $g=-1$ and $g=-8$.

### Q33 (slide 34)

"Boundary B makes 0 errors on the training fish and boundary A makes 5%. So B will also make fewer errors on the fish of tomorrow." How do you judge this sentence?

- A) It is correct, because the training error is the best estimate of the error on new fish.
- B) It is wrong, because 0 errors on training fish is impossible, so B was counted wrongly.
- C) It is wrong, because a lower training error always means a higher error on new fish.
- D) It is wrong, because a complex curve can reach 0 training errors by following accidents.

> [!success]- Answer
> **D.** The training error only says how well the boundary fits the fish it was drawn from. A complex enough curve can go around every training point. To do this it follows accidental details of this sample. New fish do not repeat them, so the new sample "?" on the slide is misclassified. This is over-tuning (overfitting).
> - A: The training error is too optimistic, because the boundary was adjusted to exactly these fish.
> - B: 0 training errors is possible. The very complex boundary of the slide reaches it.
> - C: Too strong. The conclusion does not follow from the training error, but it is not always reversed. A gentle curve can lower both errors compared with a line.

### Q34 (slide 35)

You make the boundary more complex step by step: straight line, gentle curve, very wiggly curve. How do the training error and the error on new fish change?

- A) The training error goes down at each step. The error on new fish goes down, then up.
- B) The training error goes down at each step. The error on new fish goes down at each step.
- C) The training error goes down, then up. The error on new fish goes down at each step.
- D) The training error goes down at each step. The error on new fish goes up at each step.

> [!success]- Answer
> **A.** More complexity always fits the training fish better, down to 0 errors. The error on new fish first goes down, because the gentle curve follows the real shape of the two clouds. Then it goes up, because the wiggly curve follows single training fish. The model of slide 35 is the gentle curve in the middle.
> - B: This forgets overfitting. The wiggly curve is worse on new fish.
> - C: The two curves are swapped. The U shape belongs to the error on new fish.
> - D: This forgets that a too simple model is also poor. The gentle curve is better than the line on new fish.

### Q35 (slide 36)

Cut each feature axis into 4 bins and ask for 20 training fish per cell on average. How many training fish do you need for $l=5$ features?

- A) 400
- B) 20 480
- C) 1024
- D) 12 500

> [!success]- Answer
> **B.** Cells: $4^5=1024$. Fish: $20\cdot1024=20\,480$.
> For $l=2$ it is $20\cdot4^2=320$. Three more features need $64$ times more data. This is the curse of dimensionality: more features do not always help when the training set is fixed.
> - A: $20\cdot4\cdot5=400$ counts $4\cdot5=20$ cells. The number of cells is a power, $4^5$, not a product.
> - C: $1024$ is the number of cells. The 20 fish per cell were forgotten.
> - D: $20\cdot5^4=12\,500$ swaps the number of bins and the number of features.

## 9. Pattern recognition systems (slides 37-39)

### Q36 (slide 37)

The figure has two columns: training (right, done once at design time) and operation (left, done for every new pattern). Which single object goes from the training column to the operation column?

![[ee525_lecture_01_lecture_1_introduction_p037.jpg]]

- A) The training data, which the classifier compares with each new pattern.
- B) The training features, which are added to the features of the new pattern.
- C) The model, which is learned in training and then used by "Classification".
- D) The decision, which is made in training and then repeated in operation.

> [!success]- Answer
> **C.** The only link between the columns is the arrow from "Model" to "Classification". The model is the output of "Model learning/estimation". Both columns use the same pre-processing and the same features, because the model only makes sense for patterns described in the same way.
> - A: The training data stay in the training column. Only what was learned from them crosses.
> - B: The features of a new pattern are computed from the new pattern alone.
> - D: The decision is the output of the operation column. It is made for each new pattern.

### Q37 (slide 38)

Four items: (a) the camera has $640\times480$ pixels, (b) a filter removes the speckle noise from the image, (c) the fish is cut out from the belt background, (d) the length of the fish is measured in the cleaned image. Which assignment to the stages is correct?

- A) (a) sensing, (b) pre-processing, (c) pre-processing, (d) feature extraction
- B) (a) sensing, (b) pre-processing, (c) feature extraction, (d) feature extraction
- C) (a) pre-processing, (b) pre-processing, (c) pre-processing, (d) feature extraction
- D) (a) sensing, (b) sensing, (c) pre-processing, (d) feature extraction

> [!success]- Answer
> **A.** (a) The number of pixels is the resolution of the sensor: sensing. (b) Removal of noise and (c) isolation of the pattern from the background are the two jobs of pre-processing. (d) The image is replaced by a number: feature extraction.
> - B: Cutting out the fish gives no feature yet. It only isolates the pattern, which is pre-processing.
> - C: The resolution is a property of the camera. It is fixed at sensing, before any processing.
> - D: The filter works on the image after it was captured, so it is pre-processing.

### Q38 (slide 39)

"Combination of experts": three classifiers label five fish (S = salmon, B = sea bass). Truth: S B B S B. Expert 1: S B B B B. Expert 2: S S B S B. Expert 3: B B B S B. A majority vote gives each fish the class that most experts chose. What are the error rates?

- A) Each expert 20%, majority vote 20%
- B) Each expert 20%, majority vote 60%
- C) Each expert 20%, majority vote 0.8%
- D) Each expert 20%, majority vote 0%

> [!success]- Answer
> **D.** Expert 1 is wrong on fish 4, expert 2 on fish 2, expert 3 on fish 1: each 1 error out of 5, $20\%$.
> Vote: fish 1: S, S, B gives S. Fish 2: B, S, B gives B. Fish 3: B. Fish 4: B, S, S gives S. Fish 5: B. Result S B B S B, equal to the truth: $0\%$.
> The experts make their mistakes on different fish, so each wrong answer is outvoted by two correct ones. This is a post-processing step.
> - A: The vote is not limited by the single experts. Here it is better than each of them.
> - B: $3/5=60\%$ adds the three errors. In the vote each of them is outvoted.
> - C: $0.2^3=0.008$ is a probability that three independent experts are all wrong. The question asks to count on these five fish.

## 10. The design cycle (slides 40-44)

### Q39 (slide 40)

You have 500 labelled fish, all caught in July. You use 80% for training and the rest for testing. The machine will work the whole year. The slide asks if the data set is "large and representative". Which statement is correct?

- A) 400 training and 100 test fish. The danger is "large": more July fish would solve the problem.
- B) 80 training and 420 test fish. The danger is "representative": July fish may differ from winter fish.
- C) 400 training and 100 test fish. The danger is "representative": July fish may differ from winter fish.
- D) 400 training and 100 test fish. There is no danger: the test fish show the error for the whole year.

> [!success]- Answer
> **C.** $0.80\cdot500=400$ and $500-400=100$. Fish of one month may differ from winter fish in size and colour. A training set must look like the data the system will meet later.
> - A: A larger set does not repair this. More July fish still say nothing about January.
> - B: 80 fish is not 80%. The split is $400$ and $100$.
> - D: The test fish are also from July. So the test error looks better than the real error over the year.

### Q40 (slide 41)

Two features of a fish in a camera image: (a) the area in pixels, (b) the area divided by the length squared. The camera is moved higher, so every length in the image is multiplied by $s=0.5$. Before the move a fish had length 200 pixels and area 8000 pixels. Which result is correct?

- A) Area: 8000 to 4000. Ratio: 0.2 to 0.4. Neither feature is scale invariant.
- B) Area: 8000 to 2000. Ratio: 0.2 to 0.2. Only the ratio is scale invariant.
- C) Area: 8000 to 2000. Ratio: 40 to 20. Neither feature is scale invariant.
- D) Area: 8000 to 8000. Ratio: 0.2 to 0.8. Only the area is scale invariant.

> [!success]- Answer
> **B.** A scale factor $s$ multiplies lengths by $s$ and areas by $s^2$. Length: $0.5\cdot200=100$. Area: $0.25\cdot8000=2000$.
> Ratio before: $8000/200^2=8000/40\,000=0.2$. After: $2000/100^2=2000/10\,000=0.2$. The factor $s^2$ cancels.
> Both features are invariant to translation and rotation.
> - A: The area was multiplied by $s$, not by $s^2$: $0.5\cdot8000=4000$, then $4000/100^2=0.4$.
> - C: The area was divided by the length, not by the length squared: $8000/200=40$ and $2000/100=20$.
> - D: The area in the image was kept at 8000. The real fish does not change, but its image does: $8000/100^2=0.8$.

### Q41 (slide 42)

Two species of fish. Model 1 assumes that the lightness of each species is Gaussian and estimates a mean and a variance per species. Model 2 stores a lightness histogram with 20 bins per species. Count the learned numbers for both species together. Which statement is correct?

- A) Model 1 is parametric and learns 2 numbers. Model 2 is non-parametric and learns 20 numbers.
- B) Model 1 is non-parametric and learns 4 numbers. Model 2 is parametric and learns 40 numbers.
- C) Model 1 is parametric and learns 4 numbers. Model 2 is non-parametric and learns 40 numbers.
- D) Model 1 is parametric and learns 4 numbers. Model 2 is non-parametric and learns 0 numbers.

> [!success]- Answer
> **C.** Model 1 fixes the form (Gaussian) and learns only its parameters: $2$ per species, $2\cdot2=4$. Model 2 assumes no form, the data draw the shape: $20$ bar heights per species, $2\cdot20=40$.
> Model 1 needs few training fish but is wrong if the true distribution is not Gaussian. Model 2 follows any shape but needs many more fish.
> - A: These are the numbers for one species. There are two species.
> - B: The names are swapped. "Parametric" does not mean "many numbers". It means a fixed form with a few parameters.
> - D: "Non-parametric" does not mean that nothing is stored. It means that no form is assumed. The histogram stores 40 numbers.

### Q42 (slide 43)

Which kind of learning is each case? (a) A fisherman labels 200 fish, and the threshold is chosen from them. (b) The machine sorts fish with no labels, and after each fish a worker presses a button only when the can was wrong. (c) A net brings up fish of an unknown number of species, and the system groups them by their measurements.

- A) (a) supervised, (b) reinforcement, (c) unsupervised
- B) (a) supervised, (b) unsupervised, (c) reinforcement
- C) (a) reinforcement, (b) supervised, (c) unsupervised
- D) (a) supervised, (b) supervised, (c) unsupervised

> [!success]- Answer
> **A.** Ask what the teacher gives. (a) The class label of every training pattern: supervised. (b) Only "right" or "wrong" after each decision: reinforcement. (c) Nothing, the system forms clusters: unsupervised.
> - B: In (b) there is feedback, so it is not unsupervised. In (c) there is no feedback at all, so it is not reinforcement.
> - C: In (a) the full label of each fish is given. This is more than right or wrong feedback.
> - D: In (b) the worker never says the correct class. He only says that the decision was wrong.

### Q43 (slide 44)

A classifier makes 0 errors on its 300 training fish and 18 errors on 150 new fish. Which number is the estimate of its performance with future data?

- A) 0%, the training error, because it is measured on the larger set.
- B) 4%, the error over all fish, because it uses the most data.
- C) 6%, the mean of the two errors, because both sets count.
- D) 12%, the test error, because these fish were not used for the design.

> [!success]- Answer
> **D.** Test error: $18/150=0.12=12\%$. These fish were not used to design the classifier, like the fish of tomorrow. The training error $0/300=0\%$ is too optimistic, because the boundary was adjusted to exactly these 300 fish. The gap of 12 points is a sign of overfitting.
> - A: A larger set does not help. The classifier has already seen these fish.
> - B: $18/450=4\%$ mixes seen and unseen fish. The 300 seen fish pull the number down.
> - C: $(0\%+12\%)/2=6\%$ also mixes the two. Only the unseen fish count.

## 11. Supervised and unsupervised learning, summary (slides 45-48)

### Q44 (slide 45)

Figure 1.2 is a scatter plot of two classes, benign (o) and malignant (+), in the plane of $\mu$ and $\sigma$. Erase the labels, so that every point becomes the same dot. Which statement is correct now?

- A) It is still a supervised problem. An algorithm can read "benign" and "malignant" from the positions.
- B) It is an unsupervised problem. An algorithm can find the two groups, but not what they mean.
- C) It is an unsupervised problem. An algorithm can find nothing, because the structure was in the labels.
- D) It is a reinforcement problem. An algorithm finds the two groups and learns their names from them.

> [!success]- Answer
> **B.** Only feature vectors without labels remain: unsupervised pattern recognition (clustering). The dots still form two separate groups, so an algorithm can put similar vectors together. Nothing in the dots says "benign" or "malignant". A person with extra information must name the clusters.
> - A: Supervised learning needs the class labels of the training patterns. They were erased.
> - C: The structure is in the positions of the points. The two clouds are still there.
> - D: Reinforcement needs right or wrong feedback after each decision. Here nobody gives any feedback.

### Q45 (slide 46)

A satellite image covers $1000\times1000$ ground cells, and the scanner measures each cell in $l=6$ wavelength bands. How many feature vectors are there, in which space, and how many numbers in total?

- A) 6 vectors in $\mathbb{R}^{1\,000\,000}$, with 6 000 000 numbers in total
- B) 1 000 000 vectors in $\mathbb{R}^6$, with 1 000 006 numbers in total
- C) 1 000 000 vectors in $\mathbb{R}^6$, with 6 000 000 numbers in total
- D) 2000 vectors in $\mathbb{R}^6$, with 12 000 numbers in total

> [!success]- Answer
> **C.** One feature vector per cell: $1000\cdot1000=1\,000\,000$ vectors. Each holds one intensity per band: $\mathbf{x}\in\mathbb{R}^6$. Total: $6\cdot1\,000\,000=6\,000\,000$ numbers.
> Several bands are used because two cover types that look equal in one band can differ in another.
> - A: Patterns and features are swapped. The pattern is the ground cell, and the bands are its features.
> - B: $1\,000\,000+6$ adds. Every vector has 6 entries, so multiply.
> - D: $1000+1000=2000$ adds the two sides of the image. The number of cells is the product.

### Q46 (slide 47)

In Figure 1.4 the map (a) has 6 regions (soil, forest, water, vegetation, forest, soil), and the plot (b) has 4 clusters in the plane of the two bands $x_1$ and $x_2$. Why are the two numbers different?

![[ee525_lecture_01_lecture_1_introduction_p047.jpg]]

- A) Clustering works in feature space. The two forest regions reflect alike and form one cluster, and so do the two soil regions.
- B) Clustering works on the map. It merged the regions that are neighbours on the ground into one cluster, so two regions disappeared.
- C) Clustering made two errors. Six regions on the map must give six clusters in the plot, one cluster for each of the regions.
- D) Clustering found six groups. The analyst then had names for only four of them, so two clusters are left without a name.

> [!success]- Answer
> **A.** A cell is placed by its band intensities, not by its position on the ground. Cells of the two forest regions have similar intensities, so they fall into one cluster. The same holds for soil. Result: water, soil, vegetation, forest.
> Here $l=2$. The algorithm only finds the groups. The analyst names them with reference ground data.
> - B: The position on the ground is not a feature here. The two forest regions are merged because they reflect alike, not because they touch.
> - C: Nothing went wrong. A cluster is a kind of ground cover, and the map has only four kinds.
> - D: The plot shows four groups. The analyst names clusters after the algorithm found them and does not change their number.
