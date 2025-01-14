# Plotting with [Vega-Altair](https://altair-viz.github.io/)

:::{objectives}
- Be able to create simple plots with Vega-Altair and tweak them
- Know how to look for help
- Reading data with Pandas from disk or a web resource
- Know how to tweak example plots from a gallery for your own purpose
- We will build up
  [this notebook](https://nbviewer.org/github/coderefinery/python-progression/blob/main/notebooks/plotting.ipynb)
  (spoiler alert!)
:::


## Repeatability/reproducibility

From [Claus O. Wilke: "Fundamentals of Data Visualization"](https://clauswilke.com/dataviz/):

> *One thing I have learned over the years is that automation is your friend. I
> think figures should be autogenerated as part of the data analysis pipeline
> (which should also be automated), and they should come out of the pipeline
> ready to be sent to the printer, no manual post-processing needed.*

- **Try to minimize manual post-processing**. This could bite you when you need to regenerate 50
  figures one day before submission deadline or regenerate a set of figures
  after the person who created them left the group.
- There is not the one perfect language and **not the one perfect library** for everything.
- Within Python, many libraries exist:
  - [Vega-Altair](https://altair-viz.github.io/gallery/index.html):
    declarative visualization, statistics built in
  - [Matplotlib](https://matplotlib.org/stable/gallery/index.html):
    probably the most standard and most widely used
  - [Seaborn](https://seaborn.pydata.org/examples/index.html):
    high-level interface to Matplotlib, statistical functions built in
  - [Plotly](https://plotly.com/python/):
    interactive graphs
  - [Bokeh](https://demo.bokeh.org/):
    also here good for interactivity
  - [plotnine](https://plotnine.readthedocs.io/):
    implementation of a grammar of graphics in Python, it is based on [ggplot2](https://ggplot2.tidyverse.org/)
  - [ggplot](https://yhat.github.io/ggpy/):
    R users will be more at home
  - [PyNGL](https://www.pyngl.ucar.edu/Examples/gallery.shtml):
    used in the weather forecast community
  - [K3D](https://k3d-jupyter.org/gallery/index.html):
    Jupyter Notebook extension for 3D visualization
  - [Mayavi](https://docs.enthought.com/mayavi/mayavi/):
    3D scientific data visualization and plotting in Python
  - ...
- Two main families of libraries: procedural (e.g. Matplotlib) and declarative (e.g. Vega-Altair).


## Why are we starting with [Vega-Altair](https://altair-viz.github.io/)?

- Concise and powerful
- "Simple, friendly and consistent API" allows us to focus on the data
  visualization part and get started without too much Python knowledge
- The way it **combines visual channels with data columns** can feel intuitive
- Interfaces very nicely with [Pandas](https://pandas.pydata.org/)
- Easy to change figures
- Good documentation
- Open source
- Makes it easy to save figures in a number of formats (svg, png, html)
- Easy to save interactive visualizations to be used in websites


## Example data: Weather data from two Norwegian cities

We will experiment with some example weather data obtained from [Norsk
KlimaServiceSenter](https://seklima.met.no/observations/), Meteorologisk
institutt (MET) (CC BY 4.0).  The data is in CSV format (comma-separated
values) and contains daily and monthly weather data for two cities in Norway:
Oslo and Tromsø. You can browse the data
[here in the lesson repository](https://github.com/coderefinery/python-progression/tree/main/data).

We will use the Pandas library to read the data into a dataframe.

Pandas can read from and write to a large set of formats
([overview of input/output functions and formats](https://pandas.pydata.org/pandas-docs/stable/reference/io.html)).
We will load a CSV file directly from the web. Instead of using a web URL we
could use a local file name instead.

Pandas dataframes are a great data structure for **tabular data** and tabular
data turns out to be a great input format for data visualization libraries.
Vega-Altair understands Pandas dataframes and can plot them directly.


## Reading data into a dataframe

We can try this together in a notebook:
Using Pandas we can **merge, join, concatenate, and compare**
dataframes, see <https://pandas.pydata.org/pandas-docs/stable/user_guide/merging.html>.

Let us try to **concatenate** two dataframes: one for Tromsø weather data (we
will now load monthly values) and one for Oslo:
```{code-block} python
---
emphasize-lines: 8
---
import pandas as pd

url_prefix = "https://raw.githubusercontent.com/coderefinery/python-progression/main/data/"

data_tromso = pd.read_csv(url_prefix + "tromso-monthly.csv")
data_oslo = pd.read_csv(url_prefix + "oslo-monthly.csv")

data_monthly = pd.concat([data_tromso, data_oslo], axis=0)

# let us print the combined result
data_monthly
```

Before plotting the data, there is a problem which we may not see yet: Dates
are not in a standard date format (YYYY-MM-DD). We can fix this:
```python
# replace mm.yyyy to date format
data_monthly["date"] = pd.to_datetime(list(data_monthly["date"]), format="%m.%Y")
```

With Pandas it is possible to do a lot more (adjusting missing values, fixing
inconsistencies, changing format).


## What is in a dataframe?

The name pandas is derived from the term "**pan**el **da**ta".

```{figure} img/pandas/dataframe.svg
:alt: A pandas dataframe object is composed of rows and columns.

A pandas dataframe object is composed of rows and columns.
```

Let us explore these together in the notebook (run these in separate cells):
```python
# print an overview of the dataset
data_monthly

# print the first 5 rows
data_monthly.head()

# print the last 5 rows
data_monthly.tail()

# print all column titles - no parentheses here
data_monthly.columns

# show which data types were detected
data_monthly.dtypes

# print table dimensions - no parentheses here
data_monthly.shape

# print one column
data_monthly["max temperature"]

# get some statistics
data_monthly["max temperature"].describe()

# what was the maximum temperature?
data_monthly["max temperature"].max()

# print all rows where max temperature was above 20
data_monthly[data_monthly["max temperature"] > 20.0]
```


## Where to learn more about pandas

Pandas is extremely powerful and there is a lot that can be done and there are
great resources to explore more:
- [Getting started guide](https://pandas.pydata.org/getting_started.html)
  (including tutorials and a 10 minute flash intro)
- [10 minutes to pandas tutorial](https://pandas.pydata.org/docs/user_guide/10min.html)
- [Pandas documentation](https://pandas.pydata.org/docs/)
- [Cheatsheet](https://pandas.pydata.org/Pandas_Cheat_Sheet.pdf)
- [Cookbook](https://pandas.pydata.org/docs/user_guide/cookbook.html#cookbook)
- [Data Carpentry lesson](https://datacarpentry.org/python-ecology-lesson/) "Data Analysis and Visualization in Python for Ecologists"
  (useful not only for ecologists)


## Plotting the data

Now let's plot the data. We will start with a plot that is not optimal and
then we will explore and improve a bit as we go:

```python
import altair as alt

alt.Chart(data_monthly).mark_bar().encode(
    x="date",
    y="precipitation",
    color="name",
)
```

:::{figure} img/plotting-vega-altair/precipitation-on-top.svg
:alt: Monthly precipitation for the cities Oslo and Tromsø over the course of a year.

Monthly precipitation for the cities Oslo and Tromsø over the course of a year.
:::

:::{discussion} Let us pause and explain the code
- `alt` is a short-hand for `altair` which we imported on top of the notebook
- `Chart()` is a function defined inside `altair` which takes the data as argument
- `mark_bar()` is a function that produces bar charts
- `encode()` is a function which encodes data columns to **visual channels**

Observe how we connect (encode) **visual channels** to data columns:
- x-coordinate with "date"
- y-coordinate with "precipitation"
- color with "name" (name of weather station; city)
:::

We can improve the plot by giving Vega-Altair a bit more information that the
x-axis is **temporal** (T) and that we would like to see the year and
month (yearmonth):
```{code-block} python
---
emphasize-lines: 2
---
alt.Chart(data_monthly).mark_bar().encode(
    x="yearmonth(date):T",
    y="precipitation",
    color="name",
)
```

Apart from T (temporal), there are other
[encoding data types](https://altair-viz.github.io/user_guide/encodings/index.html#encoding-data-types):
- Q (quantitative)
- O (ordinal)
- N (nominal)
- T (temporal)
- G (geojson)

:::{figure} img/plotting-vega-altair/precipitation-on-top-yearmonth.svg
:alt: Monthly precipitation for the cities Oslo and Tromsø over the course of a year.

Monthly precipitation for the cities Oslo and Tromsø over the course of a year.
:::

Let us improve the plot with another one-line change:
```{code-block} python
---
emphasize-lines: 5
---
alt.Chart(data_monthly).mark_bar().encode(
    x="yearmonth(date):T",
    y="precipitation",
    color="name",
    column="name",
)
```

:::{figure} img/plotting-vega-altair/precipitation-side.svg
:alt: Monthly precipitation for the cities Oslo and Tromsø over the course of a year with with both cities plotted side by side.

Monthly precipitation for the cities Oslo and Tromsø over the course of a year with with both cities plotted side by side.
:::

With another one-line change we can make the bar chart stacked:
```{code-block} python
---
emphasize-lines: 5
---
alt.Chart(data_monthly).mark_bar().encode(
    x="yearmonth(date):T",
    y="precipitation",
    color="name",
    xOffset="name",
)
```
:::{figure} img/plotting-vega-altair/precipitation-stacked-x.svg
:alt: Monthly precipitation for the cities Oslo and Tromsø over the course of a year with with both cities plotted side by side.

Monthly precipitation for the cities Oslo and Tromsø over the course of a year
plotted as stacked bar chart.
:::

This is not publication-quality yet but a really good start!

Let us try one more example where we can nicely see how Vega-Altair
is able to map visual channels to data columns:
```python
alt.Chart(data_monthly).mark_area(opacity=0.5).encode(
    x="yearmonth(date):T",
    y="max temperature",
    y2="min temperature",
    color="name",
)
```

:::{figure} img/plotting-vega-altair/temperature-ranges-combined.svg
:alt: Monthly temperature ranges for two cities in Norway.

Monthly temperature ranges for two cities in Norway.
:::

:::{discussion} What other marks and other visual channels exist?
- [Overview of available marks](https://altair-viz.github.io/user_guide/marks/index.html)
- [Overview of available visual channels](https://altair-viz.github.io/user_guide/encodings/channels.html)
- [Gallery of examples](https://altair-viz.github.io/gallery/index.html)
:::


## Exercise: Using visual channels to re-arrange plots

::::{exercise} Exercise Plotting-1: Using visual channels to re-arrange plots
1. Try to reproduce the above plots if they are not already in your notebook.

1. Above we have plotted the monthly precipitation for two cities side by side
   using a stacked plot. Try to arrive at the following plot where months are
   along the y-axis and the precipitation amount is along the x-axis:
   :::{figure} img/plotting-vega-altair/precipitation-stacked-y.svg
   :::

1. Ask the Internet or AI how to change the axis title from "precipitation"
   to "Precipitation (mm)".

1. Modify the temperature range plot to show the temperature ranges for the
   two cities side by side like this:
   :::{figure} img/plotting-vega-altair/temperature-ranges-side.svg
   :::

:::{solution}
1. Copy-paste code blocks from above.

1. Basically we switched x and y:
   ```{code-block} python
   ---
   emphasize-lines: 2,3,5
   ---
   alt.Chart(data_monthly).mark_bar().encode(
       y="yearmonth(date):T",
       x="precipitation",
       color="name",
       yOffset="name",
   )
   ```

1. This can be done with the following modification:
   ```{code-block} python
   ---
   emphasize-lines: 3
   ---
   alt.Chart(data_monthly).mark_bar().encode(
       y="yearmonth(date):T",
       x=alt.X("precipitation").title("Precipitation (mm)"),
       color="name",
       yOffset="name",
   )
   ```

1. We added one line:
   ```{code-block} python
   ---
   emphasize-lines: 6
   ---
   alt.Chart(data_monthly).mark_area(opacity=0.5).encode(
       x="yearmonth(date):T",
       y="max temperature",
       y2="min temperature",
       color="name",
       column="name",
   )
   ```
:::
::::


## More fun with visual channels

Now we will try to **plot the daily data and look at snow depths**. We first
read and concatenate two datasets:
```python
url_prefix = "https://raw.githubusercontent.com/coderefinery/python-progression/main/data/"

data_tromso = pd.read_csv(url_prefix + "tromso-daily.csv")
data_oslo = pd.read_csv(url_prefix + "oslo-daily.csv")

data_daily = pd.concat([data_tromso, data_oslo], axis=0)
```

We adjust the data a bit:
```python
# replace dd.mm.yyyy to date format
data_daily["date"] = pd.to_datetime(list(data_daily["date"]), format="%d.%m.%Y")

# we are here only interested in the range december to may
data_daily = data_daily[
    (data_daily["date"] > "2022-12-01") & (data_daily["date"] < "2023-05-01")
]
```

Now we can plot the snow depths for the months December to May for the two
cities:
```python
alt.Chart(data_daily).mark_bar().encode(
    x="date",
    y="snow depth",
    column="name",
)
```

:::{figure} img/plotting-vega-altair/snow-depth.svg
:alt: Snow depth (in cm) for the months December 2022 to May 2023 for two cities in Norway.

Snow depth (in cm) for the months December 2022 to May 2023 for two cities in Norway.
:::

What happens if we try to color the plot by the "max temperature" values?
```{code-block} python
---
emphasize-lines: 4
---
alt.Chart(data_daily).mark_bar().encode(
    x="date",
    y="snow depth",
    color="max temperature",
    column="name",
)
```

The result looks neat:
:::{figure} img/plotting-vega-altair/snow-depth-color.svg
:alt: Snow depth (in cm) for the months December 2022 to May 2023 for two cities in Norway. Colored by daily max temperature.

Snow depth (in cm) for the months December 2022 to May 2023 for two cities in Norway. Colored by daily max temperature.
:::

We can change the color scheme ([available color schemes](https://vega.github.io/vega/docs/schemes/)):
```{code-block} python
---
emphasize-lines: 4
---
alt.Chart(data_daily).mark_bar().encode(
    x="date",
    y="snow depth",
    color=alt.Color("max temperature").scale(scheme="plasma"),
    column="name",
)
```

With the following result:
:::{figure} img/plotting-vega-altair/snow-depth-plasma.svg
:alt: Snow depth (in cm) for the months December 2022 to May 2023 for two cities in Norway. Colored by daily max temperature. Warmer days are often followed by reduced snow depth.

Snow depth (in cm) for the months December 2022 to May 2023 for two cities in Norway. Colored by daily max temperature. Warmer days are often followed by reduced snow depth.
:::

Let's try one more change to show that we can experiment with different
plot types by changing `mark_bar()` to something else, in this case `mark_circle()`:
```{code-block} python
---
emphasize-lines: 1
---
alt.Chart(data_daily).mark_circle().encode(
    x="date",
    y="snow depth",
    color=alt.Color("max temperature").scale(scheme="plasma"),
    column="name",
)
```

:::{figure} img/plotting-vega-altair/snow-depth-circles.svg
:alt: Snow depth (in cm) for the months December 2022 to May 2023 for two cities in Norway. Colored by daily max temperature. Warmer days are often followed by reduced snow depth.

Snow depth (in cm) for the months December 2022 to May 2023 for two cities in Norway. Colored by daily max temperature. Warmer days are often followed by reduced snow depth.
:::


## Themes

In [Vega-Altair](https://altair-viz.github.io/) you can change the theme and
select from a long [list of themes](https://github.com/vega/vega-themes).  On
top of your notebook try to add:
```python
alt.themes.enable('dark')
```
Then re-run all cells. Later you can try some other themes such as:
- `fivethirtyeight`
- `latimes`
- `urbaninstitute`

You can even define your own themes!


## Exercise: Anscombe's quartet

Save the following data as `example.csv` (you can do this directly from
JupyterLab; this data is the [Anscombe's
quartet](https://en.wikipedia.org/wiki/Anscombe%27s_quartet)):
```text
dataset,x,y
I,10.0,8.04
I,8.0,6.95
I,13.0,7.58
I,9.0,8.81
I,11.0,8.33
I,14.0,9.96
I,6.0,7.24
I,4.0,4.26
I,12.0,10.84
I,7.0,4.82
I,5.0,5.68
II,10.0,9.14
II,8.0,8.14
II,13.0,8.74
II,9.0,8.77
II,11.0,9.26
II,14.0,8.1
II,6.0,6.13
II,4.0,3.1
II,12.0,9.13
II,7.0,7.26
II,5.0,4.74
III,10.0,7.46
III,8.0,6.77
III,13.0,12.74
III,9.0,7.11
III,11.0,7.81
III,14.0,8.84
III,6.0,6.08
III,4.0,5.39
III,12.0,8.15
III,7.0,6.42
III,5.0,5.73
IV,8.0,6.58
IV,8.0,5.76
IV,8.0,7.71
IV,8.0,8.84
IV,8.0,8.47
IV,8.0,7.04
IV,8.0,5.25
IV,19.0,12.5
IV,8.0,5.56
IV,8.0,7.91
IV,8.0,6.89
```

:::::{exercise} Exercise Plotting-2: Read and plot a CSV file
- Save the above CSV file to disk as `example.csv` in the same folder where
  you run JupyterLab. We recommend to create the file in the JupyterLab
  interface.
- Plot the data using `mark_point`.
- Your goal is to arrive at four plots for the four data sets, all side by side.
- If you have time, try to customize the plot.
::::{solution}
  ```python
  # we don't need to import again but just in case you started here
  import pandas as pd

  data = pd.read_csv("example.csv")

  alt.Chart(data).mark_point().encode(
      x="x",
      y="y",
      color="dataset",
      column="dataset",
      )
  ```

  Here is a more advanced example where the four plots are arranged
  in a 2 x 2 grid:
  ```python
  def create_chart(data, number):
      chart = (
          alt.Chart(data)
          .transform_filter(alt.datum.dataset == number)
          .mark_point()
          .encode(x="x", y="y")
      )
      return chart


  chart1 = create_chart(data_example, "I")
  chart2 = create_chart(data_example, "II")
  chart3 = create_chart(data_example, "III")
  chart4 = create_chart(data_example, "IV")

  chart = alt.vconcat(
      alt.hconcat(chart1, chart2),
      alt.hconcat(chart3, chart4),
  )

  chart.display()
  ```
::::
:::::

---

:::{keypoints}
- Browse a number of example galleries to help you choose the library
  that fits best your work/style.
- Minimize manual post-processing and try to script all steps.
- CSV (comma-separated values) files are often a good format to store the data
  that we wish to plot.
- Read the data into a Pandas dataframe and then plot it with Vega-Altair
  where you connect data columns to
  [visual channels](https://altair-viz.github.io/user_guide/encodings/channels.html).
:::
