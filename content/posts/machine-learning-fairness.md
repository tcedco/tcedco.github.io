---
title: Exploring Bias and Fairness in Machine Learning Models
layout: post
outputs: [html, json]
categories: [machine learning, python]
date: 2024-09-21
---

Machine learning models are shaping our world, from recommending what movies to watch next, to determining credit scores, to screening job candidates. At first glance, these algorithms seem like objective decision-makers, built to remove human error and bias. But as we've seen time and again, algorithms are only as unbiased as the data they're trained on. And if there's one thing we know about historical data—it often carries the weight of societal prejudices and inequalities.

When we talk about bias in machine learning, we're referring to a systematic error that causes a model to produce unfair outcomes. This often results in certain groups being treated unfavorably compared to others, based purely on characteristics such as race, gender, or age. Even when data scientists have the best intentions, bias can creep into a model, largely because it's rooted in the data itself or in the assumptions we make while building the model.

Take hiring algorithms, for example. Companies want to automate the process of filtering resumes, so they feed an AI thousands of resumes of previously successful employees. But if the company historically hired predominantly male candidates, the AI might "learn" that men are more likely to be successful, and start favoring male resumes. The AI isn't inherently sexist—it's just mimicking the patterns it learned from biased data.

## Different Types of Bias in Machine Learning

Bias can show up in various forms, and it’s not just about gender or race. There are several ways bias can sneak into a model, but the main three types we'll look into is historical bias, selection bias, and measurement bias.

### Historical Bias

One common type is **historical bias**. This happens when the data we collect reflects existing inequalities. If you're building a healthcare algorithm that predicts who is more likely to suffer from a heart attack, but your dataset is primarily composed of data from men, your model might underperform on women because it's learned mostly from male patients. Historical bias is hard to avoid since it’s rooted in real-world data, but it's also crucial to recognize so we can address it.

### Selection Bias

**Selection bias** is another tricky one. This occurs when the data we use to train the model doesn't accurately represent the population we want it to generalize to. Imagine an algorithm designed to predict the performance of students in a university. If most of the training data comes from students in top-tier schools, the algorithm may not perform well when applied to students in community colleges, because their experiences and academic backgrounds differ. The model "learned" patterns from a skewed subset of students.

### Measurement Bias

Then there’s **measurement bias**, which happens when the data collection process is flawed or inconsistent. For instance, if we're building a face-recognition system but most of the images used to train it come from one ethnic group, the system may not work well for other ethnicities.

Here's an example: an AI system used in courts to predict recidivism rates (whether a person will commit another crime) was found to be biased against Black defendants. It consistently predicted higher rates of re-offense for Black individuals compared to white individuals with similar criminal histories. This is a clear example of how bias in training data (historical patterns of incarceration) can lead to biased predictions in real-world applications.

## The Consequences of Bias

The consequences of biased models go far beyond academic discussions. In domains like criminal justice, hiring, healthcare, and finance, bias can lead to unfair treatment and perpetuate societal inequalities. Imagine being denied a job interview because an algorithm inferred that your qualifications were less valuable based on historical data favoring another demographic. Or consider a healthcare algorithm that underestimates the risk of disease for certain ethnic groups because it was trained mostly on data from a different population.

Bias in algorithms has real, tangible impacts on people's lives. Using hiring as an example again, biased algorithms can lead to discrimination against qualified candidates from underrepresented backgrounds. In criminal justice, biased AI systems can result in harsher sentencing recommendations for certain groups. And in healthcare, biased models can misdiagnose patients or under-treat populations that were underrepresented in the training data.

## How Can Bias Creep in?

Let’s look at a simplified example to show how bias can creep into a machine learning model. Suppose we’re building a hiring algorithm that predicts whether a candidate will be successful at a company. We’ll train it using historical data. We first start by preparing our dataset.

```python
import pandas as pd
from sklearn.linear_model import LogisticRegression

# Let's say we have a dataset of previous candidates
data = {
    'name': ['Alice', 'Bob', 'Carol', 'David', 'Eve'],
    'gender': ['female', 'male', 'female', 'male', 'female'],
    'years_experience': [5, 7, 3, 8, 4],
    'hired': [1, 1, 0, 1, 0]  # 1 = Hired, 0 = Not Hired
}

df = pd.DataFrame(data)

# We'll encode gender as a feature and train a simple model
df['gender_encoded'] = df['gender'].apply(lambda x: 1 if x == 'male' else 0)

# Features: gender, years of experience
X = df[['gender_encoded', 'years_experience']]
y = df['hired']

# Train a simple logistic regression model
model = LogisticRegression()
model.fit(X, y)
```

On the surface, it looks like we’re training a model to predict hiring outcomes based on years of experience and gender. But notice how gender is encoded as a feature. If men were hired more often in the past (perhaps due to gender bias in hiring decisions), our model might learn to associate being male with a higher chance of getting hired.

We can check the model's coefficients to see how much weight it assigns to each feature:

```python
print(model.coef_)
```

If we see that the coefficient for the "gender" feature is significant, it suggests the model has learned to associate gender with hiring success—a sign of bias. In this case here is the output:

```
[[0.11592645 0.96432529]]
```

Although not significant in this example, the model assigns a higher weight to years of experience, but there is a pretty sizeable weight for gender as well. This could indicate that although years of experience is the primary reason for hiring, gender is also playing a role in some way.

## How to Mitigate Bias in ML Models

So, what can we do to reduce bias? One common technique is **data rebalancing**. If we notice that our dataset is skewed (e.g., we have more male candidates than female candidates), we can either collect more data from underrepresented groups or use techniques like **oversampling** to balance the dataset.

```python
from sklearn.utils import resample

# Separate the data into majority (males) and minority (females)
df_majority = df[df.gender_encoded == 1]
df_minority = df[df.gender_encoded == 0]

# Upsample the minority class (females)
df_minority_upsampled = resample(df_minority, 
                                 replace=True,     # sample with replacement
                                 n_samples=len(df_majority),  # to match majority class
                                 random_state=42)  

# Combine the majority class with the upsampled minority class
df_balanced = pd.concat([df_majority, df_minority_upsampled])

# Now, we can retrain the model using this balanced dataset
X_balanced = df_balanced[['gender_encoded', 'years_experience']]
y_balanced = df_balanced['hired']

model_balanced = LogisticRegression()
model_balanced.fit(X_balanced, y_balanced)
```

After rebalancing the data, our model should rely less on gender and more on relevant features like years of experience.

Other techniques include **adversarial debiasing**, where we train a secondary model to detect and penalize bias, and **post-processing methods**, where we adjust model outcomes after training to ensure fairer results across different groups. Each technique has its advantages and limitations, and often multiple approaches are used in combination to mitigate bias effectively.

## How Do We Measure Fairness?

Once we have a model, how can we tell if it’s biased? Measuring fairness in machine learning is a field of study in itself, but there are some widely used methods.

One approach is **demographic parity**. This means that the model’s predictions should be equally distributed across different demographic groups. For instance, if a hiring algorithm selects 50% of male candidates, it should also select around 50% of female candidates. This ensures that no one group is favored in terms of model output.

Another metric is **equal opportunity**, which focuses on ensuring that qualified candidates have an equal chance of being selected, regardless of their demographic group. If two people are equally qualified for a job, they should have an equal chance of being hired, regardless of their gender, race, or other characteristics.

We can also measure fairness using **counterfactual fairness**, which asks: would the model make the same prediction if we changed a sensitive attribute (like gender or race) while keeping everything else the same? If the prediction changes, the model might be biased.

For example, let’s say we have a hiring algorithm, and we take a male candidate’s resume, change only the gender to female, and the model gives a different score. That would indicate bias because gender, which should be irrelevant to the hiring decision, is influencing the outcome.