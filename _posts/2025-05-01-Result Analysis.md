---
layout: post
title: Result Analysis
subtitle: There's lots to learn!
comments: true
mathjax: true
---
## 4.	Results
### 4.1 User Demographics for All Loan Ads
To set the stage for our analysis, we first characterize who is seeing loan ads on Meta. As shown in Figure 1, among 269 loan ads we filtered out, male users—particularly older men—appear to be more heavily targeted. Specifically, 16.4% of viewers are males aged 55–64, and 15.7% are males over 65. However, this pattern may be misleading. Given that Meta as a platform has a larger base of male users overall, it is expected that male users would naturally make up a larger share of ad impressions—regardless of the ad type. Therefore, raw exposure percentages alone are insufficient to determine targeting bias, highlighting the need for normalization across demographic baselines.
<center>![Figure1](/assets/img/figure1.jpg) </center>
<center>Figure 1: Population Distribution of Users Exposed to Loan Ads by Demographic Group</center>
### 4.2 Rationale for Dual-Model Approach:
To explore the prevalence of harmful and fraudulent loan ads, we initially applied a straightforward classifier (facebook/bart-large-mnli model) that flags each ad as “fraud” (1) or “non-fraud” (0) based on a single dummy variable. However, this model wasn’t designed specifically for nuanced fraud detection. Thus, to strengthen our findings, we then leveraged another powerful LLM fine-tuned on multiple fraud and harm labels(e.g., fake government grants, currency exchange risk, high APR), allowing us to capture a broader spectrum of deceptive tactics. By comparing outputs from both systems, we cross-validate our findings and mitigate the risks associated with relying on any single “black-box” algorithm—ultimately strengthening the credibility and depth of our analysis

### 4.3 Model One: Harmful and Fraudulent Loan Labels
#### 1.	Frequency of Loss‑Related Labels
Building on our demographics overview, we next quantify how often risky loan ads occur, applying the mDeBERTa-v3-base-mnli-xnli model—fine-tuned on 11 fraud-related labels—to a large corpus of loan ads. Our goal was to flag those offers most likely to inflict financial loss or exploit vulnerable consumers. After running the model, we identified 93 loan ads as harmful or fraudulent accounting for 34.6% of all loan ads, suggesting they carried one or more loss labels.
Out of 93 harmful loan ads detected, we focused on the five most frequent loss labels (Figure 2)—currency exchange risk (49 ads), instant loans (22), fake government grants (20), false loan guarantees (2), and high APR (1). To assess whether certain demographic groups are disproportionately exposed to these risks, we then computed a normalized exposure ratio for each group, comparing their exposure to each specific risk label against their overall exposure to loan-related ads.

<center> ![Figure2](/assets/img/figure2.jpg)</center>
<center> Figure2: Top 5 Most Frequent Loss-Related Labels in Loan Ads</center>
#### 2.	Unaggregated Exposure: Harmful Loan Ads by Group
Regarding the potential misrepresentation in the initial findings, we calculated a normalized exposure ratio to adjust for the general exposure of harmful loan ads across different demographic groups. This normalized ratio measures the relative concentration of harmful content within each demographic’s exposure to loan advertising. A ratio greater than 1 indicates that the group is overrepresented in harmful loan ads compared to their general exposure, suggesting targeted overrepresentation. Conversely, a ratio below 1 signals underrepresentation, meaning the group is less likely to be exposed to risky or deceptive financial advertisements.
Figure 3 below illustrates that only male users have a normalized exposure ratio greater than 1, with the highest targeting seen among males aged 35-45 (with a normalized exposure of about 1.4), meaning they encounter 40 % more risky ads than would be expected given their overall ad exposure. They are closely followed by males 45–54 (1.38) and 18–24 (1.25), while the 25–34 and 55–64 cohorts also remain above the 1.0 threshold at roughly 1.13 and 1.10, respectively. By contrast, no female demographic breaches unity: their ratios range from around 0.85 for women 18–24 down to approximately 0.75 for those aged 65 and older.

<center>![Figure3](/assets/img/figure3.jpg)</center>
<center> Figure3: Unaggregated Normalized Exposure to Harmful Loan Ads by Demographic Group</center>
In addition to the general analysis, a closer look at the normalized exposure ratios for the top loss labels reveals distinct patterns of overexposure among specific demographic groups. As is shown in Figure 4, young males, particularly those aged 18–24, are significantly overexposed to false loan guarantee ads, with a normalized exposure ratio of 12.93—the highest across all age and gender groups. This indicates a concentrated targeting of this especially vulnerable segment. Males aged 35–44 also experience elevated exposure to ads involving currency exchange risks and instant loan offers. Among female users, women aged 25–34 show the highest normalized exposure to instant loan ads, while elderly women (65+) are disproportionately exposed to promotions for fake government grants.

<center>![Figure4](/assets/img/figure4.jpg)</center>
<center> Figure4:  Heatmap of Unaggregated Normalized Exposure to Frequent Loss Label Across Demographic Groups</center>
Several factors could explain this pattern, each implicating different parties in terms of responsibility. First, the sponsors of these financially harmful ads play a key role. Advertisers on platforms like Meta can specifically choose which demographic groups they wish to target. Similar to how cosmetic commercials predominantly target women, these loan ads may be focused on men, especially those assumed to be seeking financial products like business loans or mortgages.
A second significant factor contributing to this disproportionate targeting is the potential bias in Meta’s algorithms, which is the primary focus of this study. However, it is important to recognize that liability may not rest solely with the platform in this stage of analysis. Whether Meta’s algorithm is indeed biased will be further examined during the aggregation stage.
#### 3.	Aggregation Reveals Hidden Bias
As mentioned in the initial normalized ratio analysis, there are mainly two reasons accounting for the biased targeting of harmful loan commercials. One lies in the distributor’s intent when promoting their products, and the other is the potential bias in Meta’s algorithm. To explore whether the bias originates from Meta’s algorithm, we conducted an aggregated analysis. During this process, we aggregated the ads with the same content into one row, summing up their audience demographic data. Theoretically, the results should remain the same as the non-aggregated analysis.
However, after aggregation, we observed an unexpected group emerging in the overexposed category from Figure 5 : female users aged 45–54. This stands in contrast to the non-aggregated results, where only male users appeared overexposed across the top harmful loan ad categories. The emergence of this new group after aggregation suggests a potential bias within Meta’s ad delivery algorithm. Specifically, the fact that female users aged 45–54 were not overrepresented in the disaggregated data—but become so once impressions are grouped by ad content—implies that the algorithm may be unevenly amplifying exposure for certain demographics.

<center>![Figure5](/assets/img/figure5.jpg)</center>
<center> Figure5: Aggregated Normalized Exposure to Harmful Loan Ads by Demographic Group</center>
Figure 6 also reveals notable inconsistencies between the pre- and post-aggregation data. In the non-aggregated data, male users—especially those in the 18–24 age group—were clearly overexposed to false loan guarantee ads. However, when the data is aggregated, an unexpected shift occurs: female users aged 45–54 emerge as a new overexposed group. This demographic, which did not appear as a major target in the non-aggregated data, becomes prominent in the aggregated analysis, suggesting that aggregation may uncover hidden patterns of exposure.

<center>![Figure6](/assets/img/figure6.jpg)</center>
<center> Figure6: Heatmap of Aggregated Normalized Exposure to Frequent Loss Label Across Demographic Groups</center>
This could be due to the way the system interprets engagement signals, demographic clusters, or inferred interests. For example, the algorithm may learn over time that users in this age/gender group are more likely to engage with financial content in general, and as a result, it disproportionately delivers loan ads to them—even if advertisers did not explicitly target them. 
This finding strengthens the argument that the bias in the ad targeting is more likely a result of the platform’s algorithmic behavior, rather than the advertiser’s explicit targeting choices

### 4.4 Model Two: Comparing Unaggregated vs. Aggregated Demographic Exposure to Fraud Loan Ads

For the fraud ads, we applied the same comparative logic as before: analyzing results both before and after aggregating similar ads. Using the facebook/bart-large-mnl model, we initially identified 55 ads as “fraud loan ads”, accounting for 20.4% of all loan ads. After grouping highly similar creatives, this number was reduced to 13. A normalized exposure analysis by demographic group revealed a striking inconsistency between the pre- and post-aggregation results. Prior to aggregation, the ads exhibited a clear bias toward male users, particularly those aged 18–24 (with a normalized exposure of 1.22), followed by males aged 65+ (1.05). However, after aggregation, the demographic skew shifted markedly: exposure became disproportionately high among female users, especially in the 18–24 and 45–54 age group. This reversal suggests that although individual ads may appear targeted toward one group, the delivery algorithm—after aggregating similar content—amplifies exposure in ways that disproportionately affect others.
This pattern reinforces our hypothesis: Meta’s ad delivery system is not just passively distributing content, but actively shaping who sees what based on inferred engagement patterns. The fact that women, particularly younger and middle-aged groups, become overexposed after aggregation points to a systemic algorithmic bias. These findings raise critical questions about the fairness and accountability of algorithm-driven ad platforms and highlight the need for greater transparency in how targeting mechanisms operate at scale.

<figure class="half">
![Figure7](/assets/img/figure7.jpg)
<center> Figure7: Unaggregated Normalized Exposure to Fraud Loan Ads by Demographic Group</center>

![Figure8](/assets/img/figure8.jpg)
<center> Figure8: Aggregated Normalized Exposure to Fraud Loan Ads by Demographic Group</center>
</figure>
