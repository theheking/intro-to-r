---
layout: page
title: Extras 2 - Automation
---

Automation in R
===========================

> Learning Objectives
> ====================
> * Understand why automation is useful for avoiding repetitive code
> * Use a for loop to repeat a step across multiple elements
> * Use lapply to apply a function across a list
> * Understand when to choose a loop vs an apply function


# Why automate?

A core principle in programming is **don't repeat yourself**. If you find yourself copying and pasting the same code and just changing one value each time, that is a sign that automation will save you time and reduce errors.

For example, imagine you want to plot histograms of three different columns from your metadata dataframe:

```
    hist(metadata$genome_size)
    hist(metadata$generation)
    hist(metadata$clade)
```

This works, but if you later want to change the plot style — say, add a title or change the colour — you have to remember to update every line. With automation, you write the logic once and let R handle the repetition.

There are two main approaches to automation in R:

- **For loops** — repeat a block of code for each element in a list or vector
- **Apply functions** — a compact R-style alternative that applies a function to every element of a list, vector, or dataframe


# For loops
-------------------

A for loop repeats a block of code once for each element in a sequence. The basic structure is:

```
    for (variable in sequence) {
      # code to run for each element
    }
```

Each time the loop runs, `variable` takes the next value from `sequence`. For example:

```
    for (i in 1:5) {
      print(i)
    }
```
```
    [1] 1
    [1] 2
    [1] 3
    [1] 4
    [1] 5
```

The loop ran the `print()` call five times — once for each value in `1:5` — without us having to write it out five times.

## Using a loop to generate multiple plots

This connects directly to what you learned in the [Data Visualisation](5_datavisualisation) section. Instead of writing a separate `ggplot` call for each column you want to inspect, you can loop over a list of column names and generate each plot automatically.

Using the `metadata` dataframe, suppose we want to produce a histogram for each of the numeric columns:

```
    library(ggplot2)

    numeric_cols <- c("genome_size", "generation")

    for (col in numeric_cols) {
      p <- ggplot(metadata, aes(x = .data[[col]])) +
        geom_histogram(binwidth = 0.05, fill = "steelblue", colour = "white") +
        ggtitle(paste("Distribution of", col)) +
        xlab(col) +
        ylab("Count") +
        theme_minimal()
      print(p)
    }
```

Each time the loop runs, `col` takes the next value from `numeric_cols` ("genome_size" then "generation"), and a new plot is produced and printed. The `.data[[col]]` syntax is the ggplot2 way of referring to a column by a variable name.

> Exercise
> --------
> 1. Add a third column to `numeric_cols` and re-run the loop. What happens?
> 2. Try changing `geom_histogram` to `geom_boxplot` — you will also need to change `aes(x = ...)` to `aes(y = ...)`. What does the output look like?


# Apply functions
-------------------

R has its own built-in approach to automation called the **apply family**. These functions do the same job as a for loop — repeating an operation across each element — but in a more compact form. They are especially useful when working with dataframes and lists.

We will briefly explain `apply`, `sapply`, and `lapply`. There are many others (vapply, tapply, mapply) that we won't cover today.

First, check the help page:

```
    help(apply)
```

This shows the general structure: `apply(X, MARGIN, FUN)`

- **X** — a dataframe or matrix (the data you will perform the function on)
- **MARGIN** — whether to apply across rows (`1`) or columns (`2`)
- **FUN** — the function you want to use

## Example of apply

Using the `metadata` dataframe:

```
    apply(metadata, 1, sum)
```

This should output an error:
```
    Error in FUN(newX[, i], ...) : invalid 'type' (character) of argument
```

Reading the error message tells us we need to subset for only the numeric columns:

```
    apply(metadata[,c("generation", "genome_size")], 1, sum)
```

The apply function returns a vector containing the sums.

> Exercise
> --------
> 1. By running the command, have we calculated:
>    
>   a. the sum of the rows
> 
>   b. the sum of the columns
> 
> 2. How would you calculate the opposite? (E.g. if we calculated rows, how do we calculate the sum of columns and vice versa?)


# Forming a function and using apply

Sometimes R does not have an appropriate built-in function. This is when you need to make a custom function. We will not go into detail, but an example of the notation for adding a log pseudocount is as follows:

```
    logpseudocount <- function(x){
      log(x+1)
    }
```

This custom function can then be passed into apply:

```
    apply(metadata[,c("generation", "genome_size")], 2, logpseudocount)
```

lapply, sapply and more
============================

`lapply` and `sapply` are functions that will loop a function through data in a list or vector. They work the same way as `apply`, but are designed for lists and vectors rather than dataframes.

| Function | Arguments             | Objective                                         | Input                      | Output              |
| -------- | --------------------- | ------------------------------------------------- | -------------------------- | ------------------- |
| apply    | apply(x, MARGIN, FUN) | Apply a function to the rows or columns or both   | Data frame or matrix       | vector, list, array |
| lapply   | lapply(X, FUN)        | Apply a function to all the elements of the input | List, vector or data frame | list                |
| sapply   | sapply(X, FUN)        | Apply a function to all the elements of the input | List, vector or data frame | vector or matrix    |

Again, X is a vector or list, and FUN is the function you want to use.

### lapply example

`lapply()` is useful for performing operations on list objects and returns a list of the same length as the input. For example, converting a vector of species names to uppercase:

```
    model_org <- c("echerichia_coli", "homo_sapiens", "chlamydomonas_reinhardtii",
                   "drosophilia_melanogaster", "schizosaccharomyces_pombe",
                   "Saccharomyces_cerevisiae", "arabidopsis_thaliana",
                   "cavia_porcellus", "xenopus_laevis", "nothobranchius_furzeri",
                   "rattus_norvegicus", "danio_rerio")

    model_org_upper <- lapply(model_org, toupper)
```

Check the structure of the output:

```
    str(model_org_upper)
```

`sapply` works just like `lapply`, but will simplify the output to a vector if possible:

```
    model_org_upper <- sapply(model_org, toupper)
```

> Exercise
> --------
> Use the two functions `is.vector` and `is.list` to check if the output of each is a vector or a list respectively.


# Should I use a loop or an apply function?

Both do the same thing — the choice comes down to readability and context:

- **For loops** are easier to read and understand when you are starting out, or when the code inside the loop is complex (e.g., building a plot with many layers).
- **apply / sapply / lapply** are more compact and are the preferred R style for simple operations on dataframes, matrices, and lists.

If you are choosing between `lapply` and `sapply`: use `sapply` when you want a simple vector back, `lapply` when you want to keep the result as a list. If you want to specify the exact type of the output, use `vapply` — read more [here](https://www.r-bloggers.com/2020/10/why-you-should-use-vapply-in-r/).




---
### Bonus: Super advanced automation with functions and plotting

Remember our plot of animal phylogenetic orders vs sleep cycle?

```
library('forcats')
msleep %>%
  drop_na(order, vore, sleep_cycle) %>%
  mutate(order_new = fct_reorder(order, sleep_cycle)) %>%
    ggplot(data = to_plot)+
        geom_point(mapping = aes(x = order_new, y = sleep_cycle, colour = vore))+
        facet_grid(cols = vars(vore), scales = 'free_x', space = 'free_x')+
        theme_classic()+
        theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust = 1))
```

Well part of the reason we plotted `order` on the x axis was because there were just too many animals otherwise. But what if we really want to compare within each group? i.e. For the herbivores, which animal sleeps the most? and so on?

We can write a function that produces a single generalised plot:

```
plotPerVore <- function(diettype){
  p <- msleep %>%
    drop_na(vore, sleep_cycle) %>%
    mutate(animal = fct_reorder(name, sleep_cycle)) %>%
    filter(vore == diettype) %>%
    ggplot()+
      geom_col(mapping = aes(x = animal, y = sleep_cycle, fill = vore))+
      guides(fill = 'none')+
      ggtitle(paste0(diettype, 'vore sleep cycle'))+
      theme_classic()+
      theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust = 1))
  
  return(p)
}

## check if the function works by plotting just one of the options!
plotPerVore('herbi')

```
<img width="600" alt="image" src="https://github.com/user-attachments/assets/acbc60b6-2c17-4269-aadb-b0fb1a3429a0" />

But what's this?? Why is herbivore now in the red, when it used to be green?

It's because we were using the default ggplot2 colours, which are assigned based on order, and not by the ID. 

If you want the colours to stay consistent, you need to predefine them! however, the plot otherwise looks good. Now lets define the colours.

```
colours <- c('herbi' = 'yellowgreen', 'omni' = 'purple', 'insecti' = 'cyan', 'carni' = 'salmon')

## get all the different diets automatically!
alldiets <- msleep %>%
  drop_na(vore) %>%
  pull(vore) %>%
  unique()

plotPerVore <- function(diettype){
  p <- msleep %>%
    drop_na(vore, sleep_cycle) %>%
    mutate(animal = fct_reorder(name, sleep_cycle)) %>%
    filter(vore == diettype) %>%
    ggplot()+
      geom_col(mapping = aes(x = animal, y = sleep_cycle, fill = vore))+
      scale_fill_manual(values = colours, guide = 'none')+
      ggtitle(paste0(diettype, 'vore sleep cycle'))+
      theme_classic()+
      theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust = 1))
  
  return(p)
}

per_diet_plots <- lapply(alldiets, plotPerVore)
```

Well now what? Nothing got produced in the plots tab? That's because all 4 of these plots are now saved in `per_diet_plots`. When you automate plotting, the Plots window becomes useless because automation will happen too fast for you to manually export those plots from the Plots window. Either you end with `ggsave()` instead of return, or you collect a list of plots to do what? To combine them of course!

Introducing combination plots, using either the `cowplot` or `patchwork` package. 

```
library('cowplot')
plot_grid(plotlist = per_diet_plots, nrow = 1, align = 'hv')

```
<img width="1300" alt="image" src="https://github.com/user-attachments/assets/971d2c11-7200-4ba7-a700-87e86f5201ce" />

The result is somewhat similar to faceting isnt it? But it also opens up a lot of its own possibilities!

***
Adapted from https://ademos.people.uic.edu/Chapter4.html
Which in turn adapted from 
[Datacamp tutorial on apply functions](https://www.datacamp.com/community/tutorials/r-tutorial-apply-family)
[r-bloggers: Using apply, sapply, and lapply in R](https://www.r-bloggers.com/using-apply-sapply-lapply-in-r/)
