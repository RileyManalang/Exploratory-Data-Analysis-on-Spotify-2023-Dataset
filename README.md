# Spotify Songs Analysis 2023
## 📋 Project Description
This is an exploratory data analysis project on Spotify's most streamed songs in 2023 using Python. 

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
from typing import Dict, Tuple 

# For statistical functions and tests 
# Specifically for the advanced analysis part
from scipy import stats
```

## Overview of the Dataset
- There are 953 rows and 24 columns
- Below are the data types of each column
- The missing values were also indicated, which can be read for the streams column; there is one missing entry or rather an abnormal entry, there are 50 missing values in the 'in_shazam_charts' column, and there are 95 missing values in the key column. 
![image](https://github.com/user-attachments/assets/19687f59-f440-49cc-b8b2-b42a671e228a)
![image](https://github.com/user-attachments/assets/334f5fec-1388-4f7a-929c-1e1673247558)

- This is the piece of code that produced these outputs
```
# Find the problematic rows in the streams column
print("=== Problematic Values in Streams Column ===")

# Print rows where streams contains non-numeric characters (after removing commas)
problematic_streams = df[~df['streams'].str.replace(',', '').str.match(r'^\d+$')]
print("\nRows with non-numeric streams values:")
print(problematic_streams[['track_name', 'artist(s)_name', 'streams']])

# Clean the streams column properly
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

# Print the missing values
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

## Advanced Analysis
#### Key and Mode Patterns:
1. Mode Distribution:
    - Major mode tracks (549 songs) slightly outnumber Minor mode tracks (403 songs)
    - The average streams for Major mode (534.8M) is slightly higher than Minor mode (485.9M)
    - However, the t-test results (p-value = 0.1887) indicate this difference is not statistically significant
    - Looking at the violin plots in the bottom left, both modes show similar distribution shapes
  
2. Key Distribution:
    - From the box plots in the top right, stream distributions across different keys are relatively consistent
    - There's no clear key that dominates in terms of streaming performance
    - All keys show similar median values with some outliers in the higher ranges

#### Artist and Playlist Patterns:
1. Playlist Presence Leaders:
    - Pharrell Williams, Nile Rodgers, and Daft Punk have the highest playlist presence
    - The Killers and a-ha follow closely
    - These artists represent different eras and genres (disco/funk, rock, synthpop), suggesting playlist curators value variety
   
2. Chart Presence:
    - Latin artists (Bad Bunny, Jung Kook) show strong chart presence
    - The Weeknd appears multiple times in different collaborations
    - This suggests contemporary pop and Latin music perform well on charts

3. Overall Presence Score (combining playlist and chart metrics):
    - Kendrick Lamar/Jay Rock lead overall presence
    - Frank Ocean and Coldplay follow closely
    - The top 10 includes a mix of genres: hip-hop, alternative, pop, and rock
    - Artists with longevity (Coldplay, The Police) perform well on this metric

4. Total Streams:
    - The Weeknd, Taylor Swift, and Ed Sheeran dominate total streams
    - These are all contemporary pop artists with broad appeal
    - Interesting to note that high stream counts don't always correlate with high playlist presence

#### Key Insights 
- Musical mode (Major vs. Minor) has minimal impact on streaming performance
- Playlist presence seems to favor legacy artists with established catalogs
- Chart presence favors contemporary pop and Latin artists
- The most successful artists tend to cross genre boundaries or have broad mainstream appeal
- High playlist presence doesn't necessarily translate to highest streams, suggesting different paths to streaming success

![image](https://github.com/user-attachments/assets/e7aa0751-40cc-4e30-ae94-3ef7d7ed1b6d)
![image](https://github.com/user-attachments/assets/f64d78dc-3f35-469a-8c52-7d18f3374a98)

- This is the piece of code that produced these graphs
```
   # For better documentation
def analyze_spotify_patterns(df):
    """
    Analyzes Spotify 2023 data focusing on:
    1. Stream patterns by key and mode
    2. Artist and genre presence in playlists/charts
    """
    
    def preprocess_data(df):
        """Preprocess the dataframe to ensure correct data types"""
        # Convert streams and playlist/chart columns to numeric
        df['streams'] = pd.to_numeric(df['streams'].astype(str).str.replace(',', ''), errors='coerce')
        
        playlist_chart_cols = [
            'in_spotify_playlists', 'in_spotify_charts', 
            'in_apple_playlists', 'in_apple_charts',
            'in_deezer_playlists', 'in_deezer_charts'
        ]
        
        for col in playlist_chart_cols:
            df[col] = pd.to_numeric(df[col], errors='coerce')
            
        return df
    
    def analyze_key_mode_patterns():
        """Analyze streaming patterns based on key and mode"""
        # Streaming analysis by mode
        mode_stats = df.groupby('mode').agg({
            'streams': ['count', 'mean', 'median', 'std'],
            'in_spotify_playlists': 'mean',
            'in_spotify_charts': 'mean'
        }).round(2)
        
        # Streaming analysis by key
        key_stats = df.groupby('key').agg({
            'streams': ['count', 'mean', 'median', 'std'],
            'in_spotify_playlists': 'mean',
            'in_spotify_charts': 'mean'
        }).round(2)
        
        # Perform t-test between Major and Minor modes
        major_streams = df[df['mode'] == 'Major']['streams'].dropna()
        minor_streams = df[df['mode'] == 'Minor']['streams'].dropna()
        mode_ttest = stats.ttest_ind(major_streams, minor_streams)
        
        # Create visualizations
        fig, axes = plt.subplots(2, 2, figsize=(15, 12))
        
        # 1. Box plot of streams by mode
        sns.boxplot(data=df, x='mode', y='streams', ax=axes[0, 0])
        axes[0, 0].set_title('Distribution of Streams by Mode')
        axes[0, 0].set_yscale('log')  # Use log scale for better visualization
        
        # 2. Box plot of streams by key
        sns.boxplot(data=df, x='key', y='streams', ax=axes[0, 1])
        axes[0, 1].set_title('Distribution of Streams by Key')
        axes[0, 1].set_yscale('log')
        axes[0, 1].tick_params(axis='x', rotation=45)
        
        # 3. Violin plot of streams by mode
        sns.violinplot(data=df, x='mode', y='streams', ax=axes[1, 0])
        axes[1, 0].set_title('Stream Distribution Shape by Mode')
        axes[1, 0].set_yscale('log')
        
        # 4. Average streams by key and mode
        avg_by_key_mode = df.pivot_table(
            values='streams', 
            index='key', 
            columns='mode', 
            aggfunc='mean'
        ).fillna(0)
        avg_by_key_mode.plot(kind='bar', ax=axes[1, 1])
        axes[1, 1].set_title('Average Streams by Key and Mode')
        axes[1, 1].tick_params(axis='x', rotation=45)
        
        plt.tight_layout()
        
        return {
            'mode_stats': mode_stats,
            'key_stats': key_stats,
            'mode_ttest': mode_ttest,
            'visualization': plt
        }
    
    def analyze_artist_genre_presence():
        """Analyze artist and genre presence in playlists and charts"""
        # Calculate total playlist and chart presence for each artist
        artist_presence = df.groupby('artist(s)_name').agg({
            'streams': 'sum',
            'in_spotify_playlists': 'mean',
            'in_spotify_charts': 'mean',
            'in_apple_playlists': 'mean',
            'in_apple_charts': 'mean',
            'in_deezer_playlists': 'mean',
            'in_deezer_charts': 'mean'
        }).round(2)
        
        # Calculate a presence score (weighted average of playlist and chart presence)
        artist_presence['total_presence_score'] = (
            artist_presence['in_spotify_playlists'] * 0.2 +
            artist_presence['in_spotify_charts'] * 0.3 +
            artist_presence['in_apple_playlists'] * 0.2 +
            artist_presence['in_apple_charts'] * 0.2 +
            artist_presence['in_deezer_playlists'] * 0.05 +
            artist_presence['in_deezer_charts'] * 0.05
        )
        
        # Analyze genre presence (using 'tracks_genre' column)
        if 'tracks_genre' in df.columns:
            genre_presence = df.groupby('tracks_genre').agg({
                'streams': 'sum',
                'in_spotify_playlists': 'mean',
                'in_spotify_charts': 'mean',
                'in_apple_playlists': 'mean',
                'in_apple_charts': 'mean'
            }).round(2)
        else:
            genre_presence = pd.DataFrame()  # Empty DataFrame if column doesn't exist
        
        # Create visualizations
        fig, axes = plt.subplots(2, 2, figsize=(15, 12))
        
        # 1. Top 10 artists by playlist presence
        top_10_playlist = artist_presence.nlargest(10, 'in_spotify_playlists')
        top_10_playlist['in_spotify_playlists'].plot(
            kind='barh', ax=axes[0, 0], title='Top 10 Artists by Spotify Playlist Presence'
        )
        axes[0, 0].invert_yaxis()  # Better visualization with artist names
        
        # 2. Top 10 artists by chart presence
        top_10_charts = artist_presence.nlargest(10, 'in_spotify_charts')
        top_10_charts['in_spotify_charts'].plot(
            kind='barh', ax=axes[0, 1], title='Top 10 Artists by Spotify Chart Presence'
        )
        axes[0, 1].invert_yaxis()
        
        # 3 & 4. Genre plots (only if genre data exists)
        if not genre_presence.empty:
            # Top 10 genres by playlist presence
            top_10_genre_playlist = genre_presence.nlargest(10, 'in_spotify_playlists')
            top_10_genre_playlist['in_spotify_playlists'].plot(
                kind='barh', ax=axes[1, 0], title='Top 10 Genres by Spotify Playlist Presence'
            )
            axes[1, 0].invert_yaxis()
            
            # Top 10 genres by chart presence
            top_10_genre_charts = genre_presence.nlargest(10, 'in_spotify_charts')
            top_10_genre_charts['in_spotify_charts'].plot(
                kind='barh', ax=axes[1, 1], title='Top 10 Genres by Spotify Chart Presence'
            )
            axes[1, 1].invert_yaxis()
        else:
            # Alternative plots if genre data isn't available
            # Top artists by total presence score
            artist_presence.nlargest(10, 'total_presence_score')['total_presence_score'].plot(
                kind='barh', ax=axes[1, 0], title='Top 10 Artists by Overall Presence Score'
            )
            axes[1, 0].invert_yaxis()
            
            # Top artists by streams
            artist_presence.nlargest(10, 'streams')['streams'].plot(
                kind='barh', ax=axes[1, 1], title='Top 10 Artists by Total Streams'
            )
            axes[1, 1].invert_yaxis()
        
        plt.tight_layout()
        
        return {
            'artist_presence': artist_presence.sort_values('total_presence_score', ascending=False),
            'genre_presence': genre_presence if not genre_presence.empty else None,
            'top_artists_playlists': top_10_playlist,
            'top_artists_charts': top_10_charts,
            'visualization': plt
        }
    
    # Preprocess the data
    df = preprocess_data(df)
    
    # Perform analyses
    results = {
        'key_mode_patterns': analyze_key_mode_patterns(),
        'presence_patterns': analyze_artist_genre_presence()
    }
    
    return results

# Example usage:
if __name__ == "__main__":
    # Read the dataset
    df = pd.read_csv('spotify-2023.csv')
    
    # Print available columns (helpful for debugging)
    print("Available columns:", df.columns.tolist())
    
    # Perform analysis
    results = analyze_spotify_patterns(df)
    
    # Print key findings
    print("\n=== KEY AND MODE STREAMING PATTERNS ===")
    print("\nMode Statistics:")
    print(results['key_mode_patterns']['mode_stats'])
    print("\nMode T-Test Results:")
    print(f"t-statistic: {results['key_mode_patterns']['mode_ttest'].statistic:.2f}")
    print(f"p-value: {results['key_mode_patterns']['mode_ttest'].pvalue:.4f}")
    
    print("\n=== TOP ARTISTS BY PRESENCE ===")
    print("\nTop 10 Artists by Overall Presence:")
    print(results['presence_patterns']['artist_presence'].head(10)[['total_presence_score', 'streams']])
    
    # Display visualizations
    results['key_mode_patterns']['visualization'].show()
    results['presence_patterns']['visualization'].show()
```

## Insights and Recommendations
1. Temporal Strategy
- Modern releases dominate streams, with a dramatic increase from 2015 onwards
- Recommendation: Focus on current/recent releases for maximum streaming potential
- Strategic timing: January and May show peak releases (130-140 tracks), while August has lowest releases (~45 tracks)
- Recommendation: Consider releasing tracks in less saturated months to stand out, or align with peak months to ride the wave of heightened listener activity

2. Artist Collaboration Patterns
- Most successful tracks feature solo artists or duets
- While collaborations with 3+ artists are rare, some outliers have up to 8 artists
- Recommendation: Focus on solo or duet releases, but consider strategic high-profile collaborations for standout tracks

3. Platform Strategy
- Platform presence varies significantly:
    - Spotify Playlists: ~5,200 tracks average
    - Apple Playlists: ~68 tracks average
    - Spotify Charts: ~12 tracks average
- Recommendation: 
    - Focus on Spotify playlist inclusion as primary goal
    - Build relationships with playlist curators
    - Consider platform-specific optimization strategies

4. Genre and Style Considerations
- Cross-genre artists tend to perform well
- Artists with broad mainstream appeal dominate streaming numbers
- Recommendation:
    - Consider genre-blending approaches
    - Focus on universal appeal while maintaining artistic authenticity

- These insights suggest that while there's no single formula for streaming success, a strategic approach considering release timing, platform dynamics, and artistic positioning can significantly impact a track's performance. The key appears to be balancing artistic authenticity with strategic market awareness, rather than over-optimizing for any single metric or characteristic.

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
- Updated the readme file to make it simpler
- Removed unneccesary pieces of code and information
