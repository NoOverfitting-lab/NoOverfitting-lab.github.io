---
layout: distill
title: Towards Interpretability of BERT Models Detecting Depression from Text
description: We  have conducted research on whether BERT models are able to successfully learn depression specific language markers and symptoms from text. 
date: 2021-08-01  11:00:00 -0600
published: true

authors:
  - name: Ksenia Shkaruta
    affiliations:
      name: No Overfitting Lab


bibliography: 2021-07-23-bigbench.bib

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

## Related Work

After exploring various evaluation techniques we have picked the CheckList from (Ribeiro et al., 2020), a behavioral testing framework that follows software engineering “black box” concept. Main idea behind this evaluation methodology is to create tests that are model-, data- and task-independent and instead focus on testing certain model capabilities. CheckLists have three types of tests: Minimum Functionality tests (MFT) that are similar to unit testing, Invariance (INV) tests akin to metamorphic tests as they are focused on the relationship between input and output, and Directional Expectation (DIR) tests measure the change in the direction of prediction of a model.

We were interested to test whether BERT-based models are able to learn depression specific language markers from text. For example, many studies show that increased usage of first-person pronouns can be a reliable indicator of the onset of depression as a person becomes self-focused in their speech (Bucci and Freedman, 1981; Rude et al., 2004; Zimmermann et al., 2013). Thus, we decided to create tests following the CheckList methodology to analyze this particular language marker. 

Further, (Smirnova et al., 2018) claim that cognitive depression symptoms are known to be the most expressed through language. Other researchers show that certain depression-specific somatic symptoms, such as sleep deprivation, fatigue or loss of energy, significantly affect language production (Harrison and Horne, 1998). Patient Health Questionnaire (PHQ-9), a self-administered test that is routinely used for depression severity assessment, evaluates the four cognitive symptoms, four somatic symptoms, and suicidal ideation (Kroenke et al., 2001; Kroenke and Spitzer, 2002; Arroll et al., 2010). For this reason, we used PHQ-9 to build behavioral tests that analyze BERT models’ ability to detect somatic and cognitive depression symptoms from text.

## Methodology

To create all our behavioral tests we used the dataset of tweets for depression detection collected by (Shen et al., 2017). Note, that behavioral tests could also be created from scratch.

We created four MFT and two INV tests to assess whether a model learns the link between personal pronouns usage and depression. For MFT we took the subset of data with the label “non-depressed” and replaced all “I” pronouns with “She”, “He”, “They”. We considered a test to fail when a model predicted these tests as “depressed” class. We did exactly the opposite by replacing “She”, “He”, “They” with “I” and establishing a failure criteria when the model predicts this test as “non-depressed”. For the two INV tests we took data from both classes and swapped “He” with “She” and the other way around. We considered the model to fail if there was a change in the predicted class.

For DIR tests we created two tests for eight out of PHQ-9 symptoms, one test for presence of a symptom in text and another for absence of such a symptom. For example, for the depression symptom "lack of energy" we added sentences “I feel tired all the time” to “depressed” class to indicate presence of a symptom and “I feel rested and full of energy” to “non-depressed” class to show its absence. We then measure the change in direction of prediction with the failure criteria that the change can only be towards the class label.


## Data, Models and Experiments

We tested three sets of classifiers fine-tuned from three different, pre-trained BERT variants: BERT, RoBERTa, and ALBERT, downloaded from Huggingface. 

We trained these models on a balanced dataset of 23,454 tweets, TWSELFDIAG,  that we created from (Shen et al., 2017), a self-reported collection of tweets from 2009 to 2019 where users were labeled as depressed if they tweeted “(I’m/ I was/ I am/ I’ve 281 been) diagnosed depression”. We used three additional datasets to test models in Out-Of-Distribution (OOD) settings. Two of them being Twitter-based with self-reporting and others-reporting class labeling. Another is the dataset DAIC-WoZ, clinical interviews from the Distress Analysis Interviews Corpus (Gratch et al., 2014).

We were interested if standard performance metrics were fully representative of the capabilities and limitations of BERT-based models in recognizing signs of depression from text. As such, we first performed In-Distribution (ID, same distribution as training data) classification experiments by training each of three models on the training subset of TWSELFDIAG and testing them on the test subset of the same TWSELFDIAG dataset. We selected the best performing model for BERT, RoBERTa and ALBERT based on the standard evaluation metrics of Accuracy, AUC and Brier score, for use in further experiments. Next, we wanted to see the generalizability of these models by testing them on OOD, the three datasets mentioned above. Finally, we assessed models performance on our depression behavioral tests and compared the results with standard performance metrics and OOD results. 
                                         

## Results 

The results show that the best performing RoBERTa is able to achieve the highest classification performance based on accuracy,  Brier and AUC scores and ALBERT is the worst model (Tab.1).  This follows the order of the behavioral testing performance across MFT and INV tests (Tab. 3) and thus suggests that valuation metrics may be good in assessing capability of models in terms of its basic functionality (e.g. detecting most common and most generic depression-based language patterns), as well as models’ robustness against simple perturbations. 


| Model             |    Acc    |   Brier   |     AUC     |
| ----------------- | --------- | --------- | ----------- |
| BERT              |    0.752  |    0.248  |    0.752    |
| ALBERT            |    0.714  |    0.286  |    0.709    |
| RoBERTa           | **0.787** | **0.213** |  **0.787**  |

Failure rates of all three models on the DIR tests are substantially higher than those on the INV and MFT tests indicating that the models are not capable of detecting these specifics in text with sufficient accuracy. Across DIR tests the somatic symptoms prove to be easier to detect. Suicidal ideation is the most difficult symptom of depression for the models to detect.

| Tested on       | Model             |    Acc       |   Brier      |      AUC         |
| ----------------| ----------------- | ------------ | ------------ | ---------------- |
|      **TWHash**         | BERT              |    **0.5660**  |    **0.4340**  |     **0.5770**      |
| **TWHash**   | ALBERT            |    0.5221  |    0.4779  |     0.5225      |
|    **TWHash**           | RoBERTa           | 0.5369 | 0.4631 |   0.5349    |
| **TWPhmDEPR**             | BERT              |    **0.5604**  |    **0.4396**  |     **0.5604**      |
| **TWPhmDEPR** | ALBERT            |    0.5421  |    0.4579  |     0.5421     |
| **TWPhmDEPR**             | RoBERTa           | 0.5421 | 0.4579 |   0.5421    |
| **DAIC-WoZ**             | BERT              |    0.4671  |    0.5329  |     0.4994      |
| **DAIC-WoZ**   | ALBERT            |    0.5062  |    0.4938  |     0.4983      |
| **DAIC-WoZ**             | RoBERTa           | **0.5689** | **0.4311** |   **0.5301**    |

                                     
If we order the models according to their behavior on the DIR tests that are based on the presence of depression symptoms, BERT is the best performer with RoBERTa coming next and ALBERT being the worst (Tab. 3). Interestingly, model performance in OOD settings of small/medium distance follows this same ordering, with BERT achieving the greatest performance and ALBERT achieving  the worst (Tab. 2).

![alt text](https://github.com/NoOverfitting-lab/NoOverfitting-lab.github.io/blob/ks_draft/assets/img/BERTdepression_Table3.PNG?raw=true?width="60%")



| Type | Test Description               |      BERT         |      RoBERTa      |      ALBERT       |
| -----| ------------------------------ | ----------------- | ----------------- | ----------------- |
| INV  | swap personal pronouns         |    1.82 / 2.23    |    1.82 / 2.06    |    2.85 / 2.06    |
| MFT  | 1st person <-> 3rd person            |    0.82 / 0.81  |    0.52 / 0.19  |     0.95 / 0.35      |
| DIR  | presence of somatic symptoms       | 21.38 / 1.62 | 31.96 / 2.23 |   0.5349    |
| **TWPhmDEPR**             | BERT              |    **0.5604**  |    **0.4396**  |     **0.5604**      |
| **TWPhmDEPR** | ALBERT            |    0.5421  |    0.4579  |     0.5421     |
| **TWPhmDEPR**             | RoBERTa           | 0.5421 | 0.4579 |   0.5421    |
| **DAIC-WoZ**             | BERT              |    0.4671  |    0.5329  |     0.4994      |
| **DAIC-WoZ**   | ALBERT            |    0.5062  |    0.4938  |     0.4983      |
| **DAIC-WoZ**             | RoBERTa           | **0.5689** | **0.4311** |   **0.5301**    |
