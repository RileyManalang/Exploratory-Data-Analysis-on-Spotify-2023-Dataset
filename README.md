# Spotify Songs Analysis 2023
## 📋 Project Description
This project performs an exploratory data analysis on Spotify's most streamed songs in 2023. 

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

print("\n=== Missing Values ===")
missing_values = df.isnull().sum()
print(missing_values[missing_values > 0])

print("\n=== Streams Statistics ===")
print(df['streams'].describe())

# Show top 5 most streamed songs
print("\n=== Top 5 Most Streamed Songs ===")
top_5_streams = df.nlargest(5, 'streams')[['track_name', 'artist(s)_name', 'streams']]
print(top_5_streams)
```







#### Version History:
##### [v1.1.0] - 10/28/2024
###### Changes:
- Added a code that will show me the data types of each column, number of rows and columns, missing values, and top 5 most streamed songs
##### [v1.2.0] - 9/21/2024

-
