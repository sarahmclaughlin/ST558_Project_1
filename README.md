Project 1
================
Sarah McLaughlin
6/5/2020

  - [Introduction to JSON Data](#introduction-to-json-data)
      - [What is JSON Data?](#what-is-json-data)
      - [Where and why is JSON Data
        used?](#where-and-why-is-json-data-used)
      - [How to Read JSON Data](#how-to-read-json-data)
  - [NHL API](#nhl-api)
      - [Functions to Access Data from NHL
        API](#functions-to-access-data-from-nhl-api)
  - [Exploratory Analysis of Data
    Sets](#exploratory-analysis-of-data-sets)
      - [Franchise Data](#franchise-data-1)
      - [Team Totals Data Set](#team-totals-data-set)
      - [Specific Franchise Data](#specific-franchise-data)
      - [Goalie, by Franchise Data](#goalie-by-franchise-data)
      - [Skater, by Franchise Data
        Analysis](#skater-by-franchise-data-analysis)

**VIGNETTE**  
Reading and summarizing a JSON data set using data from the National
Hockey League (NHI) API.

# Introduction to JSON Data

## What is JSON Data?

JSON, which stands for **JavaScript Object Notation** is a method for
writing data that is simple to read, and write, and for computers to
parse and generate. JSON data is easy to code as it is text that is
separated into *objects* and within each object, if needed, *arrays*.
Objects are inside braces (`{}`) and arrays are inside brackets (`[]`).
Each object typically contains one or more *name-value pairs*; a
name-value pair contains a key and its value, separated by a colon. An
example of a name-value pair would be `"Name"` (key)`:"Sarah
McLaughlin"` (value). Keys within an object are unique. Keys with the
same name need to be put into another object (possibly within the same
object) or into an array. An *array* is a grouping of elements,
separated by a comma. The elements in an array can be anything,
including objects. The value of a key could be an array.

For example, if you wanted to create a data set that included the name
and birthday of everyone in your family, you could do so by creating one
GIANT object, with one name-value pair. The key would be `data` and the
value would be a large array that contains an object for each person in
your family. Here is what it could look like: { “data” :
\[{“Name”:“Dad”, “Birthday”:“April 2”}, {“Name”:“Mom”,
“Birthday”:“March 7”}\]}.

## Where and why is JSON Data used?

JSON Data is not dependent on any specific programming language and
thus, can be read by many different programming languages. It is also
not difficult for humans to write JSON Data. JSON Data is universal and
is typically used for transmitting data from a server and a website. For
example, in this vignette, I will download JSON Data from the NHL API
(“Application Programming Interface”), which I will then read in using
`R`.

*References for paragraphs above:
[JSON.org](https://www.json.org/json-en.html),
[Wikipedia.com](https://en.wikipedia.org/wiki/JSON#Data_types_and_syntax),
[stackoverflow](https://stackoverflow.com/questions/383692/what-is-json-and-why-would-i-use-it#:~:text=The%20JSON%20format%20is%20often,as%20an%20alternative%20to%20XML.&text=JSON%20is%20JavaScript%20Object%20Notation.),
and [NHL Franchise JSON](https://records.nhl.com/site/api/franchise).*

## How to Read JSON Data

There are 3 packages that allow `R` to read JSON Data: 1. `rjson`, 2.
`rjsonio`, and 3.`jsonlite`. According to CRAN, `rjson` only converts R
objects into JSON objects and JSON objects into R objects. It was
published in 2018. `rjsonio` was made as a faster version of `rjson`
that can also handle larger objects. It was published this year with a
note that `rjson` has been updated to work just as fast, if not faster.
The last package is the one that I used for this project: `jsonlite`.
According to CRAN, `jsonlite` is fast, robust, and *“particularly
powerful for … interacting with a web API”.* I picked `jsonlite` for
this reason and because it was familiar to me, having learned and used
`jsonlite` in past homework assignments.

*References for paragraph above: [RJSON via
CRAN](https://cran.r-project.org/package=rjson), [RJSONIO via
CRAN](https://cran.r-project.org/package=rjsonio), and [JSONLITE via
CRAN](https://cran.r-project.org/package=jsonlite)*

# NHL API

Below is the code that will be used to create functions to read the JSON
data. The function are also used to either create the data that will be
used later or to show the function works.

### Base URL Code (name: `baseURL`)

Here is the code to create the Base URL that will begin each of our
functions, used to access the NHL API.

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

  # Close function  
}
```

**Franchise Data Set (name: `franchiseData`)**  
Here, I used the `franchise` funtion to create a data set.

``` r
franchiseData <- franchise( )
```

*Franchise Data*

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

The function (`team_totals()`) below accesses the Franchise Team Totals
Data.

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
      group_by(franchiseId) %>% 
      select("id", "franchiseId", "teamId", "teamName", "gamesPlayed", "homeTies", "homeWins", "homeLosses", "roadTies", "roadWins", "roadLosses") %>%
      collect()

  # Return data set 
    return(Data)
    
  # Close function
    }
```

**Team Totals Data Set (name: `totalsData`)**  
Here, I used the `team_totals` function to create a data set.

``` r
totalsData <- team_totals()
```

*Team Totals Data*

    ## # A tibble: 104 x 11
    ## # Groups:   franchiseId [38]
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

The function (`One_Franchise()`) below accesses the Franchise Specific
Data.

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
      select("franchiseId", "franchiseName", "homeWinStreak", "fewestLosses", "fewestTies","fewestWins", "mostLosses", "mostTies", "mostWins")

  # Return data set 
    return(Data)
   
  # Close function  
}
```

**Demonstrate Function: Data for the New Jersey Franchise**  
Here, I will demonstrate that the `One_Franchise` function works. It
will be used again later for numerical analysis.

``` r
OneFranch <- One_Franchise(23)
```

*New Jersey Franchise Data*

    ## # A tibble: 1 x 9
    ##   franchiseId franchiseName homeWinStreak fewestLosses fewestTies fewestWins
    ##         <int> <chr>                 <int>        <int>      <int>      <int>
    ## 1          23 New Jersey D~            11           19          3         12
    ## # ... with 3 more variables: mostLosses <int>, mostTies <int>, mostWins <int>

### Franchise Specific Goalie Function

The function (`goalie()`) below accesses the Franchise Specific Goalie
Data.

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
    Data <- J_Data%>%
      select("activePlayer", "firstName", "lastName", "franchiseName", "gamesPlayed", "mostGoalsAgainstOneGame", "wins", "losses", "mostSavesOneGame")

  # Return data set 
    return(Data)
    
  # Close function  
}
```

**Demonstrate Function: Goalie Data for New Jersey Franchise**  
Here, I will demonstrate that the `goalie` function works. It will be
used again later for numerical analysis.

``` r
goalieData <- goalie(23)
```

*New Jersey Franchise Goalie Data*

    ## # A tibble: 27 x 9
    ##    activePlayer firstName lastName franchiseName gamesPlayed mostGoalsAgains~
    ##    <lgl>        <chr>     <chr>    <chr>               <int>            <int>
    ##  1 FALSE        Martin    Brodeur  New Jersey D~        1259                6
    ##  2 FALSE        Sean      Burke    New Jersey D~         162                9
    ##  3 FALSE        Doug      Favell   New Jersey D~          84                9
    ##  4 FALSE        Ron       Low      New Jersey D~          81                8
    ##  5 FALSE        Peter     McDuffe  New Jersey D~          36               10
    ##  6 FALSE        Bill      McKenzie New Jersey D~          22               10
    ##  7 FALSE        Roland    Melanson New Jersey D~           1                2
    ##  8 FALSE        Lindsay   Middleb~ New Jersey D~           9                7
    ##  9 FALSE        Phil      Myre     New Jersey D~          34                8
    ## 10 FALSE        Michel    Plasse   New Jersey D~          24                9
    ## # ... with 17 more rows, and 3 more variables: wins <int>, losses <int>,
    ## #   mostSavesOneGame <int>

### Franchise Specific Skater Function

The function (`skater`) below accesses the Franchise Specific Skater
Data.

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
    Data <- J_Data%>%
     select("franchiseId", "franchiseName", "fewestGoals", "mostGoals", "fewestLosses", "mostLosses", "mostWins", "pointStreak")

  # Return data set 
    return(Data)
    
  # Close function  
}
```

**Demonstrate Function: Skater Data for New Jersey Franchise**  
Here, I will demonstrate that the `skater` function works. It will be
used again later for numerical analysis.

``` r
SData <- skater(23)
```

*New Jersey Franchise Skater Data*

    ## # A tibble: 1 x 8
    ##   franchiseId franchiseName fewestGoals mostGoals fewestLosses mostLosses
    ##         <int> <chr>               <int>     <int>        <int>      <int>
    ## 1          23 New Jersey D~         174       308           19         56
    ## # ... with 2 more variables: mostWins <int>, pointStreak <int>

# Exploratory Analysis of Data Sets

## Franchise Data

Here, I will compare the number current teams to former teams in the
`franchiseData` data set. I will do so by creating another variable
(called `Current`) using the `mutate` and `ifelse` functions. The
variable `Current` will label each team as currently playing or not
currently playing. Once I have created that variable, I will compare the
factors in a bar graph using the `ggplot` package.

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

### Analysis

According to the graph, the information in the `franchiseData` data set
is mostly on teams that are currently playing in the NHL. There are
fewer than 10 teams that are not playing in the data set. This gives a
“Big Picture” of the data that was queried. This graph shows that a
majority of the data (about 75%) on the NHL API is on teams that are
still playing in the NHL.

## Team Totals Data Set

Here, I will run two analyses on the totals data set. First, I will
create a variable that calculates the overall proportion of games won
for each team (`avgWins`). I will then create a histogram of those
percentages.

*Note: I realize that `avgWins` is not an appropriate label for this
variable as it is actually the proportion of wins, not the average. I
have already coded `avgWins` many times and thus, adjusted the labels on
my graphics instead.*

### Code to Create `avgWins` Variable

``` r
totals <- totalsData %>% group_by(teamName) %>% summarise(totalGames = sum(gamesPlayed), totalHWins = sum(homeWins), totalRWins = sum(roadWins))

totals <- totals %>% mutate(avgWins = (totalHWins+totalRWins)/totalGames)
```

**Print Resulting Data Set**

    ## # A tibble: 57 x 5
    ##    teamName                totalGames totalHWins totalRWins avgWins
    ##    <chr>                        <int>      <int>      <int>   <dbl>
    ##  1 Anaheim Ducks                 2217        602        460   0.479
    ##  2 Arizona Coyotes                480        104         86   0.396
    ##  3 Atlanta Flames                 653        163        107   0.413
    ##  4 Atlanta Thrashers              906        183        159   0.377
    ##  5 Boston Bruins                 7221       2056       1473   0.489
    ##  6 Brooklyn Americans              48         10          6   0.333
    ##  7 Buffalo Sabres                4145       1118        796   0.462
    ##  8 Calgary Flames                3309        906        663   0.474
    ##  9 California Golden Seals        472         84         32   0.246
    ## 10 Carolina Hurricanes           1849        460        380   0.454
    ## # ... with 47 more rows

### Code to Create Histogram of `avgWins` Variable

``` r
g <- ggplot(totals, aes(x = avgWins))

g + geom_histogram() + 
  labs(x = "Proportion of Games Won", title = "Distribution of Proportion of Wins") 
```

![](README_files/figure-gfm/avgWins%20histogram-1.png)<!-- -->

Second, I will make a factor dividing the teams into two groups: *those
that have played more than 500 games, and those that have not*. Then, I
will compare the distributions of `avgWins` for those two groups using
side by side box-plots.

### Code to Create 500+ Games Factor

``` r
totals <- 
  totals %>% 
  mutate(FiveHunPlus = ifelse(totalGames >= 500, "More than 500 Games", "Less than 500 Games"))
```

**Print Resulting Data Set**

    ## # A tibble: 57 x 6
    ##    teamName            totalGames totalHWins totalRWins avgWins FiveHunPlus     
    ##    <chr>                    <int>      <int>      <int>   <dbl> <chr>           
    ##  1 Anaheim Ducks             2217        602        460   0.479 More than 500 G~
    ##  2 Arizona Coyotes            480        104         86   0.396 Less than 500 G~
    ##  3 Atlanta Flames             653        163        107   0.413 More than 500 G~
    ##  4 Atlanta Thrashers          906        183        159   0.377 More than 500 G~
    ##  5 Boston Bruins             7221       2056       1473   0.489 More than 500 G~
    ##  6 Brooklyn Americans          48         10          6   0.333 Less than 500 G~
    ##  7 Buffalo Sabres            4145       1118        796   0.462 More than 500 G~
    ##  8 Calgary Flames            3309        906        663   0.474 More than 500 G~
    ##  9 California Golden ~        472         84         32   0.246 Less than 500 G~
    ## 10 Carolina Hurricanes       1849        460        380   0.454 More than 500 G~
    ## # ... with 47 more rows

### Code to Create Side-by-Side Boxplots

``` r
g <- ggplot(totals, aes(x = FiveHunPlus, y = avgWins, fill = FiveHunPlus))

g + geom_boxplot() + 
  labs(title = "Comparison of the Proportion of Wins", x = "Number of Games Played", y = "Proportion of Games Won") + 
  scale_color_discrete(name = " ")
```

![](README_files/figure-gfm/wins%20boxplots-1.png)<!-- -->

### Analysis

When the data is analyzed as a whole, the distribution of the proportion
of wins is left skewed. A large amount of the teams had proportions
between 0.4 and 0.5. However, when the data is divided into two groups
(“Teams who have played less than 500 games” and “Teams who have
played more than 500 games”), you can see that the teams who have played
more than 500 games have most of the proportions between 0.4 and 0.5.
For the teams that have played less than 500 games, over 75% of the
teams have a proportion of wins less than 0.4.

## Specific Franchise Data

Below, I will create a data set that includes the franchise data from
multiple franchises. I used the `One_Franchise` function I created
earlier in this vignette.

**Code to Create Multiple Franchise Data (name: `FranchiseData`)**

``` r
# Create Multiple Data Vectors
NJ <- One_Franchise(23)
NY <- One_Franchise(22)
PHL <- One_Franchise(16)
PIT <- One_Franchise(17)
FLO <- One_Franchise(13)

FranchiseData <- rbind(NJ, NY, PHL, PIT, FLO)
```

**Print Resulting Data Set**

    ## # A tibble: 5 x 9
    ##   franchiseId franchiseName homeWinStreak fewestLosses fewestTies fewestWins
    ##         <int> <chr>                 <int>        <int>      <int>      <int>
    ## 1          23 New Jersey D~            11           19          3         12
    ## 2          22 New York Isl~            14           15          4         12
    ## 3          16 Philadelphia~            20           12          4         17
    ## 4          17 Pittsburgh P~            13           21          4         16
    ## 5          13 Cleveland Ba~             5           36          5         13
    ## # ... with 3 more variables: mostLosses <int>, mostTies <int>, mostWins <int>

**Code to Create Side by Side Bar Graph of Franchise Data, by
Franchise**

``` r
g <- ggplot(FranchiseData, aes(x = franchiseName, y = homeWinStreak, fill = franchiseName))

g + geom_col() +
  labs(title = "Home Win Streak", x = " ", y = "Count") +
  scale_fill_discrete(name = " " )
```

![](README_files/figure-gfm/Franchise%20Bar%20Graph-1.png)<!-- -->

### Analysis

In this data set, we are looking at specific variables for five
different franchises. I picked the Longest Home Winning Streak variable
and made a simple bar graph to show that the Philadelphia Flyers have
had the longest win streak by about 6 games. The Cleveland Barons had
the shortest win streak; as we will see later, the Cleveland Barons do
not play anymore.

## Goalie, by Franchise Data

Here, I will create a data set that include the goalie data from five
different franchises. I used the `goalie` function I created earlier in
this vignette. I will run quite a few analyses on this set of data.

**Code to Create Goalie Data (name: `GoalieData`)**

``` r
NJ <- goalie(23)
NY <- goalie(22)
PHL <- goalie(16)
PIT <- goalie(17)
FLO <-goalie(13)

GoalieData <- rbind(NJ, NY, PHL, PIT, FLO)
```

**Print Resulting Data Set**

    ## # A tibble: 133 x 9
    ##    activePlayer firstName lastName franchiseName gamesPlayed mostGoalsAgains~
    ##    <lgl>        <chr>     <chr>    <chr>               <int>            <int>
    ##  1 FALSE        Martin    Brodeur  New Jersey D~        1259                6
    ##  2 FALSE        Sean      Burke    New Jersey D~         162                9
    ##  3 FALSE        Doug      Favell   New Jersey D~          84                9
    ##  4 FALSE        Ron       Low      New Jersey D~          81                8
    ##  5 FALSE        Peter     McDuffe  New Jersey D~          36               10
    ##  6 FALSE        Bill      McKenzie New Jersey D~          22               10
    ##  7 FALSE        Roland    Melanson New Jersey D~           1                2
    ##  8 FALSE        Lindsay   Middleb~ New Jersey D~           9                7
    ##  9 FALSE        Phil      Myre     New Jersey D~          34                8
    ## 10 FALSE        Michel    Plasse   New Jersey D~          24                9
    ## # ... with 123 more rows, and 3 more variables: wins <int>, losses <int>,
    ## #   mostSavesOneGame <int>

Here, I am going to create a contingency table to see the number of
inactive and active goalies for each franchise. I do so by changing the
`activePlayer` variable into a factor with labels “Inactive” and
“Active”.

**Code to Create Contingency Table of Active vs. Inactive Goalies for
the 5 Franchises**

``` r
# Make activePlayer variable a factor with two labels "Active" and "Inactive"  

GoalieData[1] <- factor(as.character(GoalieData$activePlayer), levels = c("FALSE", "TRUE"), labels = c("Inactive", "Active"))  

kable(table(GoalieData$activePlayer, GoalieData$franchiseName))
```

|          | Cleveland Barons | New Jersey Devils | New York Islanders | Philadelphia Flyers | Pittsburgh Penguins |
| -------- | ---------------: | ----------------: | -----------------: | ------------------: | ------------------: |
| Inactive |                5 |                24 |                 26 |                  30 |                  32 |
| Active   |                0 |                 3 |                  4 |                   4 |                   5 |

### Analysis

Within `GoalieData`, a majority of the data is from inactive goalies.
This means, we will be analyzing data from seasons past. You can see
that the Cleveland Barons have only 5 inactive goalies while the other
four teams have over 20. This makes me believe that the Cleveland Barons
existed for a short time, much less than the other teams. The other four
teams have between 3-5 active goalies. It would seem that each active
franchise would have between 3-5 goalies at one time.

**Code to Create Bar Graphs for Table Above**

``` r
g <- ggplot(GoalieData, aes(x = franchiseName))

g + geom_bar(aes(fill = activePlayer), position = "dodge") +
  labs(x = "Franchise") + scale_fill_discrete(name = " ")
```

![](README_files/figure-gfm/goalie%20bar%20graph-1.png)<!-- -->

### Analysis

This side-by-side bar graph shows the same information that is in the
contingency table. Again, we can see that a majority of the data is from
inactive goalies and that the Cleveland Barons have no active goalies.

**Code to Create Summaries for a few variables for Inactive and Active
Goalies**  
Here, I am going to compare the quantitative data from a few of
variables within `GoalieData`. I do so using the `apply` function to
calculate the summary statistics. These numbers will give us an idea of
the skill of the goalies in our data set. I have kept the data separated
into “Inactive vs. Active” but have kepts all of the franchises
together. This is because some franchises have so few goalies.

**Inactive Goalies**

``` r
# Create Inactive Summaries
InActiveData <- GoalieData %>% 
  filter(activePlayer == "Inactive")%>%
  select("gamesPlayed", "wins", "losses", "mostSavesOneGame")

mat <- apply(InActiveData, 2, summary, digits = 3)

kable(mat, caption = "Summaries for Inactive Players")
```

|         | gamesPlayed |  wins | losses | mostSavesOneGame |
| ------- | ----------: | ----: | -----: | ---------------: |
| Min.    |         1.0 |   0.0 |      0 |              5.0 |
| 1st Qu. |        10.0 |   3.0 |      4 |             36.0 |
| Median  |        36.0 |  13.0 |     18 |             42.0 |
| Mean    |        88.7 |  36.8 |     34 |             40.5 |
| 3rd Qu. |       103.0 |  42.0 |     43 |             47.0 |
| Max.    |      1260.0 | 688.0 |    394 |             58.0 |

Summaries for Inactive Players

**Active Goalies**

``` r
# Create Active Summaries  
ActiveData <- GoalieData %>% 
  filter(activePlayer == "Active")%>%
  select("gamesPlayed", "wins", "losses", "mostSavesOneGame")

stuff <- apply(ActiveData, 2, summary, digits = 3)

kable(stuff, caption = "Summaries for Active Players")
```

|         | gamesPlayed |  wins | losses | mostSavesOneGame |
| ------- | ----------: | ----: | -----: | ---------------: |
| Min.    |         1.0 |   0.0 |    0.0 |              7.0 |
| 1st Qu. |        16.8 |   5.5 |    6.0 |             36.0 |
| Median  |        48.0 |  23.0 |   14.5 |             44.5 |
| Mean    |       122.0 |  60.4 |   40.4 |             40.9 |
| 3rd Qu. |       181.0 |  91.2 |   54.8 |             48.5 |
| Max.    |       691.0 | 375.0 |  216.0 |             52.0 |

Summaries for Active Players

### Analysis

The inactive goalies have a large range for all of the variables. This
would suggest that there is a wide variety within the skill level of the
inactive goalies. Our means and medians for all of the variables except
“Most Saves One Game” are far apart which suggests the data is right
skewed. The maximum values for the first four variables are much larger
than the rest of the data. These seem to be outliers. The IQR is a
shorter length than the distance from the third quartile to the maximum.
The summary stats for “Most Saves One Game” seem fairly symmetric; the
mean and median are close in value. This shows that a large proportion
of the goalies were closer in skill for that variable.

For the active goalie data, the shape of the distributions are similar
than the inactive; however, the maximum values are much less. Remember,
there are fewer active goalies than inactive ones in our data sets.
Overall, the analysis is the same as the inactive goalies.

**Code to Create Scatterplot with `gamesPlayed` vs. `mostSavesOneGame`
by Franchise**  
Here, I am comparing the number of games played to the most saves in one
game for all of the goalies. The goalies have been colored by franchise
as well.

``` r
g <- ggplot(GoalieData, aes(x = gamesPlayed, y = `mostSavesOneGame`, color = franchiseName))

g + geom_point() + 
  labs(title = "Most Saves vs. Games Played", x = "Number of Games Played", y = "Most Saves in One Game")+
  scale_color_discrete(name = "Franchise Name")
```

![](README_files/figure-gfm/scatter%20goalie-1.png)<!-- -->

### Analysis

In the scatter plot, we can see the presence of a few outliers for the
number of games played. A goalie on the New Jersey Devils has played
more than 1200 games; however, this does not give him the highest saves
in one game despite having played more than 800 more games than the rest
of the goalies in the data set. You can see that a majority of the
goalies in the data set have played less than 400 games. As most of the
goalies are inactive, we can see that perhaps 400 games is the averager
career span of a goalie. The few goalies we have who played more than
that are the exception. A majority of the goalies had the most number of
saves between 40 and 60 saves.

## Skater, by Franchise Data Analysis

``` r
# Creating SkaterData data set 
NJ <- skater(23)
NY <- skater(22)
PHL <- skater(16)
PIT <- skater(17)
FLO <-skater(13)

SkaterData <- rbind(NJ, NY, PHL, PIT, FLO)
SkaterData
```

    ## # A tibble: 5 x 8
    ##   franchiseId franchiseName fewestGoals mostGoals fewestLosses mostLosses
    ##         <int> <chr>               <int>     <int>        <int>      <int>
    ## 1          23 New Jersey D~         174       308           19         56
    ## 2          22 New York Isl~         170       385           15         60
    ## 3          16 Philadelphia~         173       350           12         48
    ## 4          17 Pittsburgh P~         182       367           21         58
    ## 5          13 Cleveland Ba~         153       250           36         55
    ## # ... with 2 more variables: mostWins <int>, pointStreak <int>

``` r
g <- ggplot(SkaterData, aes(x = mostLosses, y = mostWins, color = franchiseName))

g + geom_point() + 
  labs(x = "Most Losses", y = "Most Wins", title = "Most Losses vs. Most Wins") +
  scale_color_discrete(name = "Franchise Name")
```

![](README_files/figure-gfm/skater%20graphic-1.png)<!-- -->
