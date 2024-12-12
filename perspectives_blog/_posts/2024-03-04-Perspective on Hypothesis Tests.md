---
layout: post
title: "Perspective on Hypothesis Tests"
---

<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: {
      skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
      displayMath: [['$$','$$']],
      inlineMath: [['$','$']],
    },
  });
</script>

## What is a hypothesis test?

Hypothesis tests are used by data scientists to distinguish between meaningful results and noise in a sample of data.

The general idea is that the data we observe is the product of, more or less, chance occurences. If we run an advertising campaign that yields 100 conversions, there's no guarantee that we'd get the same results from delivering the same campaign to the same people. A restaurant runs a discount on  

## When should I use a hypothesis test?

- In general, when you run an experiment, you can use a hypothesis test to determine whether the resulting change in effect was due to random chance, or a systematic difference.
- Hypothesis tests are also useful when you have only observational data – compared with experimental data, observational data cannot be used to make claims that _x_ caused _y_ (that's a topic for another post)
  - Most data in the business world is observational
    - Example: Customer data is one example of observational data. By definition, a customer who enters your database is someone who made a purchase, and you only have information about that one class of person.

I’d like to explain the key ideas behind hypothesis testing through a toy example I made up.

## Example: Hospitality

**Description**:

While reviewing their financial statements from the last quarter, a hotel chain discovered an opportunity. They noticed that long term bookings generated substantially more profit than shorter bookings. Digging deeper, they discovered that the cancellation rate for these long term bookings was much much lower than short term stays, which explained the difference in revenue. Specifically, the shorter-term stays had a cancellation rate that was 20 percentage points higher than the long-term cancellation rate. The team speculates that people who book longer term stays have their plans more solidified, while shorter term clients are more whimsical with their travel. For internal purposes, they identify stays longer than 14 days as “long term”.

The CEO wants to explore the link between length of stay and cancel rates. If shorter stays have significantly more cancellations, they would consider countermeasures to mitigate these losses. Implementing trip insurance and cancellation fees have been debated in the past. However, before they make any policy changes, they’d like to have confidence that the difference in cancellation rates is meaningful, and not just due to chance. In general, a strong and sound understanding of their business will help the executive team make smart strategic decisions.

**The Hypothesis Test**:

In the language of hypothesis testing, what are the two scenarios we are interested in? How are we going to formulate the null and alternative hypotheses?

You can think of the null hypothesis as the “cynical” perspective. In this scenario, the null hypothesis is that there is no true difference between cancellation rates of long and short term bookings, and the observed difference is due to pure random chance.

The alternative hypothesis, on the other hand, asserts that the observed difference is meaningful; in this case, that short term bookings cancel at a higher rate than long term bookings.

How can we formulate these hypotheses as something testable with data?

The null says that there is no true difference between cancellation rates on average. Sure, there are observed differences between the two – that was what the financial performance review told us at the very start of this case study. But, the null model asserts, this was just chance variation – in the long run, the averages will end up being the same. Thus, we can formulate our null hypothesis as saying that the average cancellation rate of a short term stay is the same as the average cancellation rate of a long term stay. Using the variable $\bar{X}$ to denote the average, the null says
$$ \begin{equation}\bar{X}_{\text{long term}} = \bar{X}_{\text{short term}}\end{equation} $$

On the other hand, the alternative hypothesis says that there is a true difference in the observed cancellation rates. That, on average, we would still expect to see a difference in the cancellation rates between long and short term stays. We can formulate the alternative hypothesis as saying that the average cancellation rate is higher for a short term stay than for a long term stay.[^label1] $$ \begin{equation}\bar{X}_{\text{long term}} \neq \bar{X}_{\text{short term}}\end{equation} $$

[^label1]: The formulation of the alternative hypothesis --- "the average cancellation rate is **higher** for a short term stay" --- is purely a function of the problem setup. Oftentimes, we are testing for a _difference_ in two averages, without any prior knowledge of what direction that difference is in. Recall that here, the starting point was noticeably less cancellations for long term stays, so we specifically want to test for the difference being a higher cancel rate for long term stays. These are known as one vs. two tailed tests. Read more [here](https://stats.oarc.ucla.edu/other/mult-pkg/faq/general/faq-what-are-the-differences-between-one-tailed-and-two-tailed-tests/).

**Comparing Hypotheses**:

Now that our hypotheses are defined, it's time to compare the two to see is where the power of big data comes in.

When you have a large number of data points, as many modern enterprises do, statistical theory lets you make some nice assumptions about what that data means. One such assumption, known as the Law of Large Numbers (LLN), says that the average you observe from your data gets closer to the true average as you increase your sample size.[^label2]

[^label2]:
    Mathematically, we express the weak LLN as saying: for a sequence of independently and identically distributed random variables with finite mean and variance, $X_1, X_2,...$ the sample average, defined as $ \bar{X_n} = \frac{1}{n}(X_1 + ... + X_n) $, converges to the expected value: $\bar{X_n} \rightarrow^p \mu$ (which, since the variables were i.i.d., is the expected value of all the random variables in the sequence). The proof relies on Chebyshev's inequality, and is quick --- using the definition of sample variance, $Var(X_n) = \frac{\sigma^2_X}{n}$, Chebyshev's inequality says:
    $$ \begin{equation}P(|\bar{X} - \mu| \geq \epsilon) \leq \frac{Var(X_n)}{\epsilon^2} = \frac{\sigma^2_x}{n\epsilon^2} \end{equation} $$.
    Through the $n$ in the denominator, you can see that as the sample size grows infinitely large, the probability $P(|\bar{X} - \mu| \geq \epsilon)$ converges to zero and
    thus $\bar{X} \rightarrow^p \mu$

But what does that have to do with hypothesis tests?

Think about what the null model is saying: that the observed data is just due to chance, and does not reflect a statistically meaningful result. At first glance, this seems like a claim that’s impossible to refute – no matter how extreme the observed results are, the null model would always blow it off as coincidence!

The LLN is the cornerstone that makes hypothesis testing useful at all. Remember what the LLN says:

> as the sample size grows large, the average of our data sample gets closer to the true average.

This is the silver bullet against the "irrefutability" of the null model: the averages of the cancellation rates in our data sample are very closely approximating the true averages. The "true average" is a statistical concept[^label3] describing how the data was generated. In our example, the true average is the probability that determined whether a booking cancelled or not, and subsequently the data we collected was populated by the true average. It's quite abstract, and it really only exists in theory --- in practice, we'd never be able to measure the true average. The LLN tells us that in a large sample, the average we see in our data is closely approximating the true average, and a large difference in the observed averages suggests a meaningful difference in the true averages.

[^label3]: More formally known as the population parameter

Let’s check-in: we have our null model, our alternative model, and an understanding of how our data relates to the two.

Our null model, again, is that there is no difference between the cancel rates for long term stays and short term stays. Our alternative model is that there is a difference in cancel rates. From the description, the observed difference from our data was 20 percentage points.

We can convert this observed difference to something known as a z-statistic: we divide the observed difference of 20 by the standard error, and that’s our z-statistic. I’ll save the details for the technical commentary, but at a high level, the z-statistic measures how far away our data is from the average that the null hypothesis expects. [^label4]

[^label4]: Formally, the z statistic

z-statistic: $z = \frac{\text{difference in null & alternative averages}}{\text{standard error}}$

From this definition, we can start to see what the hypothesis test is really doing. If the average we observe is very far from the average that the null model expects, it suggests that **the null model is a bad model for the data**, and we should consider the alternative instead.

**The Results**:

Once we have the z-statistic, we are ready to test the hypotheses. Since we assume that we have a lot of data, we can use the normal approximation.[^label5] By providing the z-statistic computed from our data to the normal distribution, we get a probability in return. This probability is the result of our hypothesis test. Forget about the technical details for a moment, since any hypothesis test will ultimately lead you to this point, and assume that the probability you got as a result was 3.5%. What does this mean?

[^label5]: CLT

By the construction of our hypothesis test, the result of 3.5% represents the probability that we observe data at least as extreme as what we got, assuming that the null hypothesis was true. In the context of the example:

The null hypothesis was that the cancellation rates for long and short term stays were the same.
Our data shows that long term stays have a cancellation rate that is 20 percentage points lower than the cancellation rate for short term stays
Our hypothesis test says that, if the null hypothesis is true, there is a 3.5% chance that we would observe that 20 percentage point difference, or any other difference larger than 20 points.

This is the result of hypothesis testing. You start by assuming that the null hypothesis is true. Since you’re going out of your way to run a test, you probably observed something that isn’t compatible with the null hypothesis – your observed difference. You use a hypothesis test to tell you how likely it is that you got that observed difference, assuming that the null hypothesis was true.

In most settings, 3.5% is such a small probability that you’d start to begin to doubt the null hypothesis. Then, because your null hypothesis is so unlikely given the data that you have, you reject the null hypothesis in favor of the alternative.

Technical Commentary:
