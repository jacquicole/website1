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


```r
#creating a dataframe of the frequency (n) of mass shootings per year and saving it as msperyear.
df_msperyear <- mass_shootings %>%
count(year)
```

-   Generate a bar chart that identifies the number of mass shooters associated with each race category. The bars should be sorted from highest to lowest and each bar should show its number.


```r
#Read in data and filter out entries for race that are not applicable (NA)
mass_shootings %>%
  filter(!is.na(race)) %>%
#Produce a frequency metric, n, for the number of times a mass shooter is of a specific race
  count(race) %>%
#Plot the result, ordering the race from highest to lowest frequency of occurrence.
ggplot(mapping = aes(reorder(race, -n, sum),n)) + geom_col() +labs(title = "Relationship between Mass Shooters and Race", subtitle = "Frequency of mass shootings in the US by shooter's race", x = "Race of shooter", y = "Frequency")
```

<img src="/blogs/homework2_files/figure-html/unnamed-chunk-4-1.png" width="672" />

-   Generate a boxplot visualizing the number of total victims, by type of location.


```r
# I produce a boxplot to visualise the number of total victims of mass shootings in the US by type of location. The code and plot are given below.
  ggplot(data = mass_shootings, mapping = aes(location_type,total_victims)) + geom_boxplot() + labs(x = "Location Type", y = "Total number of victims", title = "Victim Numbers by Mass-Shooting Location", subtitle = "Variation in victim count with mass-shooting location type")
```

<img src="/blogs/homework2_files/figure-html/unnamed-chunk-5-1.png" width="672" />

-   Redraw the same plot, but remove the Las Vegas Strip massacre from the dataset.


```r
#I note that there is a extreme data outlier in the location_type 'other' category and that its particularly high total number of victims skews the presentation of the plot. There are also two other much larger data points in other. In order that the rest of the data show up in good proportioning, I identify these three data points in order to consider removing them and then replot the trend:
   mass_shootings %>%
     count(total_victims) %>%
     arrange(desc(total_victims))
```

```
## # A tibble: 38 × 2
##    total_victims     n
##            <dbl> <int>
##  1           604     1
##  2           102     1
##  3            82     1
##  4            55     1
##  5            48     1
##  6            46     1
##  7            44     2
##  8            41     1
##  9            37     1
## 10            36     1
## # ℹ 28 more rows
```

```r
#This produces a table where the three highest values can be easily identified. I first filter out the largest data outlier and replot the trend:
   
   mass_shootings %>%
     filter(total_victims != 604) %>%
   ggplot(mapping = aes(location_type,total_victims)) + geom_boxplot() + labs(x = "Location Type", y = "Total number of victims", title = "Victim Numbers by Mass-Shooting Location", subtitle = "Variation in victim count with mass-shooting location type", caption = "*'Other' data with 604 victims from the Las Vegas Strip massacre are not shown")
```

<img src="/blogs/homework2_files/figure-html/unnamed-chunk-6-1.png" width="672" />

```r
#I then filter out the other two large 'Other' data outliers and then replot the trend in order to obtain even better data proportioning of the results:
   mass_shootings %>%
     filter(total_victims != 604) %>%
     filter(total_victims != 102) %>%
     filter(total_victims != 82) %>%
   ggplot(mapping = aes(location_type,total_victims)) + geom_boxplot() + labs(x = "Location Type", y = "Total number of victims", title = "Victim Numbers by Mass-Shooting Location", subtitle = "Variation in victim count with mass-shooting location type", caption = "*Three 'Other' data, with 604, 102 and 82 victims, are not shown, for clarity.")
```

<img src="/blogs/homework2_files/figure-html/unnamed-chunk-6-2.png" width="672" />

### More open-ended questions

Address the following questions. Generate appropriate figures/tables to support your conclusions.

-   How many white males with prior signs of mental illness initiated a mass shooting after 2000?


```r
# There are 125 entries of mass shootings in this database. I filter these down to those that occurred after 2000, where the shooter is white and had shown signs of prior mental illness. This produces a tibble of 23 entries.

mass_shootings %>%
  filter(year > 2000, race == "White", prior_mental_illness == "Yes")
```

```
## # A tibble: 23 × 14
##    case       year month   day location summary fatalities injured total_victims
##    <chr>     <dbl> <chr> <dbl> <chr>    <chr>        <dbl>   <dbl>         <dbl>
##  1 FedEx wa…  2021 Apr      15 Indiana… "Brand…          8       7            15
##  2 Odessa-M…  2019 Aug      31 Odessa,… "Seth …          7      25            32
##  3 SunTrust…  2019 Jan      23 Sebring… "Zephe…          5       0             5
##  4 Waffle H…  2018 Apr      22 Nashvil… "Travi…          4       4             8
##  5 Marjory …  2018 Feb      14 Parklan… "Nikol…         17      17            34
##  6 Texas Fi…  2017 Nov       5 Sutherl… "Devin…         26      20            46
##  7 Rural Oh…  2017 May      12 Kirkers… "Thoma…          3       0             3
##  8 Isla Vis…  2014 May      23 Santa B… "Ellio…          6      13            19
##  9 Santa Mo…  2013 Jun       7 Santa M… "John …          6       3             9
## 10 Sandy Ho…  2012 Dec      14 Newtown… "Adam …         27       2            29
## # ℹ 13 more rows
## # ℹ 5 more variables: location_type <chr>, male <lgl>, age_of_shooter <dbl>,
## #   race <chr>, prior_mental_illness <chr>
```

```r
#i.e. There are 23 white males with prior signs of mental illness initiated a mass shooting after 2000.
```

-   Which month of the year has the most mass shootings? Generate a bar chart sorted in chronological (natural) order (Jan-Feb-Mar- etc) to provide evidence of your answer.


```r
mass_shootings %>%
  count(month) %>%
  ggplot(mapping = aes(x = month, y = n)) + geom_col() + scale_x_discrete(limits = month.abb) + labs(title = "When are mass shootings more common?", subtitle = "No. of mass shootings n versus their monthly occurrence")
```

<img src="/blogs/homework2_files/figure-html/unnamed-chunk-8-1.png" width="672" />

```r
#This bar plot reveals that mass shootings in the US most commonly occur during the month of February. I now produce a data fram that shows the explicit number that occur per month (revealing that this is 13 in February):

monthlyvariation <- mass_shootings %>%
  count(month) %>%
  arrange(desc(n)) 
monthlyvariation
```

```
## # A tibble: 12 × 2
##    month     n
##    <chr> <int>
##  1 Feb      13
##  2 Jun      12
##  3 Mar      12
##  4 Nov      12
##  5 Apr      11
##  6 Dec      11
##  7 Oct      11
##  8 Jul      10
##  9 Sep      10
## 10 Aug       8
## 11 May       8
## 12 Jan       7
```

-   How does the distribution of mass shooting fatalities differ between White and Black shooters? What about White and Latino shooters?


```r
mass_shootings %>%
  filter (!is.na(race)) %>%
  count(fatalities,race) %>%
  group_by(fatalities,n,race) %>%
  ggplot() + (aes(x = fatalities, y = n)) + geom_col() + facet_wrap(vars(race)) + labs(title = "How Shooter's Race Affects their Killing Tally?", subtitle = "Frequency distribution of fatalities per shooter's race", x = "Number of fatalities", y = "Frequency") 
```

<img src="/blogs/homework2_files/figure-html/unnamed-chunk-9-1.png" width="672" />

```r
#This set of plots reveal that white, black and Latino shooters track a similar killing distribution profile. There are many fewer cases of black and Latino shooters, but the max point in the distribution appears to be similar between all three cases while the distribution for white shooters seems to have a longer tail at the upper boundary; this suggests that a small proportion of white shooters may shoot many more than those from another race (although it is difficult to make any concrete conclusions as the sample is small). We can calculate the mean to assist although these values not surprisingly vary because of the small samples involved (so we cannot regard these as representative except for the overall mean and the mean for white shooters - which are similar):

#Determining the overall mean (= 8)

mass_shootings %>%
  filter(!is.na(fatalities)) %>%
  summarise(meanfat = mean(fatalities)) 
```

```
## # A tibble: 1 × 1
##   meanfat
##     <dbl>
## 1       8
```

```r
#Determining the mean for white shooters (= 8.8)

mass_shootings %>%
  filter(!is.na(fatalities)) %>%
  filter(race == "White") %>%
  summarise(meanfat = mean(fatalities)) 
```

```
## # A tibble: 1 × 1
##   meanfat
##     <dbl>
## 1    8.78
```

```r
#Determining the mean for black shooters (= 5.6)
mass_shootings %>%
  filter(!is.na(fatalities)) %>%
  filter(race == "Black") %>%
  summarise(meanfat = mean(fatalities)) 
```

```
## # A tibble: 1 × 1
##   meanfat
##     <dbl>
## 1    5.57
```

```r
#Determining the mean for latino shooters (= 4.4)
mass_shootings %>%
  filter(!is.na(fatalities)) %>%
  filter(race == "Latino") %>%
  summarise(meanfat = mean(fatalities)) 
```

```
## # A tibble: 1 × 1
##   meanfat
##     <dbl>
## 1     4.4
```

### Very open-ended

-   Are mass shootings with shooters suffering from mental illness different from mass shootings with no signs of mental illness in the shooter?


```r
mass_shootings %>%
  filter (!is.na(prior_mental_illness)) %>%
  group_by(total_victims,prior_mental_illness) %>%
  ggplot(mapping = aes(total_victims)) + geom_bar() + facet_wrap(vars(prior_mental_illness)) + labs(x = "Number of Total Victims", y = "Frequency", title = "Shooter's Mental State Results in More Victims", subtitle = "Shooter's Prior Mental Illness (Yes/No) vs No. of Victims")
```

<img src="/blogs/homework2_files/figure-html/unnamed-chunk-10-1.png" width="672" />

```r
# This plot shows that there are many more shooters who have a priori known signs of mental illness than there are otherwise. The max number of victims that they affect is the same in each case. There is a greater tail of more victims in the case where a mental illness is known in the shooter prior to the shooting.
```

-   Assess the relationship between mental illness and total victims, mental illness and location type, and the intersection of all three variables.


```r
#I then explore the relative distribution of total victims versus fatalities in shootings and see if there is a difference between those who are killed or injured as a function of whether or not the shooter showed any prior signs of mental illness. I plot the total victims (green) versus fatalities (dark blue) on each of the two faceted plots that partitions those showing prior signs of mental illness ("Yes") and those that did not. The code and graph are below. The results show that the relative proportions of fatalities and injured victims are similar. However, there is a slightly higher proportion of fatalities if the shooter has no priori mental health issues, suggesting that they kill fewer people but that their victims are more targeted (and thus die); contrast this with the more sparse profile of victims when a mental health issue is known prior to the shooting - where there are more victims but the attack is more indiscriminate as there are more gaps in the histogram between fatalities (as opposed to injured). This said, it is difficult to judge entirely because the statistics of the 'No' data are not so many.

mass_shootings %>%
  filter (!is.na(prior_mental_illness)) %>%
  group_by(total_victims,fatalities,prior_mental_illness) %>%
  ggplot(mapping = aes(x = total_victims, y = fatalities, fill = fatalities)) + geom_bar(stat="identity", colour = "green") + facet_wrap(vars(prior_mental_illness)) + labs(x = "Number of Total Victims (Green) and Fatalities (Black)", y = "Frequency", title = "Mentally Ill Shooters Cause More Fatalities", subtitle = "Shooter's Prior Mental Illness (Y/N) vs State of Victim")
```

<img src="/blogs/homework2_files/figure-html/unnamed-chunk-11-1.png" width="672" />

```r
#I now explore possible correlation between mental health and location type using similar code and visualisation:

mass_shootings %>%
  filter (!is.na(prior_mental_illness)) %>%
  group_by(location_type,prior_mental_illness) %>%
  ggplot(mapping = aes(location_type)) + geom_bar() + facet_wrap(vars(prior_mental_illness)) + theme(axis.text = element_text(size=5)) + labs(x = "Type of Location where the Shooting Occurred", y = "Frequency", title = "Shooter's Mental State Affects Location of Attack", subtitle = "Shooter's Prior Mental Illness (Y/N) vs Shooting Location")
```

<img src="/blogs/homework2_files/figure-html/unnamed-chunk-11-2.png" width="672" />

```r
#This plot shows that the school, workplace or other location types are shooting venues with the same proportion, while only shooters with prior signs of mental illness appear to target religious, military and airport locations.

#I then explore possible correlation between the three variables: prior_mental_illness, location_type and total_victims:

mass_shootings %>%
  filter (!is.na(prior_mental_illness)) %>%
  group_by(total_victims,location_type,prior_mental_illness) %>%
  ggplot(mapping = aes(x = total_victims)) + geom_histogram() + facet_grid(rows = vars(location_type), cols = vars(prior_mental_illness)) + theme(axis.text = element_text(size=5),strip.text.y = element_text (size = 5),plot.subtitle = element_text(size=5)) + labs(x = "Number of Total Victims", y = "Frequency", title = "Shooter's Mental State Controls Venue & Victims", subtitle = "Correlating Shooter's Prior Mental Illness (Yes/No) with Shooter's Choice of Location and the Number of Victims Incurred")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

<img src="/blogs/homework2_files/figure-html/unnamed-chunk-11-3.png" width="672" />

```r
#These results show that the Workplace and 'Other' are the worst types of locations for mass shootings that result in the greatest number of victims. The maximum number of victims in each case lies in the same bin of 8-10 in these histograms, irrespective of the mental state of the shooter, or the location type. Schools are the next most frequent type of location for a mass shooting in the US, with fewer victims overall but a much wider statistical variation of the number of victims for a given shooting than any other location type except for Other. One might interpret this in terms of US school staff being briefed in what to do in a mass shooting but the number of victims varying a lot because of the more unpredictable actions of a child when placed in the path of a shooter. However, we cannot make this interpretation with too much conviction as we don't have any corroboratory evidence beyond these distributions. There are few instances of shootings at religious, military and airport locations and those that exist occur exclusively when the shooter has shown prior signs of mental illness. 
```

Make sure to provide a couple of sentences of written interpretation of your tables/figures. Graphs and tables alone will not be sufficient to answer this question.

