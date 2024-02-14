# Prediction of HDB Flat Resale Price

School of Computer Science and Engineering 
Nanyang Technological University 
Lab: B133 
Team: 2

Members:

1. Low Kar Choon
2. Lim Zhi Yong
3. Law Wei Lu 

---

### Introduction

The goal of this mini project:
> To predict the resale prices of HDB Flats according to their specifications such as the floor area, storey, location etc.

This repository contains the source code and dataset used throughout the project.
The notebook contains all the source codes, graphs and some explanation using markdown cells or comments within the code, this readme contains the explanations/reasons on the general ideas and decisions we took.
The dataset used for this project can be found [here](https://data.gov.sg/dataset/resale-flat-prices?resource_id=f1765b54-a209-4718-8d38-a39237f502b3).

---

### Table of Contents

1. [Motivation and Problem Definition](#1-Motivation-and-Problem-Definition)
2. [Exploratory Data Analysis](#2-Exploratory-Data-Analysis)
3. [Models](#3-Models)
4. [Insights and Conclusions](#4-Insights-and-Conclusions)

---

### 1. Motivation and Problem Definition

In September 2022, the prices of Housing Board resale flats in Singapore continued to rise for the 27th consecutive month, with a staggering 45 units sold for at least $1 million in September alone. In the same year, a total of over 27,000 of resale applications were registered . Now that, is a big number for a small nation like Singapore. With inflation and other economic factors, it has become increasingly challenging to determine the fair value of a resale flat in such a volatile market.

With this many people buying and selling flats, a question comes to mind, how much is my flat worth, or for the buyers, how much is a reasonable price to pay for a flat like this? Wouldn’t it be nice if I could input the basic details of a flat and get a rough estimate of the resale price to make sure that the deal is decent?

That is exactly the motivation behind our project. We want to implement a model that helps both buyers and sellers predict the resale price of a specific flat, such that they will be able to make a more informed decision. 

Thus, our problem definition is *How will the different variables of a resale flat affect its resale price?* And the goal of this mini project is to create a model that can accurately predict the sale price of a flat to help buys and sellers make a more informed decision.

### 2. Exploratory Data Analysis

Fortunately, there were no *Nan/NULL* or any other missing values in this dataset. The following columns are excluded as they were either too specific and have been covered by other columns that provided a general information on similar topic or they did not have an significant impact on the resale price. Excluded columns: ***block***, ***street_name***, ***flat_model***, ***lease_commence_date***.

Subsequently, we proceeded with the data preparation cleaning process. The following lines explains the columns and process to ensure the data were in satisfactory format.

1. Rename ***month*** to ***year*** | Remove month value from ***remaining_lease***
   The values of ***month*** were initally in the *yyyy-mm* format and ***remaining_lease*** were in *XX years XX months*. We decided to retain only the year value for both columns in order to not further complicate things such as the visualisations later on. The ***month*** column was also renamed to ***year***.

2. ***storey_range***
   This column contains the range of storeys in the following format, *XX to XX*. It seems categorical, but we did not want it to be categorical. Thus, we took the average of the range. For example, a value of *10 to 12* is modified to *11*, and values this column becomes numerical.

3. ***town***
   Comparing the boxplot of the different towns and their corresponding median resale prices. Ang Mo Kio was the lowest and Bukit Timah was the highest. Therefore, the baseline that we compare to is Ang Mo Kio since it has the lowest median resale price. The formula for encoding this categorical ***town*** is:
  > $$ Town \_ Numeric = {Median \ of \ Town \over Median \ of \ Ang \ Mo \ Kio} $$

4. ***flat_type***
   The boxplot visualisation of ***flat_type*** depicts a positive correlation with their respective resale prices. However, this column contained values in string format. Thus, we modified it such that a value of **1 to 5** represents the corresponding **1 - 5 Room**,  **6 - 7** represents **Executive** and **Multi-Generation** respectively.

### 3. Models

1. Linear Regression 
   Linear Regression is one of the simplest model to implement and we tried to use it as a baseline in terms of accuracy. On average for both Train and Test data, it had R² value of 0.68 and Root Mean Squared Error (RMSE) of 94000 which is bad. This was not the result we wanted, thus we decided to explore further on how we can improve these metrics.

2. Random Forest Regression 
   Random Forest is a type of Ensemble learning that combines the output of multiple decision trees to reach a single result and handles both classification and regression problems. This is the first time we utilise this model and while it did provide us with a tremendous improvement in R² (0.88), we decided to not use this model because we could not visualise the tree properly since the number of branches increased exponentially as the depth increased.

3. Polynomial Regression
   Upon further inspection, the relationship between resale price and its features were not really linear. Hence, we decided, why not we try to use a curve to represent this relationship? Similar to the previos model, we have not tried Polynomial Regression before.
   
   We started off with the basic degree 2 and realised a great improvement in R² (0.76) and RMSE (81000). However, we also realised that as the degree increased, the R² increased as well since the model will try to fit its curve to all the data points. But, the RMSE did not necessary increase in all cases. Furthermore, this increases the risk of over-fitting the model.

   To resolve the issue of retrieving the optimal degree for polynomial regression, we must first think of the goal for our results, that is too minimise the RMSE as much as possible because we already know that a higher degree yields a higher R² value. We implemented GridSearchCV to do the work for us. The parameters were set up as such, **Degree Range of 1 - 10** and **Scoring based on Negative RMSE**. The reason why it uses negative RMSE is such that the highest *"negative RMSE"* will be returned and that translates to the *lowest RMSE*, which is what we want.

   After a long **255 minutes and 28 seconds**, the optimal degree GridSearchCV returned was 5 and its corresponding RMSE is 72000. That is a whopping **20% decrease in RMSE** compared to Linear Regression!

### 4. Insights and Conclusions
   We chose to implement the Polynomial Regression of Degree 5 for the prediction of the resale prices. However, we did find some unexpected issues with the prediction.

- Negative Resale Price
   Interestingly, a negative resale price was possible! But how? It turns out we did not limit the user inputs. For instance, a 2 Room HDB Flat should not have a floor area (sqm) of 100. Thus, to reduce the risk of a negative resale price, we implemented additional constraints to ensure the user inputs are valid.

   To further minimise this risk, we utilised Polynomial Regression of Degree 4 as a backup model in case the Degree 5 model predicts a negative resale price. Additionally, the Linear Regression model serves as the final solution to the issue of negative resale price.

- Conclusion   
   Throughout this project, we have learnt tremendously, the importance of thinking outside the box, and the time and processing power needed to train a model or generate the optimal model. We hope that such models will be able to help individuals make a more informed decision such that they will be able to buy or sell their flats at a fair price and save the hassle of searching for the best price.