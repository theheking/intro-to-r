---
layout: page
title: 5 - Apply Functions
---

Using the Apply Functions
===========================
 
> Learning Objectives
> ====================
> * Be able to understand the basic concept of the apply function 
> * See the structure of a function is formatted
> * Use apply,lapply and sapply 


# Why would we use apply functions?
In python, we use a for loop to automate applying the same function across every element in a list. In R, we use apply....
This is because it runs faster than loops and requires less code.

We will briefly explain apply, sapply and lapply. However, there are so many different types such as vapply, tapply, and mapply that we won't explain today.

First, lets bring up the help page for apply functions.
```
    help(apply)
```

This shows the general structure of the apply arguments `apply(X, MARGIN, FUN)`

These mean:
-  X is an array, matrix or dataframe(this is the data that you will be performing the function on)
-   MARGIN specifies whether you want to apply the function across rows (1) or columns (2)
-   FUN is the function you want to use

# Example of applying apply
---------------------------

Using our metadata dataframe as before. We want to apply this to our dataframe. 

```
    apply(metadata, 1, sum)
```
This should output an error:
```
    Error in FUN(newX[, i], ...) : invalid 'type' (character) of argument
```

Reading the error message it tells us that we need to subset for only the numeric columns. 

```
    apply(metadata[,c("generation", "genome_size")], 1, sum)

```
The apply function returned a vector containing the sums.

 
> Exercise
> --------
> 1. By running the command. Have we calculated:
>    
>   a. the sum of the rows
> 
>   b. the sum of the columns
> 
> 2. How would you calculate the opposite. (E.g. if we calculated rows, how do we calculate the sum of columns and vice versa)


# Forming a function and using apply
Sometimes R does not have an appropriate function for you to use. This is when you need to make a custom function.  We will not go into detail but an example of the notation for adding a log pseudocount is as follows:

```
    logpseudocount <- function(x){
      log(x+1)
    }

```
  
  In this example, a function to find standard error was created, then passed into an apply function.

```
    apply(metadata[,c("generation", "genome_size")],2, pseudocount)

```

# lapply, sapply and more....
============================

lapply, and sapply are functions that will loop a function through data in a list or vector. There are numerous other apply functions with different arguement and outputs but we will just focus on these two.

First, lets check the help page.
```
    ?lapply
```

| Function | Arguments             | Objective                                         | Input                      | Output              |
| -------- | --------------------- | ------------------------------------------------- | -------------------------- | ------------------- |
| apply    | apply(x, MARGIN, FUN) | Apply a function to the rows or columns or both   | Data frame or matrix       | vector, list, array |
| lapply   | lapply(X, FUN)        | Apply a function to all the elements of the input | List, vector or data frame | list                |
| sapply   | sapply(X, FUN)        | Apply a function to all the elements of the input | List, vector or data frame | vector or matrix    |


Again, X is a vector or list, and FUN is the function you want to use. sapply and vapply have extra arguments, but most of them have default values, so you don’t need to worry about them. 

### lapply example
lapply() function is useful for performing operations on list objects and returns a list object of same length of original set. lappy() returns a list of the similar length as input list object, each element of which is the result of applying FUN to the corresponding element of list. Lapply in R takes list, vector or data frame as input and gives output in list.

A very easy example can be to change the string value of a  to lower case with tolower function. We construct a matrix with the name of the famous movies. The name is in upper case format.

```
    model_org <- c("echerichia_coli", "homo_sapiens", "chlamydomonas_reinhardtii","drosophilia_melanogaster","schizosaccharomyces_pombe","Saccharomyces_cerevisiae","arabidopsis_thaliana","cavia_porcellus","xenopus_laevis","nothobranchius_furzeri","xenopus_laevis","rattus_norvegicus","danio_rerio")

    model_org_upper <-lapply(model_org, toupper)
```

Check the structure the output

```
    str(model_org_upper)

```

sapply works just like lapply, but will simplify the output if possible. This means that instead of returning a list like lapply, it will return a vector instead if the data is simplifiable.

```
    model_org_upper <-sapply(model_org, toupper)

```

 
> Exercise
> --------
>   Use the two functions `is.vector` and `is.list` to check if the output is a vector or a list respectively. 
>
> 

# Should I use, lapply sapply or another member of the apply group?

If you are trying to decide which of these three functions to use, because it is the simplest, I would suggest to use sapply if possible. If you do not want your results to be simplified to a vector, lapply should be used. If you want to specify the type of result you are expecting, use vapply. To use vapply, please




***
Adapted from https://ademos.people.uic.edu/Chapter4.html
Which in turn adapted from 
[Datacamp tutorial on apply functions](https://www.datacamp.com/community/tutorials/r-tutorial-apply-family)
[r-bloggers: Using apply, sapply, and lapply in R](https://www.r-bloggers.com/using-apply-sapply-lapply-in-r/)

