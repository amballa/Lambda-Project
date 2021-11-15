## Historical Relationship between UFOs and Science Fiction Films
Abdel Balla

The goal of this project...

### Background


### Datasets
For the analysis, I used 3 publicly available dataset:
1. UFO Sightings (National UFO Reporting Center via Kaggle)
2. TMDB 5000 Movie (The Movie Database via Kaggle)
3. US Population by Year (US Census Bureau)

### Hypothesis

### Data Wrangling & Feature Engineering

```markdown
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

### Statistical Methods and Results


### (Conclusion Section)


### Limitations of Analysis

From the two-variable linear regression analysis, we can see that there is _indeed_ a correlation between the number of reported UFO sightings and the adjusted revenue of science-fiction movies in the US from 1960 to 2013. However, **correlation does NOT mean causation**. We cannot attribute the rise of sightings exclusively to the growing popularity of the film genre as this analysis is extremely narrow and limited in scope.

- g


You can use the [editor on GitHub](https://github.com/amballa/Lambda-Project/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/amballa/Lambda-Project/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
