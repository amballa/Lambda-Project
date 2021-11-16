
# Historical Relationship between UFO Sightings and Popularity of Science-Fiction Movies

### Background
Humans have long been fascinated by space and the prospect of intelligent extraterrestrial life. Surely, the cosmos is too vast for a bipedal ape species to be the only technologically-inclined life-form! We look to the sky for answers with equal parts hope and fear. But what we see is often not so clear. Whether it's blinking lights or impossible aerial movements, many have claimed to lay sight on phenomena that is beyond explanation. Over the past century alone, the National UFO Reporting Center has catalogued over 80 thousand eyewitness accounts of unidentified flying objects across the world. Perhaps unsurprisingly, nearly 90% of those accounts came from Americans. So what it about America that makes UFO sightings such a regular occurence?  Is there really something non-human out there or are the sightings the result of an overactive collective imagination? Are there cultural or political forces at play that might explain why so many Americans have these strange experiences? These questions remain open. But with this anaylsis, my goal is to compare the trends in the number of UFO sightings in the US with the box office performance of science-fiction films. I will also account for changes in the country's population over the same time period.



### Datasets
For this analysis, I used 3 publicly available dataset:
1. UFO Sightings (National UFO Reporting Center via Kaggle)
    
    - 80,332 records from 1910 to 2014 with date, location, coordinates, shape of object, duration, and other comments.
    - 
    - 
2. TMDB 5000 Movie (The Movie Database via Kaggle)
    
    - 10,866 movie titles from 1960 to 2015 with information such as release date, cast, genre, plot summary, IMDB rating, production budget, and worldwide revenue.
    - 
3. US Population by Year (US Census Bureau)

    -k
    

Limited by the time frames of the NUFORC and TMDB datasets, I chose to compare data from 1960 to 2013. Additionally, because the UFO reports date back to much farther than the establishment of the reporting center, older accounts may be obscured.

### Hypothesis


### Data Wrangling & Feature Engineering

```
states = ['al', 'ak', 'az', 'ar', 'ca', 'co', 'ct', 'dc', 'de', 'fl', 'ga', 'hi', 'id', 'il', 'in', 'ia', 'ks', 'ky', 'la', 'me', 'md', 'ma', 'mi', 'mn', 'ms', 'mo', 'mt', 'ne', 'nv', 'nh', 'nj', 'nm', 'ny', 'nc', 'nd', 'oh', 'ok', 'or', 'pa', 'ri', 'sc', 'sd', 'tn', 'tx', 'ut', 'vt', 'va', 'wa', 'wv', 'wi', 'wy']
for i in range(len(ufo)):
  if ufo.loc[i]['state'] in states or ufo.loc[i]['country'] == 'us':
    pass
  else:
    ufo.drop(index = i, inplace=True)
    
ufo['datetime'] = ufo['datetime'].apply(fix_datetime_format)
ufo['datetime'] = pd.to_datetime(ufo['datetime'], infer_datetime_format= True)
ufo['year'] = pd.DatetimeIndex(ufo['datetime']).year
ufo.sort_values(by = 'datetime', ascending = True, inplace = True)

ufo_final = ufo['year'].value_counts().rename_axis('year').to_frame('ufo_counts').sort_values(by = 'year').reset_index()
ufo_final = ufo_final.loc[30:][:].reset_index()
ufo_final = ufo_final.drop('index', axis = 1)
```

```markdown
movies = pd.read_csv('tmdb_movies_data.csv')
movies.drop(['id', 'cast', 'homepage', 'director', 'tagline', 'keywords', 'overview', 'runtime', 'production_companies', 'vote_count', 'vote_average'], axis = 1, inplace = True)

filtered_movies = movies.drop_duplicates()
genre_drop = filtered_movies[filtered_movies['genres'].isnull() == True]
filtered_movies = filtered_movies.drop(genre_drop.index)
rev_drop = filtered_movies[filtered_movies['revenue'] == 0]
filtered_movies = filtered_movies.drop(rev_drop.index)
filtered_movies = filtered_movies.reset_index()
filtered_movies = filtered_movies.drop('index', axis=1)

def genre_movies(genre):
  temp = filtered_movies
  for i in range(len(filtered_movies)):
    if genre not in temp.loc[i]['genres']:
      temp = temp.drop(index=i)
  temp = temp.sort_values(by = 'release_year')
  return temp

scifi_movies = genre_movies('Science Fiction')

def genre_rev(genre_movies):
  unique_years = genre_movies['release_year'].unique()
  temp = pd.DataFrame(columns = ['year', 'adj_rev_total'])
  temp['year'] = unique_years
  r_vals = []
  for i in range(len(unique_years)):
    r = scifi_movies.loc[scifi_movies['release_year'] == unique_years[i], 'revenue_adj'].sum()
    r_vals.append(r)
  temp['adj_rev_total'] = r_vals
  return temp

scifi_rev = genre_rev(scifi_movies)
```

```markdown
pop = pd.read_excel('us population.xlsx')

pop['Date'] = pd.to_datetime(pop['Date'])
pop['year'] = pd.DatetimeIndex(pop['Date']).year

mil_pop = []
for i in range(len(pop)):
  mil_pop.append(float(pop.loc[i]['Population'][:-8]))
pop['us_pop_mil'] = mil_pop

pop.drop(['Date', 'Population'], axis = 1, inplace = True)
```

```markdown
df_final = pd.merge(ufo_final ,scifi_rev, how = 'left')
df_final = pd.merge(df_final, pop, how = 'left')
df_final = df_final.fillna(0)
df_final['adj_rev_mil'] = df_final['adj_rev_total'] / 1000000
df_final = df_final.loc[:53]
df_final.head(10)
```
![image](https://user-images.githubusercontent.com/92558174/141859358-838200cc-d7e6-4af7-b4bd-17e6383e9555.png)


### Statistical Methods and Results
![image](https://user-images.githubusercontent.com/92558174/141721215-3fc2695b-7aba-4f78-8535-fc3b0ae293f9.png)

![image](https://user-images.githubusercontent.com/92558174/141721549-5026871c-b1dd-4924-9e3e-8d59054b547f.png)

```markdown
model = ols('ufo_counts ~ us_pop_mil', data = df_final).fit()
print(model.summary())
```
![image](https://user-images.githubusercontent.com/92558174/141722252-109dea83-e2b4-41d6-b358-ca640b3e1c3d.png)


```markdown
model2 = ols('ufo_counts ~ us_pop_mil + adj_rev_mil', data = df_final).fit()
print(model2.summary())
```
![image](https://user-images.githubusercontent.com/92558174/141722375-79531167-5fd7-4494-ba06-5249f306d793.png)



### (Conclusion Section)


### Limitations of Analysis

From the two-variable linear regression analysis, we can see that there is _indeed_ a correlation between the number of reported UFO sightings and the adjusted revenue of science-fiction movies in the US from 1960 to 2013. However, **correlation does NOT mean causation**. We cannot attribute the rise of sightings exclusively to the growing popularity of the film genre as this analysis is extremely narrow and limited in scope.

- g

