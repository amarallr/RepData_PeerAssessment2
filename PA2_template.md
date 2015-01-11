# Reproducible Research: Peer Assessment 2
Lucas Rodrigues Amaral  
# Title
USA weather events most harmful with respect to population health and greatest economic consequences.  

# Synopsis
Immediately after the title, there should be a synopsis which describes and summarizes your analysis in at most 10 complete sentences.  

# Data Processing
There should be a section titled Data Processing which describes (in words and code) how the data were loaded into R and processed for analysis. In particular, your analysis must start from the raw CSV file containing the data. You cannot do any preprocessing outside the document. If preprocessing is time-consuming you may consider using the cache = TRUE option for certain code chunks.  

## Libraries

```r
library(plyr)
library(sqldf)
```

```
## Loading required package: gsubfn
## Loading required package: proto
## Loading required package: RSQLite
## Loading required package: DBI
```

## Loading and preprocessing the data

1. Load the data (i.e. read.csv())

```r
#  Set aspects of the locale for the R process
Sys.setlocale("LC_TIME","English") 
```

```
## [1] "English_United States.1252"
```

```r
# Set the working directory
setwd("C:\\R\\courseraworkspace\\repdata\\RepData_PeerAssessment2")

# Define some options for knitr
knitr::opts_chunk$set(tidy=FALSE, fig.path='figures/')
knitr::opts_chunk$set(cache=TRUE, cache.path = "knitrcache/test-")

# Set data file name
dataFileName <- "repdata-data-StormData.csv"

if(!exists("dsStormData")) {
        # Read data file
        dsStormData <- read.csv(dataFileName)
        
        # Select only the collumns needed
        dsStormData <- dsStormData[ , c("EVTYPE", "BGN_DATE", "FATALITIES", "INJURIES", "PROPDMG", "PROPDMGEXP", "CROPDMG", "CROPDMGEXP")]
        
        # Format column as POSIXct datetime
        dsStormData$BGN_DATE <- as.POSIXct(dsStormData$BGN_DATE,format="%m/%d/%Y %H:%M:%S")
}


# Display the dataset structure
str(dsStormData)
```

```
## 'data.frame':	902297 obs. of  8 variables:
##  $ EVTYPE    : Factor w/ 985 levels "   HIGH SURF ADVISORY",..: 834 834 834 834 834 834 834 834 834 834 ...
##  $ BGN_DATE  : POSIXct, format: "1950-04-18" "1950-04-18" ...
##  $ FATALITIES: num  0 0 0 0 0 0 0 0 1 0 ...
##  $ INJURIES  : num  15 0 2 2 2 6 1 0 14 0 ...
##  $ PROPDMG   : num  25 2.5 25 2.5 2.5 2.5 2.5 2.5 25 25 ...
##  $ PROPDMGEXP: Factor w/ 19 levels "","-","?","+",..: 17 17 17 17 17 17 17 17 17 17 ...
##  $ CROPDMG   : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ CROPDMGEXP: Factor w/ 9 levels "","?","0","2",..: 1 1 1 1 1 1 1 1 1 1 ...
```

```r
# Display the dataset dimensions
dim(dsStormData)
```

```
## [1] 902297      8
```

```r
# Display the 10th first rows
head(dsStormData, 10)
```

```
##     EVTYPE   BGN_DATE FATALITIES INJURIES PROPDMG PROPDMGEXP CROPDMG
## 1  TORNADO 1950-04-18          0       15    25.0          K       0
## 2  TORNADO 1950-04-18          0        0     2.5          K       0
## 3  TORNADO 1951-02-20          0        2    25.0          K       0
## 4  TORNADO 1951-06-08          0        2     2.5          K       0
## 5  TORNADO 1951-11-15          0        2     2.5          K       0
## 6  TORNADO 1951-11-15          0        6     2.5          K       0
## 7  TORNADO 1951-11-16          0        1     2.5          K       0
## 8  TORNADO 1952-01-22          0        0     2.5          K       0
## 9  TORNADO 1952-02-13          1       14    25.0          K       0
## 10 TORNADO 1952-02-13          0        0    25.0          K       0
##    CROPDMGEXP
## 1            
## 2            
## 3            
## 4            
## 5            
## 6            
## 7            
## 8            
## 9            
## 10
```

```r
# Decode PROPDMGEXP and CROPDMGEXP to numeric
dsStormData$PROPDMGEXPValue <- as.numeric(mapvalues(dsStormData$PROPDMGEXP,
c("K","M","", "B","m","+","0","5","6","?","4","2","3","h","7","H","-","1","8"), 
c(1e3,1e6, 1, 1e9,1e6,  1,  1,1e5,1e6,  1,1e4,1e2,1e3,  1,1e7,1e2,  1, 10,1e8)))

dsStormData$CROPDMGEXPValue <- as.numeric(mapvalues(dsStormData$CROPDMGEXP,
c("","M","K","m","B","?","0","k","2"),
c( 1,1e6,1e3,1e6,1e9,1,1,1e3,1e2)))


# Calculate PROPDMG and CROPDMG values
dsStormData$PROPDMGValue <- dsStormData$PROPDMG * dsStormData$PROPDMGEXPValue
dsStormData$CROPDMGValue <- dsStormData$CROPDMG * dsStormData$CROPDMGEXPValue
```


# Results
There should be a section titled Results in which your results are presented.

You may have other sections in your analysis, but Data Processing and Results are required.

The analysis document must have at least one figure containing a plot.

Your analyis must have no more than three figures. Figures may have multiple plots in them (i.e. panel plots), but there cannot be more than three figures total.

You must show all your code for the work in your analysis document. This may make the document a bit verbose, but that is okay. In general, you should ensure that echo = TRUE for every code chunk (this is the default setting in knitr).
