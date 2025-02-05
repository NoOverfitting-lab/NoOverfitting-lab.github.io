---
layout: distill
title: What Checklist Know about Depression Detection Models?
description: We  have conducted research on whether BERT models are able to successfully learn depression specific language markers and symptoms from text. 
date: 2021-08-01  11:00:00 -0600
published: true

authors:
  - name: Ksenia Shkaruta
    affiliations:
      name: No Overfitting Lab


bibliography: 2021-08-01-BERTdepression.bib

# Below is an example of injecting additional post-specific styles.
# If you use this post as a template, delete this _styles block.
_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }

---

## Motivation

With the coronavirus pandemic setting off the worst mental health crisis of the 21st century we recognized the importance of having models that can accurately classify depression from text. 

Naturally, our attention turned to the pre-trained transformers that have been revolutionizing the NLP industry, specifically BERT-based models. However, there have been observations about the lack of generalizability and inconsistencies in performance results. Thus, this motivated us to explore in greater detail these models' performance on depressive text. 

<img src="/assets/img/BERTdepression_pic1.PNG" width="80%" />

(Image credit: https://media-cldnry.s-nbcnews.com)

## Related Work

After exploring various evaluation techniques we have picked the CheckList from <d-cite key="ribeiro-etal-2020-beyond"></d-cite>, a behavioral testing framework that follows software engineering “black box” concept. Main idea behind this evaluation methodology is to create tests that are model-, data- and task-independent and instead focus on testing certain model capabilities. CheckLists have three types of tests: Minimum Functionality tests (MFT) that are similar to unit testing, Invariance (INV) tests akin to metamorphic tests as they are focused on the relationship between input and output, and Directional Expectation (DIR) tests measure the change in the direction of prediction of a model.


<img src="/assets/img/BERTdepression_pic2.PNG" width="50%" />

(Image credit: https://www.shutterstock.com)

We were interested to test whether BERT-based models are able to learn depression specific language markers from text. For example, many studies show that increased usage of first-person pronouns can be a reliable indicator of the onset of depression as a person becomes self-focused in their speech <d-cite key="bucci1981language"></d-cite> <d-cite key="rude2004language"></d-cite> <d-cite key="zimmermann2013way"></d-cite>. Thus, we decided to create tests following the CheckList methodology to analyze this particular language marker. 

<img src="/assets/img/BERTdepression_pic3.PNG" width="60%" />

(Image credit: https://www.shutterstock.com)

Further, <d-cite key="smirnova2018language"></d-cite> claim that cognitive depression symptoms are known to be the most expressed through language. Other researchers show that certain depression-specific somatic symptoms, such as sleep deprivation, fatigue or loss of energy, significantly affect language production <d-cite key="harrison1998sleep"></d-cite>. Patient Health Questionnaire (PHQ-9), a self-administered test that is routinely used for depression severity assessment, evaluates the four cognitive symptoms, four somatic symptoms, and suicidal ideation <d-cite key="kroenke2001phq"></d-cite> <d-cite key="kroenke2002phq"></d-cite> <d-cite key="arroll2010validation"></d-cite>. For this reason, we used PHQ-9 to build behavioral tests that analyze BERT models’ ability to detect somatic and cognitive depression symptoms from text.

<img src="/assets/img/BERTdepression_pic4.PNG" width="80%" />

(Image credit: https://www.shutterstock.com)

## Methodology

To create all our behavioral tests we used the dataset of tweets for depression detection collected by <d-cite key="shen2017depression"></d-cite>. Note, that behavioral tests could also be created from scratch.

We created four MFT and two INV tests to assess whether a model learns the link between personal pronouns usage and depression. For MFT we took the subset of data with the label “non-depressed” and replaced all “I” pronouns with “She”, “He”, “They”. We considered a test to fail when a model predicted these tests as “depressed” class. We did exactly the opposite by replacing “She”, “He”, “They” with “I” and establishing a failure criteria when the model predicts this test as “non-depressed”. For the two INV tests we took data from both classes and swapped “He” with “She” and the other way around. We considered the model to fail if there was a change in the predicted class.

For DIR tests we created two tests for eight out of PHQ-9 symptoms, one test for presence of a symptom in text and another for absence of such a symptom. For example, for the depression symptom "lack of energy" we added sentences “I feel tired all the time” to “depressed” class to indicate presence of a symptom and “I feel rested and full of energy” to “non-depressed” class to show its absence. We then measure the change in direction of prediction with the failure criteria that the change can only be towards the class label.


## Data, Models and Experiments

We tested three sets of classifiers fine-tuned from three different, pre-trained BERT variants: BERT, RoBERTa, and ALBERT, downloaded from Huggingface. 

We trained these models on a balanced dataset of 23,454 tweets, TWSELFDIAG,  that we created from <d-cite key="shen2017depression"></d-cite>, a self-reported collection of tweets from 2009 to 2019 where users were labeled as depressed if they tweeted “(I’m/ I was/ I am/ I’ve been) diagnosed depression”. We used three additional datasets to test models in Out-Of-Distribution (OOD) settings. Two of them being Twitter-based with self-reporting and others-reporting class labeling. Another is the dataset DAIC-WoZ, clinical interviews from the Distress Analysis Interviews Corpus <d-cite key="gratch2014distress"></d-cite>.

We were interested if standard performance metrics were fully representative of the capabilities and limitations of BERT-based models in recognizing signs of depression from text. As such, we first performed In-Distribution (ID, same distribution as training data) classification experiments by training each of three models on the training subset of TWSELFDIAG and testing them on the test subset of the same TWSELFDIAG dataset. We selected the best performing model for BERT, RoBERTa and ALBERT based on the standard evaluation metrics of Accuracy, AUC and Brier score, for use in further experiments. Next, we wanted to see the generalizability of these models by testing them on OOD, the three datasets mentioned above. Finally, we assessed models performance on our depression behavioral tests and compared the results with standard performance metrics and OOD results. 
                                         

## Results 

The results show that the best performing RoBERTa is able to achieve the highest classification performance based on accuracy,  Brier and AUC scores and ALBERT is the worst model (Tab.1).  This follows the order of the behavioral testing performance across MFT and INV tests (Tab. 3) and thus suggests that valuation metrics may be good in assessing capability of models in terms of its basic functionality (e.g. detecting most common and most generic depression-based language patterns), as well as models’ robustness against simple perturbations. 

<img src="/assets/img/BERTdepression_Table1_v2.PNG" width="40%" />

Failure rates of all three models on the DIR tests are substantially higher than those on the INV and MFT tests indicating that the models are not capable of detecting these specifics in text with sufficient accuracy. Across DIR tests the somatic symptoms prove to be easier to detect. Suicidal ideation is the most difficult symptom of depression for the models to detect.

<img src="/assets/img/BERTdepression_Table2_v2.PNG" width="40%" />
                                    
If we order the models according to their behavior on the DIR tests that are based on the presence of depression symptoms, BERT is the best performer with RoBERTa coming next and ALBERT being the worst (Tab. 3). Interestingly, model performance in OOD settings of small/medium distance follows this same ordering, with BERT achieving the greatest performance and ALBERT achieving  the worst (Tab. 2).

<img src="/assets/img/BERTdepression_Table3_v3.PNG" width="40%" />

## Takeaways

RoBERTA’s higher performance suggests that  longer training and incorporating more context into the training process can potentially improve model’ capability to recognize the absence of depressive symptoms. A potential way to improve model performance on the DIR tests of type a could be adding symptom-related information to training data before fine-tuning models. However, these results and thus suggestions could only be considered as a preliminary investigation and motivation for future research.

