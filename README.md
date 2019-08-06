#### project incomplete as of April 17, just testing this out.

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

.gz file format is some time of file compression. I used 7zip to unzip to access the tsv files. Rename or keep track of file names when you do decompress, they all seemed to default to data.tsv for me.

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


## Setting up PostgreSQL for IMDB database

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

## Copying the data to SQL

Time to copy the data from the downloaded files to tables in the imdb database in your SQL server. This part gets a bit messy, which is a big part of data science.

You will need to do this separately for each file from imdb you want. Let's start with `title.basics.tsv.gz`. Unzip the gz file with whatever you use for compressed files. The file may default to data.tsv, rename it back to `title.basics.tsv` to keep track of which table is which.

Postgresql seems to handle CSV's better than tab separated files, so we will convert from tab separated .tsv to comma separated .csv for several reasons

1. There is a header in the imdb tables, which can only be handled as a csv in postgresql
2. There are non standard characters in the data set from foreign films etc. so we need to handle encoding
3. We can specify how to handle Nulls etc.


[Here is a link how to convert the files to csv from tsv](https://stackoverflow.com/a/2535337)


We need to modify the script in the link for windows or it inserts extra line breaks between every row. We can add `lineterminator='\n'` to the arguments in `csv.writer()` to fix the problem. Here is the modified suggested python script for format conversion from the link above:

```
import sys
import csv

tabin = csv.reader(sys.stdin, dialect=csv.excel_tab)
commaout = csv.writer(sys.stdout, lineterminator='\n', dialect=csv.excel)
for row in tabin:
  commaout.writerow(row)
```
- note for later: Maybe the csv library imported by the script can deal with problems in dataset? Open double quotes?

Cut and paste the python code into an editor and save as something like tsvconvertscript.py with the .py file extension. 

To start the conversion from tsv to csv, from a command prompt ~~or powershell~~ (Powershell doesn't like the ">") navigate to the directory where the tsv files located and run something like this:

```
python tsvconvertscript.py < title.basics.tsv > title.basics.csv
```

The conversion may take some time, and there is no progress indicator in the script. Once complete you'll have a very large .csv file, (too large to open with excel! one reason you're probably here). Now we can try importing the file into our postgres database.

#### Copy from .csv to PostgreSQL

Back in your postgreQSL prompt/console/window try this to import data from the csv we just made, adjusting for your local file location:

```
\COPY title_basics FROM 'C:\IMDBdata\raw\title.basics.csv' (FORMAT csv, HEADER, DELIMITER ',', NULL '\N');
```

If windows doesn't like the encoding, try this in the psql prompt:
```
SET CLIENT_ENCODING TO 'utf8';
```

Postgresql will try copying the data into your table until it hits an error. This is where I don't have a good solution, but we can edit the files to fix the errors manually. This is a bit difficult because the files are so large. Sometimes as a data scientist you just need to manually fix the data. Ideally we should script out this fix or find a programmatic way to handle this or we will have to do this over and over again everytime. If you find a way, please let me know. There may be a way with the python csv conversion script.
 
 For the title.basics.csv I get an error: `invalid input syntax for type boolean` at line `1102398`. It looks like the year is getting bumped into the "is adult" column. Let's take a look at what is happening. In windows you can inspect `title.basics.csv` using "open with" notepad or another text editor. Try using `ctrl + g` to jump to the line in the error. If ctrl+g is not working you may need check the menu bar for "format" and turn off "word wrap". 

When we check out line 1,102,398 in title.basics, we see:
```
tt10233364,tvEpisode,Rolling in the Deep Dish	Rolling in the Deep Dish,0,2019,\N,\N,Reality-TV
```
It looks like there is a missing comma and a tab leftover between the primaryTitle and originalTitle columns for this row. Let's manually select the tab whitespace and overwrite it with a `,`, save the csv file, and then run the `\COPY title_basics...` command in postgresql again. 

For me, it runs until like `2604327` with a similar error where a column must have been skipped because a non boolean is ending up in the boolean datatype only "isadult" column. Repeat this process until you successfully copy the whole csv into posgres. It seems like postgres will not duplicate previous rows from each attempt as you work through these errors.

1. At postgresql prompt run `\COPY table_name FROM 'filelocation.csv' (FORMAT csv, HEADER, DELIMITER ',', NULL '\N');`
2. Open filename.csv in text editor that can handle large file.
3. Go to line from error message with `ctrl +g`
4. Figure out what caused the error (usually a missing comma, or quotations that trap a comma or are never closed)
5. Carefully fix the error, usually delete or overwrite the offending characters.
6. Save file as same name and retry from step #1 until the copy command completes for your file.

If you are getting errors every few lines you need to find the root cause. For the errors I mentioned here, they are rare and hundreds of thousands of rows apart so I just did it by hand which seemed faster to me that trying to find the underlying issue.

I got eight errors between the primaryTitle and originalTitle columns, at lines:
```
1102398 -tab to comma
2604327 -tab to comma
3329456 -space to quotation marks and comma
5079326 -missing comma, maybe due to backslash earlier
5500686 -missing close quotation marks and comma
6014341 -tab to comma
6051196 -missing comma, probably due to lots of apostrophe's
6055561 -missing comma
```

When the copy completes you should get a message like `COPY 6059226` with the number indicating the number of rows imported. This number will depend on how many titles are in the imdb data when you download the files. I initially got a message: 'COPY 159546' which was suspiciously low, so I ran the copy command again and it worked.

You can also now query this table with SQL. From the postgresql prompt, try the command below and you should get the same number as the message at the end of the copy command:

```
SELECT COUNT(*) FROM title_basics;
```



#### Import the other tables

Now you can use the same steps to import the other tables you want to use.

1. Initialize the next table schema with the column names and datatypes in the imdb database in postgresql
2. Extract the .tsv from the downloaded .gz file and rename
3. Run the .tsv conversion python script to convert from .tsv to .csv
4. Copy the data from the .csv to the empty table in postgresql

For example: `title_episode`

1. Try setting up the table in sql like this:
```
CREATE TABLE title_episode (
  tconst character varying(80),
  parentTconst character varying(80),
  seasonNumber int,
  episodeNumber int
);
```

2. Extract and rename the file to title.episode.tsv
3. Convert to .csv in command line from directory with data files and script:
```
python tsvconvertscript.py < title.episode.tsv > title.episode.csv
```
4. Copy into the imdb database in postgresql from wherever you located the csv file in the postgresql prompt:
```
\COPY title_episode FROM 'C:\IMDBdata\raw\title.episode.csv' (FORMAT csv, HEADER, DELIMITER ',', NULL '\N');
```

Here is a working table schema for title.ratings:

```
CREATE TABLE title_ratings (
  tconst character varying(80),
  averageRating numeric,
  numVotes int
);
```

After you import title_ratings, try to query how many total ratings/votes IMDB has received from users!

## Query the database in Jupyter Notebook

When you have postgresql running your imdb database should be accessible to outside programs accessing postgresql as a server. This means we can query the data from Jupyter notebook! I'm largely assuming you, as an aspiring data scientist, have already installed and worked with Jupyter notebooks a bit.

I used this pdf guide a lot to get my notebook up and running with sql: [PostgreSQL and Jupyter setup](https://www.systems.ethz.ch/sites/default/files/ex1a_postgresql_jupyter_setup.pdf)

First, there are some python packages/libraries we need to work with SQL in jupyter notebooks. You can install them from command line with the following code depending on how your anaconda or python install worked. I'm assuming pip will work, you maybe able to use conda instead depending on if you are using different environments, you'll have to figure this out for yourself, I just want to get sql working in ipython/jupoyter notebooks for now:
```
pip install ipython-sql pandas sqlalchemy psycopg2
```
- ipython-sql is for magic functions which can work with SQL.
- pandas allows you to work with large datasets in python.
- sqlalchemy - ORM object relational mapper for databases
- psycopg2 required for sqlalchemy?




```
%sql postgresql://postgres:@localhost/imdb
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
