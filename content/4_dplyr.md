---
layout: page
title: 4 - Utilising dplyr
---

Aggregating and analyzing data with dplyr
=====================

> Learning Objectives
> -------------------
> 
> * Install and load dplyr package
> * Apply common dplyr functions to manipulate data in R.
> * Save a dataframe to csv file
> * Utilise the ‘pipe’ operator to link together a sequence of functions.
> * Utilise the ‘mutate’ function to apply other chosen functions to existing columns and create new columns of data.
> * Apply the ‘split-apply-combine’ concept to split the data into groups, apply analysis to each group, and combine the results.


# Installing packages 
`dplyr` is a package for making data manipulation easier.

It is a member of the tidyverse family, which is a collection of packages aimed to transform and better present data. In this course, we are learning dplyr and ggplot that have their own unique roles visualised below. 

![tidyverse](../img/tidyverse-package-workflow.png)

Packages in R are basically a collection of functions. The functions we’ve been using, like `str()`, come built into R; packages give you access to more functions. You need to install a package and then load it to be able to use it.

    install.packages("dplyr") ## install

You might get asked to choose a CRAN mirror – this is basically asking you to choose a site to download the package from. The choice doesn’t matter too much; I’d recommend choosing the RStudio mirror.

    library("dplyr")          ## load

You only need to install a package once per computer, but you need to load it every time you open a new R session and want to use that package.

> Hint: Some libraries are not available via the `install.packages` function, especially Bioconductor. For example, DESeq2 used for differential expression analysis  (https://bioconductor.org/packages/release/bioc/html/DESeq2.html). To do this copy the code into the console.
> 
> ```
> if (!require("BiocManager", quietly = TRUE))
>   install.packages("BiocManager")
>
> BiocManager::install("DESeq2")
> ```

What is dplyr?
--------------

The package `dplyr` is a fairly new (2014) package that tries to provide easy tools for the most common data manipulation tasks. 
It is built to work directly with data frames. 

### Load Metadata CSV File
Please rerun the command from Monday's session. This might be different depending on where you are within your directory. 

```
    metadata <- read.csv("data/Ecoli_metadata.csv", stringsAsFactors = TRUE)
```

### Selecting columns and filtering rows

We’re going to learn some of the most common `dplyr` functions: `select()`, `filter()`, `mutate()`, `group_by()`, and `summarize()`. To select columns of a data frame, use `select()`. The first argument to this function is the data frame (`metadata`), and the subsequent arguments are the columns to keep.
```
    select(metadata, sample, clade, cit, genome_size)
```

To choose rows, use `filter()`:

```
    filter(metadata, cit == "plus")
```

### Pipes

But what if you wanted to select and filter? There are three ways to do this: use intermediate dataframes, nested functions or finally, pipes.

 - By forming <b>intermediate data frames</b>, you create a temporary data frame and use that as input to the subsequent function. This can clutter up your workspace with lots of objects.
 - You can also <b>nest functions</b> (i.e. one function inside of another). This is handy but can be difficult to read if too many functions are nested as the process from the inside out.
 -   The last option, pipes, takes one function's output and sends it directly to the next. This is useful when you need apply different filtering or functions to the same data set. Pipes in R look like `%>%` and are made available via the `magrittr` package. We actually installed as part of `dplyr`.

First, we are <i>piping</i> 
```
    metadata %>%
      filter(cit == "plus")
```
```
  metadata %>%
        filter(cit == "plus") %>%
        select(sample, generation, clade)
```

In the above, we use the pipe to send the `metadata` data set first through `filter` to keep rows where `cit` was equal to ‘plus’, and then through `select` to keep the `sample` and `generation` and `clade` columns. 

When the data frame is being passed to the `filter()` and `select()` functions through a pipe, we no longer need to include it as an argument to these functions.

If we wanted to create a new object with this smaller version of the data, we could do so by assigning it a new name:

```
    meta_citplus <- metadata %>%
      filter(cit == "plus") %>%
      select(sample, generation, clade)
```

> ### Exercise
> ======
> 
> Using pipes, subset the data to include rows where the clade is ‘Cit+’. Retain columns `sample`, `cit`, and `genome_size.`

### Mutate

Frequently you’ll want to create new columns based on the values in existing columns, for example to do unit conversions or find the ratio of values in two columns. For this we’ll use `mutate()`.

To create a new column of genome size in bp:
```
    metadata %>%
      mutate(genome_bp = genome_size *1e6)
```

If this runs off your screen and you just want to see the first few rows, you can use a pipe to view the `head()` of the data (pipes work with non-dplyr functions too, as long as the `dplyr` or `magrittr` packages are loaded).
```
    metadata %>%
      mutate(genome_bp = genome_size *1e6) %>%
      head
```
The row has a NA value for clade, so if we wanted to remove those we could insert a `filter()` in this chain:
```
    metadata %>%
      mutate(genome_bp = genome_size *1e6) %>%
      filter(!is.na(clade)) %>%
      head
```

`is.na()` is a function that determines whether something is or is not an `NA`. The `!` symbol negates it, so we’re asking for everything that is not an `NA`.


> Exercise
> --------
> Using pipes, visualise the last few rows using the function `tail` of the data manipulated above.
>

### Split-apply-combine data analysis and the summarize() function

Many data analysis tasks can be approached using the “split-apply-combine” paradigm.
1. split the data into groups
2. apply some analysis to each group
3. then combine the results.
  

`dplyr` makes this very easy through the use of the `group_by()` function, which splits the data into groups. When the data is grouped in this way, `summarize()` can be used to collapse each group into a single-row summary. `summarize()` does this by applying an aggregating or summary function to each group. For example, if we wanted to group by citrate-using mutant status and find the number of rows of data for each status, we would do:

```
    metadata %>%
      group_by(cit) %>%
      summarize(n())
```


Here the summary function used was `n()` to find the count for each group. 

We can also apply many other functions to individual columns to get other summary statistics. For example, in the R base package, we can use built-in functions like `mean`, `median`, `min`, and `max`. 

By default, all **R functions operating on vectors that contain missing data will return NA**. It’s a way to make sure that users know they have missing data, and make a conscious decision on how to deal with it. When dealing with simple statistics like the mean, the easiest way to ignore `NA` (the missing data) is to use `na.rm=TRUE` (`rm` stands for remove).

So to view mean `genome_size` by mutant status:

```
    metadata %>%
      group_by(cit) %>%
      summarize(mean_size = mean(genome_size, na.rm = TRUE))
```

You can group by multiple columns too:

```
    metadata %>%
      group_by(cit, clade) %>%
      summarize(mean_size = mean(genome_size, na.rm = TRUE))
```

Looks like for one of these clones, the clade is missing. We could then discard those rows using `filter()`:

```
    metadata %>%
      group_by(cit, clade) %>%
      summarize(mean_size = mean(genome_size, na.rm = TRUE)) %>%
      filter(!is.na(clade))
```

All of a sudden, the results are not running off the screen anymore. That’s because `dplyr` has changed our `data.frame` to a `tbl_df`. 

This is a data structure that’s very similar to a data frame; for our purposes the only difference is that it won’t automatically show tons of data going off the screen.

You can also summarize multiple variables at the same time:
```
    summarise_metadata <- metadata %>%
      group_by(cit, clade) %>%
      summarize(mean_size = mean(genome_size, na.rm = TRUE),
                min_generation = min(generation))
```
Look at [Handy dplyr cheatsheet](http://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf) for more possibilities with the dplyr package.

> Exercise
> --------
> Using the cheatsheet above,
> 1) Can you randomly select 3 rows from metadata?
> 
> 2) Can you group by the cit and find the mean generation number across the groups?
>
>

### Exporting to csv 
The goal is to export our processed dataset to a CSV file. 
We will use the `write.csv` function which takes two arguments: the data frame you want to export and the location. 

```
write.csv(summarise_metadata, "data/summarised_metadata.csv")
```


****
This lesson was copied or adapted from Jeff Hollister’s [materials](http://usepa.github.io/introR/2015/01/14/03-Clean/)_
Material adapted from (https://datacarpentry.org/R-genomics/01-intro-to-R.html) and (https://datacarpentry.org/semester-biology/materials/r-intro/)



