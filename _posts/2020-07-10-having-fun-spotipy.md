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
sns.distplot(df['track_duration']);
```

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

## Extras

- [Repo](https://github.com/lelesgaray/spotify) of the project.
- 2 great songs that keep coming to my mind in these odd, strange days that we are living.
{% include video id="0_z_UEuEMAo" provider="youtube" %}
<br>
{% include video id="NleFEDHmdhs" provider="youtube" %}
