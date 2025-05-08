---
layout: post
title: Methodology
subtitle: Detecting Demographic Bias in Harmful Loan Ads Using Multilingual Zero-Shot NLP
cover-img: /assets/img/3.2.jpg
thumbnail-img: /assets/img/3.1.jpg
share-img: /assets/img/3.2.jpg
---

This study aims to identify loan advertisements that could potentially lead to monetary losses for targeted social media users. We first analyze textual ad content using a multilingual zero-shot classification model. The methodology is designed to detect specific themes related to financial deception, enabling a systematic labeling of ad data based on semantic content. Our primary objective is to first semantically categorize each advertisement based on predefined fraud-related themes and then analyse the demographic distribution of harmful and fraudulent ads. 

For the purpose of this study, we have used data from the Meta ads database, which contains advertisements, including text content, demographic details of users who were exposed to the advertisement, delivery start dates, and advertiser names. We have narrowed down the scope of our study to France in 2024 October. 


**4.1 Data Collection and Preprocessing**

Using API, a large amount of data was retrieved from the Meta Ad Library. From this, we filtered out the data for advertisements related to loans in France in the month of October 2024. To filter these ads, we used words such as 'loan', 'prêt', 'crédit', 'financement', 'microcrédit', 'emprunt' as the word embeddings in the 'ad_creative_bodies'. We then use a zero-shot classification to determine the probability of “loan” and filter out the ones with a probability of less than 0.6.

For this study, we have used two different models–one to detect harmful loans ads using loss labels and the other to detect if the loan ads are fraudulent. Both the models use zero-shot classification, which operates on the textual content of advertisements and utilize pre-trained NLI models from the Hugging Face transformers library. A zero-shot classification is a machine learning technique where a model can classify data into categories without having seen any labeled examples of those categories during training. This model uses natural language inference (NLI) or semantic similarity to assess whether a given input text implies a particular label or category. 

In the first model, we have used a multilingual zero-shot classification approach using the mDeBERTa-v3-base-mnli-xnli model to identify harmful loan ads based on loss-related risk categories in French. We first defined a set of loss-related risk categories in French, which reflect common patterns in deceptive or high-risk loan advertisements. These include: prêt instantané (instant loan),  promesses trompeuses d'argent rapide (misleading promises of fast money), taux annuel effectif global élevé (high APR), fausses subventions gouvernementales (fake government grants), arnaque au paiement anticipé (advance fee fraud), etc. The model evaluates each ad text against these categories in multi-label mode, allowing multiple risk indicators to be scored simultaneously. The model then returns a probability score for each of the predefined loss labels. If the score exceeded a threshold of 0.6, the ad was flagged as loss-related (is_loss = 1) and the corresponding loss_label was recorded. This approach allowed for flexible classification against custom labels without the need for task-specific fine-tuning.

In the second model, we have used a transformer-based zero-shot classification model, namely facebook/bart-large-mnli model, to classify the loan advertisements into “fraud” and “non-fraud” categories. The model assessed each advertisement’s body text and provided the score associated with the "fraud" label. If fraud score was more than 0.5, the ad was flagged as potentially fraudulent (fraud = 1). Otherwise, it was labeled as non-fraudulent (fraud = 0). 

To accommodate for memory constraints, the data is processed incrementally in fixed-size chunks of 100 rows. This facilitates checkpointing and resumption of execution, thus ensuring fault tolerance and reducing memory overhead. 

**4.2 Classification of loan ads**

We have used a list of 10 fraud-related thematic labels that was manually curated in French.
These labels were selected to reflect common patterns in financial fraud and deceptive advertising. The classification process is designed such that each advertisement's textual body passes through a zero-shot classification pipeline with the labels mentioned above. The pipeline outputs a probability of 0 or 1 for each label, indicating the degree to which the text is semantically aligned with that concept. The label with the highest score is recorded as the primary category. Once again, to enhance scalability and fault tolerance, processed rows are saved to disk after every 100 entries as separate chunk files. This strategy facilitates resumable execution, reduces memory usage, and mitigates the impact of interruptions during long processing sessions. 

**4.3 Data Analysis**

For the data analysis part, we first identify viewers by demographic group of loan-related ads on Meta. We do so by clustering these ads to identify whether content labeled as leading to financial loss is more frequently delivered to specific user profiles. In particular, we investigate the distribution of the labels across different age groups. We then classify the ads as "fraud" or "non-fraud" using the facebook/bart-large-mnli model. Then, we apply the mDeBERTa-v3-base-mnli-xnli model to fine-tune the fraud-related labels. For each ad, we calculate the normalized exposure ratio per demographic group to compare how often each group saw harmful ads versus how often they saw loan ads generally. The core idea behind this is that if a group saw 10% of all harmful loan ads, but only 5% of all loan ads overall, they’re twice as exposed to harmful content compared to their general ad exposure (normalized ratio = 2.0). A normalized exposure of >1 indicates overexposure. 

We start the analysis with the unaggregated data to detect possible patterns of targeting (such as the advertiser intent). Then, by aggregating, we detect algorithmic amplification—bias introduced by how the platform chooses to distribute ads once published. So, in the unaggregated phase, we analyse the ads individually to identify the demographic groups that are often overrepresented. Then, in the aggregated phase, we group ads with identical content to isolate the algorithm’s effect from advertiser intent. A change in the overexposure patterns imply systemic bias in the algorithm. Thus, by comparing results across both models and aggregation levels, we cross validate our findings.

To evaluate potential gender- and age-based biases in the targeting of harmful and fraudulent loan advertisements, we calculate a normalized exposure ratio that adjusts for general exposure to loan-related content across demographic groups. Specifically, for each demographic group X (e.g., females aged 18–24), we define:

- Normalized exposure = (Group’s ad exposure %) ÷ (Group’s population share)
- Normalized exposure > 1 → Overexposed
- Normalized exposure < 1 → Underexposed

This ratio measures the relative concentration of harmful content within each demographic group’s exposure to loan advertising. A value of Normalised Exposure > 1 indicates that group X is overrepresented in harmful loan ads compared to their baseline exposure—suggesting a disproportionate targeting. In contrast, a value below 1 signals underrepresentation, implying that the group is less likely to be exposed to risky or deceptive financial advertisements relative to their general presence in loan ad audiences. This normalization enables a bias-aware assessment of risk distribution, highlighting potential disparities in exposure to financial harm across demographic lines. In simple terms, it allows us to account for disparities in group sizes and ensures that comparisons of exposure are not biased by the natural prevalence of certain user segments. 

To assess the performance of both the models in labelling loss-related loan advertisement and detecting fraudulent ads, we constructed two balanced evaluation datasets for human annotation. These datasets were used for comparison between the models' automated predictions and human judgments, thereby facilitating error analysis and validation of model reliability. The results for the performance of both models are mentioned in section 5 on Qualitative Analysis. 

While this methodology is robust, it has several limitations. In this methodology, the fraud detection relies entirely on the semantic alignment of ad text with predefined labels, while  contextual or multimedia cues are not taken into consideration. So, language ambiguity and informal phrasing in ad texts may reduce classification accuracy, particularly for underrepresented dialects or idiomatic expressions. It is important to note that zero-shot models are not trained directly on the classification task at hand. Instead, they rely on general-purpose reasoning to approximate categorization. Thus, the accuracy of this model will be less compared to models fine-tuned on labeled examples specific to the domain (such as financial fraud detection). Moreover, even if the model is multilingual, its performance can vary widely between languages. In addition, the model may not be optimal across labels and lead to false positives and false negatives due to the absence of a fixed probability threshold.
