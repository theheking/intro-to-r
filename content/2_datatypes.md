---
layout: page
title: 2 - Understanding Data Types
---

2 - Understanding Data Types and Basic Functions
================================================

## Learning Objectives
- Describe what variables, vectors and matrixes are and how they can be manipulated in R.
- Use the built-in RStudio help interface to search for more information on R functions.
- Describe what a function is in R.
- Inspect the content of vectors in R and describe their content with class and str.



# Recap Variables
A variable is a name that has a value associated with it
```
    # Assigns a value to a variable
    genome_size_mb <- 35

    # Assigns a value to a variable and prints it out on the console
    (genome_size_mb <- 35)

    # Prints out the value of a variable on the console
    genome_size_mb

```

> Hint: tab key autocompletes. `Type geno and then tab`. Let the computer do repetitious work. It’s easier and with fewer mistakes.
>Hint: A history of the commands you’ve run under `History` in case you forgot to write something down



# Functions 
A function is a takes the input returns an output. 
```
    sqrt(49)
```

A function call is composed of two parts.
- Name of the function
- Arguments that the function requires to calculate the value it returns.

For instance as above: 
    `sqrt()` is the name of the function, and `49` is the argument.

We can also pass variables as the argument.
```
weight_kg <- 0.11
sqrt(weight_kg)
```

Another function that we’ll use a lot is class(). All values and therefore all variables have types.

```
class(weight_kg)
```

Functions can take multiple arguments. For instance, let's round weight_lb to one decimal place.
Typing `round()` shows there are two arguments (pops up a yellow box).


To find out more details about the command of interest, we can use the `help` command too. 

```
help(round)
```

There are two arguments: 1) number to be rounded and 2) number of digits
```
round(weight_kg, 1)
```

Functions return values, so as with other values and expressions, if we don’t save the output of a function then there is no way to access it later.
Check the current value using the `print` command.
```
print(weight_kg)
```

To save the output of a function we assign it to a variable.
```
weight_rounded <- round(weight_kg, 1)
```


> Exercise
> --------
> 
> Check the data type of the following variables. 
> one <- 1.5
> two <- 2L
> three <- FALSE
> 




# Vectors

Let’s create a vector containing the first ten letters of the alphabet.

```
    ten_letters <- c('a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j')
```

In order to extract one or several values from a vector, we must provide one or several indices in square brackets, just as we do in math. R indexes start at 1. Programming languages like Fortran, MATLAB, and R start counting at 1, because that’s what human beings typically do. Languages in the C family (including C++, Java, Perl, and Python) count from 0 because that’s simpler for computers to do.

So, to extract the 2nd element of `ten_letters` we type:
```
    ten_letters[2]
```

We can extract multiple elements at a time by specifying mulitple indices inside the square brackets as a vector. Notice how you can use `:` to make a vector of all integers two numbers.
```
    ten_letters[c(1,7)]
    
    ten_letters[3:6]
    
    ten_letters[10:1]
    
    ten_letters[c(2, 8:10)]
```



> Exercise
> --------
> 
> 1) Select every other element in `ten_letters`.
> Using ten_letters\[\_\_\_\_\_\_\_\_\_\_\_\_\]
> 
> 
> 2) However, this is laborious. If we were dealing with a much longer vector- we can use the `seq()` function to quickly create sequences of numbers.
> Use the help command to find how to form that repeating element. Fill in the blank to select the even elements of ten\_letters using the seq() function.
>



# Matrixes 
- two dimensional organisation of a m*n array
- good for arithmetc operations
- only a single class of data
    - why we utilise dataframes instead

To form a simple matrix we use the command as below.
```
matrix(1:9, nrow = 3, ncol = 3)
```
However, when we want to fill the matrix row wise, we can start using different arguements. 
We will use the `byrow` arguement to fill matrix row-wise using boolean T/F. 
````
x <- matrix(1:9, nrow=3, byrow=TRUE)    
```


> Exercise
> --------
> 
> Let's check the dimensions using the `dim` command or `attibutes`. What is the difference between the outputs of both commands?
> ```
> attributes(x)
> dim(x)
> ```


To access elements of a matrix. 
- Use square brackets `[` indexing method
- elements can be accessed as `matrix[row_num,col_num]` where row_num/column_num is a vector of the number assigned to the row/column of interest.

Select rows 2 and 3 and columns 1 and 3.
```
x[c(2,3),c(1,3)]    
```

> Exercise
> --------
> 
> Access:
> 1) the 2nd row of the matrix, x
> 2) the element from the 3rd row and 2nd column in the matrix, x
>  Extension:
> 1) Check the class of (1) , (2) and (3) the matrix, x, 
> 2) Try leaving the column field blank. What does this output?




Material adapted from (https://datacarpentry.org/R-genomics/01-intro-to-R.html) and (https://datacarpentry.org/semester-biology/materials/r-intro/)

[Data Carpentry](http://datacarpentry.org/), 2017-2018. [License](LICENSE.html). [Contributing](CONTRIBUTING.html).  

