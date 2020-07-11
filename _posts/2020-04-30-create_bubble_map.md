---
title: "Tutorial: how to easily create a bubble map with Plotly"
date: 2020-04-30T15:34:30-04:00
categories:
  - Tutorial
tags:
  - Plotly
  - EDA
  - Covid
  - Python
classes: wide
---

Creating engaging plots is one useful skill to present data from your work. In this entry we will work with [Plotly](https://plotly.com/) library and information regarding Coronavirus spread, trying to emulate some of the [plots we are seeing these days.](https://coronavirus.jhu.edu/)

## Data import and libraries

We will start by importing the usual libraries for data wrangling and the Plotly libraries we will use.

``` python
import numpy as np
import pandas as pd
# plotly libraries
#pip install plotly <-- in case you need to intall plotly
import plotly.offline as py 
import plotly.graph_objs as go # it's like "plt" of matplot
import plotly.tools as tl
```
Next we will load a DataFrame that contains tidy information about cases, tests and deaths for several countries around the world.

``` python
df = pd.read_csv('https://raw.githubusercontent.com/nikkisharma536/streamlit_app/master/covid.csv')
df.head()
```





<div class="output_html rendered_html output_subarea output_execute_result">
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Country</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>date</th>
      <th>total_cases</th>
      <th>new_cases</th>
      <th>total_deaths</th>
      <th>new_deaths</th>
      <th>total_cases_per_million</th>
      <th>new_cases_per_million</th>
      <th>total_deaths_per_million</th>
      <th>new_deaths_per_million</th>
      <th>total_tests</th>
      <th>new_tests</th>
      <th>total_tests_per_thousand</th>
      <th>new_tests_per_thousand</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Argentina</td>
      <td>-34.0</td>
      <td>-64.0</td>
      <td>9/4/2020</td>
      <td>1795</td>
      <td>80</td>
      <td>65</td>
      <td>5</td>
      <td>39.716</td>
      <td>1.770</td>
      <td>1.438</td>
      <td>0.111</td>
      <td>14850</td>
      <td>1520</td>
      <td>0.329</td>
      <td>0.034</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Argentina</td>
      <td>-34.0</td>
      <td>-64.0</td>
      <td>10/4/2020</td>
      <td>1894</td>
      <td>99</td>
      <td>79</td>
      <td>14</td>
      <td>41.907</td>
      <td>2.190</td>
      <td>1.748</td>
      <td>0.310</td>
      <td>16379</td>
      <td>1529</td>
      <td>0.362</td>
      <td>0.034</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Argentina</td>
      <td>-34.0</td>
      <td>-64.0</td>
      <td>11/4/2020</td>
      <td>1975</td>
      <td>81</td>
      <td>82</td>
      <td>3</td>
      <td>43.699</td>
      <td>1.792</td>
      <td>1.814</td>
      <td>0.066</td>
      <td>18027</td>
      <td>1648</td>
      <td>0.399</td>
      <td>0.036</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Argentina</td>
      <td>-34.0</td>
      <td>-64.0</td>
      <td>14/4/2020</td>
      <td>2272</td>
      <td>69</td>
      <td>98</td>
      <td>3</td>
      <td>50.270</td>
      <td>1.527</td>
      <td>2.168</td>
      <td>0.066</td>
      <td>22805</td>
      <td>3047</td>
      <td>0.505</td>
      <td>0.067</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Argentina</td>
      <td>-34.0</td>
      <td>-64.0</td>
      <td>15/4/2020</td>
      <td>2432</td>
      <td>160</td>
      <td>105</td>
      <td>7</td>
      <td>53.810</td>
      <td>3.540</td>
      <td>2.323</td>
      <td>0.155</td>
      <td>24374</td>
      <td>1569</td>
      <td>0.539</td>
      <td>0.035</td>
    </tr>
  </tbody>
</table>
</div>
</div>

## Grouping info and plot

Before jumping into the plot details, let's group the data by country and keep only the relevant features (*recall from the table before that we have data by date*). 
```python
df_plot = df[['total_cases', 'Country', 'Latitude', 'Longitude','total_deaths_per_million' ]]\
          .groupby(df['Country']).mean().reset_index()
```
Now we have all set up to start the map plot. To make it easier, we will divide it into 4 parts

### Marker text and color
In this section we will define the marker text to show and variables. As you can see below we will select `Total Cases` and `Total deaths per million`. The `scale` variable will help us adjust the size of the bubble.
```python
df_plot['text'] = df_plot['Country'] + '<br>Cases: ' + (df_plot['total_cases'].astype('int')).astype(str)\
+ '<br>Total Deaths per million: ' + (df_plot['total_deaths_per_million'].astype('int')).astype(str) 
colors = "#981E32"
scale = 200
```
### Figure instantiation and definition
In this section we will define a `ScatterGeo()` figure, that will allow us to render a full map.
```python
fig = go.Figure()

fig.add_trace(go.Scattergeo(
        locationmode = 'country names',
        lon = df_plot['Longitude'],
        lat = df_plot['Latitude'],
        text = df_plot['text'],  #<--- the text for the market that we already defined
        mode = 'markers',
        marker = dict(
            size = df_plot['total_cases']/scale, #<-- size of the bubble (normalized by scale)
            color = colors, <--- color of the bubble
            line_color='rgb(200,200,200)', #<--- color for the bubble line
            line_width=0.5,
            sizemode = 'area' )))
```
### Layout configuration
Last, we will define the base map.
```python
fig.update_layout(
        showlegend = False,
        mapbox_style="open-street-map",
        geo = dict(
            scope = 'world', #<--- define base map (as far as I know you can choose between world and US
            landcolor = 'rgb(220, 220, 220)', #<--- you can change the color of the land
        )
    )

fig.show()
```
And voilá!

 <div>



                <script type="text/javascript">window.PlotlyConfig = {MathJaxConfig: 'local'};</script>
        <script src="https://cdn.plot.ly/plotly-latest.min.js"></script>    
            <div id="6046d766-c20d-4db1-ace7-18b6a671d787" class="plotly-graph-div" style="height:500px; width:800px;"></div>
            <script type="text/javascript">
                
                    window.PLOTLYENV=window.PLOTLYENV || {};
                    
                if (document.getElementById("6046d766-c20d-4db1-ace7-18b6a671d787")) {
                    Plotly.newPlot(
                        '6046d766-c20d-4db1-ace7-18b6a671d787',
                        [{"lat": [-34.0, -27.0, 47.33329999999994, 26.0, 24.0, 53.0, 50.83329999999994, -17.0, -17.0, 22.0, 60.0, -30.0, 4.0, 10.0, 45.1667, 21.5, 49.75, 56.0, -2.0, 13.833299999999998, 59.0, 8.0, 64.0, 46.0, 8.0, 39.0, 47.0, 65.0, 20.0, -5.0, 32.0, 31.5, 42.83329999999994, 48.0, 1.0, 37.0, 57.0, 56.0, 49.75, 2.5, 23.0, 32.0, 22.0, 28.0, 52.5, -41.0, 10.0, 62.0, 30.0, 9.0, -23.0, -10.0, 13.0, 52.0, 39.5, 25.5, 46.0, 60.0, 60.0, -2.0, 14.0, 44.0, 48.6667, 46.0, -29.0, 37.0, 47.0, 23.5, 23.5, 15.0, 34.0, 39.0, 1.0, 49.0, 54.0, 38.0, -33.0, 16.0, 16.0, -20.0], "locationmode": "country names", "lon": [-64.0, 133.0, 13.333300000000007, 50.549999999999976, 90.0, 28.0, 4.0, -65.0, -65.0, 98.0, -95.0, -71.0, -72.0, -84.0, 15.5, -80.0, 15.5, 10.0, -77.5, -88.9167, 26.0, 38.0, 26.0, 2.0, -2.0, 22.0, 20.0, -18.0, 77.0, 120.0, 53.0, 34.75, 12.833300000000007, 68.0, 38.0, 127.5, 25.0, 24.0, 6.166700000000002, 112.5, -102.0, -5.0, 98.0, 84.0, 5.75, 174.0, 8.0, 10.0, 70.0, -80.0, -58.0, -76.0, 122.0, 20.0, -8.0, 51.25, 25.0, 100.0, 100.0, 30.0, -14.0, 21.0, 19.5, 15.0, 24.0, 127.5, 8.0, 121.0, 121.0, 100.0, 9.0, 35.0, 32.0, 32.0, -2.0, -97.0, -56.0, 106.0, 106.0, 30.0], "marker": {"color": "#981E32", "line": {"color": "rgb(200,200,200)", "width": 0.5}, "size": [14.654736842105262, 32.194, 39.32225806451613, 7.11703125, 6.71563829787234, 27.30676470588235, 79.57822033898304, 1.7643589743589743, 1.7643589743589743, 0.462, 109.08914634146342, 42.843275862068964, 9.112448979591836, 1.9508, 5.334565217391304, 3.080394736842105, 11.93890243902439, 25.237105263157897, 25.763947368421054, 1.0572727272727274, 3.8161016949152544, 0.40857142857142853, 8.773839285714285, 1.7776666666666667, 2.963333333333333, 8.681451612903226, 4.930943396226414, 4.595396825396826, 54.065483870967746, 21.995555555555555, 397.82875, 27.079765625, 449.66878787878784, 4.84468085106383, 0.9120967741935483, 31.603944444444448, 2.0561818181818183, 4.9785, 17.36973684210526, 15.35547619047619, 8.28063063063063, 7.562159090909091, 0.462, 0.07068627450980391, 190.84333333333333, 3.648, 4.935, 25.0275, 22.277395833333333, 10.988137254901961, 0.5562765957446808, 31.627340425531916, 32.170625, 22.91423076923077, 44.41359649122807, 19.097763157894736, 22.974285714285717, 103.0935, 103.0935, 0.7172916666666667, 1.2318627450980393, 14.098365384615386, 4.061428571428571, 4.409479166666667, 9.550869565217392, 31.603944444444448, 42.71098958333333, 1.3868867924528303, 1.3868867924528303, 8.38670731707317, 2.2701020408163264, 236.92372093023255, 0.3057142857142857, 36.373333333333335, 156.380625, 1752.8253773584906, 2.3211290322580647, 0.84425, 0.84425, 0.08951612903225806], "sizemode": "area"}, "mode": "markers", "text": ["Argentina<br>Cases: 2930<br>Total Deaths per million: 3", "Australia<br>Cases: 6438<br>Total Deaths per million: 2", "Austria<br>Cases: 7864<br>Total Deaths per million: 20", "Bahrain<br>Cases: 1423<br>Total Deaths per million: 3", "Bangladesh<br>Cases: 1343<br>Total Deaths per million: 0", "Belarus<br>Cases: 5461<br>Total Deaths per million: 4", "Belgium<br>Cases: 15915<br>Total Deaths per million: 165", "Bolivia<br>Cases: 352<br>Total Deaths per million: 1", "Bolivia, Plurinational State of<br>Cases: 352<br>Total Deaths per million: 1", "Burma<br>Cases: 92<br>Total Deaths per million: 0", "Canada<br>Cases: 21817<br>Total Deaths per million: 23", "Chile<br>Cases: 8568<br>Total Deaths per million: 5", "Colombia<br>Cases: 1822<br>Total Deaths per million: 1", "Costa Rica<br>Cases: 390<br>Total Deaths per million: 0", "Croatia<br>Cases: 1066<br>Total Deaths per million: 4", "Cuba<br>Cases: 616<br>Total Deaths per million: 1", "Czech Republic<br>Cases: 2387<br>Total Deaths per million: 4", "Denmark<br>Cases: 5047<br>Total Deaths per million: 37", "Ecuador<br>Cases: 5152<br>Total Deaths per million: 12", "El Salvador<br>Cases: 211<br>Total Deaths per million: 1", "Estonia<br>Cases: 763<br>Total Deaths per million: 11", "Ethiopia<br>Cases: 81<br>Total Deaths per million: 0", "Finland<br>Cases: 1754<br>Total Deaths per million: 7", "France<br>Cases: 355<br>Total Deaths per million: 0", "Ghana<br>Cases: 592<br>Total Deaths per million: 0", "Greece<br>Cases: 1736<br>Total Deaths per million: 7", "Hungary<br>Cases: 986<br>Total Deaths per million: 8", "Iceland<br>Cases: 919<br>Total Deaths per million: 10", "India<br>Cases: 10813<br>Total Deaths per million: 0", "Indonesia<br>Cases: 4399<br>Total Deaths per million: 1", "Iran, Islamic Republic of<br>Cases: 79565<br>Total Deaths per million: 59", "Israel<br>Cases: 5415<br>Total Deaths per million: 6", "Italy<br>Cases: 89933<br>Total Deaths per million: 183", "Kazakhstan<br>Cases: 968<br>Total Deaths per million: 0", "Kenya<br>Cases: 182<br>Total Deaths per million: 0", "Korea, Republic of<br>Cases: 6320<br>Total Deaths per million: 1", "Latvia<br>Cases: 411<br>Total Deaths per million: 1", "Lithuania<br>Cases: 995<br>Total Deaths per million: 8", "Luxembourg<br>Cases: 3473<br>Total Deaths per million: 115", "Malaysia<br>Cases: 3071<br>Total Deaths per million: 1", "Mexico<br>Cases: 1656<br>Total Deaths per million: 1", "Morocco<br>Cases: 1512<br>Total Deaths per million: 2", "Myanmar<br>Cases: 92<br>Total Deaths per million: 0", "Nepal<br>Cases: 14<br>Total Deaths per million: 0", "Netherlands<br>Cases: 38168<br>Total Deaths per million: 263", "New Zealand<br>Cases: 729<br>Total Deaths per million: 1", "Nigeria<br>Cases: 987<br>Total Deaths per million: 0", "Norway<br>Cases: 5005<br>Total Deaths per million: 14", "Pakistan<br>Cases: 4455<br>Total Deaths per million: 0", "Panama<br>Cases: 2197<br>Total Deaths per million: 13", "Paraguay<br>Cases: 111<br>Total Deaths per million: 0", "Peru<br>Cases: 6325<br>Total Deaths per million: 5", "Philippines<br>Cases: 6434<br>Total Deaths per million: 3", "Poland<br>Cases: 4582<br>Total Deaths per million: 4", "Portugal<br>Cases: 8882<br>Total Deaths per million: 27", "Qatar<br>Cases: 3819<br>Total Deaths per million: 1", "Romania<br>Cases: 4594<br>Total Deaths per million: 11", "Russia<br>Cases: 20618<br>Total Deaths per million: 1", "Russian Federation<br>Cases: 20618<br>Total Deaths per million: 1", "Rwanda<br>Cases: 143<br>Total Deaths per million: 0", "Senegal<br>Cases: 246<br>Total Deaths per million: 0", "Serbia<br>Cases: 2819<br>Total Deaths per million: 8", "Slovakia<br>Cases: 812<br>Total Deaths per million: 1", "Slovenia<br>Cases: 881<br>Total Deaths per million: 16", "South Africa<br>Cases: 1910<br>Total Deaths per million: 0", "South Korea<br>Cases: 6320<br>Total Deaths per million: 1", "Switzerland<br>Cases: 8542<br>Total Deaths per million: 32", "Taiwan<br>Cases: 277<br>Total Deaths per million: 0", "Taiwan, Province of China<br>Cases: 277<br>Total Deaths per million: 0", "Thailand<br>Cases: 1677<br>Total Deaths per million: 0", "Tunisia<br>Cases: 454<br>Total Deaths per million: 1", "Turkey<br>Cases: 47384<br>Total Deaths per million: 13", "Uganda<br>Cases: 61<br>Total Deaths per million: 0", "Ukraine<br>Cases: 7274<br>Total Deaths per million: 4", "United Kingdom<br>Cases: 31276<br>Total Deaths per million: 58", "United States<br>Cases: 350565<br>Total Deaths per million: 49", "Uruguay<br>Cases: 464<br>Total Deaths per million: 2", "Viet Nam<br>Cases: 168<br>Total Deaths per million: 0", "Vietnam<br>Cases: 168<br>Total Deaths per million: 0", "Zimbabwe<br>Cases: 17<br>Total Deaths per million: 0"], "type": "scattergeo"}],
                        {"geo": {"landcolor": "rgb(220, 220, 220)", "scope": "world"}, "height": 500, "mapbox": {"style": "open-street-map"}, "margin": {"b": 20, "l": 20, "pad": 4, "r": 20, "t": 0}, "showlegend": false, "template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "pie": [{"automargin": true, "type": "pie"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "coloraxis": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}}}, "width": 800},
                        {"responsive": true}
                    )
                };
                
            </script>
        </div>

Plotly allows a full range of different modes and configuration, and you can experiment with the [full list of parameters](https://plotly.com/python-api-reference/generated/plotly.graph_objects.Figure.html) to get better results for your project. The idea behind this post was to get you working on a simple map and build up from there. Having said that, I will present you with the same map as before, but this time rendered in a 3D globe.

``` python
fig = go.Figure()
fig.add_trace(go.Scattergeo(
        locationmode = 'country names',
        lon = df_plot['Longitude'],
        lat = df_plot['Latitude'],
        text = df_plot['text'],
        mode = 'markers',
        marker = dict(
            size = df_plot['total_cases']/scale,
            color = "#981E32",
            line_color='rgb(200,200,200)',
            line_width=0.5,
            sizemode = 'area' )))
       
fig.update_layout(
        showlegend = False,
        mapbox_style="open-street-map",
          width=800,
    height=500,
    margin=dict(
        l=20,
        r=20,
        b=20,
        t=0,
        pad=4),
        geo = dict(
            scope = 'world',
            landcolor = '#fffdd0',
            showland= True,
            showocean=True,
            oceancolor="LightBlue",
            showcountries=True
        )
    )

fig.update_geos(projection_type="orthographic")

fig.show()
```

<div>
        
                <script type="text/javascript">window.PlotlyConfig = {MathJaxConfig: 'local'};</script>
        <script src="https://cdn.plot.ly/plotly-latest.min.js"></script>    
            <div id="fff9fe9d-24b4-48ae-9e4d-b273de9865b6" class="plotly-graph-div" style="height:500px; width:800px;"></div>
            <script type="text/javascript">
                
                    window.PLOTLYENV=window.PLOTLYENV || {};
                    
                if (document.getElementById("fff9fe9d-24b4-48ae-9e4d-b273de9865b6")) {
                    Plotly.newPlot(
                        'fff9fe9d-24b4-48ae-9e4d-b273de9865b6',
                        [{"lat": [-34.0, -27.0, 47.33329999999994, 26.0, 24.0, 53.0, 50.83329999999994, -17.0, -17.0, 22.0, 60.0, -30.0, 4.0, 10.0, 45.1667, 21.5, 49.75, 56.0, -2.0, 13.833299999999998, 59.0, 8.0, 64.0, 46.0, 8.0, 39.0, 47.0, 65.0, 20.0, -5.0, 32.0, 31.5, 42.83329999999994, 48.0, 1.0, 37.0, 57.0, 56.0, 49.75, 2.5, 23.0, 32.0, 22.0, 28.0, 52.5, -41.0, 10.0, 62.0, 30.0, 9.0, -23.0, -10.0, 13.0, 52.0, 39.5, 25.5, 46.0, 60.0, 60.0, -2.0, 14.0, 44.0, 48.6667, 46.0, -29.0, 37.0, 47.0, 23.5, 23.5, 15.0, 34.0, 39.0, 1.0, 49.0, 54.0, 38.0, -33.0, 16.0, 16.0, -20.0], "locationmode": "country names", "lon": [-64.0, 133.0, 13.333300000000007, 50.549999999999976, 90.0, 28.0, 4.0, -65.0, -65.0, 98.0, -95.0, -71.0, -72.0, -84.0, 15.5, -80.0, 15.5, 10.0, -77.5, -88.9167, 26.0, 38.0, 26.0, 2.0, -2.0, 22.0, 20.0, -18.0, 77.0, 120.0, 53.0, 34.75, 12.833300000000007, 68.0, 38.0, 127.5, 25.0, 24.0, 6.166700000000002, 112.5, -102.0, -5.0, 98.0, 84.0, 5.75, 174.0, 8.0, 10.0, 70.0, -80.0, -58.0, -76.0, 122.0, 20.0, -8.0, 51.25, 25.0, 100.0, 100.0, 30.0, -14.0, 21.0, 19.5, 15.0, 24.0, 127.5, 8.0, 121.0, 121.0, 100.0, 9.0, 35.0, 32.0, 32.0, -2.0, -97.0, -56.0, 106.0, 106.0, 30.0], "marker": {"color": "#981E32", "line": {"color": "rgb(200,200,200)", "width": 0.5}, "size": [14.654736842105262, 32.194, 39.32225806451613, 7.11703125, 6.71563829787234, 27.30676470588235, 79.57822033898304, 1.7643589743589743, 1.7643589743589743, 0.462, 109.08914634146342, 42.843275862068964, 9.112448979591836, 1.9508, 5.334565217391304, 3.080394736842105, 11.93890243902439, 25.237105263157897, 25.763947368421054, 1.0572727272727274, 3.8161016949152544, 0.40857142857142853, 8.773839285714285, 1.7776666666666667, 2.963333333333333, 8.681451612903226, 4.930943396226414, 4.595396825396826, 54.065483870967746, 21.995555555555555, 397.82875, 27.079765625, 449.66878787878784, 4.84468085106383, 0.9120967741935483, 31.603944444444448, 2.0561818181818183, 4.9785, 17.36973684210526, 15.35547619047619, 8.28063063063063, 7.562159090909091, 0.462, 0.07068627450980391, 190.84333333333333, 3.648, 4.935, 25.0275, 22.277395833333333, 10.988137254901961, 0.5562765957446808, 31.627340425531916, 32.170625, 22.91423076923077, 44.41359649122807, 19.097763157894736, 22.974285714285717, 103.0935, 103.0935, 0.7172916666666667, 1.2318627450980393, 14.098365384615386, 4.061428571428571, 4.409479166666667, 9.550869565217392, 31.603944444444448, 42.71098958333333, 1.3868867924528303, 1.3868867924528303, 8.38670731707317, 2.2701020408163264, 236.92372093023255, 0.3057142857142857, 36.373333333333335, 156.380625, 1752.8253773584906, 2.3211290322580647, 0.84425, 0.84425, 0.08951612903225806], "sizemode": "area"}, "mode": "markers", "text": ["Argentina<br>Cases: 2930<br>Total Deaths per million: 3", "Australia<br>Cases: 6438<br>Total Deaths per million: 2", "Austria<br>Cases: 7864<br>Total Deaths per million: 20", "Bahrain<br>Cases: 1423<br>Total Deaths per million: 3", "Bangladesh<br>Cases: 1343<br>Total Deaths per million: 0", "Belarus<br>Cases: 5461<br>Total Deaths per million: 4", "Belgium<br>Cases: 15915<br>Total Deaths per million: 165", "Bolivia<br>Cases: 352<br>Total Deaths per million: 1", "Bolivia, Plurinational State of<br>Cases: 352<br>Total Deaths per million: 1", "Burma<br>Cases: 92<br>Total Deaths per million: 0", "Canada<br>Cases: 21817<br>Total Deaths per million: 23", "Chile<br>Cases: 8568<br>Total Deaths per million: 5", "Colombia<br>Cases: 1822<br>Total Deaths per million: 1", "Costa Rica<br>Cases: 390<br>Total Deaths per million: 0", "Croatia<br>Cases: 1066<br>Total Deaths per million: 4", "Cuba<br>Cases: 616<br>Total Deaths per million: 1", "Czech Republic<br>Cases: 2387<br>Total Deaths per million: 4", "Denmark<br>Cases: 5047<br>Total Deaths per million: 37", "Ecuador<br>Cases: 5152<br>Total Deaths per million: 12", "El Salvador<br>Cases: 211<br>Total Deaths per million: 1", "Estonia<br>Cases: 763<br>Total Deaths per million: 11", "Ethiopia<br>Cases: 81<br>Total Deaths per million: 0", "Finland<br>Cases: 1754<br>Total Deaths per million: 7", "France<br>Cases: 355<br>Total Deaths per million: 0", "Ghana<br>Cases: 592<br>Total Deaths per million: 0", "Greece<br>Cases: 1736<br>Total Deaths per million: 7", "Hungary<br>Cases: 986<br>Total Deaths per million: 8", "Iceland<br>Cases: 919<br>Total Deaths per million: 10", "India<br>Cases: 10813<br>Total Deaths per million: 0", "Indonesia<br>Cases: 4399<br>Total Deaths per million: 1", "Iran, Islamic Republic of<br>Cases: 79565<br>Total Deaths per million: 59", "Israel<br>Cases: 5415<br>Total Deaths per million: 6", "Italy<br>Cases: 89933<br>Total Deaths per million: 183", "Kazakhstan<br>Cases: 968<br>Total Deaths per million: 0", "Kenya<br>Cases: 182<br>Total Deaths per million: 0", "Korea, Republic of<br>Cases: 6320<br>Total Deaths per million: 1", "Latvia<br>Cases: 411<br>Total Deaths per million: 1", "Lithuania<br>Cases: 995<br>Total Deaths per million: 8", "Luxembourg<br>Cases: 3473<br>Total Deaths per million: 115", "Malaysia<br>Cases: 3071<br>Total Deaths per million: 1", "Mexico<br>Cases: 1656<br>Total Deaths per million: 1", "Morocco<br>Cases: 1512<br>Total Deaths per million: 2", "Myanmar<br>Cases: 92<br>Total Deaths per million: 0", "Nepal<br>Cases: 14<br>Total Deaths per million: 0", "Netherlands<br>Cases: 38168<br>Total Deaths per million: 263", "New Zealand<br>Cases: 729<br>Total Deaths per million: 1", "Nigeria<br>Cases: 987<br>Total Deaths per million: 0", "Norway<br>Cases: 5005<br>Total Deaths per million: 14", "Pakistan<br>Cases: 4455<br>Total Deaths per million: 0", "Panama<br>Cases: 2197<br>Total Deaths per million: 13", "Paraguay<br>Cases: 111<br>Total Deaths per million: 0", "Peru<br>Cases: 6325<br>Total Deaths per million: 5", "Philippines<br>Cases: 6434<br>Total Deaths per million: 3", "Poland<br>Cases: 4582<br>Total Deaths per million: 4", "Portugal<br>Cases: 8882<br>Total Deaths per million: 27", "Qatar<br>Cases: 3819<br>Total Deaths per million: 1", "Romania<br>Cases: 4594<br>Total Deaths per million: 11", "Russia<br>Cases: 20618<br>Total Deaths per million: 1", "Russian Federation<br>Cases: 20618<br>Total Deaths per million: 1", "Rwanda<br>Cases: 143<br>Total Deaths per million: 0", "Senegal<br>Cases: 246<br>Total Deaths per million: 0", "Serbia<br>Cases: 2819<br>Total Deaths per million: 8", "Slovakia<br>Cases: 812<br>Total Deaths per million: 1", "Slovenia<br>Cases: 881<br>Total Deaths per million: 16", "South Africa<br>Cases: 1910<br>Total Deaths per million: 0", "South Korea<br>Cases: 6320<br>Total Deaths per million: 1", "Switzerland<br>Cases: 8542<br>Total Deaths per million: 32", "Taiwan<br>Cases: 277<br>Total Deaths per million: 0", "Taiwan, Province of China<br>Cases: 277<br>Total Deaths per million: 0", "Thailand<br>Cases: 1677<br>Total Deaths per million: 0", "Tunisia<br>Cases: 454<br>Total Deaths per million: 1", "Turkey<br>Cases: 47384<br>Total Deaths per million: 13", "Uganda<br>Cases: 61<br>Total Deaths per million: 0", "Ukraine<br>Cases: 7274<br>Total Deaths per million: 4", "United Kingdom<br>Cases: 31276<br>Total Deaths per million: 58", "United States<br>Cases: 350565<br>Total Deaths per million: 49", "Uruguay<br>Cases: 464<br>Total Deaths per million: 2", "Viet Nam<br>Cases: 168<br>Total Deaths per million: 0", "Vietnam<br>Cases: 168<br>Total Deaths per million: 0", "Zimbabwe<br>Cases: 17<br>Total Deaths per million: 0"], "type": "scattergeo"}],
                        {"geo": {"landcolor": "#fffdd0", "oceancolor": "LightBlue", "projection": {"type": "orthographic"}, "scope": "world", "showcountries": true, "showland": true, "showocean": true}, "height": 500, "mapbox": {"style": "open-street-map"}, "margin": {"b": 20, "l": 20, "pad": 4, "r": 20, "t": 0}, "showlegend": false, "template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "pie": [{"automargin": true, "type": "pie"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "coloraxis": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}}}, "width": 800},
                        {"responsive": true}
                    )
                };
                
            </script>
        </div>

**Tip**: if you want to embed the map, follow theses steps:
{: .notice--success}
- Export the html to your local drive
```python
with open('plotly_graph.html', 'w') as f:
    f.write(fig.to_html(include_plotlyjs='cdn'))
```
- Open the file as a .txt and copy the <div> section
- Paste it in the code

## Concluding remarks
I hope this tutorial come in handy to start improving your plots and presentations of your projects. And also to keep your mind busy while we wait for a vaccine to arrive :syringe:

## Extras
- 2 great videos by [3blue1brown](https://www.youtube.com/channel/UCYO_jab_esuFRV4b17AJtAw) on epidemics and exponential growth.
{% include video id="Kas0tIxDvrg" provider="youtube" %}
<br>
{% include video id="gxAaO2rsdIs" provider="youtube" %}


