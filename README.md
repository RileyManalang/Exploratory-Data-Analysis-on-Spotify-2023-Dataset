# Spotify Songs Analysis 2023
## 📋 Project Description
This project performs an exploratory data analysis on Spotify's most streamed songs in 2023. 

## 🎯 Key Features
- Comprehensive data cleaning and preprocessing
- Statistical analysis of streaming patterns
- Visualization of musical characteristics
- Analysis of temporal trends
- Platform comparison (Spotify vs. Apple Music playlists)

## Libraries that were used
```
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
from datetime import datetime
from typing import List, Tuple, Dict, Optional

```
#### Exploring the Data
- In the first part of this project is getting a dataset overview. So, I created a piece of code to help me check missing values and any abnormalities within the dataset.
- After running the code, I then discovered that there were a few missing values within the 'shazam' column and 'key' column
- This piece of code also returns basic descriptive statistics of the 'streams' column such as the mean, median (50th Percentile), and standard deviation.
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

## Overview of the Dataset
- There are 953 rows and 24 columns
- Below are the data types of each column
- The missing values are were also indicated which can be read for the streams column, there is a 1 missing entry or rather an abnormal entry and for the shazam_charts column there are 50 missing values. 
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
> Mean - 5.141374
> 
> Median - 2.905309
> 
> Standard Deviation - 5.668569

- Distribution of released_year
> The majority of the songs are from the recent years

> Notable outliers are songs that were in the 1930s - 1960s

> The distribution shows that Spotify’s most streamed songs are predominantly modern

- Distribution of artist_count
> Most songs have a solo or duet

> Notable outliers are songs that have a high number of collaborating artists (up to 8 artists)

> Collaborations with 3 artists are rare.
![image](https://github.com/user-attachments/assets/6104e6c1-ca7a-4760-a52a-84259f2a39bb)



#### Version History:
##### [v1.1.0] - 10/28/2024
###### Changes:
- Added a code that will show me the data types of each column, number of rows and columns, missing values, and top 5 most streamed songs
##### [v1.2.0] - 9/21/2024

-
