---
layout: post
title: Discussion
comments: true
mathjax: true
---
## 7.1 Key Findings
In this research, we began with the data collection and processing from the Meta Ad Library dataset. Then we ran a Python script that streamed the 100,000 + rows in chunks, kept only October 2024 entries whose body or link caption contained loan-related words and whose targeting fields mentioned France, parsed each ad’s demographic JSON to extract age-gender counts and percentages, and wrote out the final 269 ads with their text, start date, advertiser, and full demographic breakdown. Next, we applied two zero shot classification models: the first targeting “harmful and fraudulent loan ads,” the second isolating “fraud loan ads.” Model one flagged 93 ads, while Model two flagged 55.

To further analyze the algorithmic bias, we computed normalized exposure ratios both before and after aggregating demographic groups. We found that women aged 45–54 were disproportionately exposed to harmful loan ads, and women aged 18–24 and 45–54 to fraud loan ads. Digging deeper into Model one’s loss‐label predictions, we saw that young males (18–24) faced substantial overexposure to false guarantee offers, while males 35–44 were more likely to encounter currency exchange risk and instant loan ads. Among women, those aged 18–24 saw the highest normalized exposure to instant loan ads, and women 65+ to fake grant promotions.

Finally, our qualitative analysis showed that Model one achieved 91 % overall label accuracy, correctly assigning the specific loss label in 93 % of cases and recalling 82 % of true loss related ads. Model two reached 80 % overall accuracy and delivered 90 % precision on fraudulent content detection

## 7.2 Algorithmic Feedback Loops Amplify Real World Vulnerabilities

These overexposure patterns make sense once we recognize that Meta’s ad‐delivery engine isn’t a neutral pipe: it learns, in real time, which demographics engage most with particular creative assets and then amplifies those placements to the same groups. In our research, we found that women—especially those aged 18–24 and 45–54—   are more tageted to harmful and fraudulent loan ads, and  likely due to an algorithmic feedback loop. As these cohorts click or spend more time on loan related content, the platform’s machine learning models infer that such ads “perform” better with them, and so they continue to receive even more of the same. Far from random distribution, this is a self reinforcing cycle in which prior engagement begets greater exposure.

That digital dynamic dovetails with offline fraud trends. The U.S. Bureau of Justice Statistics estimated that in 2017 some 3.0 million people reported personal financial fraud—1.7 million of whom were women, outpacing the 1.4 million male victims(Bureau of Justice Statistics 2021, 4). In effect, Meta’s algorithm appears to mirror and magnify these real world vulnerabilities by funneling predatory offers toward the same groups who are already most likely to fall prey. Moreover, it flips the old stereotype that only seniors are at risk: the Federal Trade Commission found in 2021 that adults aged 18–59 lose money to scams at nearly twice the rate of those 60 and over, with social media based schemes like faux shopping sites and cryptocurrency fraud driving the trend (Federal Trade Commission 2022). By privileging “digital natives” in its delivery decisions, the platform ensures that younger users—who are both more active online and statistically more likely to click fraudulent offers—remain prime targets for exploitative loan advertising (Amure 2025).

![Figure11](/assets/img/figure11.png){: .mx-auto.d-block :}
<center> Figure 11. 2021 Loss Reports by Age and Fraud Type
Source: Federal Trade Commission, “Who Experiences Scams? A Story for All Ages,” Data Spotlight, December 8, 2022 </center>

## 7.3 Algorithmic Bias and Regulatory Gaps in Meta’s Ad Delivery

**Recital 68** of the **Digital Services Act (DSA)** makes clear that 
> “Online advertising can contribute to significant risks, ranging from advertisements that are themselves illegal content, to contributing to financial incentives for the publication or amplification of illegal or otherwise harmful content and activities online, or the discriminatory presentation of advertisements with an impact on the equal treatment and opportunities of citizens.”

In practice, it’s entirely acceptable for algorithms to steer cosmetics toward women or sports gear toward young men because those groups are the natural market. But it crosses a line when the same machinery funnels predatory or fraudulent financial products to vulnerable cohorts—ads that can inflict real economic harm. This concern actually inspired us to do research on Meta’s Ad Library for harmful loans and test for demographic bias.

**Recital 79** then obliges very large online platforms like Meta to identify and mitigate systemic risks arising from their services, while **Recital 95** flags the 
> “emerging risks brought by the distribution of advertising online, for example in relation to illegal advertisements or manipulative techniques and disinformation with a real and foreseeable negative impact on public health".

Our findings—that women aged 18–24 and 45–54 are disproportionately exposed to harmful or fraudulent loan offers—show that Meta’s current moderation falls short of these DSA requirements.

Moreover, these biased delivery patterns may also cause infringement to the **GDPR**’s transparency and fairness rules. **Articles 13 and 14** give users the right to know how their data are used for personalized advertising, and **Article 5** requires 
> “all personal-data processing to be lawful, fair, and transparent”. 

## 7.4 Methodological Limitations

However, this research also has limitations. First, our models rely solely on the textual content of advertisements and lack any contextual or multimodal understanding. In real-world campaigns, visual cues, layout design, and surrounding page context can all influence meaning—and our text-only approach may miss these important signals.

Second, informal language, slang, or underrepresented dialects within ad copy can introduce ambiguity that undermines classification accuracy. Because zero-shot and few-shot models have not been explicitly trained on the full spectrum of colloquial or regional expressions used in ads, subtle cues may be misinterpreted or overlooked.

Third, while convenient for rapid analysis, zero-shot models are not optimized with domain-specific training data. As a result, their overall accuracy on fraud and harm detection tends to lag behind that of models fine-tuned on carefully curated financial-fraud datasets.

Finally, our method does not employ a fixed probability threshold for flagging content. Without a calibrated cutoff, the model remains susceptible to both false positives (over-flagging benign ads) and false negatives (missing genuinely harmful ones), which could skew our exposure estimates.

