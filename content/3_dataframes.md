---
layout: page
title: 3 - Dealing with Data Frames
---

# Import data, debug errors, and  manipulate dataframes

> ## Learning Objectives
>
> -   Load external tabular data from a .csv file into R.
> -   Describe what an R data frame is.
> -   Summarize the contents of a data frame in R.
> -   Learn what to do when you encounter an error.

------------------------------------------------------------------------

# Loading and examining an example data set

We are studying a population of Escherichia coli (designated Ara-3), which were propagated for more than 40,000 generations in a glucose-limited minimal medium. This medium was supplemented with citrate which E. coli cannot metabolize in the aerobic conditions of the experiment. Sequencing of the populations at regular time points reveals that spontaneous citrate-using mutants (Cit+) appeared at around 31,000 generations. This metadata describes information on the Ara-3 clones. Read more about the Long Term Evolution Experiment [here](https://the-ltee.org/about/) from Lenski.

The data we are using today comes from the Blount et al. paper in [Nature](https://www.nature.com/articles/nature11514).

The columns stand for:

| Column      | Description                                |
|-------------|--------------------------------------------|
| sample      | clone name                                 |
| generation  | generation when the sample was frozen      |
| clade       | based on phylogeny-based tree              |
| strain      | ancestral strain                           |
| cit         | citrate-using mutant status                |
| run         | Sequence read archive sample ID            |
| genome_size | size in Mbp (made up data for this lesson) |


### Workspace set up

We will start by making a folder for our input data. There are some functions within R that let you create folders and see what is in them. This can be useful for making output folders from your scripts and checking if files are present (the alternative is using the clickable options within RStudio). We will create the folder using the `dir.create()` function and confirm that the folder is empty using the `list.files()` function. You can also download files directly from within R using the `download.file` function

```         
## dir.create is the function for making a new folder (directory)
dir.create('data')

## you can use the list.files() function to see what files are present in a folder
list.files("data")

## Now you can put the metadata file from the course materials into the data folder
```

### Downloading the data 
  
We have put the data file within a shared folder on [Google Drive](https://drive.google.com/drive/folders/1FGAUmqkgKEisNypqnIUL2afFeCfZ4Vm1?usp=drive_link) so it is simpler to download the file manually and then add it (there is a `googledrive` R package but we are not using it here).
<b> If you cannot access the file, there is code in the Appendix section at the bottom of this page for recreating the data within R </b>

A demonstration of downloading the file directly from Google Drive using `download.file()` is shown below;
```         
## DO NOT RUN - This is the link to the file on Google Drive but it may require authentication 
#download.file(url="https://drive.google.com/uc?export=download&id=1yg29Yol0FlkUnjS78qkoWxHdA3bvijZu" ,destfile="data/Ecoli_metadata.csv")

```

### Checking that the file is in the data folder

You can check that the file is in the data folder using the files panel in RStudio or you can use R functions such as `list.files` or `file.exists` as shown below

```         
list.files("data")
# should show "Ecoli_metadata.csv" in the console
file.exists("data/Ecoli_metadata.csv")
# returns TRUE if the file is present or FALSE if the file is not present
```
  
### Loading the data into R

You are now ready to load the data. As the data is in the csv (comma-separated values) format, we are going to use the R function `read.csv()` to load the data file into memory (as a `data.frame`):

```         
metadata <- read.csv('data/Ecoli_metadata.csv')
```

This statement doesn’t produce any output because the assignment doesn’t display anything. If we want to check that our data has been loaded, we can run some checks on our new object: `metadata`

Let’s check the top (the first 6 lines) of this `data.frame` using the function `head()`:

```         
head(metadata)

##     sample generation   clade strain     cit       run genome_size
## 1   REL606          0    <NA> REL606 unknown                  4.62
## 2 REL1166A       2000 unknown REL606 unknown SRR098028        4.63
## 3   ZDB409       5000 unknown REL606 unknown SRR098281        4.60
## 4   ZDB429      10000      UC REL606 unknown SRR098282        4.59
## 5   ZDB446      15000      UC REL606 unknown SRR098283        4.66
## 6   ZDB458      20000 (C1,C2) REL606 unknown SRR098284        4.63
```

We’ve just done two very useful things.

1.  We’ve read our data in to R, so now we can work with it in R

2.  We’ve created a data frame using the `read.csv()` function. We didn't have to specify a dataframe when using `read.csv()` as it is the default format that the function will return to you

#### Some more about read.csv and loading data

> `read.csv()` is an example of a "wrapper" function as it uses `read.table()` inside of it but with the default arguments changed to what is required for a loading a .csv file. Running `read.table("data/Ecoli_metadata.csv", sep = ",", header = TRUE)` is the same as running `read.csv("data/Ecoli_metadata.csv")`
>
> If the data was in .tsv format then you could use `read.table("data/Ecoli_metadata.csv", sep = "\t", header = TRUE)` instead.
>
> There are functions for loading all sorts of data types into R but you will typically need to install other R packages (eg `read.xslx()` from the `openxlsx` package for excel files and `Read10X()`/`ReadXenium()`/`Load10X_Spatial()` from the `Seurat` package for single cell and spatial data)

# Debugging errors

Let's create a very common error that you will encounter.

```         
(metadata <- read.csv('dataa/Ecoli_metadata.csv'))
```

```         
Error in file(file, "rt") : cannot open the connection
In addition: Warning message:
In file(file, "rt") :
    cannot open file 'dataa/Ecoli_metadata.csv': No such file or directory
```

## What to do when you get an error

1.  Don't panic - you are not special

2.  First read the error message - what does it say? \> Often you end up with general error messages that might not be very helpful for diagnosing the problem (e.g. “subscript out of bounds”).

3.  Next, check your code for common errors (a) matched brackets (b) matched quotation marks (c) correct names/typos. <b> Is the text in your text editor the correct colour? </b>

4.  Then, google the error message or paste it into your LLM of choice along with the code used to generate the error. <b> Pasting code into an LLM is generally safe (as most often you will be using publicly available code) but it is better to use LLMs that Garvan has an enterprise agreement with. Avoid pasting in any sensitive information (eg patient IDs) and direct file paths to where your data is kept </b>

5.  You can also check support sites such as github or stackoverflow.com. For stackoverflow, search using the [r] tag. Most questions have already been answered, but the challenge is to use the right words in the search to find the answers: <http://stackoverflow.com/questions/tagged/r>. If your issue is specific to a particular R package then you can also go to the issues section of the github page and look to see if anyone else has had the same problem (eg. <https://github.com/satijalab/seurat/issues>)

> ## Exercise
>
> On your table can you please try to create a unique error message?

# What are data frames?

A `data.frame` is a collection of vectors of identical lengths. Each vector represents a column, and each vector can be of a different data type (e.g., characters, integers, factors). The `str()` function is useful for inspecting the data types of the columns.

By default, `data.frame` coerces columns that contain characters (i.e., text) into the `factor` data type. Depending on what you want to do with the data, you may want to keep these columns as `character`. To do so, `read.csv()` and `read.table()` have an argument called `stringsAsFactors` which can be set to `FALSE`:

Let’s now check the structure of this `data.frame` in more details with the function `str()`:

```         
str(metadata)
```

## Inspecting `data.frame` objects

We already saw how the functions `head()` and `str()` can be useful to check the content and the structure of a `data.frame`. Here is a non-exhaustive list of functions to get a sense of the content/structure of the data.

-   Size:
    -   `dim()` - returns a vector with the number of rows in the first element, and the number of columns as the second element (the dimensions of the object)
    -   `nrow()` - returns the number of rows
    -   `ncol()` - returns the number of columns
-   Content:
    -   `head()` - shows the first 6 rows
    -   `tail()` - shows the last 6 rows
-   Names:
    -   `names()` - returns the column names (synonym of `colnames()` for `data.frame` objects)
    -   `rownames()` - returns the row names
-   Summary:
    -   `str()` - structure of the object and information about the class, length and content of each column
    -   `summary()` - summary statistics for each column

Note: most of these functions are “generic”, they can be used on other types of objects besides `data.frame`.

> ## Challenge
>
> Based on the given table of functions to assess data structure, can you answer the following questions?
>
> 1)  What is the class of the object metadata?
>
> 2)  How many rows and how many columns are in this object?
>
> 3)  What are the column names for this data frame?

## Subsetting Data frames

The metadata data frame has rows and columns (it has 2 dimensions), if we want to extract some specific data from it, we need to specify the “coordinates” we want from it. Row numbers come first, followed by column numbers (i.e. $$row, column$$).

```         
metadata[1, 2]   # 1st element in the 2nd column 
metadata[1, 6]   # 1st element in the 6th column
metadata[1:3, 7] # First three elements in the 7th column
metadata[3, ]    # 3rd element for all columns
metadata[, 7]    # Entire 7th column
```

## Using column names to subset data frames

For larger datasets, it can be tricky to remember the column number that corresponds to a particular variable. Sometimes the column number for a particular variable can change if your analysis adds or removes columns. The best practice when working with columns in a data frame is to refer to them by name. This also makes your code easier to read and your intentions clearer.

There are two ways to select a column by name from a data frame:

-   Using `dataframe[ , "column_name"]`
-   Using `dataframe$column_name`

You can do operations on a particular column, by selecting it using the `$` sign.

In this case, the entire column is a vector. To see the possible columns names run

```         
names(metadata)
colnames(metadata)
```

To subset just the strain column from the metadata or

```         
metadata[ , "strain"]
    
metadata$strain
```

The first method allows you to select multiple columns at once. Suppose we wanted strain and clade information:

```         
metadata[, c("strain", "clade")]
```

You can even access columns by column name *and* select specific rows of interest. For example, if we wanted the strain and clade of just rows 4 through 7, we could do:

```         
metadata[4:7, c("strain", "clade")]
```

> # Exercise
>
> Create a new variable of a dataframe that contains just the sample and genome size.
>
> Why would you create a subset of the original dataframe?

## Factors

In the data frame we just imported, let’s do:

```         
str(metadata)
```

As you can see, many of the columns in our data frame are character datatypes. However, there is also a special class called `factor`.

They are very useful but not necessarily intuitive and therefore require some attention.

Factors are used to represent categorical data. Factors can be ordered or unordered. They are an important class for statistical analysis and for plotting.

Factors are: 
- stored as integers 
- have labels associated with these unique integers. 
- While factors look (and often behave) like character vectors, they are actually integers under the hood, and you need to be careful when treating them like strings.

Let us convert the clade columns from a character to a factor column using the `factor` command.

```         
factor(metadata[,3])
```

The levels associated with this factor are `Levels: (C1,C2) C1 C2 C3 Cit+ UC unknown`.

> # Exercise
>
> Let's reread our metadata data frame, but set every character column to be a factor.
>
> By looking at the help page, can you edit the command below with a new argument that would read all strings as factors?

```         
metadata <- read.csv('data/Ecoli_metadata.csv')

## Note that this is not the default option for read.csv and it is better
## to set the columns that you want to be factors as factors rather than 
## making every character column a factor

```

Factors are character vectors that can only contain a pre-defined set of values known as *levels*. By default, R always sorts *levels* in alphabetical order.

For instance, we see that `cit` is a Factor w/ 3 levels, `minus`, `plus` and `unknown`. We can check and change the order of the levels using the `levels()` function. To demonstrate changing the order, we will add a new column with a different ordering to our dataframe. Reordering levels of a factor is a common process that you will need to do

```         
levels(metadata$cit)
    
## make a new variable by using the <- symbol

metadata$cit_unknown_first <- factor(metadata$cit,levels=c("unknown","minus","plus"))
    
## Check the output    
    
```

> # Exercise
>
> Let's test what happens if we capitalise the levels. How would you check the output column?

> ```         
> metadata$cit_capitalised <- factor(metadata$cit,levels=c("Unknown","Minus","Plus"))
> ```

This demonstrates how the levels of a factor must match what is actually present in the column. Note that it does not return an error so it is important to check your output!

# Appendix - Additional comments on loading data

Another useful argument to `read.csv` is `na.strings`. This argument tells read.csv if you want to convert anything from your raw data into `NA`. Converting missing data to `NA` means that you can use R functions like `is.na()` to deal with them later

```
## Anything matching `NA,""," ", or "Not Detected" will be converted to NA.
## This is useful if you have been given data where someone has used a different
## term for missing data (e.g "Not Detected") 
metadata <- read.csv('data/Ecoli_metadata.csv',na.strings = c(NA,""," ","Not Detected"))
```

### Larger data sets
`read.csv()` can fail or be very slow at reading files more than a few hundred MB in size. In this case, you can look into the `data.table` package. It has functions for loading and running analyses on larger datasets


## Manually recreate the Ecoli_metadata file contents as a dataframe
If you could not access the Ecoli_metadata file. Copy and paste the code below to manually create it instead

```
# Only run this if you do not have access to the csv file
metadata <- data.frame(
    sample= c("REL606","REL1166A","ZDB409","ZDB429","ZDB446","ZDB458","ZDB464*","ZDB467","ZDB477","ZDB483","ZDB16","ZDB357","ZDB199*",
    "ZDB200","ZDB564","ZDB30*","ZDB172","ZDB158","ZDB143","CZB199","CZB152","CZB154","ZDB83","ZDB87","ZDB96","ZDB99","ZDB107","ZDB111","REL10979","REL10988"),
    generation = as.integer(c(0,2000,5000,10000,15000,20000,20000,20000,25000,25000,30000,30000,31500,31500,31500,32000,32000,32500,32500,33000,33000,33000,34000,34000,36000,36000,38000,38000,40000,40000)),
    clade = c(NA,"unknown","unknown","UC","UC","(C1,C2)","(C1,C2)","(C1,C2)","C1","C3","C1","C2","C1","C2","Cit+","C3","Cit+","C2","Cit+","C1","Cit+",
    "Cit+","Cit+","C2","Cit+","C1","Cit+","C2","Cit+","C2"),
    strain = "REL606",
    cit = c(rep("unknown",12),"minus","minus","plus","minus","plus","minus","plus","minus","plus","plus","minus","plus","plus","minus","plus","minus","plus","minus"),
    run = c("","SRR098028","SRR098281","SRR098282","SRR098283","SRR098284","SRR098285","SRR098286","SRR098287","SRR098288","SRR098031","SRR098280","SRR098044",
    "SRR098279","SRR098289","SRR098032","SRR098042","SRR098041","SRR098040","SRR098027","SRR097977","SRR098026","SRR098034","SRR098035","SRR098036","SRR098037","SRR098038","SRR098039","SRR098029","SRR098030"),
    genome_size = c(4.62,4.63,4.60,4.59,4.66,4.63,4.62,4.61,4.65,4.59,4.61,4.62,4.62,4.63,4.74,4.61,4.77,4.63,4.79,4.59,4.80,4.76,4.60,4.75,4.74,4.61,4.79,4.62,4.78,4.62))
```

------------------------------------------------------------------------

Material adapted from (<https://datacarpentry.org/R-genomics/01-intro-to-R.html>) and (<https://datacarpentry.org/semester-biology/materials/r-intro/>) by Helen King. Further revisions by the Data Science Platform.
