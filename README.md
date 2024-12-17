# Overview
This project involves analyzing a Spotify dataset with various attributes about tracks, albums, and artists using SQL. It covers an end-to-end process of normalizing a denormalized dataset, performing SQL queries of varying complexity (easy, medium, and advanced), and optimizing query performance. The primary goals of the project are to practice advanced SQL skills and generate valuable insights from the dataset.


--- CREATE TABLE
```sql
DROP TABLE IF EXISTS spotify;
CREATE TABLE music (
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
use spotify_db
select * from music

# Project Steps

1.Data Exploration
Before diving into SQL, it’s important to understand the dataset thoroughly. The dataset contains attributes such as:

Artist: The performer of the track.
Track: The name of the song.
Album: The album to which the track belongs.
Album_type: The type of album (e.g., single or album).
Various metrics such as danceability, energy, loudness, tempo, and more

# --- EDA---
select count(*) from music

select count(distinct artist) from music



select count(distinct album) from music

select distinct album_type from music 

select max(duration_min)from music

select min(duration_min)from music

select * from music
where duration_min=0

delete from music
where duration_min=0

select count(*) from music

select distinct channel from music

select distinct most_played_on from music

----------------
# --EASY Question-----
------------


1.Retrieve the names of all tracks that have more than 1 billion streams.
2.List all albums along with their respective artists.
3.Get the total number of comments for tracks where licensed = TRUE.
4.Find all tracks that belong to the album type single.
5.Count the total number of tracks by each artist.


# --Q1.Retrieve the names of all tracks that have more than 1 billion streams.
```sql
select * from music
where stream > 1000000000
```

-- Q2.List all albums along with their respective artists.
```sql
select distinct album,artist
from music
```

-- Q3.Get the total number of comments for tracks where licensed = TRUE.
```sql
select sum(comments)as total_comment from music
where licensed='true'
```

--Q4.Find all tracks that belong to the album type single.
```sql
select * from music
where album_type ='single'
```
```sql
select * from music
where album_type ilike'single'
```

--Q5.Count the total number of tracks by each artist.
```sql
select artist,count(*)as total_track from music
group by artist
order by total_track
```


-----------
# ---Moderate Questions----
---------
1.Calculate the average danceability of tracks in each album.
2.Find the top 5 tracks with the highest energy values.
3.List all tracks along with their views and likes where official_video = TRUE.
4.For each album, calculate the total views of all associated tracks.
5.Retrieve the track names that have been streamed on Spotify more than YouTube.


--Q1.Calculate the average danceability of tracks in each album.
```sql
select album,avg(danceability)as avg_danceability from music
group by album
order by avg_danceability desc
```

--Q2.Find the top 5 tracks with the highest energy values.
```sql
select track,max(energy)as highest_energy_value from music
group by track
order by highest_energy_value desc
limit 5
```

-- Q3.List all tracks along with their views and likes where official_video = TRUE.
```sql
select track,views,likes from music
where official_video ='true'
```

--Q4.For each album, calculate the total views of all associated tracks.
```sql
select album,track,sum(views) from music
group by album,track
```

--Q5.Retrieve the track names that have been streamed on Spotify more than YouTube.
```sql
with cte as(
	select track,
	--most_played_on,
	coalesce(sum(case when most_played_on ='Youtube' then stream end),0)as streamed_on_youtube,
	coalesce(sum(case when most_played_on ='Spotify' then stream end),0)as streamed_on_spotify
from music
group by track
)
select * from cte
where streamed_on_spotify>streamed_on_youtube
and streamed_on_youtube !=0
```

--------
# -----Advance Question-----
1.Find the top 3 most-viewed tracks for each artist using window functions.
2.Write a query to find tracks where the liveness score is above the average.
3.Use a WITH clause to calculate the difference between the highest and lowest energy values for tracks in each album.

---Q1.Find the top 3 most-viewed tracks for each artist using window functions.
-- each artist and total view for each track
-- track for highest view for each artist(we need top)

```sql
with cte as(
			select artist,
				track,
				sum(views)as views_track,
				dense_rank()over(partition by artist order by sum(views)desc)as dense_rnk
			from music
			group by artist,track
			order by artist,views_track desc
)
select * from cte 
where dense_rnk<=3
```

---Q2.Write a query to find tracks where the liveness score is above the average
```sql
select track,liveness from music
where liveness >(select avg(liveness)from music)
```

--Q3.Use a WITH clause to calculate the difference between the highest and lowest energy values for tracks in each album.
```sql
with cte as(select 
                  album,
                  max(energy)as highest_energy,
                  min(energy)as lowest_energy
            from music
            group by album
)
select album,
       highest_energy-lowest_energy as energy_diff
from cte
order by energy_diff desc
```
















	


















































