Project 1
================
Sarah McLaughlin
6/5/2020

# VIGNETTE

Reading and summarizing a JSON data set using data from the National
Hockey League (NHI) API.

## Introduction to JSON Data

### What is JSON Data?

JSON, which stands for **JavaScript Object Notation** is a method for
writing data that is simple to read, and write, and for computers to
parse and generate. JSON data is easy to code as it is text that is
separated into *objects* and within each object, if needed, *arrays*.
Each object typically contains one or more *name-value pairs*; a
name-value pair contains a key and its value, separated by a colon. An
example of a name-value pair would be `"Name"` (key)`:"Sarah
McLaughlin"` (value). Keys within an object are unique. Keys with the
same name need to be put into another object (possibly within the same
object) or into an array. An *array* is a grouping of elements,
separated by a comma. The elements in an array can be anything,
including objects. The value of a key could be an array. Objects are
inside braces (`{}`) and arrays are inside brackets (`[]`).

For example, if you wanted to create a data set that included the name
and birthday of everyone in your family, you could do so by creating one
GIANT object, with one name-value pair. The key would be `data` and the
value would be a large array that contains an object for each person in
your family. Here is what it could look like: { “data” :
\[{“Name”:“Dad”, “Birthday”:“April 2”}, {“Name”:“Mom”,
“Birthday”:“March 7”}\]}.

### Where and why is JSON Data used?

JSON Data is not dependent on any specific programming language and
thus, can be read by many different programming languages.JSON Data is
universal and is typically used for transmitting data from a server and
a website. For example, in this vignette, I will download JSON Data from
the NHL API (“Application Programming Interface”), which I will then
read using `R`.

*References for paragraph above:
[JSON.org](https://www.json.org/json-en.html),
[Wikipedia.com](https://en.wikipedia.org/wiki/JSON#Data_types_and_syntax),
[stackoverflow](https://stackoverflow.com/questions/383692/what-is-json-and-why-would-i-use-it#:~:text=The%20JSON%20format%20is%20often,as%20an%20alternative%20to%20XML.&text=JSON%20is%20JavaScript%20Object%20Notation.),
and [NHL Franchise JSON](https://records.nhl.com/site/api/franchise).*

## How to Read JSON Data?

There are 3 packages that allow `R` to read JSON Data. 1. `jsonlite`, 2.
`httr` and 3.

# NHL API

Below is the code that will be used to create functions to read the JSON
data.

Here is the code to create the Base URL that will begin each of our
functions, used to access the NHL API.

### Base URL Code (baseURL)

``` r
baseURL <- "https://records.nhl.com/site/api"
```

## Functions to Access Data from NHL API

### Franchise Data

The function (`franchise()`) below accesses the NHL Franchise Data.

``` r
# Create function  
franchise <- function( ){

  # Create full URL
    tabName <- "franchise"
    fullURL <- paste0(baseURL, "/", tabName)

  # Use GET function to pull data.  
    GETdata <- GET(fullURL)
    txtData <- content(GETdata, "text")
    JData <- fromJSON(txtData, flatten = TRUE)

  # Make it a tibble  
    J_Data <- tbl_df(JData$data) 

  # Query Data
    Data <- J_Data %>% select("id", "firstSeasonId", "lastSeasonId", "teamCommonName") %>% collect()

  # Give output 
    return(Data)
}
```

**Franchise Data Set (franchiseData)**

``` r
franchiseData <- franchise( )
franchiseData
```

    ## # A tibble: 38 x 4
    ##       id firstSeasonId lastSeasonId teamCommonName
    ##    <int>         <int>        <int> <chr>         
    ##  1     1      19171918           NA Canadiens     
    ##  2     2      19171918     19171918 Wanderers     
    ##  3     3      19171918     19341935 Eagles        
    ##  4     4      19191920     19241925 Tigers        
    ##  5     5      19171918           NA Maple Leafs   
    ##  6     6      19241925           NA Bruins        
    ##  7     7      19241925     19371938 Maroons       
    ##  8     8      19251926     19411942 Americans     
    ##  9     9      19251926     19301931 Quakers       
    ## 10    10      19261927           NA Rangers       
    ## # ... with 28 more rows

### Franchise Team Totals Function

``` r
team_totals <- function(){

  # Create full URL 
    tabName <- "franchise-team-totals"
    fullURL <- paste0(baseURL, "/", tabName)
    
  # Use GET function to pull data  
    GETdata <- GET(fullURL)
    txtData <- content(GETdata, "text")
    JData <- fromJSON(txtData, flatten = TRUE)
  
  # Make it a tibble  
    J_Data <- tbl_df(JData$data)
    
  # Query Data  
    Data <- J_Data %>% 
      group_by(franchiseID) %>% 
      select("id", "franchiseId", "teamId", "teamName", "gamesPlayed", "homeTies", "homeWins", "homeLosses", "roadTies", "roadWins", "roadLosses") %>%
      collect()

  # Return data set 
    return(Data)
    }
```

**Team Totals Data Set**

    ## # A tibble: 104 x 11
    ##       id franchiseId teamId teamName gamesPlayed homeTies homeWins homeLosses
    ##    <int>       <int>  <int> <chr>          <int>    <int>    <int>      <int>
    ##  1     1          23      1 New Jer~        2937       96      783        507
    ##  2     2          23      1 New Jer~         257       NA       74         53
    ##  3     3          22      2 New Yor~        3732      170      942        674
    ##  4     4          22      2 New Yor~         272       NA       84         46
    ##  5     5          10      3 New Yor~        6504      448     1600       1132
    ##  6     6          10      3 New Yor~         515        1      137        103
    ##  7     7          16      4 Philade~         433       NA      131         93
    ##  8     8          16      4 Philade~        4115      193     1204        572
    ##  9     9          17      5 Pittsbu~        4115      205     1116        679
    ## 10    10          17      5 Pittsbu~         381       NA      111         82
    ## # ... with 94 more rows, and 3 more variables: roadTies <int>, roadWins <int>,
    ## #   roadLosses <int>

### Franchise Specific Data Function

``` r
One_Franchise <- function(ID){

  # Create full URL 
    tabName <- "franchise-season-records?cayenneExp=franchiseId="
    fullURL <- paste0(baseURL, "/", tabName, ID)
    
  # Use GET function to pull data  
    GETdata <- GET(fullURL)
    txtData <- content(GETdata, "text")
    JData <- fromJSON(txtData, flatten = TRUE)
  
  # Make it a tibble  
    J_Data <- tbl_df(JData$data)
    
  # Query Data  
    Data <- J_Data%>%
      select("franchiseId", "franchiseName", "fewestLosses", "fewestTies","fewestWins", "mostLosses", "mostTies", "mostWins")

  # Return data set 
    return(Data)
}
```

**Data for the New Jersey Franchise**

    ## # A tibble: 1 x 8
    ##   franchiseId franchiseName fewestLosses fewestTies fewestWins mostLosses
    ##         <int> <chr>                <int>      <int>      <int>      <int>
    ## 1          23 New Jersey D~           19          3         12         56
    ## # ... with 2 more variables: mostTies <int>, mostWins <int>

### Franchise Specific Goalie Function

``` r
goalie <- function(ID){

  # Create full URL 
    tabName <- "franchise-goalie-records?cayenneExp=franchiseId="
    fullURL <- paste0(baseURL, "/", tabName, ID)
    
  # Use GET function to pull data  
    GETdata <- GET(fullURL)
    txtData <- content(GETdata, "text")
    JData <- fromJSON(txtData, flatten = TRUE)
  
  # Make it a tibble  
    J_Data <- tbl_df(JData$data)
    
  # Query Data  
   # Data <- J_Data%>%
      #select("franchiseId", "franchiseName", "fewestLosses", "fewestTies","fewestWins", "mostLosses", "mostTies", "mostWins")

  # Return data set 
    return(J_Data)
}
```

**Goalie Data for New Jersey Franchise**

    ## # A tibble: 27 x 29
    ##       id activePlayer firstName franchiseId franchiseName gameTypeId gamesPlayed
    ##    <int> <lgl>        <chr>           <int> <chr>              <int>       <int>
    ##  1   266 FALSE        Martin             23 New Jersey D~          2        1259
    ##  2   368 FALSE        Sean               23 New Jersey D~          2         162
    ##  3   409 FALSE        Doug               23 New Jersey D~          2          84
    ##  4   493 FALSE        Ron                23 New Jersey D~          2          81
    ##  5   506 FALSE        Peter              23 New Jersey D~          2          36
    ##  6   510 FALSE        Bill               23 New Jersey D~          2          22
    ##  7   514 FALSE        Roland             23 New Jersey D~          2           1
    ##  8   518 FALSE        Lindsay            23 New Jersey D~          2           9
    ##  9   535 FALSE        Phil               23 New Jersey D~          2          34
    ## 10   664 FALSE        Michel             23 New Jersey D~          2          24
    ## # ... with 17 more rows, and 22 more variables: lastName <chr>, losses <int>,
    ## #   mostGoalsAgainstDates <chr>, mostGoalsAgainstOneGame <int>,
    ## #   mostSavesDates <chr>, mostSavesOneGame <int>, mostShotsAgainstDates <chr>,
    ## #   mostShotsAgainstOneGame <int>, mostShutoutsOneSeason <int>,
    ## #   mostShutoutsSeasonIds <chr>, mostWinsOneSeason <int>,
    ## #   mostWinsSeasonIds <chr>, overtimeLosses <int>, playerId <int>,
    ## #   positionCode <chr>, rookieGamesPlayed <int>, rookieShutouts <int>,
    ## #   rookieWins <int>, seasons <int>, shutouts <int>, ties <int>, wins <int>

### Franchise Specific Skater Function

``` r
skater <- function(ID){

  # Create full URL 
    tabName <- "franchise-season-records?cayenneExp=franchiseId="
    fullURL <- paste0(baseURL, "/", tabName, ID)
    
  # Use GET function to pull data  
    GETdata <- GET(fullURL)
    txtData <- content(GETdata, "text")
    JData <- fromJSON(txtData, flatten = TRUE)
  
  # Make it a tibble  
    J_Data <- tbl_df(JData$data)
    
  # Query Data  
    #Data <- J_Data%>%
     #select("franchiseId", "franchiseName", "fewestLosses", "fewestTies","fewestWins", "mostLosses", "mostTies", "mostWins")

  # Return data set 
    return(J_Data)
}
```

**Franchise Specific Skater Data**

    ## # A tibble: 1 x 57
    ##      id fewestGoals fewestGoalsAgai~ fewestGoalsAgai~ fewestGoalsSeas~
    ##   <int>       <int>            <int> <chr>            <chr>           
    ## 1     1         174              164 2003-04 (82)     2010-11 (82)    
    ## # ... with 52 more variables: fewestLosses <int>, fewestLossesSeasons <chr>,
    ## #   fewestPoints <int>, fewestPointsSeasons <chr>, fewestTies <int>,
    ## #   fewestTiesSeasons <chr>, fewestWins <int>, fewestWinsSeasons <chr>,
    ## #   franchiseId <int>, franchiseName <chr>, homeLossStreak <int>,
    ## #   homeLossStreakDates <chr>, homePointStreak <int>,
    ## #   homePointStreakDates <chr>, homeWinStreak <int>, homeWinStreakDates <chr>,
    ## #   homeWinlessStreak <int>, homeWinlessStreakDates <chr>, lossStreak <int>,
    ## #   lossStreakDates <chr>, mostGameGoals <int>, mostGameGoalsDates <chr>,
    ## #   mostGoals <int>, mostGoalsAgainst <int>, mostGoalsAgainstSeasons <chr>,
    ## #   mostGoalsSeasons <chr>, mostLosses <int>, mostLossesSeasons <chr>,
    ## #   mostPenaltyMinutes <int>, mostPenaltyMinutesSeasons <chr>,
    ## #   mostPoints <int>, mostPointsSeasons <chr>, mostShutouts <int>,
    ## #   mostShutoutsSeasons <chr>, mostTies <int>, mostTiesSeasons <chr>,
    ## #   mostWins <int>, mostWinsSeasons <chr>, pointStreak <int>,
    ## #   pointStreakDates <chr>, roadLossStreak <int>, roadLossStreakDates <chr>,
    ## #   roadPointStreak <int>, roadPointStreakDates <chr>, roadWinStreak <int>,
    ## #   roadWinStreakDates <chr>, roadWinlessStreak <int>,
    ## #   roadWinlessStreakDates <chr>, winStreak <int>, winStreakDates <chr>,
    ## #   winlessStreak <int>, winlessStreakDates <chr>

# Exploratory Analysis of Data Sets

## Franchise Data (franchiseData)

Here, I will compare the number of hockey teams that are currently
playing and those that are not. I will do so by creating another
variable (called `Current`) using the `mutate` and `ifelse` functions.
Once I have created that variable, I will compare the factors in a bar
graph.

### Code to Create `Current`

``` r
franchiseData <- 
  franchiseData %>% 
  mutate(Current = ifelse(is.na(lastSeasonId), "Yes", "No"))
```

#### Print Resulting Data Set

    ## # A tibble: 38 x 5
    ##       id firstSeasonId lastSeasonId teamCommonName Current
    ##    <int>         <int>        <int> <chr>          <chr>  
    ##  1     1      19171918           NA Canadiens      Yes    
    ##  2     2      19171918     19171918 Wanderers      No     
    ##  3     3      19171918     19341935 Eagles         No     
    ##  4     4      19191920     19241925 Tigers         No     
    ##  5     5      19171918           NA Maple Leafs    Yes    
    ##  6     6      19241925           NA Bruins         Yes    
    ##  7     7      19241925     19371938 Maroons        No     
    ##  8     8      19251926     19411942 Americans      No     
    ##  9     9      19251926     19301931 Quakers        No     
    ## 10    10      19261927           NA Rangers        Yes    
    ## # ... with 28 more rows

### Code to Create Bar Graph

``` r
g <- ggplot(franchiseData, aes(Current))

g + geom_bar(aes(fill = Current)) +
  labs(title = "Comparison of Former Teams to Current Teams", x = "Currently Playing?") + 
  scale_fill_discrete(name = " ")
```

![](README_files/figure-gfm/current%20bar%20graph-1.png)<!-- -->
