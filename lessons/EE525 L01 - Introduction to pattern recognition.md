---
course: EE 525 Pattern Recognition
lecture: 1
title: Introduction to pattern recognition
tags: [ee525, lecture]
slides: lectures/lecture_01/Lecture 1- Introduction.pdf
full_notes: lecture_notes/lecture_01/EE525_Lecture_01_Introduction.pdf
---
# EE 525 · Lecture 1 · Introduction to pattern recognition

> [!summary] In short
> - Pattern recognition classifies objects (patterns) into classes: observe, learn, decide.
> - A pattern becomes a feature vector $\mathbf{x}=[x_1,\dots,x_l]^{\mathsf T}$, a point in feature space. A class is a cloud of points.
> - A classifier cuts feature space into regions. The linear one is the sign of $g(\mathbf{x})=\mathbf{w}^{\mathsf T}\mathbf{x}+w_0$.
> - Design stages: feature generation, feature selection, classifier design, system evaluation, with feedback.
> - Errors can have different costs. The cost moves the threshold away from the expensive mistake.
> - Performance on new data is what counts. A too complex model overfits. More features are not always better.

## 1. Patterns and classes (slides 2-6)
![[ee525_lecture_01_lecture_1_introduction_p002.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p003.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p004.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p005.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p006.jpg]]

**Notes.** Pattern recognition is the discipline whose goal is to classify objects into categories (classes). The objects are called **patterns**: images, signal waveforms, any measurements.

- **Pattern**: an object, event, signal or observation that can be described by measurable characteristics. Examples: fingerprint image, handwritten word, face, speech signal, DNA sequence.
- **Pattern recognition**: how machines can (i) observe the environment, (ii) learn to distinguish patterns of interest, (iii) make sound decisions about their categories.

Humans do this without effort (faces, speech, handwriting, smell). Nature inspires many algorithms, but many techniques are purely numerical.

| Word | What it is | Fish example |
|---|---|---|
| pattern | the object that comes in | one fish on the belt |
| feature | a number measured on the pattern | its length |
| class | the box it must go into | salmon or sea bass |

> [!question]- Check (slide 2): A classmate says: "Pattern recognition began in the 1960s, when computers appeared." What is wrong with this sentence?
> The field is older. Before the 1960s it existed mostly as theoretical research in statistics.
> Computers did not start it. They raised the demand for practical applications, and these applications then asked for new theory.

> [!question]- Check (slide 3): Take the slide's example "distinguishing fresh food from its smell". If a machine does this job, what is the pattern, what could be a feature, and what are the classes?
> Pattern: the smell of one food sample, measured by a gas sensor.
> Feature: a number measured on it, for example the concentration of one gas.
> Classes: fresh or not fresh.
> The slide only names the task. The sensor and the feature are one possible choice.

> [!question]- Check (slide 4): A phone unlocks with a fingerprint. Match the three verbs of the slide (observe, learn, decide) to what the phone does.
> Observe: the sensor scans the finger and gives an image.
> Learn: during set-up the phone stores several scans of the owner's finger, so it learns what "owner" looks like.
> Decide: for each new scan it chooses a category, owner or not owner, and unlocks only for "owner".

> [!question]- Check (slide 5): True or false: "Every pattern recognition algorithm copies the way a living system recognises patterns." Give a reason.
> False. Nature influences many algorithms, but the slide says we also use many techniques that are purely numerical and have no counterpart in natural systems.
> Example from this lecture: a threshold on fish length, or the sign of $g(\mathbf{x})=\mathbf{w}^{\mathsf T}\mathbf{x}+w_0$. These are arithmetic rules, not models of a brain.

> [!question]- Check (slide 6): In the slide's table, take the row "Industrial monitoring". Name the pattern and the classes. Then propose one feature. Is the feature the same thing as the pattern?
> Pattern: the motor vibration signal (one recording). Classes: normal operation or mechanical fault.
> A possible feature: the amplitude of the vibration at one frequency, or the mean power of the signal.
> No. The pattern is the whole object that comes in (the signal). A feature is one number measured on it.

## 2. Applications, part 1 (slides 7-13)
![[ee525_lecture_01_lecture_1_introduction_p007.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p008.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p009.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p010.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p011.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p012.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p013.jpg]]

**Notes.** Every application has the same three parts: pattern, features, classes.

| Application | Pattern | Classes |
|---|---|---|
| Machine vision | camera image of a part on a conveyor | defect or non-defect; or "screwdriver", "German key" |
| Character recognition (OCR) | segmented character image | letters, digits, punctuation |
| Computer-aided diagnosis | X-ray, CT, ultrasound, ECG, EEG | for example benign or malignant |
| Speech recognition | recorded speech | words, typed out as ASCII text |
| Data mining, retrieval | image, music extract, DNA sequence | "similar" items; healthy or diseased tissue |

- OCR front end: light source, scan lens, document transport, detector. Then line and character segmentation, then recognition.
- Mammography (slide 9): 10 to 30% of women with breast cancer have a negative mammogram. In about two thirds of those cases the cancer was visible in hindsight. The machine gives a second opinion; the final diagnosis is the doctor's.
- Speech: entering text by talking is about twice as fast as a skilled typist.
- Keyword search needs manual annotation, which does not scale. Content-based retrieval compares a measured "signature" instead (colour, texture, shape for images; meter, tempo, repeated patterns for music).
- DNA is a sequence over four letters A, C, G, T.

> [!question]- Check (slide 7): The slide describes two uses of machine vision: inspection and the assembly line. For each one give the classes and the action that follows the decision. What does "classes known a priori" mean?
> Inspection: two classes, defect and non-defect. Action: reject the faulty part.
> Assembly line: several classes, one per object type (screwdriver class, German key class, ...). Action: a robot arm moves the object to the right place.
> "Known a priori" means the list of classes is fixed before the system runs. The system only chooses one class from this list. It does not invent new classes.

> [!question]- Check (slide 8): Why must line and character segmentation come before the recognition step? What goes wrong if the classifier gets the image of the whole page?
> The classes are single characters: letter, number, punctuation. A classifier puts one pattern into one class.
> A whole page holds thousands of characters, so it does not belong to any of these classes. Segmentation cuts the page into lines and then into single characters, so that each pattern is one character.
> The figure shows the same idea: first text detection (find where the text is), then text recognition (read "STOP").

> [!question]- Check (slide 9): 2000 women with breast cancer have a mammogram. Assume 20% of them get a negative result (the slide says 10 to 30%). How many cancers are missed? In about how many of these cases was the cancer visible in hindsight? Why does this number support a "second opinion" system?
> Missed: $0.20\cdot2000=400$ women. (With 10% and 30% the range is $200$ to $600$.)
> Visible in hindsight: about two thirds, $\tfrac23\cdot400\approx267$ cases.
> These 267 were not hidden. The radiologist failed to see them (poor image quality, eye fatigue, subtle findings). A second reading improves the percentage of correct classifications, so a machine that gives a second opinion can catch a part of them. The final diagnosis stays with the doctor.

> [!question]- Check (slide 10): A skilled typist types 60 words per minute. Use the slide's claim to find the time for a 900-word text by typing and by talking. In the speech system, what is the pattern and what is the output?
> Typing: $900/60=15$ minutes.
> Talking is twice as fast, so $2\cdot60=120$ words per minute: $900/120=7.5$ minutes.
> Pattern: the spoken sounds recorded by the microphone. Output: the recognised text as ASCII characters, shown on the screen and stored in memory.

> [!question]- Check (slide 11): A database holds 5 million images. A person needs 30 seconds to write keywords for one image. How long does the manual annotation take, in working days of 8 hours? What does the result say about keyword search?
> Total time: $5\,000\,000\cdot30=150\,000\,000$ s.
> In hours: $150\,000\,000/3600\approx41\,667$ h.
> In working days: $41\,667/8\approx5208$ days. With 260 working days per year this is about $5208/260\approx20$ years for one person.
> Keyword search needs this manual annotation first. It is possible for a small database and impossible for a very large one. This is the reason for content-based retrieval.

> [!question]- Check (slide 12): In a music content-based retrieval system, what is presented to the system, what is measured, and what does "similar" mean? Compare with keyword search in one sentence.
> Presented: an example, a short extract of a music piece, through a microphone.
> Measured automatically: cues such as the music meter, the tempo and the location of repeated patterns. Together they are the "signature" (a feature vector).
> "Similar" means that the measured signatures are close to each other.
> Keyword search compares words written by a human. Content-based retrieval compares numbers measured on the content itself.

> [!question]- Check (slide 13): A DNA sequence uses the four letters A, C, G, T. How many different sequences of length 3 exist? Of length 10? Why does this make automatic comparison tools necessary?
> Each position has 4 choices, so a sequence of length $n$ has $4^n$ possibilities.
> Length 3: $4^3=4\cdot4\cdot4=64$.
> Length 10: $4^{10}=1\,048\,576$.
> Genes have hundreds of nucleotides, so the number of possible sequences is far too large to check by hand. Pattern recognition tools do the similarity search, for example between healthy and diseased tissue.

## 3. Applications, part 2 (slides 14-18)
![[ee525_lecture_01_lecture_1_introduction_p014.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p015.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p016.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p017.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p018.jpg]]

**Notes.** More examples: fingerprint identification, signature authentication, text retrieval, face and gesture recognition. MPEG-7 allows queries such as "find all video scenes showing person X laughing". Pattern recognition works together with linguistics, computer graphics, machine vision and database design.

> [!info] What the four figures show (beyond the slides)
> Slides 14 to 17 are almost only figures. This is what each one says in the words of this lecture.
> - **Slide 14, fingerprints.** Eight fingerprint types: plain arch, tented arch, ulnar loop, radial loop, plain whorl, central pocket loop, double loop whorl, accidental whorl. Each type is a **class**. One fingerprint image is a **pattern**.
> - **Slide 15, signature.** The signature is written on a tablet. The box lists what is measured at each point of the pen path: position $x,y$, time stamp, pressure, pen inclination, curvature, acceleration. These are the raw measurements for the **features**. The numbers 1, 2, 3 and the red arrows show the order and direction of the strokes. Classes: genuine or forged.
> - **Slide 16, text retrieval.** (a) Text-to-video: the query is a sentence, the answer is a list of videos. (b) Video-to-text: the query is a video, the answer is a list of sentences. In both cases the list is **ranked**: the most similar item comes first.
> - **Slide 17, face and gesture.** (a) The system first finds the face (red box) and the hand (green box), then names the hand shape: "HOME". (b) The 11 hand shapes the system knows. Each one is a class.

> [!warning] Watch out
> Slide 16 has the heading "Text retrieval", but its figure shows something more specific: retrieval between text and video. In (a) a sentence is the query and videos are returned. In (b) a video is the query and sentences are returned. Plain text retrieval (a text query that returns text documents) is not shown on the slide.

> [!question]- Check (slide 14): Read the figure. How many classes does it show? Is "Tented Arch" a pattern, a feature or a class? What is the pattern here?
> The figure shows 8 classes: plain arch, tented arch, ulnar loop, radial loop, plain whorl, central pocket loop, double loop whorl, accidental whorl.
> "Tented Arch" is a class: it is the name of a category.
> The pattern is one fingerprint image. A classifier puts each new image into exactly one of the 8 classes.

> [!question]- Check (slide 15): The box lists six kinds of measurement. Which of them can you still get from a signature that was scanned from paper? Why are the other ones useful for authentication?
> From a paper scan you only have the shape: the positions $x,y$ and the curvature (curvature is computed from the shape).
> Time stamp, pressure, pen inclination and acceleration need a tablet that records the pen while the person writes.
> A forger can copy the shape of a signature by looking at it. The speed, pressure and pen angle of the real writer are not visible on paper, so they are much harder to copy. They help to separate the two classes, genuine and forged.

> [!question]- Check (slide 16): In part (b) the query is a video and the system returns three sentences in a numbered order. What does the order mean? What is the difference between part (a) and part (b)?
> The order is a ranking by similarity. Sentence 1 ("A woman is laying in bed.") is the one the system finds most similar to the video, so it is the answer (red box). Sentences 2 and 3 are less similar.
> (a) is text-to-video: the query is a sentence and the ranked list holds videos.
> (b) is video-to-text: the query is a video and the ranked list holds sentences. The same similarity idea works in both directions.

> [!question]- Check (slide 17): Count the gesture classes in part (b). In part (a), which two steps does the system do before it can write "HOME" on the screen?
> Part (b) shows 11 classes: Open, Close, Home, Back Home, Up, Down, Roll Right, Roll Left, Back, Wrist Up, Wrist Down.
> Step 1, detection: find where the face and the hand are in the image (the red and the green box). This isolates the pattern from the background.
> Step 2, classification: assign the hand region to one of the 11 gesture classes. Here the result is "Home".

> [!question]- Check (slide 18): The MPEG-7 query is "find all video scenes showing person X laughing". Which two recognition decisions are hidden in this query? Why does the slide say that pattern recognition alone is not enough?
> Decision 1: who is in the scene? Classes: person X or another person (face recognition).
> Decision 2: what is the person doing? Classes: laughing or not laughing (expression recognition).
> The system must also store and search a large digital library, and show the result. This needs other disciplines: database design, machine vision, computer graphics, linguistics.

## 4. Features, feature vectors and classifiers (slides 19-22)
![[ee525_lecture_01_lecture_1_introduction_p019.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p020.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p021.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p022.jpg]]

**Notes.** Toy medical task: an image region is a benign lesion (class A) or a malignant one (class B). For each image of a labelled database measure the mean intensity $\mu$ and the standard deviation $\sigma$ of the region, and plot one point per image (Figure 1.2). The two classes fall in different areas and a straight line separates them.

For pixel values $z_1,\dots,z_N$:

$$\mu=\frac1N\sum_{k=1}^{N}z_k,\qquad \sigma=\sqrt{\frac1N\sum_{k=1}^{N}(z_k-\mu)^2}$$

- **Features**: the measurements used for classification. With $l$ features the **feature vector** is $\mathbf{x}=[x_1,x_2,\dots,x_l]^{\mathsf T}\in\mathbb{R}^l$. The space $\mathbb{R}^l$ is the **feature space**.
- Features are treated as random variables (measurement noise, natural differences between patterns), so the points of one class scatter.
- **Classifier**: divides feature space into regions, one per class. Here it is the **decision line**. A wrong decision is a **misclassification**.
- **Training patterns**: feature vectors whose true class is known and that are used to design the classifier.

Linear classifier, with weight vector $\mathbf{w}$ and offset $w_0$:

$$g(\mathbf{x})=\mathbf{w}^{\mathsf T}\mathbf{x}+w_0=w_1x_1+\dots+w_lx_l+w_0$$

Decide by the sign of $g(\mathbf{x})$. $\mathbf{w}$ is perpendicular to the line and points to the side where $g>0$. If $g(\mathbf{x})=0$ the point is on the boundary and the rule gives no class.

> [!example] Region to features
> Pixels $\{1,3,3,5\}$: $\mu=12/4=3$. Squared deviations $4,0,0,4$, sum $8$, so $\sigma^2=8/4=2$ and $\sigma=\sqrt2\approx1.41$. Feature vector $\mathbf{x}=[3,\ 1.41]^{\mathsf T}$.

> [!info] Beyond the slides: nearest-mean classifier
> With class means $\mathbf{m}_A$, $\mathbf{m}_B$: $\mathbf{w}=\mathbf{m}_B-\mathbf{m}_A$, midpoint $\mathbf{p}=\tfrac12(\mathbf{m}_A+\mathbf{m}_B)$, $w_0=-\mathbf{w}^{\mathsf T}\mathbf{p}$. The boundary is the perpendicular bisector of the two means, and B is on the $g>0$ side.

> [!warning] Watch out
> - Slide 21 says the scatter of the points is "shown in Figure 1.1". The scatter plot is Figure 1.2. Figure 1.1 shows the two example images.
> - Always check which class the problem puts on the $g>0$ side.
> - Some books divide by $N-1$ for $\sigma$. Our notes divide by $N$.

> [!question]- Check (slide 19): Look at the two regions of Figure 1.1. Which region has the larger mean intensity $\mu$? Which one has the larger standard deviation $\sigma$? Say what you see in the image that tells you.
> Region (a), class A, is light grey almost everywhere. Bright pixels mean a large $\mu$. Its pixels differ only a little from each other, so $\sigma$ is small.
> Region (b), class B, mixes very dark and very bright pixels. It is darker on average, so $\mu$ is smaller. The pixel values are far from their mean, so $\sigma$ is large.
> This agrees with Figure 1.2 on the next slide: class A (o) sits at high $\mu$ and low $\sigma$, class B (+) at lower $\mu$ and higher $\sigma$.

> [!question]- Check (slide 20): Two regions have the pixels $R_1=\{2,2,10,10\}$ and $R_2=\{5,6,6,7\}$. Compute $\mu$ and $\sigma$ for both (divide by $N$). What does the result say about using $\mu$ alone as the feature?
> $R_1$: $\mu=(2+2+10+10)/4=24/4=6$. Deviations $-4,-4,4,4$. Squares $16,16,16,16$, sum $64$. $\sigma^2=64/4=16$, $\sigma=4$.
> $R_2$: $\mu=(5+6+6+7)/4=24/4=6$. Deviations $-1,0,0,1$. Squares $1,0,0,1$, sum $2$. $\sigma^2=2/4=0.5$, $\sigma=\sqrt{0.5}\approx0.71$.
> Both regions have $\mu=6$, so $\mu$ alone cannot separate them. With $\sigma$ they are far apart: $4$ against $0.71$. In the plot of Figure 1.2 they are two points at the same height but at different positions on the $\sigma$ axis. This is why the example uses two features.

> [!question]- Check (slide 21): A team measures four numbers on each region: mean, standard deviation, area and perimeter. What is $l$ and in which space does $\mathbf{x}$ live? The same lesion is imaged twice and gives $\mu=3.0$ the first time and $\mu=3.2$ the second time. Is something broken?
> Four features, so $l=4$ and $\mathbf{x}=[x_1,x_2,x_3,x_4]^{\mathsf T}\in\mathbb{R}^4$.
> Nothing is broken. Features are treated as random variables. The slide gives two sources of the random variation: the measurement noise of the device, and the differences between patterns (for example the physiology of different patients in X-ray imaging).
> Because of this variation the points of one class form a cloud in feature space, not a single point.

> [!question]- Check (slide 22): Use the axes of Figure 1.2: $\sigma$ horizontal, $\mu$ vertical. The decision line is $g=\mu-\sigma-2=0$, class A if $g>0$, class B if $g<0$. Training patterns $(\sigma,\mu)$: class A $(1,5)$, $(2,6)$, $(3,4.5)$ and class B $(4,3)$, $(5,4)$. How many misclassifications does the line make? Why are these five points called training patterns?
> Class A must give $g>0$:
> $(1,5)$: $g=5-1-2=2>0$, correct.
> $(2,6)$: $g=6-2-2=2>0$, correct.
> $(3,4.5)$: $g=4.5-3-2=-0.5<0$, the line says B. This is a misclassification.
> Class B must give $g<0$:
> $(4,3)$: $g=3-4-2=-3<0$, correct.
> $(5,4)$: $g=4-5-2=-3<0$, correct.
> Result: 1 misclassification out of 5, an error rate of $1/5=20\%$ on these patterns.
> They are training patterns because their true class is known and they are used to design (place) the line. We could count the error only because we know the labels.

## 5. The four design stages (slides 23-25)
![[ee525_lecture_01_lecture_1_introduction_p023.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p024.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p025.jpg]]

**Notes.** The toy example hides four questions. They are the stages of Figure 1.3:

1. **Feature generation**: how are features produced? Problem dependent.
2. **Feature selection**: what is the best number $l$ of features? Generate more candidates than needed, keep the best.
3. **Classifier design**: place the boundary optimally with respect to a criterion, not "to please the eye". In general the boundary is nonlinear.
4. **System evaluation**: what is the classification error rate?

The stages are a loop. If the error rate is too high, go back and redesign an earlier stage (the feedback arrows).

> [!question]- Check (slide 23): A team invents 10 candidate features for a task and then keeps the best 3. Which stage is the first step and which stage is the second? How many different groups of 3 features can be chosen from 10? In the toy example we knew at once that $\mu$ and $\sigma$ work. Why is this not typical?
> Inventing the 10 candidates is feature generation. Keeping the best 3 is feature selection.
> Number of groups: $\binom{10}{3}=\dfrac{10\cdot9\cdot8}{3\cdot2\cdot1}=\dfrac{720}{6}=120$. So even a small selection problem has many options to compare.
> In the toy example we knew how the images had been generated, so we knew which quantities differ between the classes. For real data nobody tells us this. The slide says it is "far from obvious" and problem dependent.

> [!question]- Check (slide 24): Find the two mistakes: "The line in Figure 1.2 separates the two classes, so a line drawn by eye is a good classifier design. And for most real problems a straight line is enough."
> Mistake 1: a line drawn "to please the eye" is not a design. The slide says the line must be placed optimally with respect to an optimality criterion (for example the fewest errors). Many lines separate the training points, and a criterion is needed to choose one.
> Mistake 2: problems where a linear classifier (line, or hyperplane in $l$ dimensions) works well are not the rule. In general the surfaces between the class regions are nonlinear. Then two more questions appear: which type of nonlinearity, and which criterion.

> [!question]- Check (slide 25): A finished classifier is tested on 200 patterns and gets 26 wrong. What is the classification error rate? The customer accepts at most 5%. According to Figure 1.3, what happens next, and to which boxes do the feedback arrows lead?
> Error rate: $26/200=0.13=13\%$.
> $13\%>5\%$, so the design is not finished. The stages are not independent: we go back and redesign an earlier stage, then evaluate again.
> In the figure the feedback line starts at system evaluation and has arrows to all four earlier boxes: classifier design, feature selection, feature generation and the sensor. So the fix can be another classifier, other features, or even better measurements.

## 6. Fish sorting: one feature and a threshold (slides 26-30)
![[ee525_lecture_01_lecture_1_introduction_p026.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p027.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p028.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p029.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p030.jpg]]

**Notes.** Task: sort fish on a conveyor belt into sea bass and salmon from a camera image. Steps: capture image, isolate fish, take measurements, make decision. Sensing problems: lighting, fish position, camera noise.

Rule with one feature: sea bass if length $> l^\ast$, else salmon. The length histograms overlap a lot, so no threshold is perfect. Lightness (sea bass brighter, salmon darker) overlaps less, but still not perfectly.

With the rule "sea bass if $x>t$":

$$\text{training errors}=(\text{number of salmon}>t)+(\text{number of sea bass}\le t)$$

To choose $t$: try the halfway points between neighbouring data values, count both errors, pick the smallest total.

> [!example] Best threshold
> Salmon $2,3,5$; sea bass $4,6,7$. Errors for $t=2.5,3.5,4.5,5.5,6.5$: $2,1,2,1,2$. Best: $t=3.5$ or $t=5.5$, 1 error out of 6 (16.7%). The data alone cannot break the tie.

> [!info] How to read the two histograms (beyond the slides)
> The horizontal axis is the feature value, cut into bins. The height of a bar is the number of training fish of one species whose value falls into that bin. Black is salmon, red is sea bass. The dashed vertical line is the threshold.
> With the rule "sea bass if value $>$ threshold", the errors are easy to see: every black bar to the right of the dashed line (salmon called sea bass) and every red bar to the left of it (sea bass called salmon).
> - Slide 29, length, $l^\ast\approx11$: salmon appear at almost every length, from about 2 up to about 28. Sea bass appear from about 3 up to about 25. The two histograms overlap over almost the whole axis.
> - Slide 30, lightness, $x^\ast\approx5.5$: salmon lie between about 0.5 and 7, sea bass between about 3.5 and 10. They overlap only in the middle, between about 3.5 and 7. Outside this band a fish is classified without doubt.
> - **Rule**: a feature is better when the overlap of the two histograms is smaller. Only fish inside the overlap can be misclassified.

> [!question]- Check (slide 26): The slide assumes only two kinds of fish. Suppose a third species, trout, also arrives on the belt. What changes in the problem? Is one threshold on one feature still enough?
> The number of classes becomes 3: sea bass, salmon, trout.
> One threshold cuts the feature axis into 2 regions, so it can only name 2 classes. For 3 classes we need at least 3 regions: two thresholds on one feature, or a division of a feature space with more features.
> The rest of the method stays the same: camera image, features, decision.

> [!question]- Check (slide 27): The lamp above the belt becomes weaker during the day. Which feature is damaged more, length or lightness? At which of the slide's four steps does the problem enter, and where can it be repaired?
> Lightness is damaged more. A weaker lamp makes every fish look darker, so the measured lightness of all fish goes down and a bright sea bass can fall below the threshold. The length of the fish in the image stays almost the same.
> The problem enters at "capture image" (sensing: lighting conditions are on the slide's list).
> Repair: keep the lighting constant, or correct the image brightness before "take measurements" (a pre-processing step, see section 9).

> [!question]- Check (slide 28): The fisherman says a sea bass is "generally" longer than a salmon. Write the decision rule. Then suppose the two species had exactly the same length distribution. What could the best threshold achieve?
> Rule: sea bass if length $>l^\ast$, else salmon.
> "Generally" means on average, not for every fish. Some salmon are long and some sea bass are short, so the rule makes errors.
> With identical length distributions, on each side of any threshold the two species appear in the same proportion as on the whole belt. The length tells nothing about the species. The rule would be no better than a guess. A feature is useful only if its values differ between the classes.

> [!question]- Check (slide 29): In the length histogram, the threshold $l^\ast$ is near 11 and the rule is "sea bass if length $>l^\ast$". Which bars are errors? If you move the threshold from 11 to 16, which kind of error becomes rarer and which becomes more frequent? Can any position give zero errors?
> Errors: the black (salmon) bars to the right of the line, and the red (sea bass) bars to the left of the line.
> Threshold 11 to 16: fewer salmon are longer than the threshold, so "salmon called sea bass" becomes rarer. More sea bass are shorter than the threshold, so "sea bass called salmon" becomes more frequent.
> No position gives zero errors. Black (salmon) bars cover almost the whole length axis, also beyond the last red bar, and red (sea bass) bars start far to the left of the line, near length 3. So every threshold has salmon on its right side or sea bass on its left side, and for most positions both. This is why the slide tries another feature.

> [!question]- Check (slide 30): Lightness values of training fish: salmon $1,2,3,4,7$ and sea bass $5,6,8,9,10$. Rule: sea bass if lightness $>t$. Count the errors for $t=3.5,\ 4.5,\ 5.5,\ 7.5$. Which threshold is best? Why can no threshold be perfect here?
> Errors $=(\text{salmon}>t)+(\text{sea bass}\le t)$.
> $t=3.5$: salmon above are $4,7$ (2). Sea bass below: none (0). Total $2$.
> $t=4.5$: salmon above is $7$ (1). Sea bass below: none (0). Total $1$.
> $t=5.5$: salmon above is $7$ (1). Sea bass below is $5$ (1). Total $2$.
> $t=7.5$: salmon above: none (0). Sea bass below are $5,6$ (2). Total $2$.
> Best: $t=4.5$ with 1 error out of 10, a training error rate of $10\%$.
> Not perfect: the salmon with lightness $7$ is brighter than the sea bass with lightness $5$ and $6$. To call this salmon correctly we need $t\ge7$, and then the sea bass $5$ and $6$ are wrong. This is the slide's sentence: easier to choose $x^\ast$, but still no perfect decision.

## 7. Cost of error (slide 31)
![[ee525_lecture_01_lecture_1_introduction_p031.jpg]]

**Notes.** Salmon buyers object strongly to sea bass in their cans. Sea bass buyers do not mind some salmon. So the two errors have different costs. Let $c_1$ be the cost of a salmon called sea bass and $c_2$ the cost of a sea bass called salmon:

$$\text{total cost}=c_1\cdot(\text{number of salmon}>t)+c_2\cdot(\text{number of sea bass}\le t)$$

Pick the $t$ with the smallest total cost. The threshold moves so that the expensive mistake becomes rarer.

> [!example] The tie is broken
> Same data, $c_1=1$, $c_2=5$. $t=3.5$: cost $1\cdot1+5\cdot0=1$. $t=5.5$: cost $1\cdot0+5\cdot1=5$. Choose $t=3.5$.

This is the first glimpse of minimum-risk classification in [[EE525 L02 - Bayesian classifiers]].

> [!question]- Check (slide 31): The rule is "sea bass if lightness $>x^\ast$". Which of the two errors is the expensive one on the slide, and in which direction must $x^\ast$ move to make it rarer? Then compare two thresholds with $c_1=1$ (salmon called sea bass) and $c_2=4$ (sea bass called salmon). Threshold P: 4 salmon above, 6 sea bass below. Threshold Q: 10 salmon above, 1 sea bass below. Which one has fewer errors, and which one has the lower cost?
> Expensive error: sea bass in a salmon can. Salmon customers object strongly. This is a sea bass called salmon, so a sea bass with lightness $\le x^\ast$.
> To make it rarer, fewer sea bass must lie below the threshold: move $x^\ast$ down (to the left, towards the salmon side). The price is more salmon in sea bass cans, which customers accept.
> Number of errors: P has $4+6=10$, Q has $10+1=11$. P has fewer errors.
> Cost: P costs $c_1\cdot4+c_2\cdot6=1\cdot4+4\cdot6=4+24=28$. Q costs $1\cdot10+4\cdot1=10+4=14$.
> Q is the better choice: one more error, but half the cost. The smallest number of errors and the smallest cost are not the same goal.

## 8. Two features, complexity and generalisation (slides 32-36)
![[ee525_lecture_01_lecture_1_introduction_p032.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p033.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p034.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p035.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p036.jpg]]

**Notes.** With lightness $x_1$ and width $x_2$ each fish is a point $\mathbf{x}=[x_1,x_2]^{\mathsf T}$ in a 2-D feature space.

- A straight boundary is simple but makes some errors.
- A very complex boundary separates all training samples but misclassifies the new sample "?". It is tuned too closely to the training data: **over-tuning** (today: overfitting).
- A moderately curved boundary is slightly worse on training data and better on new fish.

**Generalisation** is the performance on new patterns that were not used for training. Judge a model by its test error, not by its training error.

Adding features does not always help (slide 36). Before adding one, ask: is it reliable, is it correlated with a feature I already have, what does it cost, how noisy is it?

> [!info] Beyond the slides: curse of dimensionality
> With $b$ bins per feature, $l$ features give $b^l$ cells, so $N$ training patterns give $N/b^l$ patterns per cell on average. Example: $b=5$, $N=500$ gives $100$, $20$, $0.8$ patterns per cell for $l=1,2,4$.

> [!warning] Watch out
> A large gap between test and training error signals overfitting. A small gap does not prove the model is good: a too simple model can have a small gap and a high test error.

> [!info] An answer to the trade-off question of slide 35 (beyond the slides)
> Slide 35 asks how to manage the trade-off between the complexity of the decision rule and its performance on unknown samples. The slide gives no answer.
> The usual answer: do not use all labelled fish for training. Keep a part aside (test data). Train boundaries of growing complexity on the training part, then measure each one on the part kept aside.
> - Training error: goes down when the boundary gets more complex, and can reach 0.
> - Test error: goes down at first (the model becomes able to follow the real shape of the classes), then goes up again (the model starts to follow single noisy fish).
> - **Choice**: take the complexity where the test error is lowest. This is the moderately curved boundary of slide 35.

> [!question]- Check (slide 32): A fish has width 18 and lightness 6.5. A classmate writes its feature vector as $[18,\ 6.5]^{\mathsf T}$. What is wrong? What is the dimension of the feature space, and what changes if we also measure the length?
> The slide defines $x_1$ as lightness and $x_2$ as width. The order is fixed, so the correct vector is $\mathbf{x}=[6.5,\ 18]^{\mathsf T}$. The classmate's vector describes a different fish (lightness 18, width 6.5).
> Two features, so the feature space is two-dimensional ($\mathbb{R}^2$) and each fish is one point in the plane.
> With length as a third feature, $\mathbf{x}=[x_1,x_2,x_3]^{\mathsf T}\in\mathbb{R}^3$ and each fish is a point in 3-D space.

> [!question]- Check (slide 33): The straight boundary in the figure passes roughly through the points (lightness 4, width 21) and (lightness 7, width 15). Write it as $g(\mathbf{x})=0$ with sea bass on the $g>0$ side. Classify the fish $[5,20]^{\mathsf T}$ and $[6,16]^{\mathsf T}$. Compare with the lightness-only rule "sea bass if $x_1>5.5$".
> Slope: $\dfrac{15-21}{7-4}=\dfrac{-6}{3}=-2$. Line: $x_2-21=-2(x_1-4)$, so $x_2=-2x_1+29$, or $2x_1+x_2-29=0$.
> Take $g(\mathbf{x})=2x_1+x_2-29$. Sea bass are on the right (large lightness), where $g>0$. Check of the two points: $2\cdot4+21-29=0$ and $2\cdot7+15-29=0$.
> Fish $[5,20]^{\mathsf T}$: $g=10+20-29=1>0$, sea bass.
> Fish $[6,16]^{\mathsf T}$: $g=12+16-29=-1<0$, salmon.
> Lightness only: $5<5.5$ gives salmon, $6>5.5$ gives sea bass. Both answers are reversed.
> The tilted line uses the width too: a wide fish is called sea bass even when it is a little dark, and a narrow fish is called salmon even when it is a little bright. A vertical line (lightness only) cannot do this.

> [!question]- Check (slide 34): Find the mistake: "Boundary B makes 0 errors on the training fish and boundary A makes 5%. So B will also make fewer errors on the fish of tomorrow."
> The training error says how well the boundary fits the fish it was drawn from. It does not say how it behaves on new fish.
> A complex enough curve can always go around every single training point and reach 0 errors. To do this it follows accidental details of this sample, such as one sea bass that lies among the salmon.
> A new fish does not repeat these accidents. On the slide the new sample "?" falls into such a twisted part of the boundary and gets the wrong class. This is over-tuning (overfitting): good on training data, poor generalisation.

> [!question]- Check (slide 35): You make the boundary step by step more complex: straight line, gentle curve, very wiggly curve. Describe how the training error changes and how the error on new fish changes. Where is the model of slide 35 on this path?
> Training error: it goes down at every step, and reaches 0 for the very wiggly curve.
> Error on new fish: it goes down from the straight line to the gentle curve, because the gentle curve follows the real shape of the two clouds. Then it goes up again for the wiggly curve, because that curve follows single training fish.
> The model of slide 35 is the gentle curve in the middle: slightly more training errors than the complex model, but the best performance on novel patterns. This is the trade-off between complexity and generalisation.

> [!question]- Check (slide 36): Cut each feature axis into 4 bins and ask for 20 training fish per cell on average. How many training fish do you need for $l=2$ features? For $l=5$? Which question of the slide does this answer?
> The slide only asks the question. The counting with bins is from the info box above (beyond the slides).
> Cells: $b^l=4^l$. Needed fish: $N=20\cdot4^l$.
> $l=2$: $4^2=16$ cells, $N=20\cdot16=320$ fish.
> $l=5$: $4^5=4\cdot4\cdot4\cdot4\cdot4=1024$ cells, $N=20\cdot1024=20\,480$ fish.
> Three more features need $20\,480/320=64$ times more data ($64=4^3$).
> This answers the last question of the slide: yes, there is a curse in very high dimensions. The data needed to fill the feature space grows exponentially with the number of features. So adding features does not always improve the result when the training set is fixed.

## 9. Pattern recognition systems (slides 37-39)
![[ee525_lecture_01_lecture_1_introduction_p037.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p038.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p039.jpg]]

**Notes.** The pipeline from sensor to decision:

1. **Data acquisition and sensing**: measure physical variables. Issues: bandwidth, resolution, sensitivity, distortion, SNR (signal-to-noise ratio), latency.
2. **Pre-processing**: remove noise, isolate the pattern from the background.
3. **Feature extraction**: a new representation in terms of features.
4. **Model learning and estimation**: learn a mapping between features and categories.
5. **Classification**: assign a pattern to a category.
6. **Post-processing**: evaluate confidence, use context, combine experts (several classifiers).

Feature generation and selection happen at design time. Feature extraction happens for every new pattern.

**How to read the figure of slide 37.** The figure has two columns.

- Right column, **training** (done once, at design time): training data, pre-processing, feature extraction and selection, features, model learning and estimation. Its result is the **model**.
- Left column, **operation** (done for every new pattern): physical environment, data acquisition and sensing, pre-processing, feature extraction, features, classification, post-processing, decision.
- The only link between the columns is the arrow from "Model" to "Classification". The classifier uses the model that was learned on the right.

Both columns use the same pre-processing and the same features. A model learned on one kind of feature cannot classify another kind.

> [!warning] Watch out
> The numbered list above follows the order of the bullet points on slides 38 and 39. It is not one single chain. In the figure of slide 37, "Model learning and estimation" (step 4) is not between feature extraction and classification. It is in the separate training column and runs at design time. For a new pattern the path is 1, 2, 3, 5, 6. Step 4 only supplies the model that step 5 uses.

> [!info] The sensing keywords of slide 38 (beyond the slides)
> The slide only lists the words. Short meanings:
> - **Bandwidth**: the range of frequencies the sensor can follow. A fast-changing signal needs a large bandwidth.
> - **Resolution**: the smallest detail the sensor separates, for example the number of pixels of a camera or the number of bits of a converter.
> - **Sensitivity**: how much the output changes for a small change of the measured quantity.
> - **Distortion**: a systematic change of the signal shape caused by the sensor, for example a lens that bends straight lines.
> - **SNR** (signal-to-noise ratio): signal power divided by noise power, $\text{SNR}=P_s/P_n$, often in decibels, $10\log_{10}(P_s/P_n)$. Example: $P_s/P_n=100$ gives $10\log_{10}100=10\cdot2=20$ dB.
> - **Latency**: the delay between the event and the moment the measurement is available.
> - **Why it matters**: a weak sensor cannot be fully repaired later. Information that was not measured is not in the features.

> [!question]- Check (slide 37): Look at the two columns of the figure. Which column runs for every new fish on the belt, and which one runs only at design time? Which single object goes from one column to the other? Why do both columns contain pre-processing and feature extraction?
> The left column (physical environment to decision) runs for every new fish. The right column (training data to model learning) runs at design time.
> The model is the only thing that crosses: it is the output of "Model learning/estimation" and an input of "Classification".
> The model is a rule that was learned on features of the training data. It only makes sense for a new pattern if this pattern is described in the same way. So a new pattern must go through the same pre-processing and the same feature extraction.

> [!question]- Check (slide 38): Put each item in the right stage (sensing, pre-processing or feature extraction): (a) the camera has $640\times480$ pixels, (b) a filter removes the speckle noise from the image, (c) the fish is cut out from the belt background, (d) the length of the fish is measured in the cleaned image.
> (a) Data acquisition and sensing. The number of pixels is the resolution of the sensor.
> (b) Pre-processing: removal of noise in the data.
> (c) Pre-processing: isolation of the pattern of interest from the background.
> (d) Feature extraction: the image is replaced by a new representation in terms of features, here one number.

> [!question]- Check (slide 39): "Combination of experts". Three classifiers label five fish (S = salmon, B = sea bass). Truth: S B B S B. Expert 1: S B B B B. Expert 2: S S B S B. Expert 3: B B B S B. Find the error rate of each expert and of the majority vote. Why does the vote help here?
> The slide only says "combination of experts". The majority vote (each fish gets the class that most experts chose) is one simple way to combine them (beyond the slides).
> Expert 1 is wrong on fish 4: 1 error out of 5, $20\%$.
> Expert 2 is wrong on fish 2: 1 error out of 5, $20\%$.
> Expert 3 is wrong on fish 1: 1 error out of 5, $20\%$.
> Majority vote, fish by fish: fish 1: S, S, B gives S. Fish 2: B, S, B gives B. Fish 3: B, B, B gives B. Fish 4: B, S, S gives S. Fish 5: B, B, B gives B.
> Vote: S B B S B. This equals the truth: 0 errors, $0\%$.
> The vote helps because the experts make their mistakes on different fish, so each wrong answer is outvoted by two correct ones. If all three were wrong on the same fish, the vote would not help.
> This is a post-processing step: it works on the decisions, after classification.

## 10. The design cycle (slides 40-44)
![[ee525_lecture_01_lecture_1_introduction_p040.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p041.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p042.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p043.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p044.jpg]]

**Notes.**

| Step | Questions and choices on the slides |
|---|---|
| Data collection | training and test data; is the set large and representative enough? |
| Feature selection | domain knowledge, computational cost; discriminative, invariant, robust features |
| Model selection | design criteria, parametric or non-parametric, missing features, complexity; templates, statistical, syntactic, neural, hybrid |
| Training | supervised, unsupervised or reinforcement |
| Evaluation | performance on future data; overfitting and generalisation |

A good feature is **discriminative** (similar values inside a class, different values between classes), **invariant** (unchanged by translation, rotation, scale) and **robust** (insensitive to occlusion, distortion, deformation, environment).

> [!info] The model-selection keywords of slide 42 (beyond the slides)
> The slide only lists the words. Short meanings:
> - **Design criteria**: what "best" means for this task, for example the fewest errors or the lowest cost (section 7).
> - **Parametric model**: you assume a fixed form with a few numbers, and you learn only these numbers. Example: "the lightness of each class is Gaussian", parameters mean and variance per class.
> - **Non-parametric model**: no fixed form. The model is built from the data itself, for example a histogram per class, or "give the class of the nearest training pattern".
> - **Missing features**: what to do when a measurement of a pattern is not available (the tail of the fish is hidden).
> - **Computational complexity**: time and memory needed to train the model and to classify one pattern.
> - **Types of models**: *template* (compare the pattern with a stored example of each class), *decision-theoretic or statistical* (features are random variables, classes are described by probabilities; this is the approach of this course), *syntactic or structural* (a pattern is built from simple parts with rules, like a sentence from words), *neural* (a network of simple units learned from data), *hybrid* (a mix).
> - **True model**: the last question of the slide, "how close are we to the true model", has no direct answer because the true model is unknown. In practice the error on test data is used as the measure.

> [!question]- Check (slide 40): You have 500 labelled fish, all caught in July. You use 80% for training and the rest for testing. How many fish are in each set? The machine will work the whole year. Which word of the slide is in danger, and why?
> Training: $0.80\cdot500=400$ fish. Testing: $500-400=100$ fish.
> The word in danger is "representative". Fish caught in one month may differ from winter fish in size and colour. A training set must look like the data the system will meet later.
> A large set does not repair this. 400 July fish say nothing about January. And the test set has the same problem here, so the test error would also look better than the real error.

> [!question]- Check (slide 41): A camera looks down on the belt. Consider the features (a) the area of the fish in pixels and (b) the area divided by the length squared. The camera is moved higher, so every length in the image is multiplied by $s=0.5$. A fish had length 200 pixels and area 8000 pixels. Compute both features before and after. Which invariances from the slide does each feature have?
> A scale factor $s$ multiplies lengths by $s$ and areas by $s^2$.
> After the move: length $0.5\cdot200=100$ pixels, area $0.5^2\cdot8000=0.25\cdot8000=2000$ pixels.
> (a) Area: $8000$ before, $2000$ after. It changed, so it is not scale invariant.
> (b) Area / length$^2$: before $8000/200^2=8000/40\,000=0.2$. After $2000/100^2=2000/10\,000=0.2$. Unchanged, so it is scale invariant ($s^2$ cancels).
> Both features are invariant to translation and rotation: moving or turning the fish on the belt does not change its area or its length.

> [!question]- Check (slide 42): Model 1 assumes that the lightness of each species is Gaussian and estimates a mean and a variance for each of the two species. Model 2 stores a lightness histogram with 20 bins for each species. Which one is parametric? How many numbers does each model learn? Name one advantage of each.
> The slide only names the two kinds of model. The Gaussian and the histogram are examples (beyond the slides).
> Model 1 is parametric: the form (Gaussian) is fixed in advance and only its parameters are learned. Numbers: $2$ per species, $2\cdot2=4$ in total.
> Model 2 is non-parametric: no form is assumed, the data draw the shape. Numbers: $20$ bar heights per species, $2\cdot20=40$ in total.
> Model 1 needs few training fish and little memory, but it is wrong if the true distribution is not Gaussian (for example if it has two peaks).
> Model 2 can follow any shape, but it needs many more training fish to fill 40 bins reliably.

> [!question]- Check (slide 43): Which kind of learning is it? (a) A fisherman labels 200 fish, and the threshold is chosen from them. (b) The machine sorts fish with no labels, and after each fish a worker presses a button only when the can was wrong. (c) A net brings up fish of an unknown number of species, and the system groups them by their measurements.
> Ask each time what the teacher gives.
> (a) The category label of every training pattern: supervised learning.
> (b) No category, only the feedback right or wrong after each decision: reinforcement learning.
> (c) Nothing. The system forms clusters, the natural groupings of the patterns: unsupervised learning.

> [!question]- Check (slide 44): A classifier makes 0 errors on its 300 training fish and 18 errors on 150 new fish. Compute the two error rates. Which one answers the slide's second question (performance with future data)? Why is the other one too optimistic?
> Training error: $0/300=0\%$.
> Test error: $18/150=0.12=12\%$.
> The test error predicts the performance on future data, because these fish were not used to design the classifier, like the fish of tomorrow.
> The training error is too optimistic because the boundary was adjusted to exactly these 300 fish. It can fit their accidental details, which new fish do not repeat. The gap of 12 points is a sign of overfitting.

## 11. Supervised and unsupervised learning, summary (slides 45-48)
![[ee525_lecture_01_lecture_1_introduction_p045.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p046.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p047.jpg]]
![[ee525_lecture_01_lecture_1_introduction_p048.jpg]]

**Notes.**

| Kind | The teacher gives | Output |
|---|---|---|
| supervised | the class (or a cost) of each training pattern | a classifier |
| unsupervised (clustering) | nothing; number of classes unknown in general | groups, named later |
| reinforcement | only "right" or "wrong" after each decision | a decision rule |

Remote sensing example: a scanner measures the reflected energy of each ground cell in $l$ wavelength bands. The feature vector of a cell holds its pixel intensities in the $l$ bands. A clustering algorithm groups the vectors, and the analyst names each cluster with reference ground data (Figure 1.4).

> [!warning] Watch out
> - Slide 45 has "semi-supervised" in its title but says nothing about it. (Beyond the slides: training on a few labelled patterns plus many unlabelled ones.)
> - Slide 45 promises "two such problems". Only one, remote sensing, follows.

> [!question]- Check (slide 45): Take Figure 1.2 and erase the labels, so that every "o" and every "+" becomes the same dot. Which kind of problem is it now? What can an algorithm still find, and what can it not find?
> It is now unsupervised pattern recognition (clustering): only feature vectors, no known class labels.
> It can still find the structure: the dots form two separate groups, so it can put "similar" vectors together into two clusters. It must also find out by itself that there are two groups, because the number of classes is in general unknown.
> It cannot find the meaning of the groups. Nothing in the dots says "benign" or "malignant". A person with extra information must name the clusters afterwards.

> [!question]- Check (slide 46): A satellite image covers $1000\times1000$ ground cells and the scanner has $l=6$ wavelength bands. How many feature vectors are there, in which space do they live, and how many numbers are stored in total? Why does the scanner use several bands and not one?
> One feature vector per cell: $1000\cdot1000=1\,000\,000$ vectors.
> Each vector has one pixel intensity per band, so $\mathbf{x}=[x_1,\dots,x_6]^{\mathsf T}\in\mathbb{R}^6$.
> Total: $6\cdot1\,000\,000=6\,000\,000$ numbers.
> Each band measures a different property of the same patch of ground. In the visible and infrared range the reflection depends on the mineral and moisture content of the soil, the sedimentation of water and the moisture of the vegetation. At the thermal end it depends on the thermal properties of the surface. Two cover types that look equal in one band can differ in another band.

> [!question]- Check (slide 47): Read Figure 1.4. How many regions does the map (a) have, and how many clusters does the plot (b) have? Why are the two numbers different? What is $l$ in this figure, and who gives the clusters their names?
> Map (a): 6 regions (soil, forest, water, vegetation, forest, soil). Plot (b): 4 clusters (water, soil, vegetation, forest).
> The clustering works in feature space, not on the map. A cell is placed by its band intensities, not by its position on the ground. The two forest regions reflect in the same way, so their cells fall into the same cluster. The same holds for the two soil regions.
> The plot has the axes $x_1$ and $x_2$: two bands, so $l=2$.
> The algorithm only finds the groups. The analyst names them, by comparing a sample of points of each cluster with reference ground data (maps or visits).

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Pattern | object described by measurable characteristics |
| Feature vector | $\mathbf{x}=[x_1,\dots,x_l]^{\mathsf T}\in\mathbb{R}^l$ |
| Region features | $\mu=\frac1N\sum z_k$, $\sigma=\sqrt{\frac1N\sum (z_k-\mu)^2}$ |
| Linear classifier | $g(\mathbf{x})=\mathbf{w}^{\mathsf T}\mathbf{x}+w_0$, decide by the sign; $\mathbf{w}$ points to $g>0$ |
| Nearest mean (beyond slides) | $\mathbf{w}=\mathbf{m}_B-\mathbf{m}_A$, $w_0=-\mathbf{w}^{\mathsf T}\mathbf{p}$, $\mathbf{p}$ the midpoint |
| Training pattern | labelled feature vector used to design the classifier |
| Threshold errors | $(\text{salmon}>t)+(\text{sea bass}\le t)$ |
| Threshold cost | $c_1(\text{salmon}>t)+c_2(\text{sea bass}\le t)$ |
| Design stages | feature generation, feature selection, classifier design, system evaluation |
| Pipeline | sensing, pre-processing, feature extraction, model learning, classification, post-processing |
| Generalisation | performance on patterns not used for training |
| Overfitting (over-tuning) | fits the training data so closely that it generalises badly |

## Check yourself
> [!question]- 1. Regions $R_1=\{2,4,4,4,5,5,7,9\}$ and $R_2=\{10,10,10,10\}$: find $[\mu,\sigma]^{\mathsf T}$ for each (divide by $N$). Which one is textured?
> $R_1$: sum $40$, $\mu=40/8=5$. Squared deviations $9,1,1,1,0,0,4,16$, sum $32$, $\sigma^2=4$, $\sigma=2$. So $[5,2]^{\mathsf T}$.
> $R_2$: $\mu=10$, $\sigma=0$. So $[10,0]^{\mathsf T}$.
> $R_1$ is textured ($\sigma>0$), $R_2$ is flat.

> [!question]- 2. $g(\mathbf{x})=x_1+x_2-10$, sea bass if $g>0$, salmon if $g<0$. Classify $[4,5]^{\mathsf T}$, $[7,4]^{\mathsf T}$, $[5,5]^{\mathsf T}$.
> $g=4+5-10=-1$: salmon. $g=7+4-10=1$: sea bass. $g=5+5-10=0$: on the decision line, the rule gives no class (use a convention or reject).

> [!question]- 3. Nearest mean with $\mathbf{m}_A=[2,3]^{\mathsf T}$, $\mathbf{m}_B=[6,7]^{\mathsf T}$. Find $g$ and classify $[3,5]^{\mathsf T}$.
> $\mathbf{w}=[4,4]^{\mathsf T}$, $\mathbf{p}=[4,5]^{\mathsf T}$, $\mathbf{w}^{\mathsf T}\mathbf{p}=36$, so $g(\mathbf{x})=4x_1+4x_2-36$.
> $g([3,5]^{\mathsf T})=12+20-36=-4<0$: class A. Check: squared distances are $5$ to $\mathbf{m}_A$ and $13$ to $\mathbf{m}_B$.

> [!question]- 4. Salmon lengths $8,9,10,11,12,13$; sea bass $11,12,14,15,16,17$. Rule: sea bass if length $>t$. Which of $t=9.5,10.5,11.5,12.5,13.5$ has the fewest errors?
> Errors: $4,3,3,3,2$. Best is $t=13.5$: 2 errors out of 12, training error rate 16.7%. The wrong fish are the sea bass of length 11 and 12.

> [!question]- 5. Same data. A sea bass called salmon costs 5, a salmon called sea bass costs 1. Which of $t=7.5,9.5,10.5,11.5,13.5$ is best now?
> Costs: $6,4,3,7,10$. Best is $t=10.5$ with cost 3. The threshold moved down from 13.5: more fish are called sea bass, so the expensive mistake does not happen.

> [!question]- 6. Training / test errors: linear 10% / 12%, moderately curved 5% / 7%, very complex 0% / 18%. Which model do you deploy, which one overfits?
> Deploy the moderately curved model (lowest test error, 7%). The very complex one overfits: 0% on training, 18% on new fish, a gap of 18 points against 2 for the others.

## Links
- Course index: [[EE525 Home]]
- Next lecture: [[EE525 L02 - Bayesian classifiers]]
- Full step-by-step notes (PDF): [EE525_Lecture_01_Introduction.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/ee_525/lecture_notes/lecture_01/EE525_Lecture_01_Introduction.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lecture_notes/lecture_01/EE525_Lecture_01_Introduction.pdf`
- Original slides (PDF): [Lecture 1- Introduction.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_01/Lecture%201-%20Introduction.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/ee_525/lectures/lecture_01/Lecture 1- Introduction.pdf`

Slides not embedded: 1 (title).
