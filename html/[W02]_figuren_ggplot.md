# Figuren via ggplot

The most common way to produce figures in R nowadays is using the package ggplot2.
It is part of the Tidyverse packages.
It is extremely versatile.

In this course we spend one lesson on ggplot to learn the basics.
We use the [online manual](https://ggplot2-book.org/index.html){target="_blank"}.

::: {.exercise #ggplotmanual}
Manual

* Read the **first steps** in the online manual

:::


## First steps

GGplot is part of tidyverse, which includes all kind of tools to work with data, with some of them quite usefully when making figures.
Therefore it is, in general, useful to make this tools available in your script.


```excercise
Tidyverse

* Add to your script a line of code that will activate tidyverse and run the code. NB: it is good custom to place all the library-function in the first rows of your script.
```


## Code for figures

On first sight, codes for ggplot-figures look unnecessarily complicated, but it is in fact quite structured.

The code works in a few steps:

* First step is to define the figure, by giving the variables for the x and y-axis.

`ggplot(data = df, aes(x = var1, y = var2)) +`

NB: replace the names var1, var2 and df for the names you use for your data frame and column names. 

The `+` is placed at the end of the row to indicate that the output of this row will be used in the next line of code.
In this way, you can build-up the code for the figure in a clear and structured way.


* The second step is defining the type of figure, e.g.:

`geom_histogram()`

NB: for some figures you only need an x-variable, like for histogram or boxplot.


::: {.exercise #histogram}
Histogram

* Add code in your script to make a histogram of body temperature

:::



## Types of figures

With ggplot there are many different types of figures possible. 
Have a look in the manual at the [geoms chapter](https://ggplot2-book.org/individual-geoms.html){target="blank"}.

::: {.exercise #geoms}
geom-functions

* Find out which functions you need for a boxplot and for a scatterplot

:::



## aes

The functions `ggplot()`has **aes** as **argument** (input for the function).
Aes stand for aesthetics.
No idea why this word is chosen, since the argument is about which data is presented on which axis.
You probably noticed that `aes` is followed by `()`, which indicates that aes is a function on its own.

In `aes()` you can add additional arguments, e.g. to color or fill different groups (using `fill=vargroup` or `color=vargroup`, vargroup the name of column that defines the group).


::: {.exercise #unnamed-chunk-1}
aes

* adjust your script in a way that the different genders (column gen) have a different fill.

:::

## position

As you probably noticed, is the result of a histogram with two groups that both histograms are stacked.
But it would give more insight if they are placed on the same level.

You can change that with the argument `position` in the geom-function: `position =  "identity"`.

The **value** identity means that both groups are positioned independently of each other on the y-axis.
For other possibilities see [manual](https://ggplot2-book.org/layers.html?q=position#position){targeg="blank"}.


::: {.exercise #position}
position

* Adjust the code, so that both histograms are not stacked anymore.
  
:::


## transparancy

Now both histograms are at the same height can we compare the numbers more easily.
Drawback is that in some places one group is hidden behind the other and you don't see how much lower the other group is.
The solution is to make the bars slightly transparant.
You can do that with the argument (also in the geom-function)  `alpha = 0.4`. 
The value 0.4 is oftern a good mix between brightness and transparency.


::: {.exercise #alpha}
alpha

* Add the argument alpha to your code and adjust the value to your preference.
  
:::


## Densityplots

A nice alternative for an histogram is a densityplot, via the function `geom_density`.

::: {.exercise #density}
density

* Copy the code for the histogram in your script.
* Replace the word histogram with density and run the code.

:::

Quite simple, isn't it?


## Scatter plots

A common graph is the scatter plot.
Both x- and y-axis contain numerical (and often continuous) data.

In the `aes()` you need column names for both x and y.
With `geom_point` you display the data as points in the graph.
As in the histogram, you can give groups different colours, in the case of points, choose the argument `color` instead of `fill` in `aes()`.


::: {.exercise #scatterplot}
Scatterplot

* Add in your script code for a scatterplot of tc on the x-axis verus hr (heart rate)
* Give the different genders a different colour.

:::


## Bar chart

A common way to present data like in the body temperature data set, is to use bar charts.

Bar charts give the average (and ofter spread in the form of error bars) of the data, which is nearly always divided in different groups (one bar for each group).

The most convinient ggplot-code for this kind of plots is `stat-summary()`.
In the function you can define the type of graph. 
For a bar chart, use `geom = "bar"`.
It automatically calculates the mean and the standard error for each group.


::: {.exercise #staafdiagram}
Bar charrt

* make in the same script new code to show the means of body temperature of men and women in separate bars. Tip: use `gen` as variable on the x-axis
* run the code

:::

Often you want to include error bars in your bar chart.
With the ggplot it is quite easy: just add another layer with `stat_summary()`, but now with `geom = "errorbar"`.
For aesthetic reasons make the error bar smaller than the bar by adding the argument `width = 0.5`.


::: {.exercise #foutenbalk}
Error bar

* Add an error bar to your plot

:::



## meerdere groepen bij staafdiagrammen

Also by bar charts you can show different groups.
In this case use the argument `fill` as you want to fill up the bars.

The default setting for position in `stat_summary()` is "identity:, but with bar charts it is normal to have the groups besides each other, that is called dodge. 
You can control how close the groups are by: `position = position_dodge(0.9)`.
A value of 0.9 is often optimal.

NB: `position_dodge()` also works with other types of graphs like box plots and violin plots.


## Trend line

When you have a scatter plot, you are often interested in the relation between the variables on the x- and y-axis.

You probably know the function for a trend line in Excel, a similar function is available in ggplot.
Just ad the line `geom_smooth(method = "lm")`.
The argument `lm` means that a linear fit is made.


::: {.exercise #geomsmooth}
trend line

* Go back to the script for the scatter plot
* Add a trend line


:::


If done right, you see now a straight line through the point cloud.
Around the line is a grey area: the 95% condifence of the line (with 95% confidence the real linear relation is with that area).
If you want to remove that area, just add the argument `se = FALSE`.


## Many more possibilities

There is a variety of possibilies to adjust your figures in ggplot. 
In addition other people have developed packages that make other figures (including GIS-maps) with ggplot-functionality.

Because a clear figure is an excellent way to communicate research results, it is clever to invest some time learning more about ggplot.
