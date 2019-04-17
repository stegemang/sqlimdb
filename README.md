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
