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

The notebook for data exploration can be accessed [here](https://github.com/rbiswasfc/Recommender-System/blob/master/notebooks/EDA.ipynb).

### Triposo EDA
Information regarding  230k+ travel attractions (POI) are extracted. A sample of 5 POIs is shown below:

![POI sample](https://github.com/rbiswasfc/Recommender-System/blob/master/images/sample_triposo_data.PNG)

Each POI is attributed to a POI type e.g. lake, mountain and sight. The top 15 POI type with respect to their frequency are:

|  POI Type  | Frequency |
|:----------:|:---------:|
|    sight   |   46675   |
|   museum   |   38720   |
|    park    |   28234   |
|   church   |   19405   |
|  mountain  |    8556   |
|  memorial  |    8493   |
|   theater  |    6235   |
|   bridge   |    5972   |
|  city hall |    5286   |
|   castle   |    5235   |
|   library  |    5209   |
|   temple   |    3863   |
|    lake    |    3008   |
|    tower   |    2592   |
| university |    2533   |


For each POI, a short explanatory description is scraped. The word count distribution of the descriptions are:

![Word count hist](https://github.com/rbiswasfc/Recommender-System/blob/master/images/word_count_poi.PNG)

Histogram plot for number of words in text description of POIs

Cluster analysis is next performed to identify patterns among the POIs. To this end, the TF-iDF features computed from the POI text descriptions are used. To find the optimum number of clusters, the elbow method is adopted.


![cluster elbow](https://github.com/rbiswasfc/Recommender-System/blob/master/images/clustering_elbow.PNG)

Subsequently, POIs are grouped into 12 clusters. The most frequent words in the top 4 clusters are shown in the following.

#### Cluster 1

![cluster1](https://github.com/rbiswasfc/Recommender-System/blob/master/images/cluster_1.PNG)

#### Cluster 2

![cluster2](https://github.com/rbiswasfc/Recommender-System/blob/master/images/cluster_2.PNG)

#### Cluster 3

![cluster3](https://github.com/rbiswasfc/Recommender-System/blob/master/images/cluster_3.PNG)

#### Cluster 4

![cluster4](https://github.com/rbiswasfc/Recommender-System/blob/master/images/cluster_4.PNG)

Evidently, each cluster captures different POI types. For example, in cluster 1 and 2, the dominant POI types are waterfall and castle respectively.


### TripAdvisor (TA)

The purpose of scraping information from TripAdvisor is to create user-attraction matrix. It is thus important to have multiple reviews from each user in the dataset. A histogram plot for number of reviews per user is shown below:

![user rating frequency](https://github.com/rbiswasfc/Recommender-System/blob/master/images/user_review_comment_dist.PNG)

As observed, 53k+ users have at least 5 reviews. A total of 436k+ reviews are considered from these users.

##### Sample from TA_reviews table

![Rating Sample](https://github.com/rbiswasfc/Recommender-System/blob/master/images/sample_ratings.PNG)

##### Reviews from a specific user
![One user ratings](https://github.com/rbiswasfc/Recommender-System/blob/master/images/ratings_one_user_TA.PNG)

The rating distribution is next explored. Each review rates an attraction from 1 to 5. The histogram for rating distribution is shown below:

![Rating Dist](https://github.com/rbiswasfc/Recommender-System/blob/master/images/TA_ratings_hist.PNG)

A non-uniform rating distribution is apparent from the above plot. The average and standard deviation is computed to be 4.37 and 0.89 respectively.

## Data cleaning

Although the information extracted from Triposo and TripAdvisor are well structured the most part, a few issues needed to be fixed before model development:

* POIs (Triposo) for which the description has less than 4 words are dropped
* For POI description leading and trailing '\n' character is removed
* Reviews from users (TripAdvisor) with less 5 reviews are dropped
* Attractions (tripAdvisor) with less than 5 reviews are removed

## [Feature extraction](https://github.com/rbiswasfc/Recommender-System/blob/master/notebooks/Feature_extraction.ipynb)

### TF-iDF

After data cleaning, a total of 230625 POIs are considered for further analyses. For each of these POIs, a 5000 dimensional feature vector is computed using TF-iDF approach. The TF-iDF matrix shape is thus (230625, 5000). TF-iDF (term frequency-inverse document frequency) is a numerical statistic that is intended to reflect how important a word is to a document. Inverse document frequency term is used to bring down importance of words that appear in many documents.

### Universal Sentence Encoder

The Universal Sentence Encoder (by Google) converts any sentence into a meaningful vector.

![USE](https://github.com/rbiswasfc/Recommender-System/blob/master/images/use_google.PNG)

The sentence encoder is built based on the Deep Average Network (DAN) architecture. It is pre-trained on a large corpus and can be used in a variety of tasks such as sentimental analysis, classification and so on. POI descriptions from Triposo are passed through the universal sentence encoder network to get a 512-dimensional vector representation for each POI. Note that, these 512 dimensional vectors are able to capture the underlying context sensitive semantic information, which is lacking in simple count based TF-iDF method.

### Image features

POIs for which a cover image is available, additional image based feature can be extracted using a pre-trained convNet architecture such as ResNet, Inception Network or EfficientNet. This step is under progress.

### User-attraction interaction matrix

The collaborative filtering mechanism leverages information from the item-attraction matrix. This interaction matrix can be created by joining the TA_reviews and TA_attractions table. (to be completed soon ...)

## Recommender system

### Content-based recommender system

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

#### WikiVoysge

I have built another content-based recommender system using the wiki-travel database. The notebooks describing the implementation can be found [here](https://github.com/rbiswasfc/Recommender-System/blob/master/notebooks/wikiVoyageRecSys.ipynb).


### Collaborative filtering
* Get user profile in terms of latent variable


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

