---
layout: post
title:  "The Perfect Song"
author: Brady Richardson
description: Using the Spotify API to identify the perfect song specifically for me.
image: "/assets/images/Spotify_Full_Logo_RGB_Green.png"
---

# Finding new music used to be such a hard problem...until I threw Python at it!

![Spotify Logo](/assets/images/Spotify_Full_Logo_RGB_Green.png)

## Introduction

I love music. I love finding new music. I love finding new music that I'll love. But finding new music is hard. It takes time. It takes effort. It takes patience.

So, considering all of this, I decided to use a little bit of Python to make it a little bit easier!


## Motivating Question

I was motivated originally by the question, "What is the perfect song for me?" It later became, "What is the perfect newly released song for me" I wanted to determine a new song that best fit my tastes by building an algorithm that incorporated my
personal preferences as well as the top songs across the US and the world, and this is the result!

## Ethical Considerations

First of all, the ethics of gathering Spotify data are pretty straightforward as they are laid out pretty clearly in the docs. According to the Spotify documentation, there are several requirements that they have for users who want to use their API (they vary between endpoints, these are the ones that applied in my case):

1. Spotify content may not be downloaded (note that this does not mean the data, but the actual audio files)
2. Visual content must not be altered
3. Proper attribution must be given to both Spotify and the artists associated with the content that is being used
4. Content cannot be used to train a machine learning model

All of these requirements were adhered to when gathering the data for this project.

## Data Collection Process

Alright, let's get into the nitty gritty of this process:

### Step 1: Creating an app in the Spotify Developer Dashboard
This is a really straightforward step that I will just gloss over. Basically, you go to https://developer.spotify.com/, click on the **Log In** button on the top right, and once you're logged in, click on the profile button on the top right and select **Dashboard**. From there, you can click on the **Create an App** button. After filling out the app info, make sure to include a redirect URL that you can easily access for development (you can change it later, but I just used `https://localhost:3000`). Finally, click `Save` and you should be redirected to the app dashboard. That's all we need to do for this step!

### Step 2: Authenticating with Spotify

Honestly, this was the most difficult step in the whole process. The first part of the whole process is to authenticate with Spotify. Spotify follows a PKCE (Proof Key for Code Exchange)-based authentication process, which is a fancy way of saying that they want to make sure that the app requesting the data is actually the app that you think it is. For the sake of this blog post, I'm not going to go into the details of how this works, but if you're interested, here is a link to learn more about it:

[PKCE Authentication](https://auth0.com/docs/get-started/authentication-and-authorization-flow/authorization-code-flow-with-pkce)

If you don't really care about the details, just know that the code is already written in the [GitHub](https://github.com/bradyrichardson/spotify) repo (link at the bottom). All that you have to do is include your client ID in the `.env` file as **SPOTIFY_CLIENT_ID**.

### Step 3: Gathering Data

Once you've successfully created your app and authenticated, the only thing left to do is to actually get the data! Spotify offers a wide variety of endpoints for all of the data that they collect. For this project, I used the following endpoints (code provided in dropdown sections):

![Get User Profile](/assets/images/get_profile.png)
<details style="margin-bottom: 20px" style="margin-bottom: 10px">
<summary>Get User Profile</summary>

```python
async def get_profile(token: str) -> dict:
    url = "https://api.spotify.com/v1/me"
    headers = {
        "Authorization": f"Bearer {token}"
    }
    
    response = requests.get(url, headers=headers)
    
    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f"Failed to get profile: {response.status_code} - {response.text}")
```
</details>

![Get Top Tracks](/assets/images/get_top_items.png) 
<details style="margin-bottom: 20px">
<summary>Get Top Tracks</summary>

```python
async def get_top_tracks(token: str, limit: int = 5, time_range: str = "short_term", offset: int = 0) -> dict:
    url = "https://api.spotify.com/v1/me/top/tracks"
    headers = {
        "Authorization": f"Bearer {token}"
    }
    params = {
        "limit": limit,
        "time_range": time_range,
        "offset": offset
    }
    
    response = requests.get(url, headers=headers, params=params)
    
    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f"Failed to get top tracks: {response.status_code} - {response.text}")
```
</details>

![Search For Item](/assets/images/search_for_item.png)
![Get Audio Features](/assets/images/get_audio_features.png)
<details style="margin-bottom: 20px">
<summary>Search For Item and Get Audio Features</summary>

```python
async def get_track_data(token: str, track_name: str, artist_name: str) -> dict:
    # first, search for the track
    url = 'https://api.spotify.com/v1/search'
    headers = {
        'Authorization': f'Bearer {token}'
    }
    params = {
        'q': f'track:{track_name} artist:{artist_name}',
        'type': 'track',
        'limit': 1
    }
    
    response = requests.get(url, headers=headers, params=params)
    if response.status_code != 200:
        raise Exception(f"Search failed: {response.status_code} - {response.text}")
    
    search_results = response.json()
    if not search_results['tracks']['items']:
        print(f"No results found for {track_name} by {artist_name}")
        return None
    
    track = search_results['tracks']['items'][0]
    
    # get audio features for the track
    audio_features_url = f"https://api.spotify.com/v1/audio-features/{track['id']}"
    features_response = requests.get(audio_features_url, headers=headers)
    if features_response.status_code == 200:
        audio_features = features_response.json()
    else:
        audio_features = None
        print(f"Could not get audio features for {track_name}")
    
    # combine track data with audio features
    track_data = {
        'name': track['name'],
        'artists': ', '.join(artist['name'] for artist in track['artists']),
        'album': track['album']['name'],
        'release_date': track['album']['release_date'],
        'popularity': track['popularity'],
        'duration_ms': track['duration_ms'],
        'explicit': track['explicit'],
        'preview_url': track['preview_url'],
        'external_url': track['external_urls']['spotify'],
        'track_id': track['id']
    }
    
    # if available we add the audio features
    if audio_features:
        features_to_include = [
            'danceability', 'energy', 'key', 'loudness', 'mode',
            'speechiness', 'acousticness', 'instrumentalness',
            'liveness', 'valence', 'tempo', 'time_signature'
        ]
        for feature in features_to_include:
            track_data[feature] = audio_features[feature]
    
    return track_data
```
</details>

![Get Playlist](/assets/images/get_playlist.png)
<details style="margin-bottom: 20px">
<summary>Get Playlist</summary>

```python
async def get_spotify_playlist(token: str, id: str) -> dict:
    url = f"https://api.spotify.com/v1/playlists/{id}" # the id corresponds to different playlists, you can find them in the link Spotify provides when sharing playlists
    headers = {
        "Authorization": f"Bearer {token}"
    }
    
    response = requests.get(url, headers=headers)
    
    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f"Failed to get global tracks: {response.status_code} - {response.text}")
```
</details>

Once you have your requests set up, it's as simple as calling the functions and storing the results in a dataframe! For my project specifically, I created a main() function that first authenticates, then it gathers my top 100 personal tracks, the top 50 global tracks, the top 50 USA tracks, and the new releases from the past week.

<details style="margin-bottom: 20px">
<summary>Main Function</summary>

```python
async def main():
    verifier = await redirect_to_auth_code_flow(CLIENT_ID)
    print("\nAfter authorizing in your browser, copy the 'code' parameter from the URL")
    print("The URL will look like: https://localhost:3000?code=SOMETHING, where https://localhost:3000 will be the url you entered as the Spotify redirect uri when you created the Spotify app on the developer website")
    
    auth_code = input("Enter the code from the URL: ")
    
    access_token = await get_access_token(CLIENT_ID, auth_code, verifier)
    print("Successfully got access token!")
    
    # get my personal top 100 tracks
    top_tracks = []

    for i in range(5):
        items = await get_top_tracks(access_token, limit=20, offset=i*20)
        top_tracks.extend(items['items'])

    print("\nYour Top 100 Tracks:")
    print("-----------------")
    for i, track in enumerate(top_tracks, 1):
        artists = ", ".join(artist['name'] for artist in track['artists'])
    
    # get my personal track data
    track_data = []
    for track in top_tracks:
        track_data.append(await get_track_data(access_token, track['name'], track['artists'][0]['name']))

    # get global top 50 tracks
    global_playlist = await get_spotify_playlist(access_token, '37i9dQZEVXbMDoHDwVN2tF') # global top 50 playlist id
    global_tracks = global_playlist['tracks']['items']
    
    print("\nGlobal Top 50 Tracks:")
    print("-----------------")
    for i, item in enumerate(global_tracks, 1):
        track = item['track']
        artists = ", ".join(artist['name'] for artist in track['artists'])
        print(f"{i}. {track['name']} by {artists}")
    
    # get track data for global tracks
    global_track_data = []
    for item in global_tracks:
        track = item['track']
        global_track_data.append(await get_track_data(access_token, track['name'], track['artists'][0]['name']))

    # get usa top 50 tracks
    usa_playlist = await get_spotify_playlist(access_token, '37i9dQZEVXbLp5XoPON0wI') # usa top 50 playlist id
    usa_tracks = usa_playlist['tracks']['items']
    
    print("\nUSA Top 50 Tracks:")
    print("-----------------")
    for i, item in enumerate(usa_tracks, 1):
        track = item['track']
        artists = ", ".join(artist['name'] for artist in track['artists'])
    
    # get track data for usa tracks
    usa_track_data = []
    for item in usa_tracks:
        track = item['track']
        usa_track_data.append(await get_track_data(access_token, track['name'], track['artists'][0]['name']))

    # get new releases
    new_releases = await get_spotify_playlist(access_token, '37i9dQZF1DX4JAvHpjipBk') # new music friday playlist id
    new_releases_tracks = new_releases['tracks']['items']

    # get track data for new releases
    new_releases_track_data = []
    for item in new_releases_tracks:
        track = item['track']
        data = await get_track_data(access_token, track['name'], track['artists'][0]['name'])
        if data:
            new_releases_track_data.append(data)

    return track_data, global_track_data, usa_track_data, new_releases_track_data
```
</details>

To run it, just call `main()`!

```python
my_track_data, global_track_data, usa_track_data, new_track_data = await main()
```

Then get the dataframes:

<details style="margin-bottom: 20px">
<summary>Get Dataframes</summary>

```python
my_track_data_df = pd.DataFrame(my_track_data)
my_track_data_df.to_csv('my_top_100_tracks.csv', index=False)

global_track_data_df = pd.DataFrame(global_track_data)
global_track_data_df.to_csv('global_top_50_tracks.csv', index=False)

usa_track_data_df = pd.DataFrame(usa_track_data)
usa_track_data_df.to_csv('usa_top_50_tracks.csv', index=False)

new_track_data_df = pd.DataFrame(new_track_data)
new_track_data_df.to_csv('new_releases_tracks.csv', index=False)
```
</details>


Now in order to get the best song according to all of the top tracks we have gathered, we combine the first three dataframes, take the average of each numerical feature, and then dot product the resulting vector with the new releases dataframe. Because of the way that dot products work, we know that the maximum value will correspond to the best song!

<details style="margin-bottom: 20px">
<summary>Get Best Song</summary>

```python
combined_df = pd.concat([my_track_data_df, global_track_data_df, usa_track_data_df])
feature_averages = combined_df.describe().loc['mean']

numeric_cols = combined_df.select_dtypes(include=['number']).columns.tolist()

# using dot products to measure similarity
dot_prods = new_track_data_df[numeric_cols].dot(feature_averages)

best_track_idx = dot_prods.idxmax()
best_track = new_track_data_df.iloc[best_track_idx]
```
</details>
And voila, we have our best song!

For me, the recommended song as of 11/12/24 is:

<iframe style="border-radius:12px" src="https://open.spotify.com/embed/track/6aC4vyxBpqYo39ym2cNOtw?utm_source=generator" width="100%" height="352" frameBorder="0" allowfullscreen="" allow="autoplay; clipboard-write; encrypted-media; fullscreen; picture-in-picture" loading="lazy"></iframe>

I actually don't love this song, though I believe this one was recommended because I listen to a lot of rap and a lot of folk music. This project was not in vain, though, as I actually found a song I like better in the new releases playlist while working on this project.

## Dataset Overview

My first dataset contains 200 total tracks, and the second dataset contains about 100 tracks. The dataset features are as follows:

| Column Name | Description |
|------------|-------------|
| name | The name of the track |
| artists | The artist(s) who performed the track |
| album | The album the track appears on |
| release_date | The date the track was released |
| popularity | Spotify's popularity score (0-100) |
| duration_ms | Length of the track in milliseconds |
| explicit | Whether the track has explicit content |
| preview_url | URL to a 30-second preview of the track |
| external_url | Spotify URL for the track |
| track_id | Unique Spotify identifier for the track |
| danceability | How suitable the track is for dancing (0.0-1.0) |
| energy | Perceptual measure of intensity and activity (0.0-1.0) |
| key | The key the track is in (0-11, mapping to pitch class notation) |
| loudness | Overall loudness in decibels (-60.0-0.0) |
| mode | Modality of the track (major=1, minor=0) |
| speechiness | Presence of spoken words (0.0-1.0) |
| acousticness | Confidence measure of whether the track is acoustic (0.0-1.0) |
| instrumentalness | Predicts whether a track contains no vocals (0.0-1.0) |
| liveness | Detects presence of audience in recording (0.0-1.0) |
| valence | Musical positiveness conveyed by the track (0.0-1.0) |
| tempo | Overall estimated tempo in beats per minute (BPM) |
| time_signature | Estimated time signature (3-7 indicating 3/4, 4/4, etc.) |

### Key Statistics

Total sample size: 200

Here is the summary of the data by using the `.describe()` method:

### Numerical Variables

| Feature | Count | Mean | Std | Min | 25% | 50% | 75% | Max |
|---------|-------|------|-----|-----|-----|-----|-----|-----|
| Popularity | 200 | 74.30 | 17.69 | 20.00 | 60.00 | 81.00 | 88.00 | 99.00 |
| Duration (ms) | 200 | 204,731 | 54,805 | 64,840 | 169,917 | 200,901 | 240,400 | 547,733 |
| Danceability | 200 | 0.61 | 0.16 | 0.00 | 0.50 | 0.62 | 0.73 | 0.96 |
| Energy | 200 | 0.61 | 0.19 | 0.02 | 0.47 | 0.64 | 0.74 | 0.94 |
| Key | 200 | 5.04 | 3.87 | 0.00 | 1.00 | 5.00 | 9.00 | 11.00 |
| Loudness | 200 | -6.92 | 2.85 | -23.16 | -8.25 | -6.34 | -4.98 | -1.15 |
| Mode | 200 | 0.66 | 0.48 | 0.00 | 0.00 | 1.00 | 1.00 | 1.00 |
| Speechiness | 200 | 0.10 | 0.12 | 0.00 | 0.03 | 0.05 | 0.12 | 0.56 |
| Acousticness | 200 | 0.28 | 0.28 | 0.00 | 0.04 | 0.18 | 0.48 | 0.99 |
| Instrumentalness | 200 | 0.02 | 0.11 | 0.00 | 0.00 | 0.00 | 0.00 | 0.96 |
| Liveness | 200 | 0.17 | 0.12 | 0.04 | 0.10 | 0.12 | 0.20 | 0.67 |
| Valence | 200 | 0.44 | 0.26 | 0.00 | 0.21 | 0.42 | 0.64 | 0.96 |
| Tempo | 200 | 117.73 | 29.33 | 0.00 | 97.10 | 114.90 | 138.03 | 207.98 |
| Time Signature | 200 | 3.84 | 0.50 | 0.00 | 4.00 | 4.00 | 4.00 | 5.00 |

And this is the summary **after I normalized it**, because I used the dot product to measure similarity. I realized that I needed to normalize the data after I really disliked the first song that it came up with. The data visualization below shows that the length of the song was by far the highest value, so I normalized the data to use the z-score of each feature:

| Feature | Count | Mean | Std | Min | 25% | 50% | 75% | Max |
|---------|-------|------|-----|-----|-----|-----|-----|-----|
| Popularity | 200 | 0.00 | 1.00 | -3.07 | -0.81 | 0.38 | 0.77 | 1.40 |
| Duration (ms) | 200 | 0.00 | 1.00 | -2.55 | -0.64 | -0.07 | 0.65 | 6.26 |
| Danceability | 200 | 0.00 | 1.00 | -3.71 | -0.67 | 0.06 | 0.76 | 2.16 |
| Energy | 200 | 0.00 | 1.00 | -3.13 | -0.72 | 0.17 | 0.73 | 1.77 |
| Key | 200 | 0.00 | 1.00 | -1.30 | -1.04 | -0.01 | 1.02 | 1.54 |
| Loudness | 200 | 0.00 | 1.00 | -5.71 | -0.47 | 0.20 | 0.68 | 2.03 |
| Mode | 200 | 0.00 | 1.00 | -1.37 | -1.37 | 0.72 | 0.72 | 0.72 |
| Speechiness | 200 | 0.00 | 1.00 | -0.89 | -0.60 | -0.50 | 0.11 | 3.91 |
| Acousticness | 200 | 0.00 | 1.00 | -0.99 | -0.86 | -0.36 | 0.71 | 2.49 |
| Instrumentalness | 200 | 0.00 | 1.00 | -0.22 | -0.22 | -0.22 | -0.22 | 8.46 |
| Liveness | 200 | 0.00 | 1.00 | -1.10 | -0.60 | -0.42 | 0.22 | 4.11 |
| Valence | 200 | 0.00 | 1.00 | -1.68 | -0.87 | -0.07 | 0.76 | 1.97 |
| Tempo | 200 | 0.00 | 1.00 | -4.01 | -0.70 | -0.10 | 0.69 | 3.08 |
| Time Signature | 200 | 0.00 | 1.00 | -7.75 | 0.32 | 0.32 | 0.32 | 2.34 |

### Categorical Variables

| Feature | Count |
|---------|-------|
| Name | 200 |
| Artists | 200 |
| Album | 200 |
| Release Date | 200 |
| Explicit | 200 |
| Preview URL | 131 |
| External URL | 200 |
| Track ID | 200 |


## Data Visualization

Here is distribution of all of the numerical features—looking at this, it's clear why normalizing the data was necessary. Had I not normalized the data, the outliers in length of the song would have determined which song was selected.

<details style="margin-bottom: 20px">
<summary>Distribution of Features - My Top 100 Tracks</summary>

![Distribution of Features](/assets/images/output.png)
</details>

<details style="margin-bottom: 20px">
<summary>Distribution of Features - Global Top 50 Tracks</summary>

![Distribution of Features](/assets/images/output-global.png)
</details>

<details style="margin-bottom: 20px">
<summary>Distribution of Features - USA Top 50 Tracks</summary>

![Distribution of Features](/assets/images/output-usa.png)
</details>

<details style="margin-bottom: 20px">
<summary>Distribution of Features - New Releases</summary>

![Distribution of Features](/assets/images/output-new.png)
</details>

<details style="margin-bottom: 20px">
<summary>Distribution of Features - Combined (My Top 100, Global Top 50, USA Top 50)</summary>

![Distribution of Features](/assets/images/output-combined.png)
</details>

I thought this was interesting to look at, who knew you could learn so much about your own music taste just by analyzing your top tracks!

## Key Insights

Some insights I found out about my own music taste (according to my top 100 tracks). Also note that I did not remove any outliers:

1. On average, my songs fall in the 60% percentile for `popularity`...so unfortunately I am not a hipster.
2. The average `loudness` of my songs is about -7.475900dB, which tends to be towards the higher end of the spectrum (the scale is -60dB to 0dB).
3. The average `valence` of my songs is 0.341277, which means that my songs are on average more negative than positive. That makes sense because dating in Provo sucks.
4. According to my top 100, 46/100 are `explicit`. Sounds like I need to reconsider the type of music I listen to.
5. Finally, the average `speechiness` of my songs is 0.114259, which means that my songs are on average more instrumental than not. That makes sense because I like a lot of folk music, which can get pretty instrumental.


There are certainly more insights that can be drawn from my data, but I hope that this gives you an idea of what kind of information can be gleaned from the Spotify API. This was a super fun project and I learned a lot about my own music taste, and I know that you can, too!

I highly recommend trying this out for yourself! The code is available on my [GitHub](https://github.com/bradyrichardson/spotify), 

## Next Steps

I think a logical next step would be to use the data I have gathered to get automatically get a new song each week. I also think I could build upon the solution by feeding my data (NOT THE SPOTIFY CONTENT) into a custom neural net to get even better recommendations rather than just using the dot product.

## Resources for Further Exploration

- [GitHub](https://github.com/bradyrichardson/spotify)
- [Spotify API](https://developer.spotify.com/documentation/web-api)
- [PKCE Authentication](https://auth0.com/docs/get-started/authentication-and-authorization-flow/authorization-code-flow-with-pkce)

## Conclusion

Thank you for reading! I hope you enjoyed reading about this project as much as I enjoyed building it! If you have any questions or feedback, please let me know!