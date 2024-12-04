---
layout: post
title:  "Spotify Song Finder EDA"
author: Brady Richardson
description: Doing some exploratory data analysis on my Spotify data.
image: "https://bradyrichardson.github.io/my-blog-stat386/assets/images/Spotify_Full_Logo_RGB_Green.png"
---

![Person Listening to Music]("https://bradyrichardson.github.io/my-blog-stat386/assets/images/c7ae99e6ab1c1055f29d216199c0f0e7b69e78a588308f67851b461db6b958ef.png")

## Introduction
In this blog post, we'll explore some of the insights I found when I used the Spotify API to analyze my own music taste. I'll also introduce a Streamlit app that allows you to explore the same data in a similar way.

## Key Insights

### Insight 1: Understanding My Musical Preferences
Using the Spotify API, I analyzed my listening history to uncover patterns in my music taste. By examining the following features: popularity, danceability, energy, loudness, speechiness, acousticness, instrumentalness, liveness, and valence, I discovered that I tend to gravitate towards music that is slightly more popular, about average in danceability, about average in energy, about average in loudness, low in speechiness, low in acousticness, very low in instrumentalness, low in liveness, and low in valence. You can find the definitions of these features in the Spotify API documentation [here](https://developer.spotify.com/documentation/web-api/reference/get-audio-features). Given that my favorite songs tend to fall in either folk or rap, this makes a lot of sense.

Additionally, I created a correlation matrix for my music taste profile:

![Correlation Matrix](https://bradyrichardson.github.io/my-blog-stat386/assets/images/newplot.png)

It was interesting to see how the features interacted with each other, although I will say that there wasn't anything too surprising. It's obvious even without a matrix that loudness and energy are highly positively correlated. However, there were some interesting insights, such as the fact that acousticness and instrumentalness are relatively uncorrelated with each other. I invite you to look at the Streamlit app to see the interactive correlation matrix for each set of track featurs yourself!

### Insight 2: How My Taste Profile Differs From the Average Listener in the US
Another insight I found was how my taste profile differed from the average listener in the US. I'm using the US data here as point of reference because I think it's a more accurate representation of my taste profile than the global data.

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

This comparison shows that while my listening habits generally align with US averages for features like danceability, energy and loudness, I tend to prefer less popular tracks (-0.27 points). I also gravitate towards songs with more speechiness, and more instrumentalness compared to the average US listener. The notably lower valence score (-0.18) suggests I tend to listen to music that is less positive or happy in tone.

## Introducing the Spotify Song Finder EDA Tool
My Streamlit app, the Spotify Song Finder EDA Tool, helps users uncover patterns in their Spotify music taste. If you're interested in visualizing the data yourself, you can find a Streamlit app built to do just that [here](https://bradyrichardson-spotify-spotify-streamlit-y2osej.streamlit.app/). And if you're interested in the code for the app, you can find it [here](https://github.com/bradyrichardson/spotify). Just follow my [previous blog post](https://bradyrichardson.github.io/my-blog-stat386/2024/11/12/spotify-post.html) to learn how to get your own Spotify data!

### What You Can Do with Spotify Song Finder
- **Feature Distribution**: Get a distribution of each feature for the top songs in the uploaded dataset
- **Scatterplot Analysis**: Get a scatterplot of any two features to see how they relate to each other
- **Correlation Matrix**: Get a correlation matrix for each dataset to see how the features interact with each other

### Exploring Further Insights
I invite you to explore the app yourself, first using the provided dataset, and then uploading your own! In order to do so, follow my [previous blog post](https://bradyrichardson.github.io/my-blog-stat386/2024/11/12/spotify-post.html) to get your own Spotify data, and then fork the [GitHub repository](https://github.com/bradyrichardson/spotify) to run the app on your own computer with your own data.

## Conclusion
By leveraging Spotify's API and data science techniques, I've created a tool that makes music discovery more personalized and engaging. The patterns I've uncovered show that no matter how well you may think you know your own music taste, you can still learn a lot via the magic of data science! I invite you to try this yourself with your own data!

### Further Information and Resources
[[Streamlit App](https://bradyrichardson-spotify-spotify-streamlit-y2osej.streamlit.app/)]
[[GitHub Repository](https://github.com/bradyrichardson/spotify)]
[[Previous Blog Post](https://bradyrichardson.github.io/my-blog-stat386/2024/11/12/spotify-post.html)]