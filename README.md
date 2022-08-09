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

Go through the variables (numerical and categorical) one at a time. I started with categorical variables and explored them with bar plots/count plots, then moved on to numerical variables, taking a look at them with histograms and box plots.

## Step 4 - Bivariate Exploration

This is where we start to explore the relationships between two variables and see if we can find any correlation. I started with a heatmap, sorted the absolute values of the correlations and looked into the pairs with moderate-high (>0.5) correlation. I used stacked bar plots, scatter plots (regplot) with jitter and CDF plot for this section of the analysis.

## Step 5 - Multivariate Exploration

This section is brief, I only went as many as three variables at a time since anything beyond that starts to look chaotic.

## Conclusions - What are the answers to our questions?

- The profile of our average customer:
Lives in Spain with Master’s degree, coupled up, in their 40s-70s, earns 35–68k per year, has no or one child

- The most popular product types:

Our customers spend the most on wine by far, followed by meat;

Customers who spend a lot on meat likes to purchase meat via catalogue;

Customers with no kids tend to spend a lot on wine

- The most popular sales channels:

Customers prefer to make their purchases in store, followed by online (web). However it is alarming that higher website visits don’t result in higher web purchases. In fact there seems to be a slightly negative correlation between website visits and website purchases. This needs to be investigated by the business, there could be something wrong with our website

- Campaign performance:

Campaign 4 is the winning campaign, campaign 2 received poor results;

Campaign 3 seems to be very attractive to customers with basic education;

Customers with 1–2 kids or teens at home really liked campaign 3;

Customers with teens (at least one) at home really appreciated campaign 4

- Other findings:

A large portion of our customers have at least a Master’s degree, higher education level seems to be correlated to higher income, which makes me wonder if we as a business are not affordable for lower income customers;

Customers with no kids seem to have higher income, which means that they might fit our targeted profile better;

As a result, it might make sense for us to figure out how to tap into their disposable income (that they have no kids to spend on!) with our campaigns;

Finally one thought on recency, I didn’t find any correlation between any campaign and recency, but I think potential future campaigns that get inactive customers making purchases could be interesting.

