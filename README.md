# Spotify Songs Analysis 2023
## 📋 Project Description
This is an exploratory data analysis project on Spotify's most streamed songs in 2023. 

## 🎯 Key Features
- Comprehensive data cleaning and preprocessing
- Statistical analysis of streaming patterns
- Visualization of musical characteristics
- Analysis of temporal trends
- Platform comparison

## Libraries that were used
```
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
from typing import List, Tuple, Dict, Optional

```
#### Exploring the Data
- In the first part of this project is getting a dataset overview. So, I created a piece of code to help me check missing values and any abnormalities within the dataset.
- Then I tried to clean the dataset of its abnormalities
- Below is the piece of code that I used
```
# Read the dataset
df = pd.read_csv('spotify-2023.csv')

# Let's first look at problematic rows in the streams column
print("=== Problematic Values in Streams Column ===")
# Print rows where streams contains non-numeric characters (after removing commas)
problematic_streams = df[~df['streams'].str.replace(',', '').str.match(r'^\d+$')]
print("\nRows with non-numeric streams values:")
print(problematic_streams[['track_name', 'artist(s)_name', 'streams']])

# Now let's clean the streams column properly
def clean_streams(value):
    try:
        # Remove commas and convert to numeric
        return pd.to_numeric(str(value).replace(',', ''))
    except:
        # If conversion fails, return NaN
        return np.nan

# Clean the streams column
df['streams'] = df['streams'].apply(clean_streams)

# Print basic info about the cleaned dataset
print("\n=== Dataset Info After Cleaning ===")
print(df.info())

```

## Overview of the Dataset
- There are 953 rows and 24 columns
- Below are the data types of each column
- The missing values were also indicated, which can be read for the streams column; there is one missing entry or rather an abnormal entry, and for the shazam_charts column, there are 50 missing values. 
![image](https://github.com/user-attachments/assets/19687f59-f440-49cc-b8b2-b42a671e228a)
![image](https://github.com/user-attachments/assets/334f5fec-1388-4f7a-929c-1e1673247558)

- This is the piece of code that produced these outputs
```
# Print basic info about the cleaned dataset
print("\n=== Dataset Info After Cleaning ===")
print(df.info())

print("\n=== Missing Values ===")
missing_values = df.isnull().sum()
print(missing_values[missing_values > 0])

```
## Basic Descriptive Statistics
- The mean, median, and standard deviation of the streams column are as follows
    - Mean - 5.141374
    - Median - 2.905309
    - Standard Deviation - 5.668569

- Distribution of released_year
    - The majority of the songs are from the recent years
    - Notable outliers are songs that were in the 1930s - 1960s
    - The distribution shows that Spotify’s most streamed songs are predominantly modern

- Distribution of artist_count
    - Most songs have a solo or duet
    - Notable outliers are songs that have a high number of collaborating artists (up to 8 artists)
    - Collaborations with three artists are rare.

![image](https://github.com/user-attachments/assets/fa29c82a-d71c-456b-92d0-585f5c0eb4a1)

- This is the piece of code that showed these graphs
```
# Set up the plotting style
plt.style.use('seaborn-v0_8-bright')
fig, ((ax1, ax2), (ax3, ax4)) = plt.subplots(2, 2, figsize=(15, 12))

# Distribution of released_year
sns.histplot(data=df, x='released_year', bins=30, ax=ax1)
ax1.set_title('Distribution of Release Years')
ax1.set_xlabel('Release Year')
ax1.set_ylabel('Number of Songs')

# Add vertical line for median year
median_year = df['released_year'].median()
ax1.axvline(x=median_year, color='red', linestyle='--', label=f'Median Year: {median_year:.0f}')
ax1.legend()

#Box plot for released_year to show outliers
sns.boxplot(data=df, y='released_year', ax=ax2)
ax2.set_title('Box Plot of Release Years')
ax2.set_ylabel('Release Year')

# Distribution of artist_count
sns.histplot(data=df, x='artist_count', bins=range(1, df['artist_count'].max() + 2), ax=ax3)
ax3.set_title('Distribution of Artist Count')
ax3.set_xlabel('Number of Artists')
ax3.set_ylabel('Number of Songs')

# Box plot for artist_count to show outliers
sns.boxplot(data=df, y='artist_count', ax=ax4)
ax4.set_title('Box Plot of Artist Count')
ax4.set_ylabel('Number of Artists')

plt.tight_layout()
plt.show()
```

## Top Performers
- This snapshot shows the top 5 most streamed tracks
![image](https://github.com/user-attachments/assets/de094956-5f69-4c8a-ae81-2cce68db1f49)

- This is the top 5 most frequent artists based on the number of tracks in the dataset
![image](https://github.com/user-attachments/assets/b93b4495-190f-486e-8104-32370ff1ab2e)

- This is the piece of code that produced these outputs
```
# Show top 5 most streamed songs
print("\n=== Top 5 Most Streamed Songs ===")
top_5_streams = df.nlargest(5, 'streams')[['track_name', 'artist(s)_name', 'streams']]
print(top_5_streams)
```
```
# Count the occurrences of each artist and get the top 5
top_artists_by_tracks = data['artist(s)_name'].value_counts().head(5)

# Display the result
print("Top 5 Artists by Track Count:")
print(top_artists_by_tracks)
```

## Temporal Trends
- There's a dramatic increase in the number of tracks released in recent years
- The peak appears to be around 2023 with approximately 400 tracks
- Prior to 2010, there were very few tracks in the dataset
- There's an exponential growth pattern from roughly 2015 onwards

- There's significant variation in releases across months
- Peak releases occur in Month 1 (January) and Month 5 (May), both with around 130-140 tracks
- The lowest number of releases appears to be in Month 8 (August) with about 45 tracks
- There seems to be a cyclical pattern with higher releases in the beginning and middle of the year
![image](https://github.com/user-attachments/assets/c15b9743-aa83-4b31-bb71-77a62960e70b)
![image](https://github.com/user-attachments/assets/becc45e4-87f6-43ec-bf3c-192cecab4918)

- This is the piece of code that managed to produce these graphs
```
   def plot_temporal_trends(self, save_path: str = None):
        """Plot yearly and monthly release trends."""
        fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(15, 12))
        
        # Yearly trends
        year_counts = self.df['released_year'].value_counts().sort_index()
        year_counts.plot(kind='bar', ax=ax1)
        ax1.set_title('Number of Tracks Released per Year')
        ax1.set_xlabel('Year')
        ax1.set_ylabel('Number of Tracks')
        ax1.tick_params(axis='x', rotation=45)
        
        # Monthly trends
        month_counts = self.df['released_month'].value_counts().sort_index()
        month_counts.plot(kind='bar', ax=ax2)
        ax2.set_title('Number of Tracks Released per Month')
        ax2.set_xlabel('Month')
        ax2.set_ylabel('Number of Tracks')
        ax2.tick_params(axis='x', rotation=45)
        
        plt.tight_layout()
        if save_path:
            plt.savefig(save_path)
        plt.show()
```

## Genere and Music Characteristics
- Overall Correlation Pattern:
    - All attributes show relatively weak negative correlations with streams (all bars point downward)
    - The correlations range from nearly 0 to approximately -0.11

- Strongest Correlations
    - Speechiness_% has the strongest negative correlation (around -0.11)
    - Danceability_% has the second strongest negative correlation (around -0.10)
    - The remaining attributes (liveness_%, instrumentalness_%, valence_%, energy_%, acousticness_%, and bpm) have weaker negative correlations

- Scatter Plot Analysis:
    - None show a strong linear relationship with streams
    - All display considerable scatter/dispersion
    - The highest streaming numbers appear across various values of these attributes, suggesting no single "optimal" value

- Correlation between danceability_% & energy_% and valence_% and acousticness_%
    - Danceability and Energy show a weak positive correlation (0.2)
    - Valence and acousticness show a weak negative correlation (-0.082)
![image](https://github.com/user-attachments/assets/1be948d8-9de2-48fd-896d-a218ed54b29e)

![image](https://github.com/user-attachments/assets/f5f2d256-5723-4255-b930-a42620d0a33e)
![image](https://github.com/user-attachments/assets/12051955-1613-4a3b-872b-1cc4581f16e1)

- This is the piece of code that produced these graphs
```
    
    def plot_correlation_analysis(self):
        """Plot correlation matrix and correlation analysis of musical features."""
        # Define musical features to analyze
        musical_features = [
            'bpm', 'danceability_%', 'valence_%', 'energy_%',
            'acousticness_%', 'instrumentalness_%', 'liveness_%', 'speechiness_%',
            'streams_m'
        ]
        
        # Calculate correlations
        correlation_matrix = self.df[musical_features].corr()
        
        # 1. Plot correlation matrix heatmap
        plt.figure(figsize=(12, 10))
        sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', center=0, fmt='.3f')
        plt.title('Correlation Matrix of Musical Features')
        plt.tight_layout()
        plt.show()
        
        # 2. Plot correlation bar chart
        correlations = correlation_matrix['streams_m'].drop('streams_m')
        
        plt.figure(figsize=(12, 6))
        correlations.plot(kind='bar')
        plt.title('Correlation between Musical Attributes and Streams')
        plt.xlabel('Musical Attributes')
        plt.ylabel('Correlation Coefficient')
        plt.xticks(rotation=45)
        plt.tight_layout()
        plt.show()
        
        # 3. Create scatter plots for key features
        fig, axes = plt.subplots(1, 3, figsize=(15, 5))
        features = ['bpm', 'acousticness_%', 'energy_%']
        
        for i, feature in enumerate(features):
            axes[i].scatter(self.df[feature], self.df['streams_m'], alpha=0.5, color='pink')
            axes[i].set_xlabel(feature)
            axes[i].set_ylabel('Streams (Millions)')
            axes[i].set_title(f'Streams vs {feature}')
        
        plt.tight_layout()
        plt.show()
        
        return correlation_matrix

# Example usage:
    if __name__ == "__main__":
        analyzer = SpotifyCorrelationAnalyzer('spotify-2023.csv')
        correlations = analyzer.plot_correlation_analysis()
        
        # Print numerical correlation values
        print("\nCorrelations with streams:")
        stream_correlations = correlations['streams_m'].sort_values(ascending=False)
        print(stream_correlations)

```
## Platform Popularity
- Platform Reach (means):
    - Spotify Playlists: 5,200 tracks
    - Spotify charts: 12 tracks
    - Apple playlists: 68 tracks
- Based on this data, Spotify playlists appear to include a much broader range of tracks and have significantly higher numbers overall. However, looking at the charts (which typically represent current popularity), Spotify seems more selective with only about 12 tracks on average compared to Apple's playlist inclusion of about 68 tracks.
![image](https://github.com/user-attachments/assets/1d78a7ff-654c-4e28-a0e9-fe2dbd22da91)

 
- This is the piece of code that produced this graph
```
 
    def analyze_platforms(self):
        """Compare average presence across Spotify and Apple platforms."""
        # Calculate mean values for each platform
        platform_means = self.df[self.platform_cols].mean().round(2)
        
        # Create visualization
        plt.figure(figsize=(10, 6))
        
        # Create bar plot
        bars = plt.bar(range(len(platform_means)), platform_means.values)
        plt.title('Average Track Presence Across Platforms')
        plt.ylabel('Average Number of Appearances')
        plt.xticks(range(len(platform_means)), 
                  ['Spotify Playlists', 'Spotify Charts', 'Apple Playlists'],
                  rotation=45)
        
        # Add value labels on top of each bar
        for bar in bars:
            height = bar.get_height()
            plt.text(bar.get_x() + bar.get_width()/2., height,
                    f'{height:,.0f}',
                    ha='center', va='bottom')
        
        plt.tight_layout()
        plt.show()
        
        # Calculate additional statistics for comparison
        platform_stats = self.df[self.platform_cols].describe()
        platform_stats.columns = ['Spotify Playlists', 'Spotify Charts', 'Apple Playlists']
        
        return platform_stats

    if __name__ == "__main__":
        analyzer = SpotifyAnalyzer('spotify-2023.csv')
        stats = analyzer.analyze_platforms()
        print("\nPlatform Statistics:")
        print(stats)
```
### Version History:
##### [v1.1.0] - 10/28/2024
###### Changes:
- Added a code that will show me the data types of each column, number of rows and columns, missing values, and top 5 most streamed songs

##### [v1.2.0] - 10/29/2024
###### Changes:
- improved the code for the overview of the dataset
- Added a code for the distribution of released_year and artist_count

##### [v1.3.0] - 10/30/2024
###### Changes:
- Added a code for the temporal trends and music characteristics correlation

##### [v1.4.0] - 10/31/2024
###### Changes:
- Added a code to get the most popular platform

##### [v1.5.0] - 11/1/2024
###### Changes:
- Improved all the codes by making it clean

##### [v1.6.0] - 11/3/2024
###### Changes:

