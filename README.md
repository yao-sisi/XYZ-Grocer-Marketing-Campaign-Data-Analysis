# Exploratory Data Analysis with Python

Data source: [Maven Analytics](https://www.mavenanalytics.io/data-playground?page=3&pageSize=5)

Marketing, social media and ad tech are among my favourite domains to do data analysis on. This is one of my first projects doing EDA with Python, so I chose a relatively smaller dataset (a few thousand rows).

In this project, I perform a complete EDA with my 6-step approach (that I learned from a few Kaggle notebooks as well as Medium articles like [this](https://towardsdatascience.com/my-6-part-powerful-eda-template-that-speaks-of-ultimate-skill-6bdde3c91431)).

## Step 1 - Asking Questions

This is where data analysis begins. Some of the questions I asked were: 
- Which campaign performed the best, which did the worst
- Which sales channel performs the best, which does the worst
- What kinds of products are doing well?

## Step 2 - Data Cleaning

This is where we clean our data and prepare it for the actual analysis
- What are the flaws in the data: Missing values? Values that don't make sense? Etc.
- Put all the items to be dealt with in a list after inspecting the dataset, then start cleaning the data item by item.
- Once an item is dealt with, use 'assert' to double check.

## Step 3 - Univariate Exploration

Go through the variables (numerical and categorical) one at a time.

## Step 4 - Bivariate Exploration

This is where we start to explore the relationships between two variables and see if we can find any correlation. I started with a heatmap, sorted the absolute values of the correlations and looked into the pairs with moderate-high (>0.5) correlation.

## Step 5 - Multivariate Exploration

This section is brief, I only went as many as three variables at a time since anything beyond that starts to look chaotic.

## Conclusions - What are the answers to our questions?

Summing up the findings from the previous steps and answer the questions we asked at the beginning, full circle!

