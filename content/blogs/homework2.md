---
title: "Data Visualisation Exercise"
date: "2023-06-21"
description: Some great data visualisation exercises in R
draft: no
image: datavistile.jpg
keywords: ''
slug: blog3
categories:
- ""
- ""
---



# Data Visualisation - Exploration

Now that you've demonstrated your software is setup, and you have the basics of data manipulation, the goal of this assignment is to practice transforming, visualising, and exploring data.

# Mass shootings in the US

In July 2012, in the aftermath of a mass shooting in a movie theater in Aurora, Colorado, [Mother Jones](https://www.motherjones.com/politics/2012/07/mass-shootings-map/) published a report on mass shootings in the United States since 1982. Importantly, they provided the underlying data set as [an open-source database](https://www.motherjones.com/politics/2012/12/mass-shootings-mother-jones-full-data/) for anyone interested in studying and understanding this criminal behavior.

## Obtain the data


```
## Rows: 125
## Columns: 14
## $ case                 <chr> "Oxford High School shooting", "San Jose VTA shoo…
## $ year                 <dbl> 2021, 2021, 2021, 2021, 2021, 2021, 2020, 2020, 2…
## $ month                <chr> "Nov", "May", "Apr", "Mar", "Mar", "Mar", "Mar", …
## $ day                  <dbl> 30, 26, 15, 31, 22, 16, 16, 26, 10, 6, 31, 4, 3, …
## $ location             <chr> "Oxford, Michigan", "San Jose, California", "Indi…
## $ summary              <chr> "Ethan Crumbley, a 15-year-old student at Oxford …
## $ fatalities           <dbl> 4, 9, 8, 4, 10, 8, 4, 5, 4, 3, 7, 9, 22, 3, 12, 5…
## $ injured              <dbl> 7, 0, 7, 1, 0, 1, 0, 0, 3, 8, 25, 27, 26, 12, 4, …
## $ total_victims        <dbl> 11, 9, 15, 5, 10, 9, 4, 5, 7, 11, 32, 36, 48, 15,…
## $ location_type        <chr> "School", "Workplace", "Workplace", "Workplace", …
## $ male                 <lgl> TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, T…
## $ age_of_shooter       <dbl> 15, 57, 19, NA, 21, 21, 31, 51, NA, NA, 36, 24, 2…
## $ race                 <chr> NA, NA, "White", NA, NA, "White", NA, "Black", "B…
## $ prior_mental_illness <chr> NA, "Yes", "Yes", NA, "Yes", NA, NA, NA, NA, NA, …
```

| column(variable)     | description                                                                 |
|--------------------------|----------------------------------------------|
| case                 | short name of incident                                                      |
| year, month, day     | year, month, day in which the shooting occurred                             |
| location             | city and state where the shooting occcurred                                 |
| summary              | brief description of the incident                                           |
| fatalities           | Number of fatalities in the incident, excluding the shooter                 |
| injured              | Number of injured, non-fatal victims in the incident, excluding the shooter |
| total_victims        | number of total victims in the incident, excluding the shooter              |
| location_type        | generic location in which the shooting took place                           |
| male                 | logical value, indicating whether the shooter was male                      |
| age_of_shooter       | age of the shooter when the incident occured                                |
| race                 | race of the shooter                                                         |
| prior_mental_illness | did the shooter show evidence of mental illness prior to the incident?      |

## Explore the data

### Specific questions

-   Generate a data frame that summarizes the number of mass shootings per year.



-   Generate a bar chart that identifies the number of mass shooters associated with each race category. The bars should be sorted from highest to lowest and each bar should show its number.



-   Generate a boxplot visualizing the number of total victims, by type of location.



-   Redraw the same plot, but remove the Las Vegas Strip massacre from the dataset.



### More open-ended questions

Address the following questions. Generate appropriate figures/tables to support your conclusions.

-   How many white males with prior signs of mental illness initiated a mass shooting after 2000?



-   Which month of the year has the most mass shootings? Generate a bar chart sorted in chronological (natural) order (Jan-Feb-Mar- etc) to provide evidence of your answer.



-   How does the distribution of mass shooting fatalities differ between White and Black shooters? What about White and Latino shooters?



### Very open-ended

-   Are mass shootings with shooters suffering from mental illness different from mass shootings with no signs of mental illness in the shooter?



-   Assess the relationship between mental illness and total victims, mental illness and location type, and the intersection of all three variables.



Make sure to provide a couple of sentences of written interpretation of your tables/figures. Graphs and tables alone will not be sufficient to answer this question.


# Deliverables

There is a lot of explanatory text, comments, etc. You do not need these, so delete them and produce a stand-alone document that you could share with someone. Knit the edited and completed R Markdown (qmd) file as a Word or HTML document (use the "Knit" button at the top of the script editor window) and upload it to Canvas. You must be comitting and pushing your changes to your own Github repo as you go along.

# Details

-   Who did you collaborate with: TYPE NAMES HERE
-   Approximately how much time did you spend on this problem set: ANSWER HERE
-   What, if anything, gave you the most trouble: ANSWER HERE

**Please seek out help when you need it,** and remember the [15-minute rule](https://dsb2023.netlify.app/syllabus/#the-15-minute-rule){target="_blank"}. You know enough R (and have enough examples of code from class and your readings) to be able to do this. If you get stuck, ask for help from others, post a question on Slack-- and remember that I am here to help too!

> As a true test to yourself, do you understand the code you submitted and are you able to explain it to someone else?

# Rubric

13/13: Problem set is 100% completed. Every question was attempted and answered, and most answers are correct. Code is well-documented (both self-documented and with additional comments as necessary). Used tidyverse, instead of base R. Graphs and tables are properly labelled. Analysis is clear and easy to follow, either because graphs are labeled clearly or you've written additional text to describe how you interpret the output. Multiple Github commits. Work is exceptional. I will not assign these often.

8/13: Problem set is 60--80% complete and most answers are correct. This is the expected level of performance. Solid effort. Hits all the elements. No clear mistakes. Easy to follow (both the code and the output). A few Github commits.

5/13: Problem set is less than 60% complete and/or most answers are incorrect. This indicates that you need to improve next time. I will hopefully not assign these often. Displays minimal effort. Doesn't complete all components. Code is poorly written and not documented. Uses the same type of plot for each graph, or doesn't use plots appropriate for the variables being analyzed. No Github commits.
