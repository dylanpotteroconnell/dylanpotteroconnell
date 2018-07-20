---
layout: post
title: iPhone Walking Data Cleaning
---

Overall, these blog posts are intended to be a simple diary for some side projects that I work on in my spare time. They have no specific end goal, I just think it's valuable to get used to recording and explaining the things that you do. I'm quite novice in most things related to computing, so this is just one person's learning exploration, and may be riddled with poor form and errors.

A few weeks ago, I was perusing the depths of my phone, when I opened the preloaded "Health" app for the first time. To my surprise, it was filled to the brim with data concerning my walking habits, accurate and detailed descriptions of the steps I took each day. I thought this was intriguing, so figured it would be an interesting mini project to export the data to my computer for easy examination.

Luckily, you can easily and simply export all current data (under the profile button in the top right), which sends you an email with two files: export_cda.xml (which contains general information about the user), and export.xml (which contains all the relevant walking data). It's worth noting that this exports all health data. Personally, I never set it up to track anything else, but for people who have heart rate sensors and etc set up, the format may well be different.

I browsed the XML file using a text editor for some time, to get a feel for the data. The data is stored as individual entries (which are termed Records in this dataset), and with a type of data (i.e. distance, steps, etc), a value, and information about the timing and device used to take the data. The next step was to clean the data into a form that we could work with. I decided to use R, because I'm learning that for a course so I could use the practice. The totality of my R experience is roughly 1/3 of an intro course, so there will certainly be huge inefficiencies with anything I do, but I thought it would be useful practice.

First, to parse the data, there is (unsurprisingly) an R library "XML" which can help parse the data. A quick google search and some trial and error shows that we can first read the XML file as a character string into R, before converting it into an XML object (xmlTreeParse), before converting that to a list in R. Then, we drop any extraneous bits that aren't a 'Record'.
 
~~~~
library(XML)
xmldoc <- paste(readLines('export.xml'), '\n', collapse='')
# We use xmlparse and xmltolist to convert it to an XML object in R
x <- xmlTreeParse(xmldoc, asText=TRUE)
x <- xmlToList(x$doc$children[[1]])
# We only keep the files of the correct form, named 'Record'
x <- x[names(x) == 'Record']

~~~~

The resuling list has a series of columns, which are each one 'Record'. We note that in our case, there are three types of health data: step count, distance walked, and flights of stairs climbed.

~~~~
unique(x[1])

#returns
                                                type
#1                  HKQuantityTypeIdentifierStepCount
#7510  HKQuantityTypeIdentifierDistanceWalkingRunning
#15022         HKQuantityTypeIdentifierFlightsClimbed

~~~~

We create three  separate data frames, one for each data type, and we iterate through x, adding each 'Record' as a row to the corresponding data frame.

~~~~
stepCount <- list()
distWalked <- list()
flightsClimbed <- list()
options(stringsAsFactors=FALSE)

for(rec in x){
  if(rec[1] == "HKQuantityTypeIdentifierStepCount"){
    stepCount <- rbind(stepCount, rec)
  }
  if(rec[1] == "HKQuantityTypeIdentifierDistanceWalkingRunning"){
    distWalked <- rbind(distWalked, rec)
  }
  if(rec[1] == "HKQuantityTypeIdentifierFlightsClimbed"){
    flightsClimbed <- rbind(flightsClimbed, rec)
  }
}

~~~~

Next, we begin the boring cleaning of this data. Due to the similarities of the data frames, this should clearly be done with some form of iteration. However, I had some trouble putting the data frames into a list and iterating through it, and thus to save time I simply did the data cleaning for each data frame separately. it's important to note that if one's health data tracked other metrics, this process would have to be adjusted for any new types of data included. Below we show the process for just stepCount, the full data file does it for all 3.

~~~~

# These records have been added as lists, rather than proper variables. 
# We thus transform each variable column into a character variable, 
# to make cleaning easier.
stepCount <- lapply(data.frame(stepCount), as.character)
distWalked <- lapply(data.frame(distWalked), as.character)
flightsClimbed <- lapply(data.frame(flightsClimbed),as.character)

# We do not want each record to be labeled 'rec'
rownames(stepCount) <- NULL
# The type variable is superfluous within each data frame
stepCount <- stepCount[!names(stepCount)=="type"]
stepCount$value<-as.numeric(stepCount$value)
# The three dates are character strings, and we put them into POSIXct format
stepCount$creationDate <- as.POSIXct(stepCount$creationDate, 
                              format="%Y-%m-%d %H:%M:%S %z")
stepCount$startDate <- as.POSIXct(stepCount$startDate,format="%Y-%m-%d %H:%M:%S %z")
stepCount$endDate <- as.POSIXct(stepCount$endDate,format="%Y-%m-%d %H:%M:%S %z")
~~~~

We do one additional bit of data management. Currently, each data record has a start and end time, to define when it was measured. For some of our more basic analysis, it will be easier to think of the data as being discretized, as this data is all quite approximate anyways. Thus, we give each observation a 'midtime', measuring halfway between the start and end time. Perhaps the most important part of working this data is getting used to the POSIXct date time format. Anything interesting regarding this data will be based on the timing of the records, so it was very useful to learn how R manages these date/time formats, as it handles most of the hard work for us. In this case, to find the midtime, we first convert them into number of seconds since the start time for POSIXct (1970), take their average, and then convert this back into POSIXct format.

~~~~
stepCount$midtime <- as.POSIXct((as.numeric(stepCount$startDate) + as.numeric(stepCount$endDate)) / 2, origin = '1970-01-01')
distWalked$midtime <- as.POSIXct((as.numeric(distWalked$startDate) + as.numeric(distWalked$endDate)) / 2, origin = '1970-01-01')
flightsClimbed$midtime <- as.POSIXct((as.numeric(flightsClimbed$startDate) + as.numeric(flightsClimbed$endDate)) / 2, origin = '1970-01-01')
~~~~


Finally, this process takes some time (especially, the for loop to sort the rec's in x, and I know for loops in R are quite inefficient). Thus, for the future, we save these data frames as CSVs for easy access.

~~~~
write.csv(stepCount,file="stepCount.csv", row.names = FALSE)
write.csv(distWalked,file="distWalked.csv", row.names = FALSE)
write.csv(flightsClimbed,file="flightsClimbed.csv", row.names = FALSE)
~~~~

As I was learning much of this for the first time, there were many dead ends not shown here, but this was a useful (if extremely basic) introduction to how to work with slightly odd data files and convert them into R formatting. The full data cleaning file (obviously, still a work in progress) is stored [here](https://github.com/dylanpotteroconnell/HealthData/blob/master/HealthDataCleaning.R).
