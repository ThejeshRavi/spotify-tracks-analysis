# Spotify-Tracks-Analysis

## Overview
This project focuses on analyzing a **Spotify dataset** containing detailed information about tracks, albums, and artists using **SQL**. It involves an **end-to-end process**, including **normalizing a denormalized dataset**, executing SQL queries of varying complexity. The primary objective is to enhance **advanced SQL skills** while extracting meaningful insights from the data.

```sql
-- create table
DROP TABLE IF EXISTS spotify;
CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);
```
## Project Steps

### 1. Data Exploration
Before diving into SQL, it’s important to understand the dataset thoroughly. The dataset contains attributes such as:
- `Artist`: The performer of the track.
- `Track`: The name of the song.
- `Album`: The album to which the track belongs.
- `Album_type`: The type of album (e.g., single or album).
- Various metrics such as `danceability`, `energy`, `loudness`, `tempo`, and more.

### 4. Querying the Data
After the data is inserted, various SQL queries can be written to explore and analyze the data. Queries are categorized into **easy**, **medium**, and **advanced** levels to help progressively develop SQL proficiency.

1. ** Retrieve the names of all tracks that have more than 1 billion streams**
```sql
SELECT * FROM spotify
WHERE stream > 1000000000
```
2. List all albums along with their respective artists.
```sql
SELECT 
	DISTINCT album, artist
FROM spotify
ORDER BY 1

SELECT 
	DISTINCT album
FROM spotify
ORDER BY 1

```
3. Get the total number of comments for tracks where `licensed = TRUE`.
```sql
SELECT
	SUM(comments) as total_comments
FROM spotify
WHERE licensed = 'true';
```
4. Find all tracks that belong to the album type `single`.
```sql

SELECT 
	track, album_type
FROM spotify
WHERE album_type = 'single'
GROUP BY 1, 2;


SELECT * FROM spotify
WHERE album_type ='single'
```
5. Count the total number of tracks by each artist.
```sql
SELECT 
	artist,
	COUNT(*) AS total_no_songs
FROM spotify
GROUP BY artist
ORDER BY 2;
```

6. Calculate the average danceability of tracks in each album.
```sql
SELECT 
	album,
	AVG(danceability) AS avg_danceability
FROM spotify
GROUP BY 1
ORDER BY 2 DESC;
```
7. Find the top 5 tracks with the highest energy values.
```sql
SELECT 
	track,
	MAX(energy)
FROM spotify
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5
```
8. List all tracks along with their views and likes where `official_video = TRUE`.
```sql
SELECT 
	track,
	SUM(views) AS total_views,
	SUM(likes) AS total_likes
FROM spotify
WHERE official_video = 'true'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5
```

9. For each album, calculate the total views of all associated tracks.
```sql
SELECT * FROM spotify

SELECT 
	album,
	track,
	SUM(views) AS total_views
FROM spotify
GROUP BY 1,2
ORDER BY  3 DESC;
```
10. Retrieve the track names that have been streamed on Spotify more than YouTube.
``` SQL
SELECT * FROM 
(SELECT 
	track,
	-- most_played_on,
	COALESCE(SUM(CASE WHEN 	most_played_on = 'Youtube' THEN stream END),0) as streamed_on_youtube,
	COALESCE(SUM(CASE WHEN 	most_played_on = 'Spotify' THEN stream END),0) as streamed_on_spotify
FROM spotify
GROUP BY 1 
) AS t1
WHERE 
	streamed_on_spotify > streamed_on_youtube
	AND 
	streamed_on_youtube <> 0

```

### Advanced Level
11. Find the top 3 most-viewed tracks for each artist using window functions.
```sql
WITH ranking_artist
AS
(SELECT
	artist,
	track,
	SUM(views) AS total_view,
	DENSE_RANK() OVER(PARTITION BY artist ORDER BY SUM(views) DESC ) AS rank 
FROM spotify
GROUP BY 1, 2   
ORDER BY 1, 3 DESC
)
SELECT * FROM ranking_artist
WHERE rank <= 3
```

12. Write a query to find tracks where the liveness score is above the average.

``` SQL 
SELECT AVG(liveness) FROM spotify -- 0.19

SELECT 
	track,
	artist,
	liveness
FROM spotify
WHERE liveness > (SELECT AVG(liveness) FROM spotify)
``` 
13. **Use a `WITH` clause to calculate the difference between the highest and lowest energy values for tracks in each album.**
```sql
WITH cte
AS
(SELECT 
	album,
	MAX(energy) as highest_energy,
	MIN(energy) as lowest_energery
FROM spotify
GROUP BY 1
)
SELECT 
	album,
	highest_energy - lowest_energery as energy_diff
FROM cte
ORDER BY 2 DESC
```
   
14. Find tracks where the energy-to-liveness ratio is greater than 1.2.
```sql
SELECT 
	track,
	energy_liveness
FROM spotify
WHERE energy_liveness > 1.2 -- if energy_liveness column is precomputed

SELECT 
    track,
    energy / NULLIF(liveness, 0) AS energy_to_liveness
FROM spotify
WHERE energy / NULLIF(liveness, 0) > 1.2; -- if energy_liveness column is not precomputed
```
15. Calculate the cumulative sum of likes for tracks ordered by the number of views, using window functions.
``` SQL
SELECT 
	track,
	SUM(likes) AS total_likes,
	SUM(views) AS total_views
FROM spotify
GROUP BY 1
ORDER BY 3 DESC
``` 


### 🎯 Key Learnings
- Mastered SQL window functions for ranking & cumulative calculations
- Learned how to normalize denormalized data
- Practiced query optimization to handle large datasets efficiently
- Gained insights into music streaming analytics

### 🛠️ Technologies Used
- PostgreSQL (Database & SQL queries)
- pgAdmin / DBeaver (SQL Query Editor)
- GitHub (Version Control)
