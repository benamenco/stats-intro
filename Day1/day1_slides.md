Data Organisation and Introduction to R
========================================================
author: Hugo Tavares (hugo.tavares@slcu.cam.ac.uk)
date: 7 Dec 2016
autosize: true
width: 1500
height: 900

Cancer Research Uk Bioinformatics Winter School: Cambridge, 7th-9th December 2016


Outline of Day 1
========================================================

* Session 1 - Data Organisation (in spreadsheets)

* Session 2 - Introduction to R and Rstudio

* Session 3 - Data manipulation in R

* Session 4 - Data visualisation in R (short intro - more on Day 2)

We will use material from the lessons developed by the [Data Carpentry](http://www.datacarpentry.org/) initiative.

This is not a full Data Carpentry course, just a shortened version of it (but we do run these at the University, if you're interested).


Data Organisation (in spreadsheets)
========================================================

* Recognise and apply principles and good practices for organising your data
* Recognise common pitfalls when recording data (and learn to avoid them!)
* Understanding file formats and their suitability for other programs (e.g. R)

The material for this session is available from the Data Carpentry website ( [here](http://www.datacarpentry.org/spreadsheet-ecology-lesson/01-format-data.html)).


Data Organisation - be cautious with dates
========================================================

Let's say I recorded some collection dates in the form "Month-Day". 

I didn't bother with the year, because I know what year I'm in right now!

Input these data in a worksheet:

```
Feb-12
Mar-14
Apr-21
```

What Day, Month, Year is assumed by your spreadsheet program?


Data Organisation - be cautious with dates
========================================================

Be careful, different spreadsheet programs assume different things!

Even different versions of the same software might assume different things!

* To avoid problems, **store dates as separate variables with the Day, Month, Year**

More about dates [here](http://www.datacarpentry.org/spreadsheet-ecology-lesson/03-dates-as-data.html).


Data Organisation - exporting data from spreadsheets
========================================================

If possible, store/distribute data in `CSV` (comma-separated-values) format:

```
species,year,month,day,weight_kg,height_cm
mouse,2014,3,21,2,10
dog,2013,7,2,20,60
cat,2016,12,7,4.2,25
```

More about exporting data [here](http://www.datacarpentry.org/spreadsheet-ecology-lesson/05-exporting-data.html).


Tea break and a stretch
========================================================




Introduction to R and Rstudio
========================================================

* Getting to know Rstudio
* Learn basic syntax of R

The materials for this session are available from the Data Carpentry website (Lesson 1 [here](http://www.datacarpentry.org/R-ecology-lesson/)).


Data manipulation in R
========================================================

* Read data from a file
* Understand what factors are
* Understand how to manipulate a `data.frame` object
* Filter and summarise data

The materials for this session are available from the Data Carpentry website (Lessons 2-4 [here](http://www.datacarpentry.org/R-ecology-lesson/)).


Data visualisation in R
========================================================

* Learn about 3 types of plots: scatterplots, histograms, boxplots
* Make some customisations to produce clearer plots

We will no longer use the Data Carpentry lessons, because we will use 
`base` R graphics (the default in R). 

However, if you want to learn about an alternative, please check the Data Carpentry 
[lesson](http://www.datacarpentry.org/R-ecology-lesson/05-visualization-ggplot2.html) 
on using the `ggplot2` package.


Data visualisation in R
========================================================

Make sure you have the `surveys` data in the right format (as detailed in [this lesson]
(http://www.datacarpentry.org/R-ecology-lesson/04-dplyr.html#exporting-data)):




```r
library(dplyr)
surveys_complete <- surveys %>%
  filter(species_id != "",         # remove missing species_id
         !is.na(weight),           # remove missing weight
         !is.na(hindfoot_length),  # remove missing hindfoot_length
         sex != "")                # remove missing sex

## Extract the most common species_id
species_counts <- surveys_complete %>%
                  group_by(species_id) %>%
                  tally %>%
                  filter(n >= 50) %>%
                  select(species_id)

## Only keep the most common species
surveys_complete <- surveys_complete %>%
                 filter(species_id %in% species_counts$species_id)
```


Data visualisation in R - scatterplot
========================================================

Let's say we want to see if there is a correlation between the
animal's weight and its hindfoot length.
We can use the `plot()` function for this, indicating what values we want 
to be assigned to the `x` and `y` axis.


```r
plot(x = surveys_complete$weight, y = surveys_complete$hindfoot_length)
```

<img src="day1_slides-figure/unnamed-chunk-3-1.png" title="plot of chunk unnamed-chunk-3" alt="plot of chunk unnamed-chunk-3" style="display: block; margin: auto;" />

Challenge: how can we change the axis labels? Check the help for the function with `?plot`


Data visualisation in R - scatterplot
========================================================


```r
plot(surveys_complete$weight, surveys_complete$hindfoot_length,
     xlab = "Weight (g)", ylab = "Hindfoot length (mm)",
     pch = 16, col = "seagreen")
```

<img src="day1_slides-figure/unnamed-chunk-4-1.png" title="plot of chunk unnamed-chunk-4" alt="plot of chunk unnamed-chunk-4" style="display: block; margin: auto;" />

What do the `pch` and `col` arguments do? 

Many of these graphical parameters can be found in the `par` function (see its help: `?par`).


Data visualisation in R - histogram
========================================================

We would like to know what kind of distribution our data follow.

A typical way to display this information is using a histogram:


```r
hist(surveys_complete$hindfoot_length, breaks = 100, xlab = "Hindfoot length (mm)")
```

<img src="day1_slides-figure/unnamed-chunk-5-1.png" title="plot of chunk unnamed-chunk-5" alt="plot of chunk unnamed-chunk-5" style="display: block; margin: auto;" />

What does this distribution suggest?


Data visualisation in R - boxplot
========================================================

The previous histogram suggests that we have several distributions, which is likely 
because we have weights from different species. We can visualise the distributions
of the weight within each species using a boxplot.


The syntax for this is special:

```r
boxplot(surveys_complete$hindfoot_length ~ surveys_complete$species_id)
```

We will learn more about the special '~' tomorrow, but for now it means that we want to
plot the hindfoot length split by species id.


Data visualisation in R - boxplot
========================================================


```r
boxplot(surveys_complete$hindfoot_length ~ surveys_complete$species_id, pch = 16, las = 2)
```

<img src="day1_slides-figure/unnamed-chunk-7-1.png" title="plot of chunk unnamed-chunk-7" alt="plot of chunk unnamed-chunk-7" style="display: block; margin: auto;" />

Why are there so many species with no data?

(also, notice the new `las` argument. Check `?par` to know more about it.)


Data visualisation in R - boxplot
========================================================

Even though we filtered our data earlier, the levels of our factor variables 
remained unchanged.


```r
levels(surveys_complete$species_id)
```

```
 [1] "AB" "AH" "AS" "BA" "CB" "CM" "CQ" "CS" "CT" "CU" "CV" "DM" "DO" "DS"
[15] "DX" "NL" "OL" "OT" "OX" "PB" "PC" "PE" "PF" "PG" "PH" "PI" "PL" "PM"
[29] "PP" "PU" "PX" "RF" "RM" "RO" "RX" "SA" "SC" "SF" "SH" "SO" "SS" "ST"
[43] "SU" "UL" "UP" "UR" "US" "ZL"
```


```r
levels(surveys$species_id)
```

```
 [1] "AB" "AH" "AS" "BA" "CB" "CM" "CQ" "CS" "CT" "CU" "CV" "DM" "DO" "DS"
[15] "DX" "NL" "OL" "OT" "OX" "PB" "PC" "PE" "PF" "PG" "PH" "PI" "PL" "PM"
[29] "PP" "PU" "PX" "RF" "RM" "RO" "RX" "SA" "SC" "SF" "SH" "SO" "SS" "ST"
[43] "SU" "UL" "UP" "UR" "US" "ZL"
```


Data visualisation in R - boxplot
========================================================

If we want to remove these levels, we can use the function `droplevels()`:


```r
surveys_complete <- droplevels(surveys_complete)
levels(surveys_complete$species_id)
```

```
 [1] "DM" "DO" "DS" "NL" "OL" "OT" "PB" "PE" "PF" "PM" "PP" "RF" "RM" "SH"
```


Data visualisation in R - boxplot
========================================================


```r
boxplot(surveys_complete$hindfoot_length ~ surveys_complete$species_id, pch = 16, las = 1)
```

<img src="day1_slides-figure/unnamed-chunk-11-1.png" title="plot of chunk unnamed-chunk-11" alt="plot of chunk unnamed-chunk-11" style="display: block; margin: auto;" />



