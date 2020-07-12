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
    <tr>
      <th>5</th>
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
      <td>0.596</td>
      <td>9</td>
      <td>-12.126</td>
      <td>0.0393</td>
      <td>0.077300</td>
      <td>0.2880</td>
      <td>0.822</td>
      <td>121.819</td>
    </tr>
    <tr>
      <th>6</th>
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
      <td>0.472</td>
      <td>8</td>
      <td>-14.991</td>
      <td>0.1790</td>
      <td>0.468000</td>
      <td>0.2560</td>
      <td>0.904</td>
      <td>88.401</td>
    </tr>
    <tr>
      <th>7</th>
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
      <td>0.497</td>
      <td>5</td>
      <td>-4.090</td>
      <td>0.0396</td>
      <td>0.653000</td>
      <td>0.0725</td>
      <td>0.599</td>
      <td>129.078</td>
    </tr>
    <tr>
      <th>8</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Everything Now</td>
      <td>1DNojVW079FU9YnAMk3Cgr</td>
      <td>Arcade Fire</td>
      <td>3kjuyTCjPG1WMFCiyc5IuB</td>
      <td>303013</td>
      <td>7KsZHCfOitA5V9oQYVdltG</td>
      <td>Everything Now</td>
      <td>64</td>
      <td>2</td>
      <td>track</td>
      <td>0.548</td>
      <td>0</td>
      <td>-5.325</td>
      <td>0.0302</td>
      <td>0.000449</td>
      <td>0.4570</td>
      <td>0.610</td>
      <td>116.046</td>
    </tr>
    <tr>
      <th>9</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Very</td>
      <td>6isi41U6Wdh0JBglB26rKX</td>
      <td>Pet Shop Boys</td>
      <td>2ycnb8Er79LoH2AsR5ldjh</td>
      <td>246560</td>
      <td>5oLjvbE4gSAXOj2m3YbGBg</td>
      <td>Liberation - 2001 Remaster</td>
      <td>27</td>
      <td>3</td>
      <td>track</td>
      <td>0.566</td>
      <td>5</td>
      <td>-6.578</td>
      <td>0.0312</td>
      <td>0.002500</td>
      <td>0.1550</td>
      <td>0.409</td>
      <td>108.707</td>
    </tr>
    <tr>
      <th>10</th>
      <td>NaN</td>
      <td>COMPILATION</td>
      <td>Passive aggressive: Singles 2002-2010</td>
      <td>3ghBcixyt16aESHfoKfcZd</td>
      <td>The Radio Dept.</td>
      <td>0utS63XytOEVN1EtzWhJpG</td>
      <td>190506</td>
      <td>4EAnLp20h2N72cOKEeqww1</td>
      <td>Why won't you talk about it?</td>
      <td>0</td>
      <td>1</td>
      <td>track</td>
      <td>0.376</td>
      <td>5</td>
      <td>-1.863</td>
      <td>0.0598</td>
      <td>0.139000</td>
      <td>0.2700</td>
      <td>0.333</td>
      <td>139.935</td>
    </tr>
    <tr>
      <th>11</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Shapeshifting</td>
      <td>3DyIAjq1iOl07Z1IV39Py6</td>
      <td>Young Galaxy</td>
      <td>5xfJLyvC5UElVSiMuLt1ss</td>
      <td>227560</td>
      <td>0RJpCkAg481Cbn4TWSzHG5</td>
      <td>COVER YOUR TRACKS</td>
      <td>0</td>
      <td>9</td>
      <td>track</td>
      <td>0.641</td>
      <td>8</td>
      <td>-8.268</td>
      <td>0.0403</td>
      <td>0.822000</td>
      <td>0.0883</td>
      <td>0.471</td>
      <td>93.983</td>
    </tr>
    <tr>
      <th>12</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Everything Now</td>
      <td>1DNojVW079FU9YnAMk3Cgr</td>
      <td>Arcade Fire</td>
      <td>3kjuyTCjPG1WMFCiyc5IuB</td>
      <td>353240</td>
      <td>0SaEmR2rdtfsZawPjMYkWg</td>
      <td>Put Your Money on Me</td>
      <td>60</td>
      <td>11</td>
      <td>track</td>
      <td>0.604</td>
      <td>7</td>
      <td>-6.484</td>
      <td>0.0308</td>
      <td>0.020100</td>
      <td>0.7520</td>
      <td>0.645</td>
      <td>123.067</td>
    </tr>
    <tr>
      <th>13</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Heaven Or Las Vegas (Remastered)</td>
      <td>37hHXJ7xas2Nb7Jbi8ip4E</td>
      <td>Cocteau Twins</td>
      <td>5Wabl1lPdNOeIn0SQ5A1mp</td>
      <td>192466</td>
      <td>6sVQNUvcVFTXvlk3ec0ngd</td>
      <td>Cherry-coloured Funk</td>
      <td>62</td>
      <td>1</td>
      <td>track</td>
      <td>0.377</td>
      <td>2</td>
      <td>-5.902</td>
      <td>0.0290</td>
      <td>0.000058</td>
      <td>0.1070</td>
      <td>0.365</td>
      <td>180.074</td>
    </tr>
    <tr>
      <th>14</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Sugar Tax</td>
      <td>1J8e1dLKVmZbsyxpGa9lGg</td>
      <td>Orchestral Manoeuvres In The Dark</td>
      <td>7wJ9NwdRWtN92NunmXuwBk</td>
      <td>269373</td>
      <td>2BXHeq9xOvSvMusfIfQLrn</td>
      <td>Speed Of Light</td>
      <td>32</td>
      <td>4</td>
      <td>track</td>
      <td>0.595</td>
      <td>7</td>
      <td>-13.543</td>
      <td>0.0327</td>
      <td>0.722000</td>
      <td>0.0496</td>
      <td>0.935</td>
      <td>122.751</td>
    </tr>
    <tr>
      <th>15</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Oshin</td>
      <td>1hSONHeTOofdeh2uoFBLgv</td>
      <td>DIIV</td>
      <td>4OrizGCKhOrW6iDDJHN9xd</td>
      <td>270173</td>
      <td>6cuF4OF8IGXXmbzeI4AHjH</td>
      <td>Air Conditioning</td>
      <td>35</td>
      <td>4</td>
      <td>track</td>
      <td>0.582</td>
      <td>7</td>
      <td>-6.592</td>
      <td>0.0319</td>
      <td>0.739000</td>
      <td>0.1020</td>
      <td>0.705</td>
      <td>119.947</td>
    </tr>
    <tr>
      <th>16</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>The Soft Bulletin</td>
      <td>0Gpcuci4CMpKHvZPM34KWg</td>
      <td>The Flaming Lips</td>
      <td>16eRpMNXSQ15wuJoeqguaB</td>
      <td>248733</td>
      <td>0bQB8vGtqWOvYu8MKLtMc7</td>
      <td>Race for the Prize - Remix</td>
      <td>0</td>
      <td>1</td>
      <td>track</td>
      <td>0.468</td>
      <td>0</td>
      <td>-5.854</td>
      <td>0.0444</td>
      <td>0.000807</td>
      <td>0.2750</td>
      <td>0.794</td>
      <td>129.963</td>
    </tr>
    <tr>
      <th>17</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Oshin</td>
      <td>1hSONHeTOofdeh2uoFBLgv</td>
      <td>DIIV</td>
      <td>4OrizGCKhOrW6iDDJHN9xd</td>
      <td>213493</td>
      <td>4oQUKT5VtopjNvy4d5rle9</td>
      <td>How Long Have You Known</td>
      <td>43</td>
      <td>5</td>
      <td>track</td>
      <td>0.384</td>
      <td>0</td>
      <td>-4.430</td>
      <td>0.0379</td>
      <td>0.899000</td>
      <td>0.1030</td>
      <td>0.372</td>
      <td>149.760</td>
    </tr>
    <tr>
      <th>18</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Slowdive</td>
      <td>4nSWX5A4xVomzrOEGDKLQ6</td>
      <td>Slowdive</td>
      <td>72X6FHxaShda0XeQw3vbeF</td>
      <td>413171</td>
      <td>6smJCC2En1iEuN3b9ZQnLX</td>
      <td>Slomo</td>
      <td>54</td>
      <td>1</td>
      <td>track</td>
      <td>0.190</td>
      <td>7</td>
      <td>-7.223</td>
      <td>0.0340</td>
      <td>0.022800</td>
      <td>0.1300</td>
      <td>0.358</td>
      <td>107.800</td>
    </tr>
    <tr>
      <th>19</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Modern Life Is Rubbish</td>
      <td>23mDNB6otna4M0e8MORCsZ</td>
      <td>Blur</td>
      <td>7MhMgCo0Bl0Kukl93PZbYS</td>
      <td>261240</td>
      <td>3UFAFyRvZ4gFORAoI1pFWQ</td>
      <td>For Tomorrow - 2012 Remaster</td>
      <td>34</td>
      <td>1</td>
      <td>track</td>
      <td>0.631</td>
      <td>2</td>
      <td>-6.025</td>
      <td>0.0285</td>
      <td>0.000000</td>
      <td>0.0712</td>
      <td>0.653</td>
      <td>95.849</td>
    </tr>
    <tr>
      <th>20</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Shapeshifting</td>
      <td>3DyIAjq1iOl07Z1IV39Py6</td>
      <td>Young Galaxy</td>
      <td>5xfJLyvC5UElVSiMuLt1ss</td>
      <td>258959</td>
      <td>09sWl4xvgOI4cJQTU2OIeK</td>
      <td>PHANTOMS</td>
      <td>0</td>
      <td>8</td>
      <td>track</td>
      <td>0.721</td>
      <td>0</td>
      <td>-8.734</td>
      <td>0.0266</td>
      <td>0.129000</td>
      <td>0.2800</td>
      <td>0.903</td>
      <td>114.004</td>
    </tr>
    <tr>
      <th>21</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Sugar Tax</td>
      <td>1J8e1dLKVmZbsyxpGa9lGg</td>
      <td>Orchestral Manoeuvres In The Dark</td>
      <td>7wJ9NwdRWtN92NunmXuwBk</td>
      <td>257026</td>
      <td>1vAc7e2NMqn9dBNErT9pSd</td>
      <td>Then You Turn Away</td>
      <td>25</td>
      <td>3</td>
      <td>track</td>
      <td>0.524</td>
      <td>0</td>
      <td>-16.758</td>
      <td>0.0492</td>
      <td>0.002590</td>
      <td>0.0529</td>
      <td>0.720</td>
      <td>188.344</td>
    </tr>
    <tr>
      <th>22</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Shapeshifting</td>
      <td>3DyIAjq1iOl07Z1IV39Py6</td>
      <td>Young Galaxy</td>
      <td>5xfJLyvC5UElVSiMuLt1ss</td>
      <td>276733</td>
      <td>7Kqxiy7IJBbLX4bTl2muTx</td>
      <td>WE HAVE EVERYTHING</td>
      <td>0</td>
      <td>4</td>
      <td>track</td>
      <td>0.592</td>
      <td>5</td>
      <td>-8.031</td>
      <td>0.0363</td>
      <td>0.504000</td>
      <td>0.3160</td>
      <td>0.187</td>
      <td>125.017</td>
    </tr>
    <tr>
      <th>23</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Modern Life Is Rubbish</td>
      <td>23mDNB6otna4M0e8MORCsZ</td>
      <td>Blur</td>
      <td>7MhMgCo0Bl0Kukl93PZbYS</td>
      <td>234653</td>
      <td>6Gf8mol1cgqSlNzE1mfQng</td>
      <td>Blue Jeans - 2012 Remaster</td>
      <td>35</td>
      <td>6</td>
      <td>track</td>
      <td>0.650</td>
      <td>1</td>
      <td>-8.203</td>
      <td>0.0275</td>
      <td>0.000000</td>
      <td>0.0859</td>
      <td>0.780</td>
      <td>106.545</td>
    </tr>
    <tr>
      <th>24</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Oshin</td>
      <td>1hSONHeTOofdeh2uoFBLgv</td>
      <td>DIIV</td>
      <td>4OrizGCKhOrW6iDDJHN9xd</td>
      <td>141626</td>
      <td>4iYLm12jWtCXAliKWKULeB</td>
      <td>Past Lives</td>
      <td>37</td>
      <td>2</td>
      <td>track</td>
      <td>0.274</td>
      <td>7</td>
      <td>-6.060</td>
      <td>0.0353</td>
      <td>0.900000</td>
      <td>0.1380</td>
      <td>0.552</td>
      <td>158.123</td>
    </tr>
    <tr>
      <th>25</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Oshin</td>
      <td>1hSONHeTOofdeh2uoFBLgv</td>
      <td>DIIV</td>
      <td>4OrizGCKhOrW6iDDJHN9xd</td>
      <td>195946</td>
      <td>2Nr6UNslucObroRSYATsTb</td>
      <td>Wait</td>
      <td>34</td>
      <td>6</td>
      <td>track</td>
      <td>0.162</td>
      <td>0</td>
      <td>-5.717</td>
      <td>0.0503</td>
      <td>0.968000</td>
      <td>0.1570</td>
      <td>0.305</td>
      <td>152.662</td>
    </tr>
    <tr>
      <th>26</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Oracular Spectacular</td>
      <td>6mm1Skz3JE6AXneya9Nyiv</td>
      <td>MGMT</td>
      <td>0SwO7SWeDHJijQ3XNS7xEE</td>
      <td>302840</td>
      <td>1jJci4qxiYcOHhQR247rEU</td>
      <td>Kids</td>
      <td>78</td>
      <td>5</td>
      <td>track</td>
      <td>0.451</td>
      <td>9</td>
      <td>-3.871</td>
      <td>0.0719</td>
      <td>0.004900</td>
      <td>0.3610</td>
      <td>0.172</td>
      <td>122.961</td>
    </tr>
    <tr>
      <th>27</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Oshin</td>
      <td>1hSONHeTOofdeh2uoFBLgv</td>
      <td>DIIV</td>
      <td>4OrizGCKhOrW6iDDJHN9xd</td>
      <td>177480</td>
      <td>368PzbpA8KgzRUN9dl8nMj</td>
      <td>Human</td>
      <td>38</td>
      <td>3</td>
      <td>track</td>
      <td>0.351</td>
      <td>5</td>
      <td>-3.682</td>
      <td>0.0401</td>
      <td>0.819000</td>
      <td>0.0769</td>
      <td>0.574</td>
      <td>147.955</td>
    </tr>
    <tr>
      <th>28</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Oshin</td>
      <td>1hSONHeTOofdeh2uoFBLgv</td>
      <td>DIIV</td>
      <td>4OrizGCKhOrW6iDDJHN9xd</td>
      <td>185600</td>
      <td>1GUQMJtIIMBLoEJNyDGA0X</td>
      <td>Sometime</td>
      <td>37</td>
      <td>10</td>
      <td>track</td>
      <td>0.341</td>
      <td>7</td>
      <td>-4.366</td>
      <td>0.1030</td>
      <td>0.759000</td>
      <td>0.1810</td>
      <td>0.293</td>
      <td>142.894</td>
    </tr>
    <tr>
      <th>29</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Oracular Spectacular</td>
      <td>6mm1Skz3JE6AXneya9Nyiv</td>
      <td>MGMT</td>
      <td>0SwO7SWeDHJijQ3XNS7xEE</td>
      <td>229640</td>
      <td>3FtYbEfBqAlGO46NUDQSAt</td>
      <td>Electric Feel</td>
      <td>78</td>
      <td>4</td>
      <td>track</td>
      <td>0.763</td>
      <td>1</td>
      <td>-3.714</td>
      <td>0.0350</td>
      <td>0.280000</td>
      <td>0.3480</td>
      <td>0.559</td>
      <td>103.038</td>
    </tr>
    <tr>
      <th>30</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>L'absente</td>
      <td>051QaBZ0LcZ7ZOM35VX8R1</td>
      <td>Yann Tiersen</td>
      <td>00sazWvoTLOqg5MFwC68Um</td>
      <td>200893</td>
      <td>3gvwAVadz2RepD5XKmXgd7</td>
      <td>Les Jours tristes</td>
      <td>34</td>
      <td>6</td>
      <td>track</td>
      <td>0.661</td>
      <td>7</td>
      <td>-9.116</td>
      <td>0.0274</td>
      <td>0.000002</td>
      <td>0.1010</td>
      <td>0.919</td>
      <td>100.137</td>
    </tr>
    <tr>
      <th>31</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>I Say I Say I Say</td>
      <td>2vMrYs6XOrLi4SfB9Qz2Vj</td>
      <td>Erasure</td>
      <td>0z5DFXmhT4ZNzWElsM7V89</td>
      <td>237840</td>
      <td>5tidDcllxdgwgS1CiFXWfN</td>
      <td>Always</td>
      <td>0</td>
      <td>6</td>
      <td>track</td>
      <td>0.601</td>
      <td>5</td>
      <td>-9.900</td>
      <td>0.0285</td>
      <td>0.000002</td>
      <td>0.2400</td>
      <td>0.322</td>
      <td>103.027</td>
    </tr>
    <tr>
      <th>32</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Shapeshifting</td>
      <td>3DyIAjq1iOl07Z1IV39Py6</td>
      <td>Young Galaxy</td>
      <td>5xfJLyvC5UElVSiMuLt1ss</td>
      <td>216760</td>
      <td>6V0PviadnezDpfKfzi7XeD</td>
      <td>PERIPHERAL VISIONARIES</td>
      <td>0</td>
      <td>6</td>
      <td>track</td>
      <td>0.741</td>
      <td>4</td>
      <td>-8.804</td>
      <td>0.0351</td>
      <td>0.073600</td>
      <td>0.1300</td>
      <td>0.760</td>
      <td>101.656</td>
    </tr>
    <tr>
      <th>33</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Awake</td>
      <td>7HWdGPosPkb9GY5MOgLgSW</td>
      <td>Tycho</td>
      <td>5oOhM2DFWab8XhSdQiITry</td>
      <td>283636</td>
      <td>2qC1sUo8xxRRqYsaYEdDuZ</td>
      <td>Awake</td>
      <td>60</td>
      <td>1</td>
      <td>track</td>
      <td>0.552</td>
      <td>1</td>
      <td>-5.767</td>
      <td>0.0353</td>
      <td>0.892000</td>
      <td>0.0746</td>
      <td>0.598</td>
      <td>176.057</td>
    </tr>
    <tr>
      <th>34</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Bona Drag</td>
      <td>5Qng7ZFQVDYd4we51XHCPZ</td>
      <td>Morrissey</td>
      <td>3iTsJGG39nMg9YiolUgLMQ</td>
      <td>229653</td>
      <td>4NBHGVwhNW4uCCWLI1UeV0</td>
      <td>Hairdresser on Fire - 2010 Remaster</td>
      <td>38</td>
      <td>8</td>
      <td>track</td>
      <td>0.654</td>
      <td>5</td>
      <td>-6.640</td>
      <td>0.0352</td>
      <td>0.038000</td>
      <td>0.0642</td>
      <td>0.644</td>
      <td>135.222</td>
    </tr>
    <tr>
      <th>35</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Shapeshifting</td>
      <td>3DyIAjq1iOl07Z1IV39Py6</td>
      <td>Young Galaxy</td>
      <td>5xfJLyvC5UElVSiMuLt1ss</td>
      <td>305413</td>
      <td>0k1o64rvuUDvOvVmNhdCcb</td>
      <td>FOR DEAR LIFE</td>
      <td>0</td>
      <td>5</td>
      <td>track</td>
      <td>0.733</td>
      <td>11</td>
      <td>-7.192</td>
      <td>0.0297</td>
      <td>0.077400</td>
      <td>0.0884</td>
      <td>0.415</td>
      <td>122.024</td>
    </tr>
    <tr>
      <th>36</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Super</td>
      <td>5L7Ph5PhFtwfE4lVtiPjDJ</td>
      <td>Pet Shop Boys</td>
      <td>2ycnb8Er79LoH2AsR5ldjh</td>
      <td>244626</td>
      <td>1JBqmBdxUpClYmW3zCCHCW</td>
      <td>Happiness</td>
      <td>32</td>
      <td>1</td>
      <td>track</td>
      <td>0.679</td>
      <td>9</td>
      <td>-6.035</td>
      <td>0.0360</td>
      <td>0.062200</td>
      <td>0.1280</td>
      <td>0.555</td>
      <td>119.987</td>
    </tr>
    <tr>
      <th>37</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Shapeshifting</td>
      <td>3DyIAjq1iOl07Z1IV39Py6</td>
      <td>Young Galaxy</td>
      <td>5xfJLyvC5UElVSiMuLt1ss</td>
      <td>219653</td>
      <td>65AVttOPJzqGWLAaE3jpGs</td>
      <td>THE ANGELS ARE SURELY WEEPING (FEATURING HANNA)</td>
      <td>0</td>
      <td>2</td>
      <td>track</td>
      <td>0.693</td>
      <td>0</td>
      <td>-10.604</td>
      <td>0.0346</td>
      <td>0.137000</td>
      <td>0.1460</td>
      <td>0.561</td>
      <td>87.992</td>
    </tr>
    <tr>
      <th>38</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Shapeshifting</td>
      <td>3DyIAjq1iOl07Z1IV39Py6</td>
      <td>Young Galaxy</td>
      <td>5xfJLyvC5UElVSiMuLt1ss</td>
      <td>203800</td>
      <td>0jqM3evfM7ueY8X00Qi9hZ</td>
      <td>BLOWN MINDED</td>
      <td>0</td>
      <td>3</td>
      <td>track</td>
      <td>0.687</td>
      <td>7</td>
      <td>-7.585</td>
      <td>0.0301</td>
      <td>0.531000</td>
      <td>0.3630</td>
      <td>0.964</td>
      <td>122.017</td>
    </tr>
    <tr>
      <th>39</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Is the Is Are</td>
      <td>5PPPABn2aZ0jRuHPMONwSR</td>
      <td>DIIV</td>
      <td>4OrizGCKhOrW6iDDJHN9xd</td>
      <td>188050</td>
      <td>5Jl1QnoXsEsRJOOoXHApab</td>
      <td>Out of Mind</td>
      <td>46</td>
      <td>1</td>
      <td>track</td>
      <td>0.478</td>
      <td>11</td>
      <td>-5.342</td>
      <td>0.0542</td>
      <td>0.892000</td>
      <td>0.0541</td>
      <td>0.546</td>
      <td>154.896</td>
    </tr>
    <tr>
      <th>40</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Dive</td>
      <td>3I3PmRvn5iFY8i6zzvEcci</td>
      <td>Tycho</td>
      <td>5oOhM2DFWab8XhSdQiITry</td>
      <td>316919</td>
      <td>6koWevx9MqN6efQ6qreIbm</td>
      <td>A Walk</td>
      <td>56</td>
      <td>1</td>
      <td>track</td>
      <td>0.659</td>
      <td>9</td>
      <td>-7.816</td>
      <td>0.0317</td>
      <td>0.909000</td>
      <td>0.1270</td>
      <td>0.382</td>
      <td>139.952</td>
    </tr>
    <tr>
      <th>41</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>I Say I Say I Say</td>
      <td>2vMrYs6XOrLi4SfB9Qz2Vj</td>
      <td>Erasure</td>
      <td>0z5DFXmhT4ZNzWElsM7V89</td>
      <td>242600</td>
      <td>7xuiWKWdKLN66Q6AGLktHT</td>
      <td>I Love Saturday</td>
      <td>0</td>
      <td>2</td>
      <td>track</td>
      <td>0.650</td>
      <td>5</td>
      <td>-8.809</td>
      <td>0.0304</td>
      <td>0.002190</td>
      <td>0.1020</td>
      <td>0.803</td>
      <td>126.969</td>
    </tr>
    <tr>
      <th>42</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Sugar Tax</td>
      <td>1J8e1dLKVmZbsyxpGa9lGg</td>
      <td>Orchestral Manoeuvres In The Dark</td>
      <td>7wJ9NwdRWtN92NunmXuwBk</td>
      <td>269493</td>
      <td>4DeY3I31BqPlwnqQwx3I7J</td>
      <td>Was It Something I Said</td>
      <td>31</td>
      <td>5</td>
      <td>track</td>
      <td>0.638</td>
      <td>7</td>
      <td>-15.574</td>
      <td>0.0311</td>
      <td>0.006560</td>
      <td>0.2420</td>
      <td>0.490</td>
      <td>106.097</td>
    </tr>
    <tr>
      <th>43</th>
      <td>NaN</td>
      <td>COMPILATION</td>
      <td>Dallas Buyers Club (Music From And Inspired By...</td>
      <td>5Eq7NDVWgHCPnkLloTg36m</td>
      <td>Marc Bolan</td>
      <td>4M2gGLzKCo0rPyn224PsoN</td>
      <td>152506</td>
      <td>4ffwnwWnUGd9UhoIsx60PS</td>
      <td>Life Is Strange</td>
      <td>21</td>
      <td>16</td>
      <td>track</td>
      <td>0.519</td>
      <td>7</td>
      <td>-6.653</td>
      <td>0.0535</td>
      <td>0.000000</td>
      <td>0.2260</td>
      <td>0.519</td>
      <td>121.356</td>
    </tr>
    <tr>
      <th>44</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Oracular Spectacular</td>
      <td>6mm1Skz3JE6AXneya9Nyiv</td>
      <td>MGMT</td>
      <td>0SwO7SWeDHJijQ3XNS7xEE</td>
      <td>261000</td>
      <td>4iG2gAwKXsOcijVaVXzRPW</td>
      <td>Time to Pretend</td>
      <td>71</td>
      <td>1</td>
      <td>track</td>
      <td>0.438</td>
      <td>2</td>
      <td>-3.249</td>
      <td>0.0452</td>
      <td>0.077700</td>
      <td>0.3000</td>
      <td>0.421</td>
      <td>100.990</td>
    </tr>
    <tr>
      <th>45</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Bona Drag</td>
      <td>5Qng7ZFQVDYd4we51XHCPZ</td>
      <td>Morrissey</td>
      <td>3iTsJGG39nMg9YiolUgLMQ</td>
      <td>168640</td>
      <td>0QKJ51hneDJaX38ri11kYB</td>
      <td>Such a Little Thing Makes Such a Big Differenc...</td>
      <td>26</td>
      <td>5</td>
      <td>track</td>
      <td>0.632</td>
      <td>1</td>
      <td>-6.190</td>
      <td>0.0255</td>
      <td>0.000124</td>
      <td>0.2260</td>
      <td>0.594</td>
      <td>109.289</td>
    </tr>
    <tr>
      <th>46</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Shapeshifting</td>
      <td>3DyIAjq1iOl07Z1IV39Py6</td>
      <td>Young Galaxy</td>
      <td>5xfJLyvC5UElVSiMuLt1ss</td>
      <td>298146</td>
      <td>3pg1g1xDpRKmd36RRCQkyo</td>
      <td>B.S.E.</td>
      <td>0</td>
      <td>10</td>
      <td>track</td>
      <td>0.554</td>
      <td>0</td>
      <td>-7.776</td>
      <td>0.0367</td>
      <td>0.118000</td>
      <td>0.0895</td>
      <td>0.375</td>
      <td>101.936</td>
    </tr>
    <tr>
      <th>47</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>It</td>
      <td>0stBtK28A7JaZAyzOz39HY</td>
      <td>Pulp</td>
      <td>36E7oYfz3LLRto6l2WmDcD</td>
      <td>257173</td>
      <td>6IxxaTqEjzdQkEKKBufB0V</td>
      <td>Wishful Thinking</td>
      <td>0</td>
      <td>2</td>
      <td>track</td>
      <td>0.494</td>
      <td>5</td>
      <td>-11.966</td>
      <td>0.0296</td>
      <td>0.009930</td>
      <td>0.1470</td>
      <td>0.154</td>
      <td>126.988</td>
    </tr>
    <tr>
      <th>48</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Principios Basicos De Astronomia</td>
      <td>6wMqAOwrW6E8FkSGBXKGVe</td>
      <td>Los Planetas</td>
      <td>0N1TIXCk9Q9JbEPXQDclEL</td>
      <td>276173</td>
      <td>4GTRPlsyZ0bnfZHCBpRX8I</td>
      <td>Corrientes Circulares En El Tiempo</td>
      <td>22</td>
      <td>10</td>
      <td>track</td>
      <td>0.520</td>
      <td>3</td>
      <td>-8.551</td>
      <td>0.0300</td>
      <td>0.869000</td>
      <td>0.1360</td>
      <td>0.560</td>
      <td>122.829</td>
    </tr>
  </tbody>
</table>
</div>
</div>

</div>

</div>
</div>

## Extras

- [Repo](https://github.com/lelesgaray/spotify) of the project.
- 2 great songs that keep coming to my mind in these odd, strange days that we are living.
{% include video id="0_z_UEuEMAo" provider="youtube" %}
<br>
{% include video id="NleFEDHmdhs" provider="youtube" %}
