---
title: Recommender System for travel destinations
tags:
    - Data Science
    - Deep Learning
---
#### -- Project Status: Active

In this contribution, I aim at building a recommender system to offer personalized suggestions for tourist destinations around the globe. This is an end-to-end data science project, spanning all stages such as data collection, data exploration, data cleaning, feature extraction, model development, model validation and data visualization.

![Pipeline](https://github.com/rbiswasfc/Recommender-System/blob/master/images/pipeline.PNG)

These stages are briefly summarized in the following.

## Data collection

The objective is to build a dataset containing information of tourist destinations around the globe. After browsing through several travel sites, I decided to collect data from Triposo and TripAdvisor, since they offer comprehensive details and user reviews regarding tourist attractions.

### [Triposo](https://github.com/rbiswasfc/Recommender-System/blob/master/scraping/scrape_triposo.ipynb)

Details of 250k Points Of Interest (POI) are collected from Triposo. The following attributes of each POI are stored:

* Title: Name of POI
* Location: Which location/city the POI belongs to
* Country: country --> location --> POI
* POI type: e.g. museum, lake, mountain, church
* POI info: a short description of the POI
* POI image: cover image of the POI
* POI rank: Index of the POI withing the POI list for a location

This [jupyter notebook](https://github.com/rbiswasfc/Recommender-System/blob/master/scraping/scrape_triposo.ipynb) contains details of the data collection steps:

* Make a list of countries to include in the dataset
* For each country find the urls of travel destinations
* For each travel destination extract details of POIs

![Scrape Location](https://github.com/rbiswasfc/Recommender-System/blob/master/images/location_triposo.PNG)

![Scrape POI](https://github.com/rbiswasfc/Recommender-System/blob/master/images/POI_triposo.PNG)

### [TripAdvisor](https://github.com/rbiswasfc/Recommender-System/blob/master/scraping/scrape_data_with_apify.ipynb)

The collaborative filtering mechanism utilizes underlying user-item interaction for making recommendations. In this case, the users and items are travelers and tourist attractions respectively. To build a user-item matrix, information from the TripAdvisor are scraped. The [APIFY TripAdvisor scraper](https://apify.com/petrpatek/tripadvisor-scraper) offers a ready-made and fast crawler to get the relevant information. Using APIFY, reviews of many tourist attractions in south-east Asia and Europe are extracted. Two separate tables are created: TA_attractions and TA_reviews.

#### TA_attractions
The table TA_attractions stores the following information for each attraction:

* location_id: unique id for an attraction
* name: name of the attraction
* latitude and longitude: geographical coordinates
* description: a short description of the attraction
* web_url: url of the page
* subcategory: type of attraction
* subtype: more refined class of subcategory
* num_reviews: total number of reviews for an attraction

![Scrape TA Location](https://github.com/rbiswasfc/Recommender-System/blob/master/images/TA_attraction.PNG)

#### TA_reviews
The reviews tables stores all scraped reviews with the following fields:

* user: TA user name of the reviewer
* location_id: the unique id of the rated location
* location_name: name of the location
* rating: given rating for the attraction (1 to 5 stars)
* comment: the text content of the review

![Scrape TA Location](https://github.com/rbiswasfc/Recommender-System/blob/master/images/TA_reviews.PNG)


### Create POI database

The extracted data is finally stored in MySQL database. The relationship schema is shown below:
![Scrape POI](https://github.com/rbiswasfc/Recommender-System/blob/master/images/poi_db.PNG)
##### -- to be updated: TA User table not implemented

## [Data exploration](https://github.com/rbiswasfc/Recommender-System/blob/master/notebooks/EDA.ipynb)

### Triposo EDA
Information regarding 230k travel attractions (POI) are extracted. A sample of 5 POIs for the dataset is sown below:

![POI sample](https://github.com/rbiswasfc/Recommender-System/blob/master/images/sample_triposo_data.PNG)

Each POI is attributed to a POI type e.g. lake, mountain and sight. The top 15 POI type with respect to their frequency are:

* sights x1
* museum x2

Each POI also has a short explanatory description. The word count distribution of the descriptions are shown below:

![Word count hist](https://github.com/rbiswasfc/Recommender-System/blob/master/images/word_count_poi.PNG)

Next the POIs are divided into clusters based on their TF-iDF features.

![cluster elbow](https://github.com/rbiswasfc/Recommender-System/blob/master/images/clustering_elbow.PNG)

different clusters
![cluster1](https://github.com/rbiswasfc/Recommender-System/blob/master/images/cluster_1.PNG)
![cluster2](https://github.com/rbiswasfc/Recommender-System/blob/master/images/cluster_2.PNG)
![cluster3](https://github.com/rbiswasfc/Recommender-System/blob/master/images/cluster_3.PNG)
![cluster4](https://github.com/rbiswasfc/Recommender-System/blob/master/images/cluster_4.PNG)

### TripAdvisor

user rating frequency
![user rating frequency](https://github.com/rbiswasfc/Recommender-System/blob/master/images/user_review_comment_dist.PNG)

sample rating

![Rating Sample](https://github.com/rbiswasfc/Recommender-System/blob/master/images/sample_ratings.PNG)

ratings by one user
![One user ratings](https://github.com/rbiswasfc/Recommender-System/blob/master/images/ratings_one_user_TA.PNG)

rating distribution
![Rating Dist](https://github.com/rbiswasfc/Recommender-System/blob/master/images/TA_ratings_hist.PNG)

## Data cleaning

* Basic text cleaning
* Text normalization

## Feature extraction

Features:

For each POI extracted from Triposo:

* TF-IDF features
* Features from a pre-trained language model: BERT-base

For each travel destination extracted from Triposo:
* Aggregate information from all POIs under the travel destination
* Get TF-iDF feature from aggregated document

## Model development

* Content-based recommender system
* Collaborative filtering
* Hybrid system

User inputs
![Input 1](https://github.com/rbiswasfc/Recommender-System/blob/master/images/rating_inp_1.PNG)

![Input 2](https://github.com/rbiswasfc/Recommender-System/blob/master/images/rating_inp_2.PNG)

![Input 3](https://github.com/rbiswasfc/Recommender-System/blob/master/images/rating_inp_3.PNG)

![Input 4](https://github.com/rbiswasfc/Recommender-System/blob/master/images/rating_inp_4.PNG)

![Input 5](https://github.com/rbiswasfc/Recommender-System/blob/master/images/rating_inp_5.PNG)


Predictions

![Sys Rec DeepFLY](https://github.com/rbiswasfc/Recommender-System/blob/master/images/rec_deepfly.PNG)

![Sys Rec 1](https://github.com/rbiswasfc/Recommender-System/blob/master/images/rec_deepfly_part_1.PNG)

![Sys Rec 2](https://github.com/rbiswasfc/Recommender-System/blob/master/images/rec_deepfly_part_2.PNG)


### Collaborative filtering
* Get user profile in terms of latent variable

### Content based Recommender System
I have built a base Content-based recommender system for travel destinations using the wiki-travel database. The notebooks describing the implementation can be found [here](https://github.com/rbiswasfc/Recommender-System/blob/master/notebooks/wikiVoyageRecSys.ipynb).
I have used TF-iDF to extract document features.
* To-do: use spaCy to extract named entities and use them as additional features.

Building a content based recommender system for travel destinations.

## Model evaluation and monitoring
* To-do: develop a web application and track the model performance
* To-do: adjust model parameters with user feedback (conduct a survey with web application)

## Upcoming features
tbc

## Appendix

### Set up Anaconda environment

It is good practice to create separate environment for each project. Anaconda distribution is commonly used for handling libraries in data science projects. Here, I will describe how to set up an Anaconda environment for the current project, which can be reproduced in another machine for recovering the experiment results. For more details, please refer to environment management [documentation](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html) by Anaconda.

* Create an Anaconda environment and give it a name e.g. *NLP*

```$conda create - -name NLP```

* Activate the environment

```$source activate NLP```

* Install required packages e.g.

```$conda install - c pytorch pytorch```

* Export the active environment to a new file in yml format

```$conda env export > NLP.yml```


* Some useful commands

    * Deactivate active environment ```$conda deactivate```
    * Delete environment ```$conda remove - -name NLP - -all```

* Open Jupyter Lab from the project folder and check whether the environment(NLP) is available in iPython kernel. If not, execute the following:

```$conda install jupyter```

```$conda install nb_conda```

```$conda install ipykernel```

```$python - m ipykernel install - -user - -name NLP - -display - name "Python (NLP)"```

* Check whether installed libraries can be imported e.g.

```python
import torch  # should not get errors
```

* Keep updating the yml file for conda environment when new libraries are installed

```$conda env export > NLP.yml```


* To recreate same conda environment in another machine from yml file use the following command

```$conda env create - f NLP.yml```

* Create a requirements.txt file for any non conda packages. You may use pip for installing such packages. To this end, first install pip for the environment

```$conda install -n NLP pip```

```$conda activate NLP```

```$pip <pip_subcommand>```

```$pip freeze > requirements.txt```

