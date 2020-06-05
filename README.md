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

### Where is JSON Data used?

JSON Data is not dependent on any specific programming language and
thus, can be

*References for paragraph above:
[JSON.org](https://www.json.org/json-en.html),
[Wikipedia.com](https://en.wikipedia.org/wiki/JSON#Data_types_and_syntax),
and [NHL Franchise JSON](https://records.nhl.com/site/api/franchise).*
