# HIV status disclosure analysis and prediction: Does incentives play a role?

## Overview
This repository contains data and code for analyzing the results of an experiment conducted in Malawi to understand the impact of cash incentives on HIV disclosure. The project explores various factors, including age, distance to testing centers, and incentives received, to uncover insights into HIV disclosure behavior.

## Key Takeaways
1. **Incentive Impact:** The majority of participants received incentives, and there is a moderate positive correlation between total incentives and HIV disclosure. This suggests that incentives may play a role in encouraging disclosure.

2. **Demographic Influence:** The age of participants varies widely, and there is an interesting relationship between age and incentive receipt. Further exploration is needed to understand the demographic factors influencing disclosure behavior.

3. **Geographic Variances:** The distance to testing centers varies across villages, and this may impact the likelihood of receiving incentives or learning HIV results. Tailored interventions may be needed for different geographic areas.

4. **Model Predictions:** The logistic regression model provides shows that incentives could play a role in enhancing HIV status disclosure.
## Data Sources
- The dataset named `hiv_disclosure.csv` was used for the analysis. Kaggle dataset link: https://www.kaggle.com/datasets/utkarshx27/hiv-information-experiment

## Data Cleaning
The dataset underwent a cleaning process to handle missing values and ensure a reliable analysis. The cleaned data includes relevant variables such as age, distance, total incentive, and HIV results.
#Import neccessary libraries
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

#import dataset
hiv_disclosure=pd.read_csv("D:\hiv_disclosure.csv")

# Remove rows with any missing values
hiv_disclosure_cleaned = hiv_disclosure.dropna()
 Rename the "Unnamed: 0" column to "pt_id"
hiv_disclosure_cleaned = hiv_disclosure_cleaned.rename(columns={'Unnamed: 0': 'pt_id'})

## Descriptive Statistics
Basic descriptive statistics were calculated for key variables, providing insights into the age distribution, distance to testing centers, and total incentives. The summary includes mean, median, standard deviation, and other relevant measures.
# Descriptive statistics
descriptive_stats = hiv_disclosure_cleaned[['age', 'distvct', 'tinc']].describe()
print("Descriptive Statistics:")
print(descriptive_stats)

# Visualize the distribution of distvct with a histogram
plt.figure(figsize=(10, 6))
sns.histplot(hiv_disclosure_cleaned['distvct'], bins=20, kde=True, color='salmon')
plt.title('Distribution of Distance to Testing Center')
plt.xlabel('Distance (km)')
plt.ylabel('Frequency')
plt.show()

# Visualize the distribution of tinc with a histogram
plt.figure(figsize=(10, 6))
sns.histplot(hiv_disclosure_cleaned['tinc'], bins=20, kde=True, color='gold')
plt.title('Distribution of Total Incentive')
plt.xlabel('Total Incentive')
plt.ylabel('Frequency')
plt.show()

# Boxplot for age
plt.figure(figsize=(10, 6))
sns.boxplot(x=hiv_disclosure_cleaned['age'], color='skyblue')
plt.title('Boxplot of Age')
plt.xlabel('Age')
plt.show()
![Age distribution on box plot](https://github.com/winok2/HIV-status-disclosure-analysis-and-prediction/assets/137515971/d8880dee-b767-498d-a08d-75344c92864a)


# Boxplot for distvct
plt.figure(figsize=(10, 6))
sns.boxplot(x=hiv_disclosure_cleaned['distvct'], color='salmon')
plt.title('Boxplot of Distance to Testing Center')
plt.xlabel('Distance (km)')
plt.show()

# Boxplot for tinc
plt.figure(figsize=(10, 6))
sns.boxplot(x=hiv_disclosure_cleaned['tinc'], color='gold')
plt.title('Boxplot of Total Incentive')
plt.xlabel('Total Incentive')
plt.show()

# Calculate the percentage of people who received any incentive
percentage_received_incentive = (
    hiv_disclosure_cleaned['any'].value_counts(normalize=True) * 100
)

# Display the percentage
print("Percentage of People Receiving Any Incentive:")
print(percentage_received_incentive)

# Visualize the percentage
plt.figure(figsize=(8, 6))
sns.barplot(x=percentage_received_incentive.index, y=percentage_received_incentive.values, palette="pastel")
plt.title('Percentage of People Receiving Any Incentive')
plt.xlabel('Received Any Incentive')
plt.ylabel('Percentage')
plt.show()

# Explore the relationship between receiving any incentive and age
plt.figure(figsize=(10, 6))
sns.boxplot(x='any', y='age', data=hiv_disclosure_cleaned, palette="pastel")
plt.title('Relationship between Receiving Any Incentive and Age')
plt.xlabel('Received Any Incentive')
plt.ylabel('Age')
plt.show()

# Investigate the relationship between distance and receiving any incentive
plt.figure(figsize=(10, 6))
sns.boxplot(x='any', y='distvct', data=hiv_disclosure_cleaned, palette="pastel")
plt.title('Relationship between Receiving Any Incentive and Distance to Testing Center')
plt.xlabel('Received Any Incentive')
plt.ylabel('Distance to Testing Center (km)')
plt.show()

# Investigate the relationship between distance and learning HIV results
plt.figure(figsize=(10, 6))
sns.boxplot(x='got', y='distvct', data=hiv_disclosure_cleaned, palette="pastel")
plt.title('Relationship between Learning HIV Results and Distance to Testing Center')
plt.xlabel('Learned HIV Results')
plt.ylabel('Distance to Testing Center (km)')
plt.show()

# Visualize the distribution of distances for different villages
plt.figure(figsize=(12, 8))
sns.boxplot(x='villnum', y='distvct', data=hiv_disclosure_cleaned, palette="pastel")
plt.title('Distribution of Distances for Different Villages')
plt.xlabel('Village ID')
plt.ylabel('Distance to Testing Center (km)')
plt.show()

# Analyze the relationship between age and learning HIV results
plt.figure(figsize=(10, 6))
sns.boxplot(x='got', y='age', data=hiv_disclosure_cleaned, palette="pastel")
plt.title('Relationship between Learning HIV Results and Age')
plt.xlabel('Learned HIV Results')
plt.ylabel('Age')
plt.show()

# Analyze the relationship between receiving any incentive and learning HIV results
plt.figure(figsize=(10, 6))
sns.countplot(x='got', hue='any', data=hiv_disclosure_cleaned, palette="pastel")
plt.title('Relationship between Learning HIV Results and Receiving Any Incentive')
plt.xlabel('Learned HIV Results (0: No, 1: Yes)')
plt.ylabel('Count')
plt.legend(title='Received Any Incentive', loc='upper right')
plt.show()

# Calculate the correlation matrix
correlation_matrix = hiv_disclosure_cleaned[['got', 'age', 'distvct', 'tinc', 'any']].corr()

# Visualize the correlation matrix using a heatmap
plt.figure(figsize=(10, 8))
sns.heatmap(correlation_matrix, annot=True, cmap="coolwarm", fmt=".2f", linewidths=.5)
plt.title('Correlation Matrix')
plt.show()
![correlation heatmap](https://github.com/winok2/HIV-status-disclosure-analysis-and-prediction/assets/137515971/6615865c-f754-45c0-9fab-72f3011da1b5)

## Percentage of People Receiving Any Incentive
The analysis revealed that approximately 78% of individuals received some form of incentive, while 22% did not.

## Incentive Analysis
Exploration of the relationship between receiving any incentive and other variables, such as age, was conducted. The percentage of people learning their HIV results based on whether they received an incentive was calculated.

## Distance Analysis
The relationship between the distance to testing centers and the likelihood of receiving an incentive or learning HIV results was investigated. Visualizations were created to show the distribution of distances for different villages.

## Key Findings

### Descriptive Statistics
- **Age:** The average age of participants is 33.4 years, with a standard deviation of 13.6 years.
- **Distance:** The average distance to testing centers is 2.01 kilometers, with a standard deviation of 1.27 kilometers.
- **Total Incentive:** The average total incentive is 1.01, with a standard deviation of 0.90.

### Percentage of People Receiving Any Incentive
- Approximately 78% of individuals received some form of incentive, while 22% did not.

### Incentive Analysis using correlation
- The correlation coefficient between total incentive and HIV disclosure (got) is moderate at 0.35 while any and any(0.40).
- The other variables showed insignificant correlation coefficient values.
- The percentage of people learning their HIV results is higher among those who received incentives.

### Distance Analysis
- Visualizations show varying distributions of distances for different villages.

## Logistic Regression Model
- The logistic regression model predicts HIV disclosure based on variables like age, distance, and total incentive.
- Confusion Matrix:
-[[ 75  95]
- [ 49 346]]
              precision    recall  f1-score   support

         0.0       0.60      0.44      0.51       170
         1.0       0.78      0.88      0.83       395

    accuracy                           0.75       565
   macro avg       0.69      0.66      0.67       565
weighted avg       0.73      0.75      0.73       565

### Interpretation:

-Precision for class 0 (did not learn HIV results) is 0.60, and recall is 0.44.
-Precision for class 1 (learned HIV results) is 0.78, and recall is 0.88.
-Overall accuracy is approximately 75%.


## Contributing
Contributions to this project are welcome. If you have ideas for improvements or additional analyses, please open an issue or submit a pull request.

## License
This project is open-source and available under the [MIT License](LICENSE). You are free to use, modify, and distribute the data and analysis results, but please give appropriate credit.

