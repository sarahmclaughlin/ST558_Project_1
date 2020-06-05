---
title: "Project 1"
author: "Sarah McLaughlin"
date: "6/5/2020"
output: 
  rmarkdown::github_document:
    toc: yes
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = FALSE, warning = FALSE, message = FALSE, cache = TRUE)
```

```{r setup libraries}
library(knitr)
library(tidyverse)
library(readr)
library(DT)
library(dplyr)
library(ggplot2)
```

# VIGNETTE  
Reading and summarizing a JSON data set using data from the National Hockey League (NHI) API.  

## What is JSON Data?  
JSON, which stands for **JavaScript Object Notation**  is a method for writing data that is simple to read, and write, and for computers to parse and generate. JSON data is easy to code as it is text that is separated into *objects* and within each object, if needed, *arrays*. Each object typically contains one or more *name-value pairs*; a name-value pair contains a key and its value, separated by a colon. An example of a name-value pair would be `"Name"` (key)`: "Sarah McLaughlin"` (value). Keys within an object are unique. Keys with the same name need to be put into another object (possibly within the same object) or into an array. An *array* is a grouping of elements, separated by a comma. The elements in an array can be anything, including objects.     

References for paragraph above: [JSON.org](https://www.json.org/json-en.html) and [Wikipedia.com](https://en.wikipedia.org/wiki/JSON#Data_types_and_syntax)  