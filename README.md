# Movies-ETL

## Overview of Movies-ETL

Amazing Prime Video, a streaming platform would like to create an algorithm to determine which new movies being released will become popular.  This will allow Amazing Prime to purchase the streaming rights and make a return on their investment. They decided to sponsor a Hack-a-Thon, inviting the local coding community to help make predictions on which upcoming movies will be popular based on data from existing films.  To do this, Amazing Prime needs a clean dataset that the coding community can use to make their predictions.  Two datasets were used to compile this information - a Wikipedia listing of all movies released since 1990, and a ratings dataset from the MovieLens website.  In order to begin the cleaning process to make the data usable, the data was first extracted from a json file and CSV files, then transformed to remove television series, duplicate entries, and unnecessary information.

## Resources

* Data sources: movies_metadata.csv, ratings.csv, wikipedia-movies.json
* Software: PostgreSQL 12, pgAdmin4, Python 3.7.6, Jupyter Notebook 7.29.0

## Movies-ETL Results

Once the data from the datasets was cleaned, it was then merged and loaded into a PostgreSQL database, to ensure that the data can be updated on a daily basis.  The steps taken during this process included:

1. Writing an ETL function to read in three data files:

```
def extract_transform_load():
    # 2. Read in the kaggle metadata and MovieLens ratings CSV files as Pandas DataFrames.
    kaggle_metadata = pd.read_csv(f'{file_dir}movies_metadata.csv', low_memory=False)
    ratings = pd.read_csv(f'{file_dir}ratings.csv')

    # 3. Open the read the Wikipedia data JSON file.
    with open(f'{file_dir}/wikipedia-movies.json', mode='r') as file:
        wiki_movies_raw = json.load(file)
    # 4. Read in the raw wiki movie data as a Pandas DataFrame.
    wiki_movies_df = pd.DataFrame(wiki_movies_raw)
    # 5. Return the three DataFrames
    return wiki_movies_df, kaggle_metadata, ratings
```

2. Extracting and Transforming Wikipedia Data:

```
 # 6. Write a try-except block to catch errors while extracting the IMDb ID using a regular expression string and
    #  dropping any imdb_id duplicates. If there is an error, capture and print the exception.
try:    
    wiki_movies_df['imdb_id'] = wiki_movies_df['imdb_link'].str.extract(r'(tt\d{7})')
    wiki_movies_df.drop_duplicates(subset='imdb_id', inplace=True)

except:
    print("error found when extracting data")
    
wiki_movies_df.head()    

print(len(wiki_movies_df))
```

3. Extracting and Transforming Kaggle Data:

```
# 2. Clean the Kaggle metadata.
kaggle_metadata['video'] = kaggle_metadata['video'] == 'True'
kaggle_metadata['budget'] = kaggle_metadata['budget'].astype(int)
kaggle_metadata['id'] = pd.to_numeric(kaggle_metadata['id'], errors='raise')
kaggle_metadata['popularity'] = pd.to_numeric(kaggle_metadata['popularity'], errors='raise')
kaggle_metadata['release_date'] = pd.to_datetime(kaggle_metadata['release_date'])
pd.to_datetime(ratings['timestamp'], unit='s')
ratings['timestamp'] = pd.to_datetime(ratings['timestamp'], unit='s')
```

4. Creating the Movie Database:

![movies_query.png](https://github.com/crtallent/Movies-ETL/blob/main/Resources/movies_query.png)

