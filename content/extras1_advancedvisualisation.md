---
layout: page
title: Extras 1 - Advanced ggplot2
---

Data Visualisation using ggplot
===================================
> Learning Objectives
> -------------------
> 
> *   Understand that data may need to be formatted correctly for plotting.
> *   Perform basic filtering and treatment for missing data
> *   Be able to reshape data into the correct format (long vs wide) for making your desired plot.
> *   Be able to perform basic calculations on your data for making plots.


Combining data wrangling and plotting
=======================

### Dealing with NAs
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
<img width="1056" height="372" alt="image" src="https://github.com/user-attachments/assets/90f04f0f-0099-4fbf-be6a-7d13943fcfe0" />

This dataset will be more like your data. There are multiple categorical character columns with grouping metadata, as well as several numerical data columns with measurement data and a lot of NAs/missing data that will affect the behaviour of ggplot.

Let's start simple by seeing whether diet affects sleep. Let's plot the `vore` column vs `sleep_cycle`.

```
ggplot(data = msleep)+
  geom_point(mapping = aes(x = vore, y = sleep_cycle))+
  theme_classic()
```
<img width="600" alt="image" src="https://github.com/user-attachments/assets/ec1871e6-8eee-4840-a827-5254dd594a9a" />

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
<img width="600" alt="image" src="https://github.com/user-attachments/assets/8dd1853b-2354-47b1-a22c-4120ad61f664" />

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
<img width="600" alt="image" src="https://github.com/user-attachments/assets/01ccaabf-86b0-4473-9363-6e2c9e01c305" />

This is now looking even more informative but I have 2 gripes with it:
1. We should order it in ascending order of sleep
2. The number of variables per group is different across the different diets and this unevenness is ugly

In order to fix the first problem, this is where factors come back in. It turns out we'll need an extra package `forcats`. This is loaded automatically if you load `tidyverse` at the very beginning instead of `tidyr`, `dplyr` and `ggplot2` separately. However, this will also load a lot of packages you may not necessarily need, so I usually prefer to just load required packages.

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
<img width="600" alt="image" src="https://github.com/user-attachments/assets/2ba31420-7c3c-4bb8-bc7b-4e22f3f1f7b6" />

**Bonus: Add a boxplot!**

You can combine plots on top of each other if you'd like. The `geom`s get stacked on top of each other in the order you write them.
```
msleep %>%
  drop_na(order, vore, sleep_cycle) %>%
  mutate(order_new = fct_reorder(order, sleep_cycle)) %>%
    ggplot(data = to_plot)+
        geom_boxplot(mapping = aes(x = order_new, y = sleep_cycle, fill = vore), colour = 'black')+
        geom_point(mapping = aes(x = order_new, y = sleep_cycle), colour = 'black')+
        facet_grid(cols = vars(vore), scales = 'free_x', space = 'free_x')+
        theme_classic()+
        theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust = 1))
```
<img width="600" alt="image" src="https://github.com/user-attachments/assets/15e3348b-676f-4ef6-b4bc-4c06a95f7af9" />

### Extra calculations

Let's say you want to check how many animals you actually have `sleep_cycle` data for for each diet (i.e. no NAs!)

You need to calculate the "counts". Let's see an example of how you'd ask the LLM:

<img width="832" height="1014" alt="image" src="https://github.com/user-attachments/assets/38cf30a0-6de2-436d-b057-d6e66269f77f" />

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
<img width="600" alt="image" src="https://github.com/user-attachments/assets/78166a2b-df9e-4a91-ab32-8de2ffc9b9a1" />

**Exercise: Can you compare the brain to body weight ratio of each animal between diets?**

This is the plot we are looking for:

<img width="600" height="918" alt="image" src="https://github.com/user-attachments/assets/b2645374-08e1-4590-b660-b9fc187d15f7" />

For anything where you have a different number of samples plotted, I HIGHLY recommend using `geom_text` to plot the number of samples, such as in this boxplot. However, this is a bit more advanced. Hint: You need to use a different `data` argument for the `geom_text`... A counts data perhaps...?

### Reshaping data

The animal sleep dataset was actually in mostly the correct format for plotting. What if we have one that is completely wrong?

Lets investigate the world population by country longitudinal data set:

```
data('world_bank_pop', package = 'tidyr')
```

<img alt="image" src="https://github.com/user-attachments/assets/3c026d73-cd08-4d22-a357-bfbb01588c0b" />

Super long format:

<img width="300" alt="image" src="https://github.com/user-attachments/assets/dd07b1fb-5dca-4713-be0e-9492d9e0557b" />

Most sensible format for plotting longitudinal data:

<img width="600" alt="image" src="https://github.com/user-attachments/assets/24c11195-98e6-4a9d-9f3c-1a1ee269e452" />

**Exercise: Can you plot the population over time for Australia?**

<img width="600" alt="image" src="https://github.com/user-attachments/assets/01ac787e-80d0-43ac-8f6a-af94d50b7fd9" />

Hint: You can try to be very specific with LLMs:

<img width="797" height="1023" alt="image" src="https://github.com/user-attachments/assets/00d375ba-cada-4790-ae0f-34e6efda45ba" />


---
### Further reading

We have really only touched the surface of basic calculations and data wrangling. 

Imagine if for the world population data, someone could give you a separate table which is basically just an index of country codes, country names and continent they're on. Example:

| country	| country_name | region |
| ------- | ------------ | ------ |
| AUS	| Australia	| Oceania |
| CAN |	Canada |	North America |
| GER	| Germany	| Europe |

If you then `merge` or `join` this table (matching the country code) to the `world_bank_pop` table, you could further group the data and start analysing regional variations in population trends for example.

Similarly, if some countries had missing data and you got an extra table with more countries in them, you could join that table based on the column names as well. 

Then there are also so many other types of data that could be represented as tables which we haven't even touched on - heatmaps are super common to plot and there are dedicated heatmap plotting packages (e.g. [ComplexHeatmap](https://jokergoo.github.io/ComplexHeatmap-reference/book/) or you could go and use `geom_tile` in ggplot2. 

This site is also a good resource that lists both common data transformations and also how you could do those transformations using different syntaxes.

https://wetlandscapes.com/blog/a-comparison-of-r-dialects/

If you plan to do a lot of Xenium/Phenocycler analysis, then the `data.table` syntax would be strongly recommended over the `tidy/dplyr` for data wrangling and calculations. You can read the vignettes [here](https://cran.r-project.org/web/packages/data.table/vignettes/datatable-intro.html).

If your goal is to learn single cell RNAseq analysis, it would be prudent to stick to tidy because the syntax is more similar.

If your goal is to just replace Excel/Graphpad prism, use whatever makes you the most comfortable!

