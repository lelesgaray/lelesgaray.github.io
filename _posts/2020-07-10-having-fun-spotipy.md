---
title: "Having fun with Spotipy!"
date: 2020-07-10T15:34:30-04:00
categories:
  - Blog
tags:
  - Music
  - EDA
toc: true
toc_label: "Table of Contents"
toc_icon: "cog"
header: 
  teaser: "/assets/images/spotify_pic.jpg"
---

{% include figure image_path="/assets/images/spotify_pic.jpg" alt="this is a placeholder image" caption="Spotify API" %}

This post explores some of the Spotipy possibilities and analyses.

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
```
> In case you are struggling to get the `user`, `client_id` and `client_secret`, I recommend this [video](https://www.youtube.com/watch?v=yxSKrVXeKcI) and this [tutorial](https://support.appreciationengine.com/article/oaNki9g06n-creating-a-spotify-application) to help you configure your app.

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
      <th>1</th>
      <td>NaN</td>
      <td>COMPILATION</td>
      <td>Study and Focus: White, Brown and Pink Noise C...</td>
      <td>6r3avwTFKjLlXlNMIamtSr</td>
      <td>Background Noise From TraxLab</td>
      <td>3GsEeenZ3Kr4LSdWmJB47J</td>
      <td>137730</td>
      <td>4BSP1PK4sLlticRfYl1M79</td>
      <td>Noise Cancelling Machine</td>
      <td>2</td>
      <td>15</td>
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
      <th>3</th>
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
    <tr>
      <th>9</th>
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
    </tr>
    <tr>
      <th>10</th>
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
    </tr>
    <tr>
      <th>11</th>
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
    </tr>
    <tr>
      <th>12</th>
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
    </tr>
    <tr>
      <th>13</th>
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
    </tr>
    <tr>
      <th>14</th>
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
    </tr>
    <tr>
      <th>15</th>
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
    </tr>
    <tr>
      <th>16</th>
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
    </tr>
    <tr>
      <th>17</th>
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
    </tr>
    <tr>
      <th>18</th>
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
    </tr>
    <tr>
      <th>19</th>
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
    </tr>
    <tr>
      <th>20</th>
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
    </tr>
    <tr>
      <th>21</th>
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
    </tr>
    <tr>
      <th>22</th>
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
    </tr>
    <tr>
      <th>23</th>
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
    </tr>
    <tr>
      <th>24</th>
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
    </tr>
    <tr>
      <th>25</th>
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
    </tr>
    <tr>
      <th>26</th>
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
    </tr>
    <tr>
      <th>27</th>
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
    </tr>
    <tr>
      <th>28</th>
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
    </tr>
    <tr>
      <th>29</th>
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
    </tr>
    <tr>
      <th>30</th>
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
    </tr>
    <tr>
      <th>31</th>
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
    </tr>
    <tr>
      <th>32</th>
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
    </tr>
    <tr>
      <th>33</th>
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
    </tr>
    <tr>
      <th>34</th>
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
    </tr>
    <tr>
      <th>35</th>
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
    </tr>
    <tr>
      <th>36</th>
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
    </tr>
    <tr>
      <th>37</th>
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
    </tr>
    <tr>
      <th>38</th>
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
    </tr>
    <tr>
      <th>39</th>
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
    </tr>
    <tr>
      <th>40</th>
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
    </tr>
    <tr>
      <th>41</th>
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
    </tr>
    <tr>
      <th>42</th>
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
    </tr>
    <tr>
      <th>43</th>
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
    </tr>
    <tr>
      <th>44</th>
      <td>NaN</td>
      <td>COMPILATION</td>
      <td>Dallas Buyers Club (Music From And Inspired By...</td>
      <td>5Eq7NDVWgHCPnkLloTg36m</td>
      <td>Marc Bolan</td>
      <td>4M2gGLzKCo0rPyn224PsoN</td>
      <td>152506</td>
      <td>4ffwnwWnUGd9UhoIsx60PS</td>
      <td>Life Is Strange</td>
      <td>22</td>
      <td>16</td>
      <td>track</td>
    </tr>
    <tr>
      <th>45</th>
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
    </tr>
    <tr>
      <th>46</th>
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
    </tr>
    <tr>
      <th>47</th>
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
    </tr>
    <tr>
      <th>48</th>
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
    </tr>
    <tr>
      <th>49</th>
      <td>NaN</td>
      <td>ALBUM</td>
      <td>Principios Basicos De Astronomia</td>
      <td>6wMqAOwrW6E8FkSGBXKGVe</td>
      <td>Los Planetas</td>
      <td>0N1TIXCk9Q9JbEPXQDclEL</td>
      <td>276173</td>
      <td>4GTRPlsyZ0bnfZHCBpRX8I</td>
      <td>Corrientes Circulares En El Tiempo</td>
      <td>23</td>
      <td>10</td>
      <td>track</td>
    </tr>
  </tbody>
</table>
</div>


You'll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

```python
# write a simple Python function
def function(number):
  result = number +1
  return result

```

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
