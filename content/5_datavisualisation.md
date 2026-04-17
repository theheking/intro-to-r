---
layout: page
title: 5 - Data Visualisation
---


Data Visualisation using ggplot
===================================
> Learning Objectives
> -------------------
> 
> *   Understand that there is basic R plotting (histograms) and more popular ggplot2 package plots (for everything else).
> *   Be able to shape data into the correct format for making your desired plot.
> *   Customise the aesthetics of an existing plot.
> *   Export plots from RStudio to standard graphical file formats.

Basic plots in R (Histogram)
============================

The mathematician Richard Hamming once said, “The purpose of computing is insight, not numbers”, and the best way to develop insight is often to visualise data. Visualisation deserves an entire lecture (or course) of its own, but we can explore a few features of R’s plotting packages.

When we are working with large sets of numbers, it can be useful to display that information graphically. R has several built-in tools for basic graph types such as histograms, scatter plots, bar charts, boxplots and much [more](http://www.statmethods.net/graphs/). 

However, for most people, you would only use the histogram function in base R plots. We will test that out on the genome size of our metadata.

```
    genome_size <- metadata$genome_size
```
We can do this by using the `hist` function:

```
    hist(genome_size)
```
![](../img/genome_size_histogram.png)


Advanced figures (`ggplot2`)
============================

More recently, R users have shifted away from base graphic options and toward a plotting package called [`ggplot2`](http://docs.ggplot2.org/), which adds significant functionality to the basic plots seen above. The syntax takes some getting used to but it’s extremely powerful and flexible. Let's try out a basic scatterplot.

`ggplot2` is best used on data in the `data.frame` form, so we will work with `metadata` for the following figures. Let’s start by loading the `ggplot2` library.
```
    library("ggplot2")
```

The `ggplot()` function is used to initialise the basic graph structure, and then we add to it. The basic idea is that you specify different parts of the plot and add them together using the `+` operator.

We will start with a blank plot and add layers as we progress.
```
    ggplot(metadata)
```

Geometric objects are the actual marks we put on a plot. Examples include:

*   points (`geom_point`, for scatter plots, dot plots, etc)
*   lines (`geom_line`, for time series, trend lines, etc)
*   boxplot (`geom_boxplot`, for, well, boxplots!)

A plot **must have at least one geom**; there is no upper limit. You can add a geom to a plot using the + operator
```
    ggplot(metadata) +
      geom_point() 
```

Each type of geom usually has a **required set of aesthetics** to be set, and usually accepts only a subset of all aesthetics – refer to the geom help pages to see what mappings each geom accepts. 


Aesthetic mappings are set with the aes() function. Examples include:
*   x (variable for the x axes)
*   y (variable for the y axes)
*   colour (variable for outline)
*   fill (variable for "inside" colour)

To start, we will add the column names that correspond to the variable we want to set for the x- and y-values.
`geom_point` requires arguments for x and y, all other arguments are optional.
We will run the most basic scatterplot of `sample` against `genome size`.
```
    ggplot(metadata) +
      geom_point(aes(x = sample, y= genome_size))
```
![](../img/ggplot_1.png)

> [!IMPORTANT]
> Common beginner mistake: If you have these aesthetics inside aes() e.g. `geom_point(aes(fill = variable))` then every unique value in the "variable" column will be assigned a unique fill colour. If you want ALL items to be the same colour, then you put the fill argument in geom_point instead e.g. `geom_point(fill = 'red')` now all items will be red. If you do `geom_point(fill = variable)`, you will probably get an error.

The problem is that the labels on the x-axis are quite hard to read. To change this, we need to add a theme layer. The ggplot2 `theme` system handles non-data plot information such as:

*   Axis labels
*   Plot background
*   Facet label background
*   Legend appearance

We have built-in themes to use, or we can adjust specific elements. 


For our figure, we will change the x-axis labels to be plotted on a 45-degree angle with a small horizontal shift to avoid overlap. 

We will also add some additional aesthetics by assigning them to other variables in our dataframe. 

_For example, the colour of the points will reflect the number of generations and the shape will reflect citrate mutant status._ The size of the points can be adjusted within the `geom_point` but does not need to be included in `aes()` since the value is not assigned to a variable.
```
    ggplot(metadata) +
      geom_point(aes(x = sample, y= genome_size, color = generation, shape = cit), size = rel(3.0)) +
      theme(axis.text.x = element_text(angle=45, hjust=1))
```
![](../img/ggplot_2.png)

Combining data wrangling and plotting: Dealing with NAs
=======================

Let's now try something a little more advanced (and a little more realistic). We will use a messier example dataset that is available in R.

Make sure you have the following packages loaded in your R session:
```
library('tidyr')
library('dplyr')
library('ggplot2')

```

Now let's bring up a dataset of sleep patterns across various animal species. 

```
data('msleep')
```

This dataset will be more like your data. There are multiple categorical character columns with grouping metadata, as well as several numerical data columns with measurement data and a lot of NAs/missing data that will affect the behaviour of ggplot.

Let's start simple by seeing whether diet affects sleep. Let's plot the `vore` column vs `sleep_cycle`.

```
ggplot(data = msleep)+
  geom_point(mapping = aes(x = vore, y = sleep_cycle))+
  theme_classic()
```

And we get a warning message:
```
Warning message:
Removed 51 rows containing missing values or values outside the scale range (`geom_point()`). 
```

These are likely NA for `sleep_cycle`. You can check whether this is the case by filtering for NAs in sleep_cycle:
```
msleep %>%
  filter(is.na(sleep_cycle))
```
What prints out in the console is `# A tibble: 51 × 11` - so there are 51 rows (i.e. animals in this dataset) that have missing `sleep_cycle` data.

Generally, missing y axis values will not be plotted by ggplot, while missing x axis values will.

You can also see that there is a point "NA" in the x axes. You can check what animal that is by running:

```
msleep %>%
  filter(is.na(vore) & !is.na(sleep_cycle))
```
Looks like researchers don't know the diet of the musk shrew or not. If you happen to know it, you can update the entry and have it included. Alternatively, remove it. We will remove missing entrie in this case. Let's do that by filtering again. 

I also felt like the range for herbivore and omnivore were too high to be informative. There's a lot of data points there so why don't we split it further?

I still want to retain info about the diet, but lets also split further on order. I also want to make the theme less ugly so lets copy what we did previously and add a common premade theme and rotate the axes.

```
msleep %>% 
  drop_na(order, vore) %>% 
    ggplot()+
    geom_point(mapping = aes(x = order, y = sleep_cycle, colour = vore))+
    theme_classic()+
    theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust = 1))

```

This is better, but the colours are all over the place! I want to more easily distinguish the diets of these animals better. This is where `facet_wrap` and `facet_grid` are your best friends and why R is better than Excel/Graphpad for plotting. Faceting lets your group by variables in a very powerful way. Let's try it:

```
msleep %>% 
  drop_na(order, vore) %>% 
  ggplot()+
    geom_point(mapping = aes(x = order, y = sleep_cycle, colour = vore))+
    facet_wrap(vars(vore), nrow = 1, scales = 'free_x')+
    theme_classic()+
    theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust = 1))

```

This is now looking even more informative but I have 2 gripes with it:
1. We should order it in ascending order of sleep
2. The number of variables per group is different across the different diets and this unevenness is ugly

Let's fix the first problem first. This is where factors come back in. First, we need to get the ascending order of the animal "order" we want to have the plot create. I don't actually use tidy syntax so let's show you how I asked the LLM:

Let's try it:
```
## get the order of values
sleepiness_order <- msleep %>%
  arrange(sleep_cycle) %>%
  pull(order) %>%
  unique()

## factor it based on the order
to_plot <- msleep %>%
  mutate(order_new = factor(order, levels = sleepiness_order)) %>% 
  drop_na(order, vore, sleep_cycle)

## plot
ggplot(data = to_plot)+
    geom_point(mapping = aes(x = order_new, y = sleep_cycle, colour = vore))+
    facet_grid(cols = vars(vore), scales = 'free_x', space = 'free_x')+
    theme_classic()+
    theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust = 1))

```

**Bonus: Add a boxplot!**

```
ggplot(data = to_plot)+
  geom_boxplot(mapping = aes(x = order_new, y = sleep_cycle, fill = vore), colour = 'black')+
  geom_point(mapping = aes(x = order_new, y = sleep_cycle), colour = 'black')+
  facet_grid(cols = vars(vore), scales = 'free_x', space = 'free_x')+
  theme_classic()+
  theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust = 1))
```

Combining data wrangling and plotting: Extra calculations
=======================

**What if you need to do extra calculations?**

Let's say you want to check how many animals you actually have `sleep_cycle` data for for each diet (i.e. no NAs!)

You need to calculate the "counts"! Let's see an example of how you'd ask the LLM:

Let's try it!

```
msleep %>%
  # keep only rows with non-missing sleep_cycle
  filter(!is.na(sleep_cycle), !is.na(vore)) %>%
  # count animals by diet type
  count(vore) %>%
  # plot
  ggplot(aes(x = vore, y = n)) +
  geom_col(fill = "steelblue") +
  labs(
    title = "Count of Animals by Diet Type",
    x = "Diet (vore)",
    y = "Number of Animals"
  ) +
  theme_minimal()

```

**Exercise: Can you compare the brain to body weight ratio of each animal between diets?**

This is the plot we are looking for:

Combining data wrangling and plotting: Reshaping data
=======================

The animal sleep dataset was actually in mostly the correct format for plotting. What if we have one that is completely wrong?

Lets investigate the world population by country longitudinal data set:

```
data('world_bank_pop', package = 'tidyr')
```

**Exercise: Can you plot the population over time for Australia?**


Writing figures to a file
=======================

In Rstudio, there are 3 ways in which figures and plots can be output to a file (rather than simply displaying on screen). The first (and easiest) is to export directly from the RStudio ‘Plots’ panel, by clicking on `Export` when the image is plotted. This will give you the option of `png` or `pdf` and selecting the directory to which you wish to save it to.

However, what if you forgot the dimensions you chose last time? And now your new plot with slightly different dimensions looks squashed in comparison. Very annoying. This is where the other 2 methods come in handy.

For the other 2 methods, I would recommend as best practise to assign the plots to an object. This is the common convention e.g.
```
p <- ggplot()
```

Option 2: `pdf()`, `png()`, etc functions with `dev.off`

These functions initialise a plot that will be written directly to a file in the `pdf` or `png` format, respectively. Within the function, you will need to specify a name for your image in quotes and the width and height. Specifying the width and height is optional, but can be very useful if you are using the figure in a paper or presentation and need it to have a particular resolution. Note that the default units for image dimensions are either pixels (for png) or inches (for pdf). To save a plot to a file, you need to:

1.  Initialise the plot using the function that corresponds to the type of file you want to make: `pdf("filename")`
2.  Write the code that makes the plot or if you assigned the plot to an object just use the object e.g.
3.  Close the connection to the new file (with your plot) using `dev.off()`.

```
    # this works!
    pdf("figure/boxplot.pdf")
    
    ggplot(example_data) +
      geom_boxplot(aes(x = cit, y =....) +
      ggtitle(...) +
      xlab(...) +
      ylab(...) +
      theme(panel.grid.major = element_line(...),
              axis.text.x = element_text(...),
              axis.title = element_text(...),
              axis.text = element_text(...)
    
    dev.off()

    # this also works!
    p <- ggplot(example_data) +
      geom_boxplot(aes(x = cit, y =....) +
      ggtitle(...) +
      xlab(...) +
      ylab(...) +
      theme(panel.grid.major = element_line(...),
              axis.text.x = element_text(...),
              axis.title = element_text(...),
              axis.text = element_text(...)

    pdf("figure/boxplot.pdf")

    p

    dev.off()


```

Option 3. `ggsave` family of functions - only works for ggplot objects but very powerful. This is the preferred method of plot saving for most people as it is easy to automate the saving of many plots.

```
p <- ggplot()

ggsave('figure/boxplot.pdf', p, height = 6, width = 4)

```


Integrating statistical tests into your plot
--------------------------------------
Utilise [ggpubr](https://rpkgs.datanovia.com/ggpubr/) to make it easier to interact with ggplot and integrate statistics. Different statistical tests are appropriate depending on the number of groups and the distribution of the data within the groups. 

This isn’t a statistics class, so we won’t cover all available methods. However, we'll walk through the logic for choosing a suitable test for this dataset.

### Step-by-step: Choosing the appropriate test:

1. Data type.
   i. genome_size is numeric.
   ii. cit is a categorical variable with three levels: "plus", "minus", and "unknown".

2. What are we comparing?
   i. We're interested in whether genome size differs between citrate-utilisation groups (cit status).
   ii. We will perform pairwise comparisons: "minus" vs "plus", "unknown" vs "plus" and "minus" vs "unknown".

3. Group sizes. These are relatively small sample sizes.

 `table(metadata$cit)`
  minus    plus unknown 
      9       9      12 

4. Data distribution and normality. While formal normality tests (e.g., shapiro.test() or ks.test()) can be used, small sample sizes and the presence of tied values (e.g., repeated 4.62, 4.63) already suggest that the data likely violate normality assumptions. The standard deviations are small, and visual inspections show limited spread within each group.

Choosing the test because:
- The data are numeric
- The group sizes are small
- There are tied values and limited variance
- And we are comparing group medians across pairs of groups.

We choose the Wilcoxon rank-sum test (a non-parametric alternative to the t-test) for pairwise comparisons.
For testing across all three groups simultaneously, we would use the Kruskal–Wallis test; however, that’s not necessary here, as we're interested in pairwise differences.

For visualisation, you'll need to install the ggubr package, load it into your library, and plot your boxplot. 

```
    # Install and load ggpubr
    install.packages("ggpubr")
    library(ggpubr)
    
    # Visualise with a boxplot
    p <- ggboxplot(metadata, x = "cit", y = "genome_size",
                   color = "cit",
                   palette = c("#4D00C7", "#DA3C07", "#05D3D3", "#C6C7C5"),
                   add = "jitter", shape = "cit") +
         xlab("Citrate Mutant") + ylab("Genome Size (Mb)")
    
    # Define pairwise comparisons
    my_comparisons <- list(
      c("unknown", "minus"),
      c("unknown", "plus"),
      c("minus", "plus")
    )
    
    # Add Wilcoxon test results with significance labels
    p + stat_compare_means(
          comparisons = my_comparisons,
          method = "wilcox.test",
          aes(label = after_stat(p.signif)),
          exact = FALSE  # avoid warning with ties
      )
```



> ![](../img/ggpubr.png)


You might get a warning:

```
Warning messages:
1: In wilcox.test.default(...):
  cannot compute exact p-value with ties
  ```

This occurs because the Wilcoxon rank-sum test (also called the Mann–Whitney U test) attempts to compute an exact p-value by default. However, this method assumes that all values are unique. When your data contains tied values—as is the case here with repeated measurements like 4.62 and 4.63—the exact method is no longer valid. In such cases, the test automatically switches to an approximate method (based on a normal approximation) and raises this warning.





Resources:
----------

We have only scratched the surface here. To learn more, see the [ggplot2 reference site](http://docs.ggplot2.org/), and Winston Chang’s excellent [Cookbook for R](http://wiki.stdout.org/rcookbook/Graphs/) site. 

Though slightly out of date, [ggplot2: Elegant Graphics for Data Analysis](http://www.amazon.com/ggplot2-Elegant-Graphics-Data-Analysis/dp/0387981403) is still the definitive book on this subject. Much of the material here wasadaptedd from [Introduction to R graphics with ggplot2 Tutorial at IQSS](http://tutorials.iq.harvard.edu/R/Rgraphics/Rgraphics.html).

To investigate more into colour palettes [viridis](https://cran.r-project.org/web/packages/viridis/vignettes/intro-to-viridis.html)



***

Material adapted from (https://datacarpentry.org/R-genomics/01-intro-to-R.html) and (https://datacarpentry.org/semester-biology/materials/r-intro/)

[Data Carpentry](http://datacarpentry.org/), 2017-2018. [License](LICENSE.html). [Contributing](CONTRIBUTING.html).  
