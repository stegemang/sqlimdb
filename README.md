# project incomplete as of April 17, just testing this out.

# Explore imdb data with SQL

The goal of this project is to provide a tutorial and some resources for novice coders or data scientists to use SQL to explore imdb data. IMDB.com has changed its sorting and search functionality over the last fews years making it a little more difficult to access certain information, for instance listing by top user-rated episodes across all seasons of a TV series. Helpfully, imdb provides subsets of their data for personal use, however these are not in a format readily accessible for most users. As I started to learn SQL it figured imdb data would provide a good set of data to practice on. I decided to share what I had in case any one else wanted to query imdb data or practice or learn SQL with their own project.

I use jupyter notebooks magic functions to perform SQL commands and queries.

Another great way to learn is on [Mode](www.mode.com) or asking around if a friend has a data base server for you to accesss and example SQL problems to work through.

## Getting Started

These instructions will get you a copy of the project up and running on your machine, assuming your machine is similar to mine. I got this working on both my Mac and my PC.

### Prerequisites

What things you need to install the software and how to install them

```
IMDB data
Jupyter Notebook
Python + Pandas
PostgreSQL
```

Getting the imdb data cleanly formatted is a bit tough, but if you're interested in Data Science, dealing with messy, broken data is a big part of life. If imdb changes the format of their data in the future or stops allowing access to it, this tutorial will become obsolete.

Instead of Jupyter, you could probably use another ipython interpreter if it also allows you to call SQL queries with magic functions.

You could also probably use another SQL relational database management system like MySQL or SQLite instead of PostgreSQL.

I'll recommend just installing Anaconda with python and jupyter together. I'm assuming most people checking this out are already learning to code python with these tools.

### Installing

A step-by-step series of examples that tell you how to get a development env running.

#### IMDB data

The data can be found at: https://datasets.imdbws.com/

For now I use just these three files in order to get a list of the top episodes of a TV series across seasons:

```
title.basics.tsv.gz
title.episode.tsv.gz
title.ratings.tsv.gz
```

#### Jupyter Notebook

You hopefully already have this, if not there are plenty of guides online and setting it up varies a little from Windows to Mac to Linux.
I just installed Anaconda and it set me up with the latest verson of Python and Jupyter Notebook.

```
Give the example
```

One thing I had trouble with early on when coding was getting libraries for python. I use PIP, I think Anaconda comes with it's own package manager.

If python is working in your command line in Windows you can try this to install pip, I believe it's the same in terminal on a Mac:

```
python -m pip install --upgrade pip
```

And then use pip to install the pandas library:

```
pip install pandas
```

Pandas is a library of tools and code helpful for working with large datatable like the imdb data. This is a bit redundant, quite often you can just use pandas instead of SQL depending on what you are doing. Pandas has more complex operations for manipulating the data. I only really use it here to keep SQL queries for further manipulation in python.

We also need the sql magic function tools for notebook:

```
pip install ipython-sql
```

#### PostgreSQL

Download and install

Start up the command line imdb PSQL (windows), on Mac use the PostgreSQL.app to start a server which should open up a command line for postgreSQL.


[Guide I'm following for windows](https://www.microfocus.com/documentation/idol/IDOL_12_0/MediaServer/Guides/html/English/Content/Getting_Started/Configure/_TRN_Set_up_PostgreSQL.htm)

Use the "PSQL" shortcut from postgresql listing in your start menu/in windows, or access postgresql with the default User (-U), 'postgres', from your command prompt with:

```
psql -U postgres
```
I suggest accessing through the windows cmd/command prompt or powershell; I found I could only cut-and-paste code into the command prompt, not the PQSL prompt.

How to get an empty database to put the imdb stuff in later:

```
CREATE DATABASE imdb WITH ENCODING 'UTF8';
```

Could also specify character types, Collate and Ctype, but leaving it out seems to default to your system settings.

use `\l` to list databases within your server. 

Switch to your new, empty imdb database with:

```
\c imdb
```

Before importing the imdb data, create an empty table with the schemas and datatypes all prepared. 

Here is the description for the imdb title.basics from the imdb site:

```
title.basics.tsv.gz - Contains the following information for titles:
    tconst (string) - alphanumeric unique identifier of the title
    titleType (string) – the type/format of the title (e.g. movie, short, tvseries, tvepisode, video, etc)
    primaryTitle (string) – the more popular title / the title used by the filmmakers on promotional materials at the point of release
    originalTitle (string) - original title, in the original language
    isAdult (boolean) - 0: non-adult title; 1: adult title
    startYear (YYYY) – represents the release year of a title. In the case of TV Series, it is the series start year
    endYear (YYYY) – TV Series end year. ‘\N’ for all other title types
    runtimeMinutes – primary runtime of the title, in minutes
    genres (string array) – includes up to three genres associated with the title
``` 

From within the imdb database in the postgresql prompt, this will create an empty table for the title.basics dataset using datatypes which work for postgresql:

```
CREATE TABLE title_basics (
  tconst character varying(80),
  titleType character varying (80),
  primaryTitle text,
  originalTitle text,
  isAdult bool,
  startYear int,
  endYear int,
  runtimeMinutes int,
  genres text
);
```

You can check that this new title_basics table exists in your imdb database with: `\dt`

You'll have to check the datatypes and initialize each of the imdb datasets you want to download and include in your sql database. See below for how I did `title.episode` and `title.ratings`

## Importing the data

Time to copy the data from the downloaded files to tables in the imdb database in your SQL server. This part gets a bit messy, which is a big part of data science.

Postgresql seems to handle CSV's better than tab separated files, so we will convert to a csv for several reasons

1. There is a header in the imdb tables, which can only be handled as a csv
2. There are non standard characters in the data set from foreign films etc. so we need to handle encoding
3. We can specify how to handle Nulls etc.

You will need to do this separately for each file from imdb you want.

[Here is a link how to convert the files to csv from tsv](https://stackoverflow.com/a/2535337)

- note for later: Maybe the csv library imported by the script can deal with problems in dataset? Open double quotes?

Here is the suggested code:

```
import sys
import csv

tabin = csv.reader(sys.stdin, dialect=csv.excel_tab)
commaout = csv.writer(sys.stdout, dialect=csv.excel)
for row in tabin:
  commaout.writerow(row)
```

Cut and paste the python code into an editor and save as a .py file extension. From a command prompt or powershell run the line:

```
python script.py title.basics.tsv title.basics.csv
```


```
\copy title_basics FROM 'G:\Users\Greg\Downloads\title.basics.tsv' (FORMAT tsv, HEADER, DELIMITER ',', NULL '\N');
```

\COPY title_basics FROM 'G:\Users\Greg\Downloads\title.basics\title.basics.tsv' DELIMITER '   ';

If windows doesn't like the encoding, try this in the psql prompt:

```
SET CLIENT_ENCODING TO 'utf8';
```















```----```

End with an example of getting some data out of the system or using it for a little demo

## Running the tests

Explain how to run the automated tests for this system

### Break down into end to end tests

Explain what these tests test and why

```
Give an example
```

### And coding style tests

Explain what these tests test and why

```
Give an example
```



## Authors

* **Gregory Stegeman** - *Initial work* -

See also the list of [contributors](https://github.com/your/project/contributors) who participated in this project.

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments

* Hat tip to anyone whose code was used
* Inspiration
* etc
