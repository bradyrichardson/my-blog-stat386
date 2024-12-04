---
layout: post
title:  "The Perfect Song"
author: Brady Richardson
description: Using the Spotify API to identify the perfect song specifically for me.
image: "https://bradyrichardson.github.io/my-blog-stat386/assets/images/Spotify_Primary_Logo_RGB_Green-300x300.png"
---

# Finding new music used to be such a hard problem...until I threw Python at it!

![Spotify Logo](https://bradyrichardson.github.io/my-blog-stat386/assets/images/Spotify_Full_Logo_RGB_Green.png)

## Introduction
In this blog post, we'll explore some of the insights I found when I used the Spotify API to analyze my own music taste. If you're interested in visualizing the data yourself, you can find a Streamlit app built to do just that [here](https://bradyrichardson-spotify-spotify-streamlit-y2osej.streamlit.app/).

## Key Insights

### Insight 1: Understanding My Musical Preferences
Using the Spotify API, I analyzed my listening history to uncover patterns in my music taste. By examining the following features: popularity, danceability, energy, loudness, speechiness, acousticness, instrumentalness, liveness, and valence, I discovered that I tend to gravitate towards music that is more popular, about average in danceability, about average in energy, about average in loudness, low in speechiness, low in acousticness, very low in instrumentalness, low in liveness, and low in valence. You can find the definitions of these features in the Spotify API documentation [here](https://developer.spotify.com/documentation/web-api/reference/get-audio-features). Given that my favorite songs tend to fall in either folk or rap, this makes a lot of sense.

Here's the correlation matrix for my music taste profile:

![Correlation Matrix](https://bradyrichardson.github.io/my-blog-stat386/assets/images/newplot.png)

### Insight 2: How My Taste Profile Differs From the Average Listener in the US
I'm using the US data here as point of reference because I think it's a more accurate representation of my taste profile than the global data.

| Feature | My Profile | US Average | Difference |
|---------|------------|------------|------------|
| Popularity | Average (0.61) | 0.88 | -0.27 | My music is less popular than the average listener in the US.
| Danceability | Average (0.58) | 0.61 | -0.03 | My music is less danceable than the average listener in the US.
| Energy | Average (0.57) | 0.65 | -0.08 | My music is less energetic than the average listener in the US.
| Loudness | Average (-7.5) | -6.2 | -1.3 | My music is quieter than the average listener in the US.
| Speechiness | Low (0.114) | 0.103 | 0.011 | My music is more speechy than the average listener in the US.
| Acousticness | Low (0.3) | 0.3 | 0 | My music is right on the money with acousticness compared to the average listener in the US.
| Instrumentalness | Very Low (0.042) | 0.003 | 0.039 | My music is more instrumental than the average listener in the US.
| Liveness | Low (0.17) | 0.21 | -0.04 | My music is less live than the average listener in the US.
| Valence | Low (0.34) | 0.52 | -0.18 | My music is less positive in tone than the average listener in the US.

This comparison shows that while my listening habits generally align with US averages for features like danceability, energy and loudness, I tend to prefer more popular tracks (+9.1 points). I also gravitate towards songs with less speechiness, acousticness, and instrumentalness compared to the average US listener. The notably lower valence score (-0.18) suggests I tend to listen to music that is less positive or happy in tone.


## Introducing the Spotify Song Finder
My Streamlit app, Spotify Song Finder, helps users discover new music that matches their personal taste profile using machine learning and Spotify's audio features.

### What You Can Do with Spotify Song Finder
- **Taste Profile Analysis**: Upload your Spotify listening history to see detailed insights about your music preferences
- **Song Recommendations**: Get personalized song recommendations based on your listening patterns
- **Feature Exploration**: Interact with visualizations showing how different audio features correlate with your favorite songs

### Exploring Further Insights
With Spotify Song Finder, you can:
- Compare your taste profile across different time periods
- Discover songs that match specific moods or activities
- Explore new genres that align with your listening patterns

## Conclusion
By leveraging Spotify's API and data science techniques, we've created a tool that makes music discovery more personalized and engaging. The patterns we've uncovered show that [brief summary of main finding].
[Optional: Include a call-to-action or invitation for feedback]

- [Try the Spotify Song Finder App](your-streamlit-app-link)
- [View the Project Code](your-github-repo-link)
- [Learn More About Spotify's Audio Features](https://developer.spotify.com/documentation/web-api/reference/tracks/get-audio-features/)

### Further Information and Resources
[[Streamlit App](https://bradyrichardson-spotify-spotify-streamlit-y2osej.streamlit.app/)]
[[GitHub Repository](https://github.com/bradyrichardson/spotify)]
