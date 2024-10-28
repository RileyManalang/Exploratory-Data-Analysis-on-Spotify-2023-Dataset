# Spotify Songs Analysis 2023
An exploratory data analysis of the most streamed Spotify songs in 2023.

## 📋 Project Description
This project performs an exploratory data analysis on Spotify's most streamed songs in 2023. The analysis includes temporal trends, musical characteristics, platform popularity, and genre analysis.

## 🎯 Key Features
- Comprehensive data cleaning and preprocessing
- Statistical analysis of streaming patterns
- Visualization of musical characteristics
- Analysis of temporal trends
- Platform comparison (Spotify vs. Apple Music playlists)

## Exploring the Data
- In the first part of this project is getting a dataset overview. So, I created a code to help me check missing values and any abnormalities within the dataset.
- After running the code, I then discovered that there were a few missing values within the 'shazam' column and 'key' column
```
import pandas as pd
import numpy as np

# Read the dataset
df = pd.read_csv('spotify-2023.csv')

# Basic information about the dataset
print("=== Dataset Info ===")
print(df.info())

print("\n=== Missing Values ===")
missing_values = df.isnull().sum()
print(missing_values[missing_values > 0])

print("\n=== Basic Statistics ===")
print(df.describe())

# Check for potential data quality issues
print("\n=== Data Quality Checks ===")

#Check for duplicate tracks
duplicates = df[df.duplicated(['track_name', 'artist_name'], keep=False)]
print(f"\nNumber of duplicate tracks: {len(duplicates)}")

#Check for invalid percentages
percentage_columns = [col for col in df.columns if '%' in col]
invalid_percentages = {}
for col in percentage_columns:
    invalid = df[(df[col] < 0) | (df[col] > 100)][col]
    if len(invalid) > 0:
        invalid_percentages[col] = len(invalid)

print("\nColumns with invalid percentages (should be 0-100):")
print(invalid_percentages)

#Check for invalid streams (negative values)
invalid_streams = df[df['streams'] < 0]
print(f"\nTracks with negative streams: {len(invalid_streams)}")

#Check data types consistency
print("\n=== Data Types ===")
print(df.dtypes)

#Check for suspicious values in key column
valid_keys = ['C', 'C#', 'D', 'D#', 'E', 'F', 'F#', 'G', 'G#', 'A', 'A#', 'B']
invalid_keys = df[~df['key'].isin(valid_keys)]
print(f"\nTracks with invalid keys: {len(invalid_keys)}")

#Check release dates
df['released_date'] = pd.to_datetime(df['released_date'], errors='coerce')
invalid_dates = df[df['released_date'].isnull()]
print(f"\nTracks with invalid release dates: {len(invalid_dates)}")

# Print sample of the data
print("\n=== Sample of the Data ===")
print(df.head())
```







#### Version History:
##### [v1.1.0] - 10/28/2024
###### Changes:
##### [v1.2.0] - 9/21/2024

-
