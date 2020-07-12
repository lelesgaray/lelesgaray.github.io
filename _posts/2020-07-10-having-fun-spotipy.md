---
title: "Having fun with Spotipy!"
date: 2020-07-10T15:34:30-04:00
categories:
  - Blog
tags:
  - Music
  - API
  - EDA
  - Python
classes: wide
header: 
  teaser: "/assets/images/spotify_pic.jpg"
---

In this post we will explore some of the [Spotipy](https://spotipy.readthedocs.io/) possibilities to retrieve and analyze your streaming data.

## Libraries import and authentification

Let's start by importing the libraries that we are going to use
```python
# write a simple Python function
# libraries import
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from dateutil.parser import parse as parse_date

# spotify libraries
import spotipy
from spotipy.oauth2 import SpotifyClientCredentials
from spotipy import util
```
Next we need to authentificate our user and credentials. To do this, we first create an application in the [Spotify Dashboard](https://developer.spotify.com/dashboard/) and gather the credentials. Then it's easy to initialize and authorize the client:

```python
token = util.prompt_for_user_token(username = user,
                                   scope = 'user-top-read',
                                   client_id= client_id,
                                   client_secret=client_secret,
                                   redirect_uri= 'http://localhost/')

spotify = spotipy.Spotify(auth=token)
spotify.trace = True
spotify.trace_out = True
```
**Tip**: In case you are struggling to get the `user`, `client_id` and `client_secret`, I recommend this [video](https://www.youtube.com/watch?v=yxSKrVXeKcI) and this [tutorial](https://support.appreciationengine.com/article/oaNki9g06n-creating-a-spotify-application) to help you configure your app.
{: .notice}

## Top tracks

The spotify object has several ways to access JSON for artists, songs and songs features. Let's start by retrieving my `top tracks`. One way to do it is to define a for loop to parse the data into a tidy dataframe.
```python
top_tracks = spotify.current_user_top_tracks(limit=1000, time_range="long_term") #<-- method to get top tracks 
cnt = 1
fields = ['rank_position', 'album_type', 'album_name', 'album_id',
              'artist_name', 'artist_id', 'track_duration', 'track_id', 
              'track_name', 'track_popularity', 'track_number', 'track_type']
    
tracks = {}

for i in fields:
        tracks[i] = []
    
for i in top_tracks['items']:
    
    #tracks['rank_position'].append(cnt)
    tracks['album_type'].append(i['album']['album_type'])
    tracks['album_id'].append(i['album']['id'])
    tracks['album_name'].append(i['album']['name'])
    tracks['artist_name'].append(i['artists'][0]['name'])
    tracks['artist_id'].append(i['artists'][0]['id'])
    tracks['track_duration'].append(i['duration_ms'])
    tracks['track_id'].append(i['id'])
    tracks['track_name'].append(i['name'])
    tracks['track_popularity'].append(i['popularity'])
    tracks['track_number'].append(i['track_number'])
    tracks['track_type'].append(i['type'])
    cnt += 1
    
df = pd.DataFrame(dict([ (k,pd.Series(v)) for k,v in tracks.items() ]))
df
```

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
      <th>rank_position</th>
      <th>album_type</th>
      <th>album_name</th>
      <th>album_id</th>
      <th>artist_name</th>
      <th>artist_id</th>
      <th>track_duration</th>
      <th>track_id</th>
      <th>track_name</th>
      <th>track_popularity</th>
      <th>track_number</th>
      <th>track_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Principios Basicos De Astronomia</td>
      <td>6wMqAOwrW6E8FkSGBXKGVe</td>
      <td>Los Planetas</td>
      <td>0N1TIXCk9Q9JbEPXQDclEL</td>
      <td>142680</td>
      <td>0oQhYCbyUqieIVsl1zt1q3</td>
      <td>Pesadilla En El Parque De Atracciones</td>
      <td>20</td>
      <td>11</td>
      <td>track</td>
    </tr>
    
    <tr>
      <th>2</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>The Messenger</td>
      <td>3ZFz6QOM8bF9yjNm5NTjWr</td>
      <td>Johnny Marr</td>
      <td>2bA2YuQk2ID3PWNXUhQrWS</td>
      <td>232120</td>
      <td>6r3eOjeAlA1SRiLMr9vNco</td>
      <td>The Crack Up</td>
      <td>17</td>
      <td>10</td>
      <td>track</td>
    </tr>
    <tr>
      <th>4</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Oshin</td>
      <td>1hSONHeTOofdeh2uoFBLgv</td>
      <td>DIIV</td>
      <td>4OrizGCKhOrW6iDDJHN9xd</td>
      <td>127800</td>
      <td>49cnatGE4zvbt5gP5DISLy</td>
      <td>(Druun)</td>
      <td>36</td>
      <td>1</td>
      <td>track</td>
    </tr>
    <tr>
      <th>5</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Oshin</td>
      <td>1hSONHeTOofdeh2uoFBLgv</td>
      <td>DIIV</td>
      <td>4OrizGCKhOrW6iDDJHN9xd</td>
      <td>165840</td>
      <td>76MJsF1rbbhrv2tDBfeRR5</td>
      <td>Follow</td>
      <td>38</td>
      <td>9</td>
      <td>track</td>
    </tr>
    <tr>
      <th>6</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Sugar Tax</td>
      <td>1J8e1dLKVmZbsyxpGa9lGg</td>
      <td>Orchestral Manoeuvres In The Dark</td>
      <td>7wJ9NwdRWtN92NunmXuwBk</td>
      <td>249173</td>
      <td>4NsNi4w10Tkpv6uikyXbJ6</td>
      <td>Pandora's Box</td>
      <td>53</td>
      <td>2</td>
      <td>track</td>
    </tr>
    <tr>
      <th>7</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Shapeshifting</td>
      <td>3DyIAjq1iOl07Z1IV39Py6</td>
      <td>Young Galaxy</td>
      <td>5xfJLyvC5UElVSiMuLt1ss</td>
      <td>32373</td>
      <td>6UvfReXhIyTime6acIlHzc</td>
      <td>NTH</td>
      <td>0</td>
      <td>1</td>
      <td>track</td>
    </tr>
    <tr>
      <th>8</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Antics</td>
      <td>58fDEyJ5XSau8FRA3y8Bps</td>
      <td>Interpol</td>
      <td>3WaJSfKnzc65VDgmj2zU8B</td>
      <td>215826</td>
      <td>6B182GP3TvEfmgUoIMVUSJ</td>
      <td>Evil</td>
      <td>63</td>
      <td>2</td>
      <td>track</td>
    </tr>
  </tbody>
</table>
</div>

Now that we have tidy data, let's analize the number of songs by artist

```python
artists = pd.DataFrame(df.groupby('artist_name')['track_name'].count().sort_values(ascending = False).reset_index())
sns.catplot(x = 'track_name', y = 'artist_name', data = artists, kind = 'bar', height = 7, aspect = 1 );
plt.title("# of Top songs by artist")
plt.xlabel('# of songs')
plt.ylabel('Artist Name')
plt.xticks(np.arange(0, 10, step=1));
```
{% include figure image_path="assets/images/top_tracks_bar.png" alt="this is a placeholder image" caption="" %}


#### What's the average length of the songs I listen to?

```python
# Average and median length of songs
avg_duration_sec = round(np.mean(df['track_duration'] / 1000), 2)
avg_duration_min = round(avg_duration_sec / 60, 2)
median_duration_sec = round(np.median(df['track_duration'] / 1000), 2)
median_duration_min = round(median_duration_sec / 60, 2)

print('The average length of top tracks is ' + str(avg_duration_sec) + ' seconds -- ' + str(avg_duration_min) + ' minutes') 
print('The median length of top tracks is ' + str(median_duration_sec) + ' seconds -- ' + str(median_duration_min) + ' minutes') 
```
<div class="output_area">

    <div class="prompt"></div>


<div class="output_subarea output_stream output_stdout output_text">
<pre>The average length of top tracks is 232.29 seconds -- 3.87 minutes
The median length of top tracks is 236.25 seconds -- 3.94 minutes
</pre>
</div>
</div>

#### What's the average popularity of the songs?

```python
# Average and median popularity of songs
avg_popularity = round(np.mean(df['track_popularity']), 2)
median_popularity = round(np.median(df['track_popularity']), 2)


print('The average popularity of top tracks is ' + str(avg_popularity))
print('The median popularity of top tracks is ' + str(median_popularity))
```
<div class="output_area">

    <div class="prompt"></div>


<div class="output_subarea output_stream output_stdout output_text">
<pre>The average popularity of top tracks is 28.78
The median popularity of top tracks is 32.0
</pre>
</div>
</div>

## Audio Features

The audio features are characteristics that Spotify assigns to the songs. Some of them are really straightforward (tempo, loudness) and others not that much (instrumentalness). You can read more on the [API reference](https://developer.spotify.com/documentation/web-api/reference/tracks/get-audio-features/).

```python
# Get audio features from top tracks and analysis
df = df.loc[df['track_id'] != '4BSP1PK4sLlticRfYl1M79',:] # drop track that's not a song
features = spotify.audio_features(tracks = df['track_id'])

# Build df from dictionary and merge
def get_features(result):
    danceability = []
    key = []
    loudness = []
    mode = []
    speechiness = []
    acousticness = []
    instrumentalness = []
    liveness = []
    valence = []
    tempo = []
    track_id = []
    t = 0
    for i in result:
        danceability.append(i['danceability'])
        key.append(i['key'])
        loudness.append(i['loudness'])
        mode.append(i['mode'])
        speechiness.append(i['speechiness'])
        instrumentalness.append(i['instrumentalness'])
        liveness.append(i['liveness'])
        valence.append(i['valence'])
        tempo.append(i['tempo'])
        track_id.append(i['id'])
    return pd.DataFrame({'danceability' : danceability, 'key' : key, 'loudness' : loudness, 'speechiness' : speechiness,
                          'instrumentalness' : instrumentalness, 'liveness' : liveness, 'valence' : valence, 
                          'tempo' : tempo, 'track_id' : track_id})
    features_df = get_features(features)
# merge dataframes
songs_and_features = pd.merge(df, features_df, left_on = 'track_id', right_on = 'track_id')
songs_and_features
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
      <th>rank_position</th>
      <th>album_type</th>
      <th>album_name</th>
      <th>album_id</th>
      <th>artist_name</th>
      <th>artist_id</th>
      <th>track_duration</th>
      <th>track_id</th>
      <th>track_name</th>
      <th>track_popularity</th>
      <th>track_number</th>
      <th>track_type</th>
      <th>danceability</th>
      <th>key</th>
      <th>loudness</th>
      <th>speechiness</th>
      <th>instrumentalness</th>
      <th>liveness</th>
      <th>valence</th>
      <th>tempo</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Principios Basicos De Astronomia</td>
      <td>6wMqAOwrW6E8FkSGBXKGVe</td>
      <td>Los Planetas</td>
      <td>0N1TIXCk9Q9JbEPXQDclEL</td>
      <td>142680</td>
      <td>0oQhYCbyUqieIVsl1zt1q3</td>
      <td>Pesadilla En El Parque De Atracciones</td>
      <td>20</td>
      <td>11</td>
      <td>track</td>
      <td>0.363</td>
      <td>2</td>
      <td>-6.365</td>
      <td>0.0814</td>
      <td>0.022700</td>
      <td>0.3970</td>
      <td>0.832</td>
      <td>132.532</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>The Messenger</td>
      <td>3ZFz6QOM8bF9yjNm5NTjWr</td>
      <td>Johnny Marr</td>
      <td>2bA2YuQk2ID3PWNXUhQrWS</td>
      <td>232120</td>
      <td>6r3eOjeAlA1SRiLMr9vNco</td>
      <td>The Crack Up</td>
      <td>17</td>
      <td>10</td>
      <td>track</td>
      <td>0.539</td>
      <td>5</td>
      <td>-6.303</td>
      <td>0.0291</td>
      <td>0.001290</td>
      <td>0.3270</td>
      <td>0.329</td>
      <td>101.007</td>
    </tr>
    <tr>
      <th>2</th>
      <td>NaN</td>
      <td>SINGLE</td>
      <td>EP II</td>
      <td>5JsmZQAcvUYjjDIdehCVub</td>
      <td>Yumi Zouma</td>
      <td>4tPyCwWrsvZ8OKYl7QRavL</td>
      <td>288080</td>
      <td>17SSL9kvysDq9D6YuBMEoP</td>
      <td>Catastrophe</td>
      <td>0</td>
      <td>3</td>
      <td>track</td>
      <td>0.696</td>
      <td>7</td>
      <td>-9.911</td>
      <td>0.0317</td>
      <td>0.190000</td>
      <td>0.1080</td>
      <td>0.567</td>
      <td>137.986</td>
    </tr>
    <tr>
      <th>3</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Oshin</td>
      <td>1hSONHeTOofdeh2uoFBLgv</td>
      <td>DIIV</td>
      <td>4OrizGCKhOrW6iDDJHN9xd</td>
      <td>127800</td>
      <td>49cnatGE4zvbt5gP5DISLy</td>
      <td>(Druun)</td>
      <td>36</td>
      <td>1</td>
      <td>track</td>
      <td>0.468</td>
      <td>9</td>
      <td>-8.553</td>
      <td>0.0305</td>
      <td>0.628000</td>
      <td>0.0977</td>
      <td>0.641</td>
      <td>134.964</td>
    </tr>
    <tr>
      <th>4</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Oshin</td>
      <td>1hSONHeTOofdeh2uoFBLgv</td>
      <td>DIIV</td>
      <td>4OrizGCKhOrW6iDDJHN9xd</td>
      <td>165840</td>
      <td>76MJsF1rbbhrv2tDBfeRR5</td>
      <td>Follow</td>
      <td>38</td>
      <td>9</td>
      <td>track</td>
      <td>0.364</td>
      <td>7</td>
      <td>-5.273</td>
      <td>0.0454</td>
      <td>0.878000</td>
      <td>0.3260</td>
      <td>0.624</td>
      <td>142.908</td>
    </tr>
  </tbody>
</table>
</div>
</div>


Let's go ahead and plot `danceability` of the artists with more than 1 song in the list.

<div>
        
                <script type="text/javascript">window.PlotlyConfig = {MathJaxConfig: 'local'};</script>
        <script src="https://cdn.plot.ly/plotly-latest.min.js"></script>    
            <div id="7d3727b3-fc28-4412-8793-00660c035c85" class="plotly-graph-div" style="height:500px; width:800px;"></div>
            <script type="text/javascript">
                
                    window.PLOTLYENV=window.PLOTLYENV || {};
                    
                if (document.getElementById("7d3727b3-fc28-4412-8793-00660c035c85")) {
                    Plotly.newPlot(
                        '7d3727b3-fc28-4412-8793-00660c035c85',
                        [{"marker": {"color": "#3D9970"}, "name": "Danceability", "type": "box", "x": ["Los Planetas", "DIIV", "DIIV", "Orchestral Manoeuvres In The Dark", "Young Galaxy", "Arcade Fire", "Pet Shop Boys", "Young Galaxy", "Arcade Fire", "Orchestral Manoeuvres In The Dark", "DIIV", "DIIV", "Blur", "Young Galaxy", "Orchestral Manoeuvres In The Dark", "Young Galaxy", "Blur", "DIIV", "DIIV", "MGMT", "DIIV", "DIIV", "MGMT", "Erasure", "Young Galaxy", "Young Galaxy", "Pet Shop Boys", "Young Galaxy", "Young Galaxy", "DIIV", "Erasure", "Orchestral Manoeuvres In The Dark", "MGMT", "Young Galaxy", "Los Planetas"], "y": [0.363, 0.468, 0.364, 0.596, 0.472, 0.548, 0.566, 0.641, 0.604, 0.595, 0.582, 0.384, 0.631, 0.721, 0.524, 0.592, 0.65, 0.274, 0.162, 0.451, 0.351, 0.341, 0.763, 0.601, 0.741, 0.733, 0.679, 0.693, 0.687, 0.478, 0.65, 0.638, 0.438, 0.554, 0.52]}],
                        {"boxmode": "group", "height": 500, "margin": {"b": 20, "l": 20, "pad": 4, "r": 20, "t": 0}, "template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "pie": [{"automargin": true, "type": "pie"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "coloraxis": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}}}, "width": 800, "yaxis": {"title": {"text": "Danceability"}}},
                        {"responsive": true}
                    )
                };
                
            </script>
        </div>
Turns out I'm not that bad... I was expecting a much darker outcome :satisfied:

## Top Artists

With the method `.current_user_top_artists()` you can retrieve your top artists and some info about them. Applying the same methods than before we get the following df:

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
      <th>artist_name</th>
      <th>genres</th>
      <th>popularity</th>
      <th>artist_image</th>
      <th>followers</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>14</th>
      <td>The Beatles</td>
      <td>[beatlesque, british invasion, classic rock, m...</td>
      <td>90</td>
      <td>https://i.scdn.co/image/1047bf172446f2a815a99a...</td>
      <td>16626426</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Frank Ocean</td>
      <td>[alternative r&amp;b, hip hop, lgbtq+ hip hop, neo...</td>
      <td>87</td>
      <td>https://i.scdn.co/image/0cc22250c0b18183e5c62f...</td>
      <td>5930758</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Daft Punk</td>
      <td>[electro, filter house]</td>
      <td>83</td>
      <td>https://i.scdn.co/image/8e189c820ba32ffd332393...</td>
      <td>6266549</td>
    </tr>
    <tr>
      <th>18</th>
      <td>U2</td>
      <td>[irish rock, permanent wave, rock]</td>
      <td>83</td>
      <td>https://i.scdn.co/image/40d6c5c14355cfc127b70d...</td>
      <td>6949831</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Cigarettes After Sex</td>
      <td>[ambient pop, dream pop, el paso indie, shoegaze]</td>
      <td>76</td>
      <td>https://i.scdn.co/image/d34e8cb22455b5d6f49fde...</td>
      <td>1570803</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Pixies</td>
      <td>[alternative rock, art rock, boston rock, mode...</td>
      <td>72</td>
      <td>https://i.scdn.co/image/f3ed963d1578a0aff0be16...</td>
      <td>1567607</td>
    </tr>
    <tr>
      <th>2</th>
      <td>The National</td>
      <td>[indie rock, modern rock]</td>
      <td>71</td>
      <td>https://i.scdn.co/image/ffaca820e2ea78e2c2cd01...</td>
      <td>1193825</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Wilco</td>
      <td>[alternative country, alternative rock, chicag...</td>
      <td>66</td>
      <td>https://i.scdn.co/image/cdf5e817af10c070b8995f...</td>
      <td>533115</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Tycho</td>
      <td>[chillwave, downtempo, electronica, indietroni...</td>
      <td>66</td>
      <td>https://i.scdn.co/image/80ba51f8c16442d839a0ed...</td>
      <td>563780</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Patti Smith</td>
      <td>[art pop, art punk, art rock, dance rock, folk...</td>
      <td>64</td>
      <td>https://i.scdn.co/image/44012cfc4fedcbe92ac5e1...</td>
      <td>621566</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Alexandre Tharaud</td>
      <td>[classical performance, classical piano]</td>
      <td>62</td>
      <td>https://i.scdn.co/image/c5e94f9df82bd5fdf9a978...</td>
      <td>18637</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Bobby Womack</td>
      <td>[classic soul, funk, motown, quiet storm, soul...</td>
      <td>61</td>
      <td>https://i.scdn.co/image/2a844501416646506eef4a...</td>
      <td>445212</td>
    </tr>
    <tr>
      <th>17</th>
      <td>The Jesus and Mary Chain</td>
      <td>[alternative rock, art rock, dance rock, new w...</td>
      <td>58</td>
      <td>https://i.scdn.co/image/7134f3041e86bb30a1aa8f...</td>
      <td>286478</td>
    </tr>
    <tr>
      <th>16</th>
      <td>John Cale</td>
      <td>[alternative rock, anti-folk, art pop, art roc...</td>
      <td>55</td>
      <td>https://i.scdn.co/image/ab6772690000dd221d8c27...</td>
      <td>111959</td>
    </tr>
      </tbody>
</table>
</div>
</div>

## Get top tracks from an artist

I wanna finish this post with a way of getting top tracks from any artist you want. You only need the spotify `uri` that identifies each artist.

```python
# Diiv Example
lz_uri = 'spotify:artist:4OrizGCKhOrW6iDDJHN9xd'

spotify = spotipy.Spotify(client_credentials_manager=credentials)
results = spotify.artist_top_tracks(lz_uri)

for track in results['tracks'][:10]:
    print('track    : ' + track['name'])
    print('audio    : ' + track['preview_url'])
    print('cover art: ' + track['album']['images'][0]['url'])
    print()
```
<div class="output_wrapper">
<div class="output">


<div class="output_area">

    <div class="prompt"></div>


<div class="output_subarea output_stream output_stdout output_text">
<pre>track    : Under the Sun
audio    : https://p.scdn.co/mp3-preview/168b3cbe7b1e7224ca297b757b6c9b35fb176629?cid=ff47cd5371a049cb87c5c6bc407f4901
cover art: https://i.scdn.co/image/ab67616d0000b2735172f44c5f8743c09fb5bbc8

track    : Doused
audio    : https://p.scdn.co/mp3-preview/204c017dbfb537a03ea1ce0146b419d6cd40fa10?cid=ff47cd5371a049cb87c5c6bc407f4901
cover art: https://i.scdn.co/image/ab67616d0000b2737bc6a0c2b8d9393a2dd80cb7

track    : Blankenship
audio    : https://p.scdn.co/mp3-preview/93ae971865b312f14d5b46b85fc75074d0daa1f3?cid=ff47cd5371a049cb87c5c6bc407f4901
cover art: https://i.scdn.co/image/ab67616d0000b2733bdae3719124f5f749feb9c5

</pre>
</div>
</div>

</div>
</div>

**Tip:** If you want to improve your analysis it's better to [Download your privacy data from Spotify](https://www.makeuseof.com/tag/download-privacy-data-spotify/). It's an easy process but it takes a few days to complete. Once it's done, they will send you an alert to your email with the [instructions to analyze the data.](https://support.spotify.com/uk/account_payment_help/privacy/understanding-my-data/)
{: .notice--success}

## Extras

- [Repo](https://github.com/lelesgaray/spotify) of the project.
- 2 great songs that keep coming to my mind in these odd, strange days that we are living.
{% include video id="0_z_UEuEMAo" provider="youtube" %}
<br>
{% include video id="NleFEDHmdhs" provider="youtube" %}
