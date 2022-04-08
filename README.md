# Movies-ETL
Performing Express, Transform, Load using functions. 

# Overview
Amazing Prime would like to host a hackaton to predict the popular motion pictures. Webscrapping data from Wikipedia, in conjunction with ratings and movies metadata from [Kaggle](https://www.kaggle.com/datasets/rounakbanik/the-movies-dataset?resource=download) were processed and combined, to be uploaded to a database for further analysis. In the challenge, this ETL process was refactored and streamlined using two primary functions: `clean_movie()` and `extract_transform_load()`. The bulk of the ETL process involved processing the Wikipedia JSON file using various filtering processes. The results were reported as row count queries from the two tables loaded: 
- movies : includes combined data from Wikipedia JSON file along with the Kaggle movies_metadata.
- ratings : large csv file containing ratings data for movies from Kaggle.  

Additional transformations to the data were performed to merge all three data sets and is reported as `movies_with_ratings_df` in [ETL_clean_kaggle_data.ipynb](https://github.com/Fabalin/Movies-ETL/blob/main/ETL_clean_kaggle_data.ipynb) but this dataset was not loaded into PostgreSQL. To view the progression of the refactored ETL process, view the following files in order: 
- [ETL_function_test.ipynb](https://github.com/Fabalin/Movies-ETL/blob/main/ETL_function_test.ipynb)
- [ETL_clean_wiki_movies.ipynb](https://github.com/Fabalin/Movies-ETL/blob/main/ETL_clean_wiki_movies.ipynb)
- [ETL_clean_kaggle_data.ipynb](https://github.com/Fabalin/Movies-ETL/blob/main/ETL_clean_kaggle_data.ipynb)
- [ETL_create_database.ipynb](https://github.com/Fabalin/Movies-ETL/blob/main/ETL_create_database.ipynb)
- [ETL_Filter_POC.ipynb](https://github.com/Fabalin/Movies-ETL/blob/main/ETL_Filter_POC.ipynb)

**Note, the final `movies_df` generated and loaded into PostgreSQL had a slight increase in the number of rows (6,075) from the desired amount of 6,052 rows. This discrepency is attributed to the lack of additional filtering criteria within the directions of the challenge and is discussed using results from the [ETL_Filter_POC](https://github.com/Fabalin/Movies-ETL/blob/main/ETL_Filter_POC.ipynb) file.**  

# Software
- Anaconda - 4.11.0
- Jupyter Notebook - 6.4.8
- Python - 3.7.11
- PostgreSQL - 12.10

# Results
- The first query from PostgreSQL displays the total rows from the movies dataframe that was loaded using the ETL_create_database file. The total was slightly higher than the target amount since the first filtering process outlined in the challenge only required the elimination of movies with episodes. 
  ```    
    # Write a list comprehension to filter out TV shows.
    wiki_movies_cleaned = [movie for movie in wiki_movies_raw if 'No. of episodes' not in movie]
  ```
  ![movies_query](https://github.com/Fabalin/Movies-ETL/blob/main/Resources/movies_query.PNG) 
  
  In order to get the target amount of rows, an additional filter is needed in the same step to also exclude movies without directors. The correct total row count of the movies dataframe is displayed below along with the associated code in the ETL_Filter_POC file: 
  ```
    # Write a list comprehension to filter out TV shows.
    wiki_movies_cleaned = [movie for movie in wiki_movies_raw 
                   if ('Director' in movie or 'Directed by' in movie) 
                   and 'imdb_link' in movie
                   and 'No. of episodes' not in movie]
  ```
  ![Filter_POC_movies_df](https://github.com/Fabalin/Movies-ETL/blob/main/Resources/Filter_POC_movies_df.PNG)

- The final query displays the correct amount of rows imported for the ratings.csv file from Kaggle, with a total of 26,024,289 rows. This import was done within the `extract_transform_load()` function while parsing the file with Pandas at a chunksize of 1,000,000, due to its large volume. The code for the import also included a statement monitoring and timing the import process. Due to its long import duration of around 11 mins, The file containing the `extract_transform_load()` function was ran only once, foregoing the need to correct the discrepency of rows in the movies_df. 
  
  ![ratings_query](https://github.com/Fabalin/Movies-ETL/blob/main/Resources/ratings_query.PNG)
  
# Summary
While refactoring code for efficiency is ideal, it can sometimes generate deviations in results, depending on the user. In this case, the entirety of the refactored code was derived from the original exploratory file [wiki_scrape.ipynb](https://github.com/Fabalin/Movies-ETL/blob/main/wiki_scrape.ipynb) that documents each step in the ETL process. This can help with the troubleshooting process should errors arise. The benefit of refactoring the code between two functions allows the code to be run faster overall but selective running of cells with issues is not feasible since the functions will run the entire ETL process from start to finish. 
