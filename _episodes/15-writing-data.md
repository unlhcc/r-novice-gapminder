---
title: Writing Data
teaching: 10
exercises: 15
questions:
- "How can I save plots and data created in R?"
objectives:
- "To be able to write out plots and data from R."
keypoints:
- "Save plots from RStudio using the 'Export' button."
- "Use `write.table` to save tabular data."
---




## Saving plots

So making publication quality plots is great but does us little good if we cannot get them out of
R and into our documents.

Let's start with a simple plot:

~~~
ggplot(data=gapminder, aes(x=year, y=lifeExp, color=country)) +
  geom_line() +
  theme(legend.position = "none")
~~~
{: .r}

There are a few different ways to save our plot. From within RStudio, we can save it interactively by using the 'Export' button
in the 'Plot' window. This will give you the option of saving as a .pdf or as .png, .jpg or other image formats.

<img src="../fig/12-data-fig1.png" title="export plots in rstudio" alt="export plots in rstudio" style="display: block; margin: auto;" />

But what if you are working in a command line environment, or want to create multiple plots without user interaction?
In `ggplot2` you can use the `ggsave` function to save your plots quickly. This function can be used to save the last displayed plot in a format specified by the file name.
You can save as several different formats, such as a PDF:

~~~
ggsave("My_most_recent_plot.pdf")
~~~
{: .r}

Or as a JPG:

~~~
ggsave("My_most_recent_plot.jpg")
~~~
{: .r}

`ggsave` also allows you to specify size and quality of the image. You can check out all of the 
options using the `?ggsave` command to view the help file.

Sometimes you will want to save plots without creating them in the
'Plot' window first. Perhaps you want to make a pdf document with
multiple pages: each one a different plot, for example. Or perhaps
you're looping through multiple subsets of a file, plotting data from
each subset, and you want to save each plot, but obviously can't stop
the loop to click 'Export' for each one.

In this case you can use a more flexible approach. The function
`pdf` creates a new pdf device. You can control the size and resolution
using the arguments to this function.


~~~
pdf("Life_Exp_vs_time.pdf", width=12, height=4)
ggplot(data=gapminder, aes(x=year, y=lifeExp, color=continent)) +
  geom_point()

dev.off()
~~~
{: .r}

The `pdf` command opens the pdf file, and any output between this command and the `dev.off` command 
will be added to that file. Forgetting to "close" your pdf device by using the `dev.off` command can
lead to an incorrect file, so be sure to include it immediately after your output.

Open up this document and have a look.

> ## Challenge 1
>
> Rewrite your 'pdf' command to print a second
> page in the pdf, showing a facet plot
> of the same data with one panel per continent.
>
> Hint: Remember that we used the `facet_wrap` command previously to create a facet plot.
>
> > ## Solution to Challenge 1
> > 
> > You can output a second plot, by adding a second `ggplot` command with the `facet_wrap` command before
> > `dev.off` command.
> >
> > ~~~
> > pdf("Life_Exp_vs_time2.pdf", width=12, height=4)
> > ggplot(data=gapminder, aes(x=year, y=lifeExp)) +
> >   geom_point()
> > ggplot(data=gapminder, aes(x=year, y=lifeExp)) +
> >   geom_point() + facet_wrap(~ continent)
> > 
> > # don't forget to close your pdf device!
> > dev.off()
> > ~~~
> > {: .r}
> {: .solution}
{: .challenge}

The commands `jpeg`, `png` etc. are used similarly to produce
documents in different formats.

## Writing data

At some point, you'll also want to write out data from R.

We can use the `write.table` function for this, which is
very similar to the `read.table` function that we mentioned previously in Lesson 2. 
For more information on reading in your own data in R and the `read.table` function, check out the
supplemental lesson [Reading and Writing CSV Files](https://carriebrown.github.io/r-novice-gapminder-2/11-supp-read-write-csv/).

Let's create a data-cleaning script, for this analysis, we
only want to focus on the gapminder data for Australia:


~~~
aust_subset <- gapminder[gapminder$country == "Australia",]

write.table(aust_subset,
  file="gapminder-aus.csv",
  sep=","
)
~~~
{: .r}

Remember in our last lesson when we discussed line breaks and the different approaches for neat and tidy code.
Here, the line breaks have been placed between the different parameters of our command to make the code easier to read.
One benefit to this approach is that we can then comment each line to remind ourselves what they do. Here's our previous
example with these **in line comments**

~~~
aust_subset <- gapminder[gapminder$country == "Australia",]

write.table(aust_subset,     # Gapminder data for countries located in Australia
  file="gapminder-aus.csv",  # Name of the output file
  sep=","                    # Comma separated
)
~~~
{: .r}

This approach becomes really beneficial when you start writing commands which use a lot of parameters.

We can examine our file right from within RStudio. In the lower-left pane under the `Files` tab, find the `gapminder-aus.csv` file. Click on it and select `View File`.

~~~
"country","year","pop","continent","lifeExp","gdpPercap"
"61","Australia",1952,8691212,"Oceania",69.12,10039.59564
"62","Australia",1957,9712569,"Oceania",70.33,10949.64959
"63","Australia",1962,10794968,"Oceania",70.93,12217.22686
"64","Australia",1967,11872264,"Oceania",71.1,14526.12465
"65","Australia",1972,13177000,"Oceania",71.93,16788.62948
"66","Australia",1977,14074100,"Oceania",73.49,18334.19751
"67","Australia",1982,15184200,"Oceania",74.74,19477.00928
"68","Australia",1987,16257249,"Oceania",76.32,21888.88903
"69","Australia",1992,17481977,"Oceania",77.56,23424.76683
~~~
{: .output}

Hmm, that's not quite what we wanted. Where did all these
quotation marks come from? Also the row numbers are
meaningless.

Let's look at the help file to work out how to change this
behaviour.


~~~
?write.table
~~~
{: .r}

By default R will wrap character vectors with quotation marks
when writing out to file. It will also write out the row and
column names.

Let's fix this:


~~~
write.table(aust_subset,     # Gapminder data for countries located in Australia
  file="gapminder-aus.csv",  # Name of the output file
  sep=",",                   # Comma separated
  quote=FALSE,               # Turn off quotation marks
  row.names=FALSE            # No row names
)
~~~
{: .r}

Now lets look at the file again:

~~~
country,year,pop,continent,lifeExp,gdpPercap
Australia,1952,8691212,Oceania,69.12,10039.59564
Australia,1957,9712569,Oceania,70.33,10949.64959
Australia,1962,10794968,Oceania,70.93,12217.22686
Australia,1967,11872264,Oceania,71.1,14526.12465
Australia,1972,13177000,Oceania,71.93,16788.62948
Australia,1977,14074100,Oceania,73.49,18334.19751
Australia,1982,15184200,Oceania,74.74,19477.00928
Australia,1987,16257249,Oceania,76.32,21888.88903
Australia,1992,17481977,Oceania,77.56,23424.76683
~~~
{: .output}

That looks better!

> ## Challenge 2
>
> Write a data-cleaning script file that subsets the gapminder
> data to include only data points collected since 1990.
>
> Use this script to write out the new subset to a file
> your working directory.
>
> Remember to use a different file name so that the new output doesn't overwrite your old output
>
> > ## Solution to Challenge 2
> > 
> > ~~~
> > new_data <- gapminder[gapminder$year >= 1990,]
> > write.table(new_data,
> >   file="gapminder-1990.csv",
> >   sep=",",
> >   quote=FALSE,
> >   row.names=FALSE
> > )
> > ~~~
> > {: .r}
>{: .solution}
{: .challenge}
