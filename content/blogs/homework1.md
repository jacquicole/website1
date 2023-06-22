---
categories:
- ""
- ""
date: "2017-10-31T21:28:43-05:00"
description: Some fascinating data manipulation exercises in R
draft: false
image: Rhubimage.jpg
keywords: ""
slug: blog4
title: Code with R
---



# Data Manipulation

## Problem 1: Use logical operators to find flights that:

```         
-   Had an arrival delay of two or more hours (\> 120 minutes)
-   Flew to Houston (IAH or HOU)
-   Were operated by United (`UA`), American (`AA`), or Delta (`DL`)
-   Departed in summer (July, August, and September)
-   Arrived more than two hours late, but didn't leave late
-   Were delayed by at least an hour, but made up over 30 minutes in flight
```


```r
# Had an arrival delay of two or more hours (> 120 minutes)

flights %>% 
  filter(arr_delay > 120)
```

```
## # A tibble: 10,034 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1     1      811            630       101     1047            830
##  2  2013     1     1      848           1835       853     1001           1950
##  3  2013     1     1      957            733       144     1056            853
##  4  2013     1     1     1114            900       134     1447           1222
##  5  2013     1     1     1505           1310       115     1638           1431
##  6  2013     1     1     1525           1340       105     1831           1626
##  7  2013     1     1     1549           1445        64     1912           1656
##  8  2013     1     1     1558           1359       119     1718           1515
##  9  2013     1     1     1732           1630        62     2028           1825
## 10  2013     1     1     1803           1620       103     2008           1750
## # ℹ 10,024 more rows
## # ℹ 11 more variables: arr_delay <dbl>, carrier <chr>, flight <int>,
## #   tailnum <chr>, origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>,
## #   hour <dbl>, minute <dbl>, time_hour <dttm>
```

```r
# Flew to Houston (IAH or HOU)

flights %>% 
  filter(dest %in% c("IAH", "HOU"))
```

```
## # A tibble: 9,313 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1     1      517            515         2      830            819
##  2  2013     1     1      533            529         4      850            830
##  3  2013     1     1      623            627        -4      933            932
##  4  2013     1     1      728            732        -4     1041           1038
##  5  2013     1     1      739            739         0     1104           1038
##  6  2013     1     1      908            908         0     1228           1219
##  7  2013     1     1     1028           1026         2     1350           1339
##  8  2013     1     1     1044           1045        -1     1352           1351
##  9  2013     1     1     1114            900       134     1447           1222
## 10  2013     1     1     1205           1200         5     1503           1505
## # ℹ 9,303 more rows
## # ℹ 11 more variables: arr_delay <dbl>, carrier <chr>, flight <int>,
## #   tailnum <chr>, origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>,
## #   hour <dbl>, minute <dbl>, time_hour <dttm>
```

```r
# Were operated by United (`UA`), American (`AA`), or Delta (`DL`)

flights %>%
  filter(carrier %in% c("UA", "AA", "DL")) 
```

```
## # A tibble: 139,504 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1     1      517            515         2      830            819
##  2  2013     1     1      533            529         4      850            830
##  3  2013     1     1      542            540         2      923            850
##  4  2013     1     1      554            600        -6      812            837
##  5  2013     1     1      554            558        -4      740            728
##  6  2013     1     1      558            600        -2      753            745
##  7  2013     1     1      558            600        -2      924            917
##  8  2013     1     1      558            600        -2      923            937
##  9  2013     1     1      559            600        -1      941            910
## 10  2013     1     1      559            600        -1      854            902
## # ℹ 139,494 more rows
## # ℹ 11 more variables: arr_delay <dbl>, carrier <chr>, flight <int>,
## #   tailnum <chr>, origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>,
## #   hour <dbl>, minute <dbl>, time_hour <dttm>
```

```r
  # Departed in summer (July, August, and September)
  
flights %>%
  filter(month %in% c(7, 8, 9))
```

```
## # A tibble: 86,326 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     7     1        1           2029       212      236           2359
##  2  2013     7     1        2           2359         3      344            344
##  3  2013     7     1       29           2245       104      151              1
##  4  2013     7     1       43           2130       193      322             14
##  5  2013     7     1       44           2150       174      300            100
##  6  2013     7     1       46           2051       235      304           2358
##  7  2013     7     1       48           2001       287      308           2305
##  8  2013     7     1       58           2155       183      335             43
##  9  2013     7     1      100           2146       194      327             30
## 10  2013     7     1      100           2245       135      337            135
## # ℹ 86,316 more rows
## # ℹ 11 more variables: arr_delay <dbl>, carrier <chr>, flight <int>,
## #   tailnum <chr>, origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>,
## #   hour <dbl>, minute <dbl>, time_hour <dttm>
```

```r
# Arrived more than two hours late, but didn't leave late

x <- flights %>% filter(arr_delay > 120)
y <- flights %>% filter(dep_delay > 0)
  anti_join(x, y, by = NULL)
```

```
## Joining with `by = join_by(year, month, day, dep_time, sched_dep_time,
## dep_delay, arr_time, sched_arr_time, arr_delay, carrier, flight, tailnum,
## origin, dest, air_time, distance, hour, minute, time_hour)`
```

```
## # A tibble: 29 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1    27     1419           1420        -1     1754           1550
##  2  2013    10     7     1350           1350         0     1736           1526
##  3  2013    10     7     1357           1359        -2     1858           1654
##  4  2013    10    16      657            700        -3     1258           1056
##  5  2013    11     1      658            700        -2     1329           1015
##  6  2013     3    18     1844           1847        -3       39           2219
##  7  2013     4    17     1635           1640        -5     2049           1845
##  8  2013     4    18      558            600        -2     1149            850
##  9  2013     4    18      655            700        -5     1213            950
## 10  2013     5    22     1827           1830        -3     2217           2010
## # ℹ 19 more rows
## # ℹ 11 more variables: arr_delay <dbl>, carrier <chr>, flight <int>,
## #   tailnum <chr>, origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>,
## #   hour <dbl>, minute <dbl>, time_hour <dttm>
```

```r
# Were delayed by at least an hour, but made up over 30 minutes in flight
  
a <- flights %>% filter(dep_delay > 60)
b <- flights %>% filter(arr_delay >= (dep_delay-30))
  anti_join(a, b, by = NULL)
```

```
## Joining with `by = join_by(year, month, day, dep_time, sched_dep_time,
## dep_delay, arr_time, sched_arr_time, arr_delay, carrier, flight, tailnum,
## origin, dest, air_time, distance, hour, minute, time_hour)`
```

```
## # A tibble: 2,071 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1     1     2205           1720       285       46           2040
##  2  2013     1     1     2326           2130       116      131             18
##  3  2013     1     2     1125            925       120     1445           1146
##  4  2013     1     2     1849           1724        85     2235           1938
##  5  2013     1     3     1503           1221       162     1803           1555
##  6  2013     1     3     1839           1700        99     2056           1950
##  7  2013     1     3     1850           1745        65     2148           2120
##  8  2013     1     3     1904           1659       125     2327           2046
##  9  2013     1     3     1941           1759       102     2246           2139
## 10  2013     1     3     1950           1845        65     2228           2227
## # ℹ 2,061 more rows
## # ℹ 11 more variables: arr_delay <dbl>, carrier <chr>, flight <int>,
## #   tailnum <chr>, origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>,
## #   hour <dbl>, minute <dbl>, time_hour <dttm>
```

## Problem 2: What months had the highest and lowest proportion of cancelled flights? Interpret any seasonal patterns. To determine if a flight was cancelled use the following code

<!-- -->

```         
flights %>% 
  filter(is.na(dep_time)) 
```


```r
# What months had the highest and lowest % of cancelled flights?

# We first determine the total number of flights per month (which appear to be similar based on the results of using the code below)

total <- flights %>%
  count(month)

# We can then determine how many flights are cancelled per month using the following code.

flights %>% 
  filter(is.na(dep_time)) %>%
  count(month) 
```

```
## # A tibble: 12 × 2
##    month     n
##    <int> <int>
##  1     1   521
##  2     2  1261
##  3     3   861
##  4     4   668
##  5     5   563
##  6     6  1009
##  7     7   940
##  8     8   486
##  9     9   452
## 10    10   236
## 11    11   233
## 12    12  1025
```

```r
# We can then determine what is the proportion of flights (in percent) that are cancelled per month using the following code:

flights %>% 
  filter(is.na(dep_time)) %>%
  count(month)/(total)*100 
```

```
##    month         n
## 1    100 1.9293438
## 2    100 5.0539057
## 3    100 2.9860581
## 4    100 2.3579245
## 5    100 1.9551327
## 6    100 3.5725667
## 7    100 3.1945624
## 8    100 1.6571760
## 9    100 1.6392254
## 10   100 0.8169199
## 11   100 0.8544814
## 12   100 3.6431491
```

```r
#The tibble from this code classifies the number of cancelled flights into monthly groups. Thereby, the minimum and maximum proportion of flights cancelled in a given month is 1.64 percent (for November) and 5.05 percent (for February), respectively. This stands to reason because February will see a lot of cancellations due to snow (noting that many US airlines are included in the data and that the US is such a massive hub for so many flights globally). November sees fewest cancellations presumably because the weather has yet to become bad in the US or the UK (London and Frankfurt and Amsterdam are major European global hubs). In addition, there will be a particularly large incentive for people in the USA to reach home for Thanksgiving in November and airlines will be under pressure to see that customers needs are met.
```

## Problem 3: What plane (specified by the `tailnum` variable) traveled the most times from New York City airports in 2013? Please `left_join()` the resulting table with the table `planes` (also included in the `nycflights13` package).

For the plane with the greatest number of flights and that had more than 50 seats, please create a table where it flew to during 2013.


```r
#check that all outbound flights originate from one of the three new york city airports, JFK (John F Kennedy), LGA (La Guardia), EWR (Newark). This appears to be the case by eye when spot checking but one needs to check this first systematically. I do this by asking for a tibble for all flights (which reveals 336,776 rows i.e. total number of flights) and then filtering for the aforementioned three airports alone and this also produces a title with 336,776 rows. Thus, I can confirm that the origin attribute is synonymous with the total number of flights departing from a new york city.

flights
```

```
## # A tibble: 336,776 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1     1      517            515         2      830            819
##  2  2013     1     1      533            529         4      850            830
##  3  2013     1     1      542            540         2      923            850
##  4  2013     1     1      544            545        -1     1004           1022
##  5  2013     1     1      554            600        -6      812            837
##  6  2013     1     1      554            558        -4      740            728
##  7  2013     1     1      555            600        -5      913            854
##  8  2013     1     1      557            600        -3      709            723
##  9  2013     1     1      557            600        -3      838            846
## 10  2013     1     1      558            600        -2      753            745
## # ℹ 336,766 more rows
## # ℹ 11 more variables: arr_delay <dbl>, carrier <chr>, flight <int>,
## #   tailnum <chr>, origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>,
## #   hour <dbl>, minute <dbl>, time_hour <dttm>
```

```r
flights %>%
  filter(origin %in% c("JFK", "LGA", "EWR")) 
```

```
## # A tibble: 336,776 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1     1      517            515         2      830            819
##  2  2013     1     1      533            529         4      850            830
##  3  2013     1     1      542            540         2      923            850
##  4  2013     1     1      544            545        -1     1004           1022
##  5  2013     1     1      554            600        -6      812            837
##  6  2013     1     1      554            558        -4      740            728
##  7  2013     1     1      555            600        -5      913            854
##  8  2013     1     1      557            600        -3      709            723
##  9  2013     1     1      557            600        -3      838            846
## 10  2013     1     1      558            600        -2      753            745
## # ℹ 336,766 more rows
## # ℹ 11 more variables: arr_delay <dbl>, carrier <chr>, flight <int>,
## #   tailnum <chr>, origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>,
## #   hour <dbl>, minute <dbl>, time_hour <dttm>
```

```r
# I then filter for the year 2013, and count the number of flights involving each plane count(tailnum) and then arrange this number, n, in descending order. This reveals the plane that has taken the most flights in 2013.
 
flights %>% 
  filter(year == 2013) %>%
  count(tailnum) %>%
  arrange(desc(n))
```

```
## # A tibble: 4,044 × 2
##    tailnum     n
##    <chr>   <int>
##  1 <NA>     2512
##  2 N725MQ    575
##  3 N722MQ    513
##  4 N723MQ    507
##  5 N711MQ    486
##  6 N713MQ    483
##  7 N258JB    427
##  8 N298JB    407
##  9 N353JB    404
## 10 N351JB    402
## # ℹ 4,034 more rows
```

```r
# The plane with tailnumber N725MQ has taken the most (575) flights in 2013. Note that the top entry is 'NA' i.e. not applicable entries, rather than actual flights as departures need to have reference tail numbers.

#We now filter further on the plane that has taken the most flights in 2013 that also has > 50 seats. 

planes %>%
  filter(seats > 50)
```

```
## # A tibble: 3,200 × 9
##    tailnum  year type              manufacturer model engines seats speed engine
##    <chr>   <int> <chr>             <chr>        <chr>   <int> <int> <int> <chr> 
##  1 N10156   2004 Fixed wing multi… EMBRAER      EMB-…       2    55    NA Turbo…
##  2 N102UW   1998 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  3 N103US   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  4 N104UW   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  5 N10575   2002 Fixed wing multi… EMBRAER      EMB-…       2    55    NA Turbo…
##  6 N105UW   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  7 N107US   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  8 N108UW   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  9 N109UW   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
## 10 N110UW   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
## # ℹ 3,190 more rows
```

```r
# This reveals that there are 3200 planes with > 50 seats.

# We now determine which plane has the greatest number of flights which has more than 50 seats.

left_join(flights, planes, by = 'tailnum') %>%
  filter(year.x == 2013, seats > 50) %>%
  count(tailnum) %>%
  arrange(desc(n))
```

```
## # A tibble: 3,200 × 2
##    tailnum     n
##    <chr>   <int>
##  1 N328AA    393
##  2 N338AA    388
##  3 N327AA    387
##  4 N335AA    385
##  5 N323AA    357
##  6 N319AA    354
##  7 N336AA    353
##  8 N329AA    344
##  9 N789JB    332
## 10 N324AA    328
## # ℹ 3,190 more rows
```

```r
#We now create a table where this plane (N328AA) flew to during 2013 on its 393 flights.

left_join(flights, planes, by = 'tailnum') %>%
  filter(tailnum == 'N328AA') %>%
  summarise(tailnum,dest)
```

```
## Warning: Returning more (or less) than 1 row per `summarise()` group was deprecated in
## dplyr 1.1.0.
## ℹ Please use `reframe()` instead.
## ℹ When switching from `summarise()` to `reframe()`, remember that `reframe()`
##   always returns an ungrouped data frame and adjust accordingly.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

```
## # A tibble: 393 × 2
##    tailnum dest 
##    <chr>   <chr>
##  1 N328AA  LAX  
##  2 N328AA  LAX  
##  3 N328AA  LAX  
##  4 N328AA  LAX  
##  5 N328AA  LAX  
##  6 N328AA  LAX  
##  7 N328AA  LAX  
##  8 N328AA  LAX  
##  9 N328AA  LAX  
## 10 N328AA  LAX  
## # ℹ 383 more rows
```

## Problem 4: The `nycflights13` package includes a table (`weather`) that describes the weather during 2013. Use that table to answer the following questions:

```         
-   What is the distribution of temperature (`temp`) in July 2013? Identify any important outliers in terms of the `wind_speed` variable.
-   What is the relationship between `dewp` and `humid`?
-   What is the relationship between `precip` and `visib`?
```


```r
#We first look at the distribution of the temperature in July using a heatmap of each hour in each day in July with the colour representing temperature:

library(ggplot2)

july <- weather %>% filter (month == 7)
  ggplot(data = july, mapping = aes(x = day, y = hour, fill = temp)) + geom_tile()
```

<img src="/blogs/homework1_files/figure-html/unnamed-chunk-5-1.png" width="672" />

```r
#This heatmap shows that the hottest period of July was from 14-16 h on 19th July.

#We then check the distribution of wind_speed over the July period. 

  ggplot(data = july, mapping = aes(x = hour, y = wind_speed)) + geom_point()
```

```
## Warning: Removed 2 rows containing missing values (`geom_point()`).
```

<img src="/blogs/homework1_files/figure-html/unnamed-chunk-5-2.png" width="672" />

```r
# This shows that there are no obvious data outliers. The data make sense in that the max wind_speed occurs the day after 19 July which experienced the hottest temperature; and that this wind_speed then falls of back to similar values to others. There were a few 'NA' (not applicable) data points, so I removed them and then calculated the exact max wind speed:

weather %>%
  filter (month == 7) %>%
  summarise(maxwind_speed = max(wind_speed))
```

```
## # A tibble: 1 × 1
##   maxwind_speed
##           <dbl>
## 1            NA
```

```r
# This shows that the max wind speed has a value of 25.31716 (I assume Celsius although we don't appear to be told of the units!). 

# I now look at the relationship between dewp and humid:

 ggplot(data = weather, mapping = aes(x = dewp, y = humid, colour = temp)) + geom_point()
```

```
## Warning: Removed 1 rows containing missing values (`geom_point()`).
```

<img src="/blogs/homework1_files/figure-html/unnamed-chunk-5-3.png" width="672" />

```r
# This shows that there is a monotonic relationship between dewp and humid whereby a greater dewp tends to indicate an upward increase in humid, but that the relationship is moderate.

#Furthermore, lower dewp and higher humid leads a higher temperature. This is a stronger correlation and can be best seen on a less dense data map. So, I select July data only to show this more clearly:
  
july <- weather %>% filter (month == 7)
  ggplot(data = july, mapping = aes(x = dewp, y = humid, colour = temp)) + geom_point()
```

<img src="/blogs/homework1_files/figure-html/unnamed-chunk-5-4.png" width="672" />

```r
#I now explore the relationship between 'precip' and 'visib'
  
   ggplot(data = weather, mapping = aes(x = precip, y = visib, colour = temp)) + geom_point()
```

<img src="/blogs/homework1_files/figure-html/unnamed-chunk-5-5.png" width="672" />

```r
# There does not seem to be any real correlation between these two variables. The only observation that may be worth mentioning is that values with precip greater than about 25 tend to show high variation. Looking a bit closer at the data above this threshold:

precip25 <- weather %>%
     filter (precip > 0.25)
  
   
   ggplot(data = precip25, mapping = aes(x = precip, y = visib, colour = temp)) + geom_point()
```

<img src="/blogs/homework1_files/figure-html/unnamed-chunk-5-6.png" width="672" />

```r
# There is still no obvious relationship between precip and visib, which one might find a bit surprising because precipiation might be assumed to reduce visibility. But perhaps other weather conditions need to be considered as being more influencial.   
```

## Problem 5: Use the `flights` and `planes` tables to answer the following questions:

```         
-   How many planes have a missing date of manufacture?
-   What are the five most common manufacturers?
-   Has the distribution of manufacturer changed over time as reflected by the airplanes flying from NYC in 2013? (Hint: you may need to use case_when() to recode the manufacturer name and collapse rare vendors into a category called Other.)
```


```r
#A rendering of planes will afford the total number of rows of manufacturer dates (year):

planes
```

```
## # A tibble: 3,322 × 9
##    tailnum  year type              manufacturer model engines seats speed engine
##    <chr>   <int> <chr>             <chr>        <chr>   <int> <int> <int> <chr> 
##  1 N10156   2004 Fixed wing multi… EMBRAER      EMB-…       2    55    NA Turbo…
##  2 N102UW   1998 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  3 N103US   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  4 N104UW   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  5 N10575   2002 Fixed wing multi… EMBRAER      EMB-…       2    55    NA Turbo…
##  6 N105UW   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  7 N107US   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  8 N108UW   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  9 N109UW   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
## 10 N110UW   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
## # ℹ 3,312 more rows
```

```r
#The tibble matrix 3,322 x 9 shows that there are 3,322 manufacturer year records. We now compute how many have a missing date:

planes %>%
  filter (!is.na(year))
```

```
## # A tibble: 3,252 × 9
##    tailnum  year type              manufacturer model engines seats speed engine
##    <chr>   <int> <chr>             <chr>        <chr>   <int> <int> <int> <chr> 
##  1 N10156   2004 Fixed wing multi… EMBRAER      EMB-…       2    55    NA Turbo…
##  2 N102UW   1998 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  3 N103US   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  4 N104UW   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  5 N10575   2002 Fixed wing multi… EMBRAER      EMB-…       2    55    NA Turbo…
##  6 N105UW   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  7 N107US   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  8 N108UW   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
##  9 N109UW   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
## 10 N110UW   1999 Fixed wing multi… AIRBUS INDU… A320…       2   182    NA Turbo…
## # ℹ 3,242 more rows
```

```r
#This gives a tibble matrix size of 3252 x 9. Thus, the number of missing manufacturer year dates is 3322-3252 = 70. 

#Now determining the five most common plane manufacturers:

#manufacturers <- planes %>%
  #group_by(manufacturer)
planes %>% 
  count(manufacturer) %>%
  arrange(desc(n)) 
```

```
## # A tibble: 35 × 2
##    manufacturer                      n
##    <chr>                         <int>
##  1 BOEING                         1630
##  2 AIRBUS INDUSTRIE                400
##  3 BOMBARDIER INC                  368
##  4 AIRBUS                          336
##  5 EMBRAER                         299
##  6 MCDONNELL DOUGLAS               120
##  7 MCDONNELL DOUGLAS AIRCRAFT CO   103
##  8 MCDONNELL DOUGLAS CORPORATION    14
##  9 CANADAIR                          9
## 10 CESSNA                            9
## # ℹ 25 more rows
```

```r
# This suggests that the five most common plane manufacturers (and their number) are:
#BOEING	(1630)			
#AIRBUS INDUSTRIE	(400)			
#BOMBARDIER INC	(368)			
#AIRBUS	(336)			
#EMBRAER	(299)	

#However, several of these manufacturers are actually the same company, so one needs to combine some of them to make the final version of the five most common plane manfacturers. 


planes <- planes %>%
   mutate(recode_manufacturer  = case_when(
     manufacturer %in% c("BOEING") ~ "Boeing",
     manufacturer %in% c("AIRBUS INDUSTRIE", "AIRBUS") ~ "Airbus",
     manufacturer %in% c("EMBRAER") ~ "Embraer",
     manufacturer %in% c("MCDONNELL DOUGLAS", "MCDONNELL DOUGLAS AIRCRAFT CO", "MCDONNELL DOUGLAS CORPORATION" ) ~ "McDonnell Douglas",
     TRUE ~ "Other"
   ))

#This affords:

#Boeing (1630)
#Airbus (400+336 = 736)
#Bombardier (368)
#Embraer (299)
#McDonnell Douglas (120+103+4 = 227)


#Now plotting the distribution of plane manufacturers over time, using the span of 2013 as a snapshot of that year's distribution, to see how they have changed:

  

flights %>% 
  left_join(planes, by = "tailnum")%>% 
  group_by(month, recode_manufacturer) %>% 
  summarise (n = n()) %>% 
  drop_na(recode_manufacturer) %>% 
  mutate(percent = n/ sum(n)) %>% 

  ggplot() +
  aes(x = month, y = n, colour = recode_manufacturer)+
  geom_line()+
  theme_light()
```

```
## `summarise()` has grouped output by 'month'. You can override using the
## `.groups` argument.
```

<img src="/blogs/homework1_files/figure-html/unnamed-chunk-6-1.png" width="672" />

## Problem 6: Use the `flights` and `planes` tables to answer the following questions:

```         
-   What is the oldest plane (specified by the tailnum variable) that flew from New York City airports in 2013?
-   How many airplanes that flew from New York City are included in the planes table?
```


```r
left_join(flights, planes, by = 'tailnum') %>%
  filter (!is.na(year.y)) %>%
  summarise(maxage = min(year.y)) 
```

```
## # A tibble: 1 × 1
##   maxage
##    <int>
## 1   1956
```

```r
#This shows that the oldest plane in service from NYC flights in 2013 is from 1956.

#I then determine how many planes that flew from NYC in 2013 (which I define as those having a valid arrival time) are included in the plane table using:

left_join(flights, planes, by = 'tailnum') %>%
  filter (!is.na(arr_time)) %>%
  count(tailnum) %>%
  arrange(desc(n))
```

```
## # A tibble: 4,037 × 2
##    tailnum     n
##    <chr>   <int>
##  1 N725MQ    546
##  2 N722MQ    485
##  3 N723MQ    476
##  4 N711MQ    464
##  5 N713MQ    451
##  6 N258JB    422
##  7 N353JB    403
##  8 N298JB    402
##  9 N351JB    391
## 10 N328AA    389
## # ℹ 4,027 more rows
```

```r
#This produces a tibble of 4037 x 2. 

#i.e. there are 4037 planes that meet this condition.
```

## Problem 7: Use the `nycflights13` to answer the following questions:

```         
-   What is the median arrival delay on a month-by-month basis in each airport?
-   For each airline, plot the median arrival delay for each month and origin airport.
```


```r
#I calculate the median value of the arr_delay variable for each month of the NYC flights data from 2013. This affords the following code and table:

flights %>%
  filter (!is.na(arr_delay)) %>%
  group_by(month, origin) %>%
  summarise(medianarrdelay = median(arr_delay)) 
```

```
## `summarise()` has grouped output by 'month'. You can override using the
## `.groups` argument.
```

```
## # A tibble: 36 × 3
## # Groups:   month [12]
##    month origin medianarrdelay
##    <int> <chr>           <dbl>
##  1     1 EWR                 0
##  2     1 JFK                -7
##  3     1 LGA                -4
##  4     2 EWR                -2
##  5     2 JFK                -5
##  6     2 LGA                -4
##  7     3 EWR                -4
##  8     3 JFK                -7
##  9     3 LGA                -7
## 10     4 EWR                -1
## # ℹ 26 more rows
```

```r
#   For each airline, I now plot the median arrival delay for each month and origin airport.

flights %>%
  filter (!is.na(arr_delay)) %>%
  group_by(month, origin, carrier) %>%
  summarise(medianarrdelay = median(arr_delay)) %>%
  
    ggplot() + (aes(x = month, y = medianarrdelay, colour = origin)) + geom_line() + facet_wrap(vars(carrier), scales = 'free') 
```

```
## `summarise()` has grouped output by 'month', 'origin'. You can override using
## the `.groups` argument.
```

<img src="/blogs/homework1_files/figure-html/unnamed-chunk-8-1.png" width="672" />

## Problem 8: Let's take a closer look at what carriers service the route to San Francisco International (SFO). Join the `flights` and `airlines` tables and count which airlines flew the most to SFO. Produce a new dataframe, `fly_into_sfo` that contains three variables: the `name` of the airline, e.g., `United Air Lines Inc.` not `UA`, the count (number) of times it flew to SFO, and the `percent` of the trips that that particular airline flew to SFO.


```r
# Joining the flights and airlines tables
fly_into_sfo <- left_join(flights, airlines, by = "carrier") %>%
# counting which airlines flew the most to SFO, and then showning their relative proportion (percent) that flew to SFO.
  filter(dest == "SFO") %>%
  count(name) %>%
  arrange(desc(n)) %>%
  mutate(percent = 100*n/sum(n))

#printing the resulting data frame as an output table:

fly_into_sfo
```

```
## # A tibble: 5 × 3
##   name                       n percent
##   <chr>                  <int>   <dbl>
## 1 United Air Lines Inc.   6819   51.2 
## 2 Virgin America          2197   16.5 
## 3 Delta Air Lines Inc.    1858   13.9 
## 4 American Airlines Inc.  1422   10.7 
## 5 JetBlue Airways         1035    7.76
```

```r
# providing a total of the number of flights that flew from NYC to SFO in 2013.

fly_into_sfo %>%
  summarise(n=sum(n))
```

```
## # A tibble: 1 × 1
##       n
##   <int>
## 1 13331
```

```r
# The table generated shows that five airlines flew to SFO from NYC during 2013, and the airline, United Air Lines, Inc., flew most times to SFO from NYC during 2013, to the tune of 6819 flights. This compares with the total number of flights from NYC to SFO which was 13331. i.e. United Air Lines Inc represents 51.15 percent (= (6819/13331) x 100) of the total number of flights from NYC to SFO in 2013.
```

And here is some bonus ggplot code to plot your dataframe


```r
fly_into_sfo %>% 

  # sort 'name' of airline by the numbers it times to flew to SFO
  mutate(name = fct_reorder(name, n)) %>% 
  ggplot() +
  
  aes(x = n, 
      y = name, ) +
  
  # a simple bar/column plot
  geom_col() +
  
  # add labels, so each bar shows the % of total flights 
  geom_text(aes(label = percent),
             hjust = 1, 
             colour = "grey", 
             size = 4)+
  
  # add labels to help our audience  
  labs(title="Which airline dominates the NYC to SFO route?", 
       subtitle = "as % of total flights in 2013",
       x= "Number of flights",
       y= NULL) +
  
  theme_minimal() + 
  
  # change the theme-- i just googled those , but you can use the ggThemeAssist add-in
  # https://cran.r-project.org/web/packages/ggThemeAssist/index.html
  
  theme(#
    # so title is left-aligned
    plot.title.position = "plot",
    
    # text in axes appears larger        
    axis.text = element_text(size=12),
    
    # title text is bigger
    plot.title = element_text(size=16)
      ) +

  # add one final layer of NULL, so if you comment out any lines
  # you never end up with a hanging `+` that awaits another ggplot layer
  NULL
```

<img src="/blogs/homework1_files/figure-html/ggplot-flights-toSFO-1.png" width="672" />

## Problem 9: Let's take a look at cancellations of flights to SFO. We create a new dataframe `cancellations` as follows


```r
cancellations <- flights %>% 
  
  # just filter for destination == 'SFO'
  filter(dest == 'SFO') %>% 
  
  # a cancelled flight is one with no `dep_time` 
  filter(is.na(dep_time))

#I have written the code below to show how to emulate the graph that has been provided us. This seemed to be the best way of explaining it. I have inserted comments within to show my workings.

#First, assemble the data that I need in a common place (via a left_join using the carrier as a common variable) and install the right grouping for the graph that was provided to us.
airlines %>%
left_join(cancellations,airlines,by="carrier")%>%
  filter(!is.na(origin)) %>%
  group_by(origin,name) %>%

# plot the data in the format required. The facet_grid option allows us to specify which grouping above goes on the rows and columns. I have then described the x values as discrete (chars not numerics) and I have tried to label the x values (not the axis) on the graph although they don't appear to show up (I tried using month.abb and month.name options before trying this explicit labelling). I request that the x and y axes are not labelled, but that the plot title is given in a suitably modest font. I then add the values of the counts on the bars themselves as an annotation. All of these things combined tend to emulate the graph that we were given.
  
  ggplot(mapping = aes(x=month)) +
    geom_bar() +
    facet_grid(rows = vars(name), cols = vars(origin))+
scale_x_discrete(breaks = 1:12, labels=c("Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec")) + xlab(NULL) + ylab(NULL) +
 labs(title = 'Cancellations at SFO by month, origin and airline') +  
  theme(plot.title = element_text(size = 12)
        ) +  geom_text(stat = 'count', aes(label = after_stat(count), vjust = 1,colour = "white", size = 2)) + theme(legend.position="none")
```

<img src="/blogs/homework1_files/figure-html/unnamed-chunk-11-1.png" width="672" />

I want you to think how we would organise our data manipulation to create the following plot. No need to write the code, just explain in words how you would go about it.

![](images/sfo-cancellations.png)

## Problem 10: On your own -- Hollywood Age Gap

The website <https://hollywoodagegap.com> is a record of *THE AGE DIFFERENCE IN YEARS BETWEEN MOVIE LOVE INTERESTS*. This is an informational site showing the age gap between movie love interests and the data follows certain rules:

-   The two (or more) actors play actual love interests (not just friends, coworkers, or some other non-romantic type of relationship)
-   The youngest of the two actors is at least 17 years old
-   No animated characters

The age gaps dataset includes "gender" columns, which always contain the values "man" or "woman". These values appear to indicate how the characters in each film identify and some of these values do not match how the actor identifies. We apologize if any characters are misgendered in the data!

The following is a data dictionary of the variables used

| variable           | class     | description                                                                                             |
|:--------------|:--------------|:------------------------------------------|
| movie_name         | character | Name of the film                                                                                        |
| release_year       | integer   | Release year                                                                                            |
| director           | character | Director of the film                                                                                    |
| age_difference     | integer   | Age difference between the characters in whole years                                                    |
| couple_number      | integer   | An identifier for the couple in case multiple couples are listed for this film                          |
| actor_1_name       | character | The name of the older actor in this couple                                                              |
| actor_2_name       | character | The name of the younger actor in this couple                                                            |
| character_1_gender | character | The gender of the older character, as identified by the person who submitted the data for this couple   |
| character_2_gender | character | The gender of the younger character, as identified by the person who submitted the data for this couple |
| actor_1_birthdate  | date      | The birthdate of the older member of the couple                                                         |
| actor_2_birthdate  | date      | The birthdate of the younger member of the couple                                                       |
| actor_1_age        | integer   | The age of the older actor when the film was released                                                   |
| actor_2_age        | integer   | The age of the younger actor when the film was released                                                 |


```r
age_gaps <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2023/2023-02-14/age_gaps.csv')
```

```
## Rows: 1155 Columns: 13
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr  (6): movie_name, director, actor_1_name, actor_2_name, character_1_gend...
## dbl  (5): release_year, age_difference, couple_number, actor_1_age, actor_2_age
## date (2): actor_1_birthdate, actor_2_birthdate
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
#1. How is `age_difference` distributed? 

ggplot(data = age_gaps, mapping = aes(age_difference)) + geom_histogram() 
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

<img src="/blogs/homework1_files/figure-html/unnamed-chunk-12-1.png" width="672" />

```r
#2. What's the 'typical' `age_difference` in movies?

ggplot(data = age_gaps, mapping = aes(age_difference)) + geom_boxplot() 
```

<img src="/blogs/homework1_files/figure-html/unnamed-chunk-12-2.png" width="672" />

```r
#3. Which movie has the greatest no. of love interests?
#

#I first look at the distribution of couple_number:
age_gaps %>%
  count(couple_number)
```

```
## # A tibble: 7 × 2
##   couple_number     n
##           <dbl> <int>
## 1             1   830
## 2             2   228
## 3             3    71
## 4             4    18
## 5             5     5
## 6             6     2
## 7             7     1
```

```r
#This reveals that there is only one movie with 7 love interests. So I filter the dataset on this number:
age_gaps %>%
  filter(couple_number == 7) 
```

```
## # A tibble: 1 × 13
##   movie_name    release_year director  age_difference couple_number actor_1_name
##   <chr>                <dbl> <chr>              <dbl>         <dbl> <chr>       
## 1 Love Actually         2003 Richard …              7             7 Martin Free…
## # ℹ 7 more variables: actor_2_name <chr>, character_1_gender <chr>,
## #   character_2_gender <chr>, actor_1_birthdate <date>,
## #   actor_2_birthdate <date>, actor_1_age <dbl>, actor_2_age <dbl>
```

```r
#The result is a dataframe that identifies the movie as Love Actually.


#4. Which actors/ actresses have the greatest number of love interests in this dataset?

#There are far too many actors to compare via a distribution. So, the best thing to do is to identify which male and female actors appear more frequently in the dataset. I selected actors who have a frequency down to 6. This means that I need the top 30 number of times that a male actor is in a couple for a movie and the top 20 number of times that a female actor is in a couple for a movie. This selection also resulted in a nearly equal number of male (33) and female (31) actors to compare. See:

actor1 <- age_gaps %>%
  count(actor_1_name) %>%
  arrange(desc(n)) %>%
  top_n(30) 
```

```
## Selecting by n
```

```r
actor1
```

```
## # A tibble: 33 × 2
##    actor_1_name          n
##    <chr>             <int>
##  1 Keanu Reeves         24
##  2 Adam Sandler         18
##  3 Roger Moore          17
##  4 Sean Connery         15
##  5 Harrison Ford        13
##  6 Johnny Depp          12
##  7 Pierce Brosnan       12
##  8 Leonardo DiCaprio    11
##  9 Richard Gere         10
## 10 Brad Pitt             9
## # ℹ 23 more rows
```

```r
actor2 <- age_gaps %>%
  count(actor_2_name) %>%
  arrange(desc(n)) %>%
  top_n(20) 
```

```
## Selecting by n
```

```r
actor2
```

```
## # A tibble: 31 × 2
##    actor_2_name           n
##    <chr>              <int>
##  1 Keira Knightley       13
##  2 Scarlett Johansson    13
##  3 Emma Stone            11
##  4 Renee Zellweger       11
##  5 Drew Barrymore         9
##  6 Jennifer Lawrence      9
##  7 Julia Roberts          9
##  8 Amanda Seyfried        8
##  9 Audrey Hepburn         8
## 10 Jennifer Aniston       8
## # ℹ 21 more rows
```

```r
#Thus, Keanu Reeves is the actors with the greatest number (24) of love interests. There are two actresses, Keira Knightley and Scarlet Johannsen, who equally register the highest (13) number of love interests.

#5. Is the age difference staying constant over the years (1935 - 2022)?

  ggplot(data = age_gaps, mapping = aes(release_year,age_difference)) + geom_point()
```

<img src="/blogs/homework1_files/figure-html/unnamed-chunk-12-3.png" width="672" />

```r
# This plot shows that the variance in age difference is decreasing with increasing year (of movie release), and there are more data at the greater age differences at the more recent years. Thus, this suggests a trend towards greater coupling up greater age differences and a greater range of age differences.  

#  6.How frequently does Hollywood depict same-gender love interests?
  age_gaps%>%
    count(character_1_gender,character_2_gender)
```

```
## # A tibble: 4 × 3
##   character_1_gender character_2_gender     n
##   <chr>              <chr>              <int>
## 1 man                man                   12
## 2 man                woman                929
## 3 woman              man                  203
## 4 woman              woman                 11
```

```r
#This table shows that same-gender love interests appear 11 times in male-male and 12 times in female-female liaisons. The total number of options is 1155, so the depiction of same-gender love interests occurs about 1 percent of the time.
```



How would you explore this data set? Here are some ideas of tables/ graphs to help you with your analysis

-   How is `age_difference` distributed? What's the 'typical' `age_difference` in movies?

-   The `half plus seven\` rule. Large age disparities in relationships carry certain stigmas. One popular rule of thumb is the [half-your-age-plus-seven](https://en.wikipedia.org/wiki/Age_disparity_in_sexual_relationships#The_.22half-your-age-plus-seven.22_rule) rule. This rule states you should never date anyone under half your age plus seven, establishing a minimum boundary on whom one can date. In order for a dating relationship to be acceptable under this rule, your partner's age must be:

`$$\frac{\text{Your age}}{2} + 7 \< \text{Partner Age} \< (\text{Your age} - 7) \* 2$$` How frequently does this rule apply in this dataset?

-   Which movie has the greatest number of love interests?
-   Which actors/ actresses have the greatest number of love interests in this dataset?
-   Is the mean/median age difference staying constant over the years (1935 - 2022)?
-   How frequently does Hollywood depict same-gender love interests?

# Deliverables

There is a lot of explanatory text, comments, etc. You do not need these, so delete them and produce a stand-alone document that you could share with someone. Render the edited and completed Quarto Markdown (qmd) file as a Word document (use the "Render" button at the top of the script editor window) and upload it to Canvas. You must be commiting and pushing tour changes to your own Github repo as you go along.

# Details

-   Who did you collaborate with: I worked on my own throughout except that I asked the course professor for help with how to code in the case_when part of Question 5; and I asked him a range of questions regarding the logistics of the assignment e.g. how we should submit it, what is expected in terms of certain questions e.g. Question 10 as this seems open ended.

-   Approximately how much time did you spend on this problem set: several full days as I have been learning R and the IDE etc at the same time, and I've been working through various on-line

    media to support my learning of R (I code in other languages, but R is new to me).

-   What, if anything, gave you the most trouble: the English comprehension of what the questions expected of me were the hardest. Once I knew what the question wanted, I could work it through pretty straight-forwardly. I found the multi-options of the ggplot pretty time consuming as there are so many options.

**Please seek out help when you need it,** and remember the [15-minute rule](https://mam2022.netlify.app/syllabus/#the-15-minute-rule){target="_blank"}. You know enough R (and have enough examples of code from class and your readings) to be able to do this. If you get stuck, ask for help from others, post a question on Slack-- and remember that I am here to help too!

> As a true test to yourself, do you understand the code you submitted and are you able to explain it to someone else?

# Rubric

13/13: Problem set is 100% completed. Every question was attempted and answered, and most answers are correct. Code is well-documented (both self-documented and with additional comments as necessary). Used tidyverse, instead of base R. Graphs and tables are properly labelled. Analysis is clear and easy to follow, either because graphs are labeled clearly or you've written additional text to describe how you interpret the output. Multiple Github commits. Work is exceptional. I will not assign these often.

8/13: Problem set is 60--80% complete and most answers are correct. This is the expected level of performance. Solid effort. Hits all the elements. No clear mistakes. Easy to follow (both the code and the output). A few Github commits.

5/13: Problem set is less than 60% complete and/or most answers are incorrect. This indicates that you need to improve next time. I will hopefully not assign these often. Displays minimal effort. Doesn't complete all components. Code is poorly written and not documented. Uses the same type of plot for each graph, or doesn't use plots appropriate for the variables being analyzed. No Github commits.
