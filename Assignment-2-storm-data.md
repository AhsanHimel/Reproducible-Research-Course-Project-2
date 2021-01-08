---
title: "Reproducible Research: Course Project 2"
author: 'MD AHSANUL ISLAM'
date: "08/01/2021"
output:
  html_document:
    keep_md: yes
    toc: true
    toc_float: true
    toc_depth: 4
    theme: cerulean
    # default, cerulean, journal, flatly, darkly, readable, spacelab, united, cosmo, lumen, paper, sandstone, simplex, and yeti
    highlight: haddock
    # default, tango, pygments, kate, monochrome, espresso, zenburn, haddock, breezedark, and textmate
    code_download: true
---


---

# Synopsis


# Introduction


This is the second course project for the course "Reproducible Research" which is part of the Coursera’s "Data Science: Foundations using R" Specialization.

Storms and other severe weather events can cause both public health and economic problems for communities and municipalities. Many severe events can result in fatalities, injuries, and property damage, and preventing such outcomes to the extent possible is a key concern.

This project involves exploring the U.S. National Oceanic and Atmospheric Administration’s (NOAA) storm database. This database tracks characteristics of major storms and weather events in the United States, including when and where they occur, as well as estimates of any fatalities, injuries, and property damage.

# Data Processing

There is also some documentation of the database available:

- [National Weather Service Storm Data Documentation](https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2Fpd01016005curr.pdf)
- [National Climatic Data Center Storm Events FAQ](https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2FNCDC%20Storm%20Events-FAQ%20Page.pdf)


## Loading data


```r
> # bz2file <- bzfile("repdata_data_StormData.csv.bz2")
> # if(!exists("storm")) {
> #   storm <- read.csv(bz2file, na.strings = c("NA"))
> # }
> 
> storm <- data.table::fread(input = "repdata_data_StormData.csv.bz2")
```


Loading required packages:

```r
> library(tidyverse)
```


## Examining the data


```r
> names(storm)
```

```
 [1] "STATE__"    "BGN_DATE"   "BGN_TIME"   "TIME_ZONE"  "COUNTY"    
 [6] "COUNTYNAME" "STATE"      "EVTYPE"     "BGN_RANGE"  "BGN_AZI"   
[11] "BGN_LOCATI" "END_DATE"   "END_TIME"   "COUNTY_END" "COUNTYENDN"
[16] "END_RANGE"  "END_AZI"    "END_LOCATI" "LENGTH"     "WIDTH"     
[21] "F"          "MAG"        "FATALITIES" "INJURIES"   "PROPDMG"   
[26] "PROPDMGEXP" "CROPDMG"    "CROPDMGEXP" "WFO"        "STATEOFFIC"
[31] "ZONENAMES"  "LATITUDE"   "LONGITUDE"  "LATITUDE_E" "LONGITUDE_"
[36] "REMARKS"    "REFNUM"    
```



```r
> dplyr::glimpse(storm)
```

```
Rows: 902,297
Columns: 37
$ STATE__    <dbl> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,...
$ BGN_DATE   <chr> "4/18/1950 0:00:00", "4/18/1950 0:00:00", "2/20/1951 0:0...
$ BGN_TIME   <chr> "0130", "0145", "1600", "0900", "1500", "2000", "0100", ...
$ TIME_ZONE  <chr> "CST", "CST", "CST", "CST", "CST", "CST", "CST", "CST", ...
$ COUNTY     <dbl> 97, 3, 57, 89, 43, 77, 9, 123, 125, 57, 43, 9, 73, 49, 1...
$ COUNTYNAME <chr> "MOBILE", "BALDWIN", "FAYETTE", "MADISON", "CULLMAN", "L...
$ STATE      <chr> "AL", "AL", "AL", "AL", "AL", "AL", "AL", "AL", "AL", "A...
$ EVTYPE     <chr> "TORNADO", "TORNADO", "TORNADO", "TORNADO", "TORNADO", "...
$ BGN_RANGE  <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,...
$ BGN_AZI    <chr> "", "", "", "", "", "", "", "", "", "", "", "", "", "", ...
$ BGN_LOCATI <chr> "", "", "", "", "", "", "", "", "", "", "", "", "", "", ...
$ END_DATE   <chr> "", "", "", "", "", "", "", "", "", "", "", "", "", "", ...
$ END_TIME   <chr> "", "", "", "", "", "", "", "", "", "", "", "", "", "", ...
$ COUNTY_END <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,...
$ COUNTYENDN <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
$ END_RANGE  <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,...
$ END_AZI    <chr> "", "", "", "", "", "", "", "", "", "", "", "", "", "", ...
$ END_LOCATI <chr> "", "", "", "", "", "", "", "", "", "", "", "", "", "", ...
$ LENGTH     <dbl> 14.0, 2.0, 0.1, 0.0, 0.0, 1.5, 1.5, 0.0, 3.3, 2.3, 1.3, ...
$ WIDTH      <dbl> 100, 150, 123, 100, 150, 177, 33, 33, 100, 100, 400, 400...
$ F          <int> 3, 2, 2, 2, 2, 2, 2, 1, 3, 3, 1, 1, 3, 3, 3, 4, 1, 1, 1,...
$ MAG        <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,...
$ FATALITIES <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 4, 0, 0, 0,...
$ INJURIES   <dbl> 15, 0, 2, 2, 2, 6, 1, 0, 14, 0, 3, 3, 26, 12, 6, 50, 2, ...
$ PROPDMG    <dbl> 25.0, 2.5, 25.0, 2.5, 2.5, 2.5, 2.5, 2.5, 25.0, 25.0, 2....
$ PROPDMGEXP <chr> "K", "K", "K", "K", "K", "K", "K", "K", "K", "K", "M", "...
$ CROPDMG    <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,...
$ CROPDMGEXP <chr> "", "", "", "", "", "", "", "", "", "", "", "", "", "", ...
$ WFO        <chr> "", "", "", "", "", "", "", "", "", "", "", "", "", "", ...
$ STATEOFFIC <chr> "", "", "", "", "", "", "", "", "", "", "", "", "", "", ...
$ ZONENAMES  <chr> "", "", "", "", "", "", "", "", "", "", "", "", "", "", ...
$ LATITUDE   <dbl> 3040, 3042, 3340, 3458, 3412, 3450, 3405, 3255, 3334, 33...
$ LONGITUDE  <dbl> 8812, 8755, 8742, 8626, 8642, 8748, 8631, 8558, 8740, 87...
$ LATITUDE_E <dbl> 3051, 0, 0, 0, 0, 0, 0, 0, 3336, 3337, 3402, 3404, 0, 34...
$ LONGITUDE_ <dbl> 8806, 0, 0, 0, 0, 0, 0, 0, 8738, 8737, 8644, 8640, 0, 85...
$ REMARKS    <chr> "", "", "", "", "", "", "", "", "", "", "", "", "", "", ...
$ REFNUM     <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 1...
```


```r
> dim(storm)
```

```
[1] 902297     37
```

## Variables to study

The variables that I will use to get some insights are as follows:

1. FATALITIES : approximate number of deaths   
2. INJURIES   : approximate number of injuries  
3. PROPDMG    : approximate property damags  
4. PROPDMGEXP : the units for property damage value  
5. CROPDMG    : approximate crop damages  
6. CROPDMGEXP : the units for crop damage value  
7. EVTYPE     : weather events

## Data Processing 


```r
> df <- storm %>% select(FATALITIES, INJURIES, PROPDMG, PROPDMGEXP,
+                        CROPDMG, CROPDMGEXP, EVTYPE) %>% 
+   mutate_if(is.character, as.factor)
```


```r
> glimpse(df)
```

```
Rows: 902,297
Columns: 7
$ FATALITIES <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 4, 0, 0, 0,...
$ INJURIES   <dbl> 15, 0, 2, 2, 2, 6, 1, 0, 14, 0, 3, 3, 26, 12, 6, 50, 2, ...
$ PROPDMG    <dbl> 25.0, 2.5, 25.0, 2.5, 2.5, 2.5, 2.5, 2.5, 25.0, 25.0, 2....
$ PROPDMGEXP <fct> K, K, K, K, K, K, K, K, K, K, M, M, K, K, K, K, K, K, K,...
$ CROPDMG    <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,...
$ CROPDMGEXP <fct> , , , , , , , , , , , , , , , , , , , , , , , , , 
$ EVTYPE     <fct> TORNADO, TORNADO, TORNADO, TORNADO, TORNADO, TORNADO, TO...
```

Checking for missing values:

```r
> summary(df)
```

```
   FATALITIES          INJURIES            PROPDMG          PROPDMGEXP    
 Min.   :  0.0000   Min.   :   0.0000   Min.   :   0.00          :465934  
 1st Qu.:  0.0000   1st Qu.:   0.0000   1st Qu.:   0.00   K      :424665  
 Median :  0.0000   Median :   0.0000   Median :   0.00   M      : 11330  
 Mean   :  0.0168   Mean   :   0.1557   Mean   :  12.06   0      :   216  
 3rd Qu.:  0.0000   3rd Qu.:   0.0000   3rd Qu.:   0.50   B      :    40  
 Max.   :583.0000   Max.   :1700.0000   Max.   :5000.00   5      :    28  
                                                          (Other):    84  
    CROPDMG          CROPDMGEXP                   EVTYPE      
 Min.   :  0.000          :618413   HAIL             :288661  
 1st Qu.:  0.000   K      :281832   TSTM WIND        :219940  
 Median :  0.000   M      :  1994   THUNDERSTORM WIND: 82563  
 Mean   :  1.527   k      :    21   TORNADO          : 60652  
 3rd Qu.:  0.000   0      :    19   FLASH FLOOD      : 54277  
 Max.   :990.000   B      :     9   FLOOD            : 25326  
                   (Other):     9   (Other)          :170878  
```
There are missing values in `PROPDMGEXP` and `CROPDMGEXP` variables. For now we may ignore this. If any issue is faced then I may exclude these observations for the specific case.

### Health Impact - Data Processing


```r
> df.injuries <- df %>% 
+   select(EVTYPE, INJURIES) %>% 
+   group_by(EVTYPE) %>% 
+   summarise(total.injuries = sum(INJURIES)) %>% 
+   arrange(-total.injuries)
```

Top 10 events according to number of injuries:

```r
> head(df.injuries, 10)
```

```
# A tibble: 10 x 2
   EVTYPE            total.injuries
   <fct>                      <dbl>
 1 TORNADO                    91346
 2 TSTM WIND                   6957
 3 FLOOD                       6789
 4 EXCESSIVE HEAT              6525
 5 LIGHTNING                   5230
 6 HEAT                        2100
 7 ICE STORM                   1975
 8 FLASH FLOOD                 1777
 9 THUNDERSTORM WIND           1488
10 HAIL                        1361
```


```r
> df.fatalities <- df %>% 
+   select(EVTYPE, FATALITIES) %>% 
+   group_by(EVTYPE) %>% 
+   summarise(total.fatalities = sum(FATALITIES)) %>% 
+   arrange(-total.fatalities)
```

Top 10 events according to number of fatalities/deaths:

```r
> head(df.fatalities, 10)
```

```
# A tibble: 10 x 2
   EVTYPE         total.fatalities
   <fct>                     <dbl>
 1 TORNADO                    5633
 2 EXCESSIVE HEAT             1903
 3 FLASH FLOOD                 978
 4 HEAT                        937
 5 LIGHTNING                   816
 6 TSTM WIND                   504
 7 FLOOD                       470
 8 RIP CURRENT                 368
 9 HIGH WIND                   248
10 AVALANCHE                   224
```



### Economic Impact - Data Processing

According to [this](https://rpubs.com/gcctang1/271126/), the data provides two types of economic impact; Property damage (PROPDMG) and crop damage (CROPDMG). 

The actual damage in $USD is indicated by PROPDMGEXP and CROPDMGEXP parameters. The index in the PROPDMGEXP and CROPDMGEXP can be interpreted as the following:-

* H, h -> hundreds = x100  
* K, K -> kilos = x1,000    
* M, m -> millions = x1,000,000  
* B,b -> billions = x1,000,000,000  
* (+) -> x1  
* (-) -> x0  
* (?) -> x0  
* blank -> x0  
 
The total damage caused by each event type needs to be calculated manually - 


```r
> df.damage <- df %>% select(EVTYPE,
+                            PROPDMG, PROPDMGEXP,
+                            CROPDMG, CROPDMGEXP) %>% 
+   group_by(EVTYPE)
```


```r
> df.damage <- df %>% select(EVTYPE,
+                            PROPDMG, PROPDMGEXP,
+                            CROPDMG, CROPDMGEXP)
> Symbol <- sort(unique(df.damage$PROPDMGEXP))
> Multiplier <- c(0,0,0,1,10,10,10,10,10,10,10,10,10,10^9,10^2,10^2,10^3,10^6,10^6)
> convert.Multiplier <- data.frame(Symbol, Multiplier)
> 
> df.damage.amount <- df.damage %>% 
+   mutate(Prop.Multiplier = Multiplier[match(df.damage$PROPDMGEXP,
+                                             convert.Multiplier$Symbol)],
+          Crop.Multiplier = Multiplier[match(df.damage$CROPDMGEXP,
+                                             convert.Multiplier$Symbol)],
+          PROPDMG.amount = PROPDMG*Prop.Multiplier,
+          CROPDMG.amount = CROPDMG*Crop.Multiplier,
+          Total.damage = PROPDMG.amount + CROPDMG.amount) %>%
+   group_by(EVTYPE) %>% 
+   summarize(TOTAL.DMG.EVTYPE = sum(Total.damage))%>%
+   arrange(-TOTAL.DMG.EVTYPE)
```

To 10 events according to amount of damage in USD:

```r
> head(df.damage.amount, 10)
```

```
# A tibble: 10 x 2
   EVTYPE            TOTAL.DMG.EVTYPE
   <fct>                        <dbl>
 1 FLOOD                 150319678250
 2 HURRICANE/TYPHOON      71913712800
 3 TORNADO                57352117607
 4 STORM SURGE            43323541000
 5 FLASH FLOOD            17562132111
 6 DROUGHT                15018672000
 7 HURRICANE              14610229010
 8 RIVER FLOOD            10148404500
 9 ICE STORM               8967041810
10 TROPICAL STORM          8382236550
```

# Visulizations 













