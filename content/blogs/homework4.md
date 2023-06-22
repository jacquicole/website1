---
title: "Machine Learning Exercise"
date: "2023-06-21"
description: A classification problem
draft: no
image: machine-learning-image.jpg
keywords: ''
slug: blog2
categories:
- ''
- ''
---




# The Bechdel Test

<https://fivethirtyeight.com/features/the-dollar-and-cents-case-against-hollywoods-exclusion-of-women/>

The [Bechdel test](https://bechdeltest.com) is a way to assess how women are depicted in Hollywood movies. In order for a movie to pass the test:

1.  It has to have at least two [named] women in it
2.  Who talk to each other
3.  About something besides a man

There is a nice article and analysis you can find here <https://fivethirtyeight.com/features/the-dollar-and-cents-case-against-hollywoods-exclusion-of-women/> We have a sample of 1394 movies and we want to fit a model to predict whether a film passes the test or not.


```r
bechdel <- read_csv(here::here("data", "bechdel.csv")) %>% 
  mutate(test = factor(test)) 
```

```
## Rows: 1394 Columns: 10
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (4): title, test, rated, genre
## dbl (6): year, budget_2013, domgross_2013, intgross_2013, metascore, imdb_ra...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
glimpse(bechdel)
```

```
## Rows: 1,394
## Columns: 10
## $ year          <dbl> 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013, 20…
## $ title         <chr> "12 Years a Slave", "2 Guns", "42", "47 Ronin", "A Good …
## $ test          <fct> Fail, Fail, Fail, Fail, Fail, Pass, Pass, Fail, Pass, Pa…
## $ budget_2013   <dbl> 2.00, 6.10, 4.00, 22.50, 9.20, 1.20, 1.30, 13.00, 4.00, …
## $ domgross_2013 <dbl> 5.3107035, 7.5612460, 9.5020213, 3.8362475, 6.7349198, 1…
## $ intgross_2013 <dbl> 15.8607035, 13.2493015, 9.5020213, 14.5803842, 30.424919…
## $ rated         <chr> "R", "R", "PG-13", "PG-13", "R", "R", "PG-13", "PG-13", …
## $ metascore     <dbl> 97, 55, 62, 29, 28, 55, 48, 33, 90, 58, 52, 78, 83, 53, …
## $ imdb_rating   <dbl> 8.3, 6.8, 7.6, 6.6, 5.4, 7.8, 5.7, 5.0, 7.5, 7.4, 6.2, 7…
## $ genre         <chr> "Biography", "Action", "Biography", "Action", "Action", …
```

How many films fail/pass the test, both as a number and as a %?


```r
bechdel %>% #read in dataset of movies
  count(test) %>% #calculate proportion of movies that fail/pass the test
  mutate(percentfail = 100*n/sum(n)) #calculate corresponding percentage
```

```
## # A tibble: 2 × 3
##   test      n percentfail
##   <fct> <int>       <dbl>
## 1 Fail    772        55.4
## 2 Pass    622        44.6
```

```r
#The answers are found in the table below:
```

## Movie scores


```r
ggplot(data = bechdel, aes(
  x = metascore,
  y = imdb_rating,
  colour = test
)) +
  geom_point(alpha = .3, size = 3) +
  scale_colour_manual(values = c("tomato", "olivedrab")) +
  labs(
    x = "Metacritic score",
    y = "IMDB rating",
    colour = "Bechdel test"
  ) +
 theme_light()
```

<img src="/blogs/homework4_files/figure-html/unnamed-chunk-3-1.png" width="672" />

# Split the data


```r
# **Split the data**

set.seed(123)

data_split <- initial_split(bechdel, # updated data
                           prop = 0.8, 
                           strata = test)

bechdel_train <- training(data_split) 
bechdel_test <- testing(data_split)
```

Check the counts and % (proportions) of the `test` variable in each set.


```r
bechdel_train %>% #read in training dataset of movies
  count(test) %>% #calculate proportion of movies that fail/pass the test
  mutate(percentfail = 100*n/sum(n)) #calculate corresponding percentage
```

```
## # A tibble: 2 × 3
##   test      n percentfail
##   <fct> <int>       <dbl>
## 1 Fail    617        55.4
## 2 Pass    497        44.6
```

The training data set has the following counts and proportion of Fail/Pass:



```r
bechdel_test %>% #read in test dataset of movies
  count(test) %>% #calculate proportion of movies that fail/pass the test
  mutate(percentfail = 100*n/sum(n)) #calculate corresponding percentage
```

```
## # A tibble: 2 × 3
##   test      n percentfail
##   <fct> <int>       <dbl>
## 1 Fail    155        55.4
## 2 Pass    125        44.6
```

```r
#The training data set has the following counts and proportion of Fail/Pass:
```

## Feature exploration

## Any outliers?


```r
bechdel %>% 
  select(test, budget_2013, domgross_2013, intgross_2013, imdb_rating, metascore) %>% 

    pivot_longer(cols = 2:6,
               names_to = "feature",
               values_to = "value") %>% 
  ggplot()+
  aes(x=test, y = value, fill = test)+
  coord_flip()+
  geom_boxplot()+
  facet_wrap(~feature, scales = "free")+
  theme_bw()+
  theme(legend.position = "none")+
  labs(x=NULL,y = NULL) 
```

<img src="/blogs/homework4_files/figure-html/unnamed-chunk-7-1.png" width="672" />

There are no massive outliers, although the maximum value of budget 2013 seems to be disproportionately larger than others. So I find the maximum value of this outlier:


```r
 bechdel %>%
  summarise(maxi = max(budget_2013)) 
```

```
## # A tibble: 1 × 1
##    maxi
##   <dbl>
## 1  46.1
```

The identity of this outlier is deduced by:


```r
 bechdel %>%
 filter (budget_2013 > 46)    
```

```
## # A tibble: 1 × 10
##    year title  test  budget_2013 domgross_2013 intgross_2013 rated metascore
##   <dbl> <chr>  <fct>       <dbl>         <dbl>         <dbl> <chr>     <dbl>
## 1  2009 Avatar Fail         46.1          82.6          302. PG-13        83
## # ℹ 2 more variables: imdb_rating <dbl>, genre <chr>
```

The outlier is Avatar. I note that its domestic gross (domgross_2013 is also the maximum value of that field (302), but it is not the max value of the intgross (although it is high at 82.5)). Overall, when considered over the full range of the variables plotted in the above boxplots, it is not an outlier in all regards. So, I keep it in the dataset in going forward. Though I just note its disproportionately large value for a few of its variables.


## Scatterplot - Correlation Matrix

Write a paragraph discussing the output of the following


```r
bechdel %>% 
  select(test, budget_2013, domgross_2013, intgross_2013, imdb_rating, metascore)%>% 
  ggpairs(aes(colour=test), alpha=0.2)+
  theme_bw()
```

<img src="/blogs/homework4_files/figure-html/unnamed-chunk-10-1.png" width="672" />

The output of the following correlation matrix tests reveal that:

(a) the variables have quite different distributions
- metascore is somewhere between a trianglular and normal distribution;
- imbb_rating appears to be a fairly normal distribution albeit with a small left skew;
-the budget_2013, domgross_2013 and intgross_2013 variables show a near Voigt function that is cut off on the left and which is highly right skewed, as most data are of low values.

(b) The test data (Fail and Pass) seem to be fairly even in number and the distribution of Fail and Pass data, as judged by the similar profiles showing in the plots that comprise the left hand column below the test histogram. This evenness is important because it shows that the test data are balanced when it comes to a machine-learning application. A corrolaary to this is t also shows that a random partitioning of the data into a training and testing dataset should result in a balanced set of split datasets.

(c) The correlation values for the total datasets (in black font) show that there are significant levels of correlation between the following variables:

- 52% correlation between domgross_2013 and budget_2013
- 62% correlation between intgross_2013 and budget_2013
- 94% correlation between intgross_2013 and domgross_2013
- 74% correlation between metascore and imdb_rating

This makes intuitive sense because one would hope that the larger the budget for a film, the more that the film will profit in both domestic and international gross. As a corrolary, one would expect a correlation between domestic and international gross, assuming that most films are international (as hollywood films tend to be).

Meanwhile, one would intuitively expect there to be a correlation in ratings: the imdb_rating and the metascore in this case.

Note that there are no correlations between the scoring-based variables and the budget variable, which means that the audience appreciation level is independent of the budget spent on the film!

These ratings are slightly correlated to the gross profits of the films which suggests that customers choose to see big budget films a bit more than others, perhaps expecting a good experience. However, the lack of correlation between ratings and budget suggest that the audience is occasionally let down by a big budget film.

There is little variation between the correlations of the total data versus those that Pass or Fail these 3 criteria. However, there are a few observable differences: e.g. domgross_2013 and budget_2013 are slightly less correlated if the criteria are not met (Fail) and slightly more correlated if the criteria are met.
There is also a slightly lower correlation between film ratings/scores and gross profits of films if the criteria are met (Pass) than if they are not met (Fail).

This all means that there appears to be a modest but statistically significant relationship in movies and these criteria being met.


## Categorical variables

Write a paragraph discussing the output of the following


```r
bechdel %>% 
  group_by(genre, test) %>%
  summarise(n = n()) %>% 
  mutate(prop = n/sum(n))
```

```
## `summarise()` has grouped output by 'genre'. You can override using the
## `.groups` argument.
```

```
## # A tibble: 24 × 4
## # Groups:   genre [14]
##    genre     test      n  prop
##    <chr>     <fct> <int> <dbl>
##  1 Action    Fail    260 0.707
##  2 Action    Pass    108 0.293
##  3 Adventure Fail     52 0.559
##  4 Adventure Pass     41 0.441
##  5 Animation Fail     63 0.677
##  6 Animation Pass     30 0.323
##  7 Biography Fail     36 0.554
##  8 Biography Pass     29 0.446
##  9 Comedy    Fail    138 0.427
## 10 Comedy    Pass    185 0.573
## # ℹ 14 more rows
```

```r
bechdel %>% 
  group_by(rated, test) %>%
  summarise(n = n()) %>% 
  mutate(prop = n/sum(n))
```

```
## `summarise()` has grouped output by 'rated'. You can override using the
## `.groups` argument.
```

```
## # A tibble: 10 × 4
## # Groups:   rated [5]
##    rated test      n  prop
##    <chr> <fct> <int> <dbl>
##  1 G     Fail     16 0.615
##  2 G     Pass     10 0.385
##  3 NC-17 Fail      5 0.833
##  4 NC-17 Pass      1 0.167
##  5 PG    Fail    115 0.561
##  6 PG    Pass     90 0.439
##  7 PG-13 Fail    283 0.529
##  8 PG-13 Pass    252 0.471
##  9 R     Fail    353 0.568
## 10 R     Pass    269 0.432
```

This output shows that a greater proportion of movies meet the aforementioned
three criteria whatever the movie rating. However, the proportion differs depending upon the rating: whereby there are two ratings with notable differences between Pass/Fail:

- a G rating shows a significantly higher proportion (62:38) of movies that fail these criteria; perhaps this is because many animations would fall into the G rating category which don't so often have gender specific characters portrayed.

- a NC-17 rating whereby such movies are categorised as showing highly sexualised content. This has the highest (by some margin) disparity between proportions (83:17) of movies that fail the subject criteria. That said, this category contains the least number of movies (6 in total) so one could argue that the sample is too small; although 5:1 is still compelling given the distinctive categorisation of the movie rating and how this aligns with the female focused nature of the criteria.

The other ratings have only a modest difference between Fail/Pass in the criteria with a representative (large) number of movies in the sample (the largest disparity amongst all 3 rating types (R, PG-13, PG)) being 56:44


# Train first models. `test ~ metascore + imdb_rating`


```r
lr_mod <- logistic_reg() %>% 
  set_engine(engine = "glm") %>% 
  set_mode("classification")

lr_mod
```

```
## Logistic Regression Model Specification (classification)
## 
## Computational engine: glm
```

```r
tree_mod <- decision_tree() %>% 
  set_engine(engine = "C5.0") %>% 
  set_mode("classification")

tree_mod 
```

```
## Decision Tree Model Specification (classification)
## 
## Computational engine: C5.0
```


```r
lr_fit <- lr_mod %>% # parsnip model
  fit(test ~ metascore + imdb_rating, # a formula
    data = bechdel_train # dataframe
  )

tree_fit <- tree_mod %>% # parsnip model
  fit(test ~ metascore + imdb_rating, # a formula
    data = bechdel_train # dataframe
  )
```

## Logistic regression


```r
lr_fit %>%
  broom::tidy()
```

```
## # A tibble: 3 × 5
##   term        estimate std.error statistic  p.value
##   <chr>          <dbl>     <dbl>     <dbl>    <dbl>
## 1 (Intercept)   2.80     0.494        5.68 1.35e- 8
## 2 metascore     0.0207   0.00536      3.86 1.13e- 4
## 3 imdb_rating  -0.625    0.100       -6.24 4.36e-10
```

```r
lr_preds <- lr_fit %>%
  augment(new_data = bechdel_train) %>%
  mutate(.pred_match = if_else(test == .pred_class, 1, 0))
```

### Confusion matrix


```r
lr_preds %>% 
  conf_mat(truth = test, estimate = .pred_class) %>% 
  autoplot(type = "heatmap")
```

<img src="/blogs/homework4_files/figure-html/unnamed-chunk-15-1.png" width="672" />

## Decision Tree


```r
tree_preds <- tree_fit %>%
  augment(new_data = bechdel) %>%
  mutate(.pred_match = if_else(test == .pred_class, 1, 0)) 
```


```r
tree_preds %>% 
  conf_mat(truth = test, estimate = .pred_class) %>% 
  autoplot(type = "heatmap")
```

<img src="/blogs/homework4_files/figure-html/unnamed-chunk-17-1.png" width="672" />

## Draw the decision tree


```r
draw_tree <- 
    rpart::rpart(
        test ~ metascore + imdb_rating,
        data = bechdel_train, # uses data that contains both birth weight and `low`
        control = rpart::rpart.control(maxdepth = 5, cp = 0, minsplit = 10)
    ) %>% 
    partykit::as.party()
plot(draw_tree)
```

<img src="/blogs/homework4_files/figure-html/unnamed-chunk-18-1.png" width="672" />

# Cross Validation

Run the code below. What does it return?


```r
set.seed(123)
bechdel_folds <- vfold_cv(data = bechdel_train, 
                          v = 10, 
                          strata = test)
bechdel_folds
```

```
## #  10-fold cross-validation using stratification 
## # A tibble: 10 × 2
##    splits             id    
##    <list>             <chr> 
##  1 <split [1002/112]> Fold01
##  2 <split [1002/112]> Fold02
##  3 <split [1002/112]> Fold03
##  4 <split [1002/112]> Fold04
##  5 <split [1002/112]> Fold05
##  6 <split [1002/112]> Fold06
##  7 <split [1002/112]> Fold07
##  8 <split [1004/110]> Fold08
##  9 <split [1004/110]> Fold09
## 10 <split [1004/110]> Fold10
```

```r
# This code returns the labelling of a 10-fold cross-validation.
```

## `fit_resamples()`

Trains and tests a resampled model.


```r
lr_fit <- lr_mod %>%
  fit_resamples(
    test ~ metascore + imdb_rating,
    resamples = bechdel_folds
  )


tree_fit <- tree_mod %>%
  fit_resamples(
    test ~ metascore + imdb_rating,
    resamples = bechdel_folds
  )
```

## `collect_metrics()`

Unnest the metrics column from a tidymodels `fit_resamples()`


```r
collect_metrics(lr_fit)
```

```
## # A tibble: 2 × 6
##   .metric  .estimator  mean     n std_err .config             
##   <chr>    <chr>      <dbl> <int>   <dbl> <chr>               
## 1 accuracy binary     0.575    10  0.0149 Preprocessor1_Model1
## 2 roc_auc  binary     0.606    10  0.0189 Preprocessor1_Model1
```

```r
collect_metrics(tree_fit)
```

```
## # A tibble: 2 × 6
##   .metric  .estimator  mean     n std_err .config             
##   <chr>    <chr>      <dbl> <int>   <dbl> <chr>               
## 1 accuracy binary     0.571    10  0.0156 Preprocessor1_Model1
## 2 roc_auc  binary     0.547    10  0.0201 Preprocessor1_Model1
```


```r
tree_preds <- tree_mod %>% 
  fit_resamples(
    test ~ metascore + imdb_rating, 
    resamples = bechdel_folds,
    control = control_resamples(save_pred = TRUE) #<<
  )

# What does the data for ROC look like?
tree_preds %>% 
  collect_predictions() %>% 
  roc_curve(truth = test, .pred_Fail)  
```

```
## # A tibble: 29 × 3
##    .threshold specificity sensitivity
##         <dbl>       <dbl>       <dbl>
##  1   -Inf         0             1    
##  2      0.262     0             1    
##  3      0.317     0.00201       0.989
##  4      0.373     0.00805       0.982
##  5      0.440     0.0181        0.976
##  6      0.459     0.0443        0.943
##  7      0.460     0.0765        0.924
##  8      0.464     0.115         0.901
##  9      0.465     0.147         0.887
## 10      0.465     0.191         0.864
## # ℹ 19 more rows
```

```r
# Draw the ROC
tree_preds %>% 
  collect_predictions() %>% 
  roc_curve(truth = test, .pred_Fail) %>% 
  autoplot()
```

<img src="/blogs/homework4_files/figure-html/unnamed-chunk-22-1.png" width="672" />

```r
# The data for ROC show that the model is not very accurate at all, because the True Positive and True Negatives (matrix diagonal) in the confusion matrix are not very distinguished from the False Negatives and False Positives (off diagonals). Indeed, the results are barely above the dotted line (x = y) which would be the case for data from random guessing.
```

# Build a better training set with `recipes`

## Preprocessing options

-   Encode categorical predictors
-   Center and scale variables
-   Handle class imbalance
-   Impute missing data
-   Perform dimensionality reduction
-   ... ...

## To build a recipe

1.  Start the `recipe()`
2.  Define the variables involved
3.  Describe **prep**rocessing [step-by-step]

## Collapse Some Categorical Levels

Do we have any `genre` with few observations? Assign genres that have less than 3% to a new category 'Other'


```r
bechdel %>% #reading in the full dataset
  count(genre) %>% # looking at the distribution of genres
  mutate(m = 100*n/sum(n)) %>% # calculating the genre proportion
  filter (m < 3) # identifying the genres that appear less than 3 percent.
```

```
## # A tibble: 6 × 3
##   genre           n      m
##   <chr>       <int>  <dbl>
## 1 Documentary     3 0.215 
## 2 Fantasy         6 0.430 
## 3 Musical         1 0.0717
## 4 Mystery        12 0.861 
## 5 Sci-Fi          5 0.359 
## 6 Thriller        3 0.215
```

```r
#
# The resulting table below identifies the six genre categories that make up less than 3 percent of the dataset.
```

<img src="/blogs/homework4_files/figure-html/unnamed-chunk-24-1.png" width="672" />


```r
movie_rec <-
  recipe(test ~ .,
         data = bechdel_train) %>%
  
  # Genres with less than 5% will be in a catewgory 'Other'
    step_other(genre, threshold = .03) 
```

## Before recipe


```
## # A tibble: 14 × 2
##    genre           n
##    <chr>       <int>
##  1 Action        293
##  2 Comedy        254
##  3 Drama         213
##  4 Adventure      75
##  5 Animation      72
##  6 Crime          68
##  7 Horror         68
##  8 Biography      50
##  9 Mystery         7
## 10 Fantasy         5
## 11 Sci-Fi          3
## 12 Thriller        3
## 13 Documentary     2
## 14 Musical         1
```

## After recipe


```r
movie_rec %>% 
  prep() %>% 
  bake(new_data = bechdel_train) %>% 
  count(genre, sort = TRUE)
```

```
## # A tibble: 9 × 2
##   genre         n
##   <fct>     <int>
## 1 Action      293
## 2 Comedy      254
## 3 Drama       213
## 4 Adventure    75
## 5 Animation    72
## 6 Crime        68
## 7 Horror       68
## 8 Biography    50
## 9 other        21
```

## `step_dummy()`

Converts nominal data into numeric dummy variables


```r
movie_rec <- recipe(test ~ ., data = bechdel) %>%
  step_other(genre, threshold = .03) %>% 
  step_dummy(all_nominal_predictors()) 

movie_rec 
```

```
## 
```

```
## ── Recipe ──────────────────────────────────────────────────────────────────────
```

```
## 
```

```
## ── Inputs
```

```
## Number of variables by role
```

```
## outcome:   1
## predictor: 9
```

```
## 
```

```
## ── Operations
```

```
## • Collapsing factor levels for: genre
```

```
## • Dummy variables from: all_nominal_predictors()
```

## Let's think about the modelling

What if there were no films with `rated` NC-17 in the training data?


```r
#Then, the model will not consider this NC-17 rating.
```

-   Will the model have a coefficient for `rated` NC-17?


```r
# The model will not have any coefficient for rated NC-17.
```

-   What will happen if the test data includes a film with `rated` NC-17?


```r
# The model will presumably ignore the rated NC-17 class of data.
```

## `step_novel()`

Adds a catch-all level to a factor for any new values not encountered in model training, which lets R intelligently predict new levels in the test set.


```r
movie_rec <- recipe(test ~ ., data = bechdel) %>%
  step_other(genre, threshold = .03) %>% 
  step_novel(all_nominal_predictors) %>% # Use *before* `step_dummy()` so new level is dummified
  step_dummy(all_nominal_predictors()) 
```

## `step_zv()`

Intelligently handles zero variance variables (variables that contain only a single value)


```r
movie_rec <- recipe(test ~ ., data = bechdel) %>%
  step_other(genre, threshold = .03) %>% 
  step_novel(all_nominal(), -all_outcomes()) %>% # Use *before* `step_dummy()` so new level is dummified
  step_dummy(all_nominal(), -all_outcomes()) %>% 
  step_zv(all_numeric(), -all_outcomes()) 
```

## `step_normalize()`

Centers then scales numeric variable (mean = 0, sd = 1)


```r
movie_rec <- recipe(test ~ ., data = bechdel) %>%
  step_other(genre, threshold = .03) %>% 
  step_novel(all_nominal(), -all_outcomes()) %>% # Use *before* `step_dummy()` so new level is dummified
  step_dummy(all_nominal(), -all_outcomes()) %>% 
  step_zv(all_numeric(), -all_outcomes())  %>% 
  step_normalize(all_numeric()) 
```

## `step_corr()`

Removes highly correlated variables


```r
movie_rec <- recipe(test ~ ., data = bechdel) %>%
  step_other(genre, threshold = .03) %>% 
  step_novel(all_nominal(), -all_outcomes()) %>% # Use *before* `step_dummy()` so new level is dummified
  step_dummy(all_nominal(), -all_outcomes()) %>% 
  step_zv(all_numeric(), -all_outcomes())  %>% 
  step_normalize(all_numeric())  
 # step_corr(all_predictors(), threshold = 0.75, method = "spearman") 



movie_rec
```

```
## 
```

```
## ── Recipe ──────────────────────────────────────────────────────────────────────
```

```
## 
```

```
## ── Inputs
```

```
## Number of variables by role
```

```
## outcome:   1
## predictor: 9
```

```
## 
```

```
## ── Operations
```

```
## • Collapsing factor levels for: genre
```

```
## • Novel factor level assignment for: all_nominal(), -all_outcomes()
```

```
## • Dummy variables from: all_nominal(), -all_outcomes()
```

```
## • Zero variance filter on: all_numeric(), -all_outcomes()
```

```
## • Centering and scaling for: all_numeric()
```

# Define different models to fit


```r
## Model Building

# 1. Pick a `model type`
# 2. set the `engine`
# 3. Set the `mode`: regression or classification

# Logistic regression
log_spec <-  logistic_reg() %>%  # model type
  set_engine(engine = "glm") %>%  # model engine
  set_mode("classification") # model mode

# Show your model specification
log_spec
```

```
## Logistic Regression Model Specification (classification)
## 
## Computational engine: glm
```

```r
# Decision Tree
tree_spec <- decision_tree() %>%
  set_engine(engine = "C5.0") %>%
  set_mode("classification")

tree_spec
```

```
## Decision Tree Model Specification (classification)
## 
## Computational engine: C5.0
```

```r
# Random Forest
library(ranger)

rf_spec <- 
  rand_forest() %>% 
  set_engine("ranger", importance = "impurity") %>% 
  set_mode("classification")


# Boosted tree (XGBoost)
library(xgboost)
```

```
## 
## Attaching package: 'xgboost'
```

```
## The following object is masked from 'package:dplyr':
## 
##     slice
```

```r
xgb_spec <- 
  boost_tree() %>% 
  set_engine("xgboost") %>% 
  set_mode("classification") 

# K-nearest neighbour (k-NN)
knn_spec <- 
  nearest_neighbor(neighbors = 4) %>% # we can adjust the number of neighbors 
  set_engine("kknn") %>% 
  set_mode("classification") 
```

# Bundle recipe and model with `workflows`


```r
log_wflow <- # new workflow object
 workflow() %>% # use workflow function
 add_recipe(movie_rec) %>%   # use the new recipe
 add_model(log_spec)   # add your model spec

# show object
log_wflow
```

```
## ══ Workflow ════════════════════════════════════════════════════════════════════
## Preprocessor: Recipe
## Model: logistic_reg()
## 
## ── Preprocessor ────────────────────────────────────────────────────────────────
## 5 Recipe Steps
## 
## • step_other()
## • step_novel()
## • step_dummy()
## • step_zv()
## • step_normalize()
## 
## ── Model ───────────────────────────────────────────────────────────────────────
## Logistic Regression Model Specification (classification)
## 
## Computational engine: glm
```

```r
## A few more workflows

tree_wflow <-
 workflow() %>%
 add_recipe(movie_rec) %>% 
 add_model(tree_spec) 

rf_wflow <-
 workflow() %>%
 add_recipe(movie_rec) %>% 
 add_model(rf_spec) 

xgb_wflow <-
 workflow() %>%
 add_recipe(movie_rec) %>% 
 add_model(xgb_spec)

knn_wflow <-
 workflow() %>%
 add_recipe(movie_rec) %>% 
 add_model(knn_spec)
```

HEADS UP

1.  How many models have you specified?


```r
#Five models have been specified in the above code run and these take the form of various types of classification: logistic regression (classification), a decision tree (tree), and various tree options: a random forest (rf), a booststrapping option: gradient boosting (xgb) and k-nearest neighbours (knn).
```

1.  What's the difference between a model specification and a workflow?


```r
#A model specification is essentially a function that one can use to apply unseen data that can fit well to the model if the data used to generate the function are similar to the unseen data. In this case, each model is a classifier which means that it partitions data into certain categories according to its relative fit to the underpinning model that was defined by the training data.

#In contrast, a workflow is a sequence of steps or operations that lead to a result. In the workflows above, a model is contained within each of them.
```

1.  Do you need to add a formula (e.g., `test ~ .`) if you have a recipe?


```r
#No, because the recipe contains a script to clean the data and prepare it for being apply to the data model via the workflow. The model contains formula(e) but that is called within the workflow after the recipe has been executed.
```

# Model Comparison

You now have all your models. Adapt the code from slides `code-from-slides-CA-housing.R`, line 400 onwards to assess which model gives you the best classification.


```r
## Evaluate Models

## Logistic regression results{.smaller}

log_res <- log_wflow %>% 
  fit_resamples(
    resamples = bechdel_folds, 
    metrics = metric_set(
      recall, precision, f_meas, accuracy,
      kap, roc_auc, sens, spec),
    control = control_resamples(save_pred = TRUE)) 
```

```
## → A | warning: glm.fit: algorithm did not converge
```

```
## 
There were issues with some computations   A: x1

                                                 
→ B | warning: prediction from rank-deficient fit; attr(*, "non-estim") has doubtful cases
## There were issues with some computations   A: x1

There were issues with some computations   A: x1   B: x1

There were issues with some computations   A: x2   B: x1

There were issues with some computations   A: x2   B: x2

There were issues with some computations   A: x3   B: x2

There were issues with some computations   A: x3   B: x3

There were issues with some computations   A: x4   B: x3

There were issues with some computations   A: x4   B: x4

There were issues with some computations   A: x5   B: x4

There were issues with some computations   A: x5   B: x5

There were issues with some computations   A: x6   B: x5

There were issues with some computations   A: x6   B: x6

There were issues with some computations   A: x7   B: x6

There were issues with some computations   A: x7   B: x7

There were issues with some computations   A: x8   B: x7

There were issues with some computations   A: x8   B: x8

There were issues with some computations   A: x9   B: x8

There were issues with some computations   A: x9   B: x9

There were issues with some computations   A: x10   B: x9

There were issues with some computations   A: x10   B: x10

There were issues with some computations   A: x10   B: x10
```

```r
# Show average performance over all folds (note that we use log_res):
log_res %>%  collect_metrics(summarize = TRUE)
```

```
## # A tibble: 8 × 6
##   .metric   .estimator    mean     n std_err .config             
##   <chr>     <chr>        <dbl> <int>   <dbl> <chr>               
## 1 accuracy  binary      0.478     10  0.0184 Preprocessor1_Model1
## 2 f_meas    binary      0.491     10  0.0285 Preprocessor1_Model1
## 3 kap       binary     -0.0420    10  0.0356 Preprocessor1_Model1
## 4 precision binary      0.531     10  0.0221 Preprocessor1_Model1
## 5 recall    binary      0.469     10  0.0413 Preprocessor1_Model1
## 6 roc_auc   binary      0.473     10  0.0189 Preprocessor1_Model1
## 7 sens      binary      0.469     10  0.0413 Preprocessor1_Model1
## 8 spec      binary      0.489     10  0.0435 Preprocessor1_Model1
```

```r
# Show performance for every single fold:
log_res %>%  collect_metrics(summarize = FALSE)
```

```
## # A tibble: 80 × 5
##    id     .metric   .estimator .estimate .config             
##    <chr>  <chr>     <chr>          <dbl> <chr>               
##  1 Fold01 recall    binary        0.403  Preprocessor1_Model1
##  2 Fold01 precision binary        0.581  Preprocessor1_Model1
##  3 Fold01 f_meas    binary        0.476  Preprocessor1_Model1
##  4 Fold01 accuracy  binary        0.509  Preprocessor1_Model1
##  5 Fold01 kap       binary        0.0417 Preprocessor1_Model1
##  6 Fold01 sens      binary        0.403  Preprocessor1_Model1
##  7 Fold01 spec      binary        0.64   Preprocessor1_Model1
##  8 Fold01 roc_auc   binary        0.508  Preprocessor1_Model1
##  9 Fold02 recall    binary        0.339  Preprocessor1_Model1
## 10 Fold02 precision binary        0.477  Preprocessor1_Model1
## # ℹ 70 more rows
```

```r
## `collect_predictions()` and get confusion matrix{.smaller}

log_pred <- log_res %>% collect_predictions()

log_pred %>%  conf_mat(test, .pred_class) 
```

```
##           Truth
## Prediction Fail Pass
##       Fail  289  254
##       Pass  328  243
```

```r
log_pred %>% 
  conf_mat(test, .pred_class) %>% 
  autoplot(type = "mosaic") +
  geom_label(aes(
      x = (xmax + xmin) / 2, 
      y = (ymax + ymin) / 2, 
      label = c("TP", "FN", "FP", "TN")))
```

<img src="/blogs/homework4_files/figure-html/unnamed-chunk-41-1.png" width="672" />

```r
log_pred %>% 
  conf_mat(test, .pred_class) %>% 
  autoplot(type = "heatmap")
```

<img src="/blogs/homework4_files/figure-html/unnamed-chunk-41-2.png" width="672" />

```r
## ROC Curve

log_pred %>% 
  group_by(id) %>% # id contains our folds
  roc_curve(test, .pred_Pass) %>% 
  autoplot()
```

<img src="/blogs/homework4_files/figure-html/unnamed-chunk-41-3.png" width="672" />

```r
## Decision Tree results

tree_res <-
  tree_wflow %>% 
  fit_resamples(
    resamples = bechdel_folds, 
    metrics = metric_set(
      recall, precision, f_meas, 
      accuracy, kap,
      roc_auc, sens, spec),
    control = control_resamples(save_pred = TRUE)
    ) 

tree_res %>%  collect_metrics(summarize = TRUE)
```

```
## # A tibble: 8 × 6
##   .metric   .estimator  mean     n std_err .config             
##   <chr>     <chr>      <dbl> <int>   <dbl> <chr>               
## 1 accuracy  binary     0.590    10  0.0131 Preprocessor1_Model1
## 2 f_meas    binary     0.632    10  0.0126 Preprocessor1_Model1
## 3 kap       binary     0.168    10  0.0276 Preprocessor1_Model1
## 4 precision binary     0.629    10  0.0125 Preprocessor1_Model1
## 5 recall    binary     0.637    10  0.0194 Preprocessor1_Model1
## 6 roc_auc   binary     0.591    10  0.0181 Preprocessor1_Model1
## 7 sens      binary     0.637    10  0.0194 Preprocessor1_Model1
## 8 spec      binary     0.530    10  0.0283 Preprocessor1_Model1
```

```r
## Random Forest

rf_res <-
  rf_wflow %>% 
  fit_resamples(
    resamples = bechdel_folds, 
    metrics = metric_set(
      recall, precision, f_meas, 
      accuracy, kap,
      roc_auc, sens, spec),
    control = control_resamples(save_pred = TRUE)
    ) 

rf_res %>%  collect_metrics(summarize = TRUE)
```

```
## # A tibble: 8 × 6
##   .metric   .estimator  mean     n std_err .config             
##   <chr>     <chr>      <dbl> <int>   <dbl> <chr>               
## 1 accuracy  binary     0.641    10  0.0141 Preprocessor1_Model1
## 2 f_meas    binary     0.706    10  0.0112 Preprocessor1_Model1
## 3 kap       binary     0.255    10  0.0296 Preprocessor1_Model1
## 4 precision binary     0.647    10  0.0116 Preprocessor1_Model1
## 5 recall    binary     0.778    10  0.0135 Preprocessor1_Model1
## 6 roc_auc   binary     0.663    10  0.0225 Preprocessor1_Model1
## 7 sens      binary     0.778    10  0.0135 Preprocessor1_Model1
## 8 spec      binary     0.471    10  0.0215 Preprocessor1_Model1
```

```r
## Boosted tree - XGBoost

xgb_res <- 
  xgb_wflow %>% 
  fit_resamples(
    resamples = bechdel_folds, 
    metrics = metric_set(
      recall, precision, f_meas, 
      accuracy, kap,
      roc_auc, sens, spec),
    control = control_resamples(save_pred = TRUE)
    ) 

xgb_res %>% collect_metrics(summarize = TRUE)
```

```
## # A tibble: 8 × 6
##   .metric   .estimator  mean     n std_err .config             
##   <chr>     <chr>      <dbl> <int>   <dbl> <chr>               
## 1 accuracy  binary     0.634    10  0.0126 Preprocessor1_Model1
## 2 f_meas    binary     0.683    10  0.0105 Preprocessor1_Model1
## 3 kap       binary     0.252    10  0.0270 Preprocessor1_Model1
## 4 precision binary     0.660    10  0.0136 Preprocessor1_Model1
## 5 recall    binary     0.712    10  0.0171 Preprocessor1_Model1
## 6 roc_auc   binary     0.645    10  0.0169 Preprocessor1_Model1
## 7 sens      binary     0.712    10  0.0171 Preprocessor1_Model1
## 8 spec      binary     0.539    10  0.0295 Preprocessor1_Model1
```

```r
## K-nearest neighbour

knn_res <- 
  knn_wflow %>% 
  fit_resamples(
    resamples = bechdel_folds, 
    metrics = metric_set(
      recall, precision, f_meas, 
      accuracy, kap,
      roc_auc, sens, spec),
    control = control_resamples(save_pred = TRUE)
    ) 
```

```
## → A | warning: While computing binary `precision()`, no predicted events were detected (i.e. `true_positive + false_positive = 0`). 
##                Precision is undefined in this case, and `NA` will be returned.
##                Note that 61 true event(s) actually occured for the problematic event level, 'Fail'.
## 
There were issues with some computations   A: x1

There were issues with some computations   A: x1
```

```r
knn_res %>% collect_metrics(summarize = TRUE)
```

```
## # A tibble: 8 × 6
##   .metric   .estimator     mean     n std_err .config             
##   <chr>     <chr>         <dbl> <int>   <dbl> <chr>               
## 1 accuracy  binary     0.543       10 0.0110  Preprocessor1_Model1
## 2 f_meas    binary     0.712        9 0.00136 Preprocessor1_Model1
## 3 kap       binary     0.000823    10 0.00424 Preprocessor1_Model1
## 4 precision binary     0.554        9 0.00102 Preprocessor1_Model1
## 5 recall    binary     0.897       10 0.0997  Preprocessor1_Model1
## 6 roc_auc   binary     0.548       10 0.0231  Preprocessor1_Model1
## 7 sens      binary     0.897       10 0.0997  Preprocessor1_Model1
## 8 spec      binary     0.104       10 0.0996  Preprocessor1_Model1
```

```r
## Model Comparison

log_metrics <- 
  log_res %>% 
  collect_metrics(summarise = TRUE) %>%
  # add the name of the model to every row
  mutate(model = "Logistic Regression") 

tree_metrics <- 
  tree_res %>% 
  collect_metrics(summarise = TRUE) %>%
  mutate(model = "Decision Tree")

rf_metrics <- 
  rf_res %>% 
  collect_metrics(summarise = TRUE) %>%
  mutate(model = "Random Forest")

xgb_metrics <- 
  xgb_res %>% 
  collect_metrics(summarise = TRUE) %>%
  mutate(model = "XGBoost")

knn_metrics <- 
  knn_res %>% 
  collect_metrics(summarise = TRUE) %>%
  mutate(model = "Knn")

# create dataframe with all models
model_compare <- bind_rows(log_metrics,
                           tree_metrics,
                           rf_metrics,
                           xgb_metrics,
                           knn_metrics) 

#Pivot wider to create barplot
  model_comp <- model_compare %>% 
  select(model, .metric, mean, std_err) %>% 
  pivot_wider(names_from = .metric, values_from = c(mean, std_err)) 

# show mean are under the curve (ROC-AUC) for every model
model_comp %>% 
  arrange(mean_roc_auc) %>% 
  mutate(model = fct_reorder(model, mean_roc_auc)) %>% # order results
  ggplot(aes(model, mean_roc_auc, fill=model)) +
  geom_col() +
  coord_flip() +
  scale_fill_brewer(palette = "Blues") +
   geom_text(
     size = 3,
     aes(label = round(mean_roc_auc, 2), 
         y = mean_roc_auc + 0.08),
     vjust = 1
  )+
  theme_light()+
  theme(legend.position = "none")+
  labs(y = NULL)
```

<img src="/blogs/homework4_files/figure-html/unnamed-chunk-41-4.png" width="672" />

```r
## `last_fit()` on test set

# - `last_fit()`  fits a model to the whole training data and evaluates it on the test set. 
# - provide the workflow object of the best model as well as the data split object (not the training data). 
 
last_fit_xgb <- last_fit(xgb_wflow, 
                        split = data_split,
                        metrics = metric_set(
                          accuracy, f_meas, kap, precision,
                          recall, roc_auc, sens, spec))

last_fit_xgb %>% collect_metrics(summarize = TRUE)
```

```
## # A tibble: 8 × 4
##   .metric   .estimator .estimate .config             
##   <chr>     <chr>          <dbl> <chr>               
## 1 accuracy  binary         0.568 Preprocessor1_Model1
## 2 f_meas    binary         0.630 Preprocessor1_Model1
## 3 kap       binary         0.114 Preprocessor1_Model1
## 4 precision binary         0.599 Preprocessor1_Model1
## 5 recall    binary         0.665 Preprocessor1_Model1
## 6 sens      binary         0.665 Preprocessor1_Model1
## 7 spec      binary         0.448 Preprocessor1_Model1
## 8 roc_auc   binary         0.610 Preprocessor1_Model1
```

```r
#Compare to training
xgb_res %>% collect_metrics(summarize = TRUE)
```

```
## # A tibble: 8 × 6
##   .metric   .estimator  mean     n std_err .config             
##   <chr>     <chr>      <dbl> <int>   <dbl> <chr>               
## 1 accuracy  binary     0.634    10  0.0126 Preprocessor1_Model1
## 2 f_meas    binary     0.683    10  0.0105 Preprocessor1_Model1
## 3 kap       binary     0.252    10  0.0270 Preprocessor1_Model1
## 4 precision binary     0.660    10  0.0136 Preprocessor1_Model1
## 5 recall    binary     0.712    10  0.0171 Preprocessor1_Model1
## 6 roc_auc   binary     0.645    10  0.0169 Preprocessor1_Model1
## 7 sens      binary     0.712    10  0.0171 Preprocessor1_Model1
## 8 spec      binary     0.539    10  0.0295 Preprocessor1_Model1
```

```r
## Variable importance using `{vip}` package

library(vip)

last_fit_xgb %>% 
  pluck(".workflow", 1) %>%   
  pull_workflow_fit() %>% 
  vip(num_features = 10) +
  theme_light()
```

```
## Warning: `pull_workflow_fit()` was deprecated in workflows 0.2.3.
## ℹ Please use `extract_fit_parsnip()` instead.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

<img src="/blogs/homework4_files/figure-html/unnamed-chunk-41-5.png" width="672" />

```r
## Final Confusion Matrix

last_fit_xgb %>%
  collect_predictions() %>% 
  conf_mat(test, .pred_class) %>% 
  autoplot(type = "heatmap")
```

<img src="/blogs/homework4_files/figure-html/unnamed-chunk-41-6.png" width="672" />

```r
## Final ROC curve
last_fit_xgb %>% 
  collect_predictions() %>% 
  roc_curve(test, .pred_Fail) %>% 
  autoplot()
```

<img src="/blogs/homework4_files/figure-html/unnamed-chunk-41-7.png" width="672" />


```r
#Assessing which model affords the best classification from these results:
#
# First, considering the results from each model in turn: 
#
# The logistic regression performs poorly, as judged by the fact that: (i) the algorithm to firm up the model did not converge (as highlighted above in the warning statements against the glm.fit); (ii) the statistical metrics that showcase overall average performance over all folds are all around (and all but one are less than) 50% (or zero in the case of the 'kap' metric). This means that the model does not perform better than random guessing, i.e. the model carries no relevant information or insight and is useless. (iii) analogous performance metrics over each fold are overall just as bad as described in (ii). (iv) There is little discrimination between any elements in the confusion matrix (all range from 243-328) with possibly just a hint of a possible correlation of note for the True negative (Fail-Truth and Pass-Prediction) but the effect is slight. (v) The ROC curves from analysis of each fold lie fairly evenly above and below the y=x line, revealing that the results overall of the model are no better than a random guess. No particular fold is far better than any other; fold 7 is perhaps a bit better than the others but its results are not great in themselves either. 
#
# The decision tree model fares a bit better, with evaluation metrics mostly in the high 50s or early 60s (except for kep which is above zero). This suggests that the model does fit some of the data with a priori information to the extent that it is better than random guessing. 
#
# The random forest results compare more favourably in terms of these same evaluation metrics, with most metrics ranging from 0.64-0.78. This could be considered to be quite strong as a model; indeed, models that are too high (a perfect one would be 100%) can suffer from being too highly correlated owing to effects such as overfitting. 
#
# The bootstrapping decision-tree option, gradient boosting, fares similarly to that of the random forest model, with overall results suggesting that this model is just slightly interior in comparison.
#
# the k-nearest neighbour classification model does not perform very well overall, although it does have high sensitivity and its f-score is over 70%; this is correlated to its high recall of 90% and the lower precision of 55% which brings down the overall f-score.
#
# A graphical summary of the mean ROC metric for each model is given above. This offers a quick realisation of the salient conclusions of the model evaluation interpretations that have been described in more detail above. In short, the logistic regression model is so poor that it is statistically worse than the case of random guessing! The k-nearest neighbour model is a pretty simple classification model and thus also performs fairly poorer (but better than logistic regression!). The next more complicated decision-tree model fares better while the random forest and gradient boosting (bootstrapping) models offer the best fits to the training data. The random forest model will train many trees independently while a gradient boosting model will train many trees subsequently (correcting the errors in a given tree from that of its forerunner tree). Thus, the gradient boosting model has bootstrapping functionality, and its results are easier to interpret than that of a random forest model because gradient boosting models are afforded from one final tree while the result of a random forest model are the ensemble of many trees that have been modelled in parallel. Given these fundemental model considerations, and the similar ROC of these two model options, the gradient boosting model was deemed to be the best model for being taken forward in classifying the test (unseen) movie data. 
#The results of applying these test data to the gradient boosting model are good, albeit less good (values of most metrics are in mid-60s) than the results from the training data (where metric values extended to the 70s). This comparision stands to reason since unseen data will naturally fit less well than the training data which were used to create (i.e. were tailored to the fit of) the model. Besides, the results of testing and training data still fare well in having fairly small overall differences.
#
# The results of the gradient boosting model were then interpreted in terms of a feature selection i.e. an assessment of the level of importance of each variable that is in the movie dataset and was applied to train the model. A graphical display of this extent of importance per movie variable is shown in the histogram above. It shows that budget_2013 is the most important feature in the model which stands to reason as we have noticed this variable in aforementioned considerations. Movie ratings (imdb highest, metascore much lower but significant) are also important, as one can imagine given audience will provide feedback on a movie in a way that the criteria may well be relevant; while the domestic and international gross profit of movies are both similarly and significantly important as one would expect given that the data contain hollywood movies that are targetted similarly for an international and domestic audience (so both domestic as well as international gross profit should highly align) and the profits are high for where these criteria are met. The year of the movie is also an important consideration as one might imagine given that the year of a movie will reflect the culture and wishes of the target audience. Some other variables in the dataset (a few 'other' genre categories, rated_PG13) are also suggested by the model as being significant, albeit distinctly less significant than the other variables that have already been mentioned in this model / data interpretation.
#
# The overall confusion matrix shown above, for the results of applying the test data to the trained model, discriminates best for when the criteria Fail, which makes sense since more Fail than Pass. The corresponding ROC curve is certainly not perfect but it is considerably better than those that were shown for the evaluation results from some of the training models that were explored, and the gradient boosting model is much better than the case of random guessing (or a guess based on a naive model based on the simple proportioning of the data).  
```
