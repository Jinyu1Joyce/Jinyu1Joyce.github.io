---
layout: post
title: Methodology
subtitle: Risk
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/thumb.png
share-img: /assets/img/path.jpg
---

This study aims to identify advertisements related to financial services that could potentially lead to monetary losses for targeted social media users. We first analyze textual ad content using a multilingual zero-shot classification model. The methodology is designed to detect specific themes related to financial deception, enabling a systematic labeling of ad data based on semantic content. Our primary objective is to first semantically categorize each advertisement based on predefined fraud-related themes and then analyse the demographic distribution of fraudulent ads. 

For the purpose of this study, we have used data from the META ads database, which contains advertisements, including text content, demographic details of users who were exposed to the advertisement, delivery start dates, and advertiser names. We have narrowed down the scope of our study to France in 2024 October. 

Data Collection and Preprocessing

Using API, a large amount of data was retrieved from the META Ad Library. From this, we filtered out the data for advertisements related to loans in France in the month of October 2024. To filter these ads, we used words such as 'loan', 'prêt', 'crédit', 'financement', 'microcrédit', 'emprunt' as the word embeddings in the 'ad_creative_bodies'. We then use a zero-shot classification to determine the probability of “loan” and filter out the ones with a probability of less than 0.5. A zero-shot classification is a machine learning technique where a model can classify data into categories without having seen any labeled examples of those categories during training. This model uses natural language inference (NLI) or semantic similarity to assess whether a given input text implies a particular label or category. For this study, we have used the MoritzLaurer/mDeBERTa-v3-base-mnli-xnli model, which is a transformer-based architecture fine-tuned for NLI across multiple languages. 

The data used for conducting a distribution analysis in our final stage to determine the exposure of different age groups to harmful loan data is also determined by the model, where in, the dummy variables for ads related to financial losses and the variable for determining if the advertisement is related to loans is positive. 

To accommodate for memory constraints, the data is processed incrementally in fixed-size chunks of 100 rows. This facilitates checkpointing and resumption of execution, thus ensuring fault tolerance and reducing memory overhead. 

Classification of loan ads

We have used a list of 11 fraud-related thematic labels that was manually curated in French.
These labels were selected to reflect common patterns in financial fraud and deceptive advertising. The classification process is designed such that each advertisement's textual body passes through a zero-shot classification pipeline with the labels mentioned above. The pipeline outputs a probability of 0 or 1 for each label, indicating the degree to which the text is semantically aligned with that concept. The label with the highest score is recorded as the primary category. To ensure safeguards against missing or malformed data, the model assigns zero scores to all labels where advertisement text is empty or the classification process fails. Once again, to enhance scalability and fault tolerance, processed rows are saved to disk after every 300 entries as separate chunk files. This strategy facilitates resumable execution, reduces memory usage, and mitigates the impact of interruptions during long processing sessions. 

The second stage of this study focuses on analyzing the distribution and targeting of potentially harmful advertisements across demographic groups, with an emphasis on age and gender. For the data analysis part, we first group advertisements that convey highly similar or semantically equivalent content. We achieve this by using textual similarity measures to cluster ads that share core messaging—particularly those related to financial services or loan offers. By clustering these ads, we aim to identify whether content labeled as leading to financial loss is more frequently delivered to specific user profiles. In particular, we investigate the distribution of the labels across different age groups. This enables us to trace how the algorithm potentially reuses or adapts fraudulent messages across user segments. Such patterns may suggest algorithmic targeting behaviors that disproportionately affect certain demographic profiles.

Our dataset includes aggregated exposure data broken down by age and gender categories for each ad. So, to accurately interpret the exposure of different demographic groups to these potentially harmful ads, we calculate the overall demographic distribution of ad impressions across all ads. This includes the proportion of views from each age–gender group, such as males aged 18–24, females aged 45–54, etc. However, we normalize the demographic data because the number of users in each demographic group would vary and a direct comparison of exposure levels would be misleading. 

To evaluate potential gender- and age-based biases in the targeting of harmful loan advertisements, we calculate a normalized exposure ratio that adjusts for general exposure to loan-related content across demographic groups. Specifically, for each demographic group g (e.g., females aged 18–24), we define:

D_loss[g] as the average percentage representation of group g in loss-inducing loan ads (i.e., ads where is_loss = 1),

D_loan[g] as the average percentage representation of group g in all loan ads (i.e., where is_loan = 1).

The normalized exposure for group g is then given by:

NormalizedExposure_g = D_loss[g] / D_loan[g]

This ratio measures the relative concentration of harmful content within each demographic group’s exposure to loan advertising. A value of NormalizedExposure_g > 1 indicates that group g is overrepresented in harmful loan ads compared to their baseline exposure—suggesting a disproportionate targeting. In contrast, a value below 1 signals underrepresentation, implying that the group is less likely to be exposed to risky or deceptive financial advertisements relative to their general presence in loan ad audiences. This normalization enables a bias-aware assessment of risk distribution, highlighting potential disparities in exposure to financial harm across demographic lines. In simple terms, it allows us to account for disparities in group sizes and ensures that comparisons of exposure are not biased by the natural prevalence of certain user segments. 

While this methodology is robust, it has several limitations. In this methodology, the fraud detection relies entirely on the semantic alignment of ad text with predefined labels, while  contextual or multimedia cues are not taken into consideration. So, language ambiguity and informal phrasing in ad texts may reduce classification accuracy, particularly for underrepresented dialects or idiomatic expressions. It is important to note that zero-shot models are not trained directly on the classification task at hand. Instead, they rely on general-purpose reasoning to approximate categorization. Thus, the accuracy of this model will be less compared to models fine-tuned on labeled examples specific to the domain (such as financial fraud detection). Moreover, even if the model is multilingual, its performance can vary widely between languages. In addition, the model may not be optimal across labels and lead to false positives and false negatives due to the absence of a fixed probability threshold.
