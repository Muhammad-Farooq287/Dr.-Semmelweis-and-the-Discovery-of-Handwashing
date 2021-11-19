# Dr.-Semmelweis-and-the-Discovery-of-Handwashing

Meet Dr. Ignaz Semmelweis:
This is Dr. Ignaz Semmelweis, a Hungarian physician born in 1818 and active at the Vienna General Hospital. If Dr. Semmelweis looks troubled it's probably because he's thinking about childbed fever: A deadly disease affecting women that just have given birth. He is thinking about it because in the early 1840s at the Vienna General Hospital as many as 10% of the women giving birth die from it. He is thinking about it because he knows the cause of childbed fever: It's the contaminated hands of the doctors delivering the babies. And they won't listen to him and wash their hands!

In this notebook, we're going to analyze the data that made Semmelweis discover the importance of handwashing. Let's start by looking at the data that made Semmelweis realize that something was wrong with the procedures at Vienna General Hospital.

# Importing modules
import pandas as pd

# Read datasets/yearly_deaths_by_clinic.csv into yearly
yearly = pd.read_csv("datasets/yearly_deaths_by_clinic.csv")

# Print out yearly
print(yearly)

# Calculate proportion of deaths per no. births
yearly['proportion_deaths']= yearly['deaths']/yearly['births']

# Extract Clinic 1 data into clinic_1 and Clinic 2 data into clinic_2
clinic_1 = yearly[yearly.clinic=='clinic 1']
clinic_2 = yearly[yearly.clinic=='clinic 2']

# Print out clinic_1
print(clinic_1)

# This makes plots appear in the notebook
%matplotlib inline

# Plot yearly proportion of deaths at the two clinics
ax = clinic_1.plot(x="year", y="proportion_deaths",
              label="Clinic_1")
clinic_2.plot(x="year", y="proportion_deaths",
         label="Clinic_2",ax=ax, ylabel="Year",xlabel="Proportion Deaths")
         
# Read datasets/monthly_deaths.csv into monthly
monthly = pd.read_csv("datasets/monthly_deaths.csv",parse_dates=["date"])

# Calculate proportion of deaths per no. births
monthly["proportion_deaths"]=monthly["deaths"]/monthly["births"]

# Print out the first rows in monthly
print(monthly.head())

# Plot monthly proportion of deaths
%matplotlib inline
ax=monthly.plot(x="date",y="proportion_deaths",
                label="Clinic 1",xlabel="Date",
                ylabel="Proportion Deaths")
# Date when handwashing was made mandatory
handwashing_start = pd.to_datetime('1847-06-01')

# Split monthly into before and after handwashing_start
before_washing = monthly[monthly["date"]<handwashing_start]
after_washing = monthly[monthly["date"]>=handwashing_start]
# Plot monthly proportion of deaths before and after handwashing
ax=before_washing.plot(x="proportion_deaths",y="date",label="Before Washing") 
after_washing.plot(x="proportion_deaths",y="date",label="After Washing",
                   xlabel="Date",ylabel="Proportion Deaths",ax=ax)

# Difference in mean monthly proportion of deaths due to handwashing
before_proportion = before_washing["proportion_deaths"]
after_proportion = after_washing["proportion_deaths"]
mean_diff = after_proportion.mean()-before_proportion.mean()
mean_diff

# A bootstrap analysis of the reduction of deaths due to handwashing
import pandas as pd
boot_mean_diff = []
for i in range(3000):
    boot_before = before_proportion.sample(frac=1,replace=True)
    boot_after = after_proportion.sample(frac=1,replace=True)
    boot_mean_diff.append( boot_after.mean()-boot_before.mean() )

# Calculating a 95% confidence interval from boot_mean_diff 
confidence_interval = pd.Series(boot_mean_diff).quantile([0.975,
                                                          0.025])
confidence_interval

# The data Semmelweis collected points to that:
doctors_should_wash_their_hands = True
