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

``` r
baseURL <- "https://records.nhl.com/site/api"
```

## Functions to Access Data from NHL API

### Franchise Data

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

**Franchise Data Set**

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
