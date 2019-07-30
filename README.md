---
title: Recommender System for travel destinations
tags:
    - Data Science
    - Deep Learning
---
#### -- Project Status: Active

In this contribution, I aim at building a recommender system for tourist destinations around the globe. This is an end-to-end data science project, spanning all stages such as data collection, data exploration, data cleaning, feature extraction, model development, model validation and data visualization. These stages are briefly summarized in the following:

![Pipeline](https://github.com/rbiswasfc/Recommender-System/blob/master/images/pipeline.PNG)

## Data collection


## Model development
* Content-based recommender system
* Collaborative filtering
* Hybrid system


Building a content based recommender system for travel destinations.


## Set up Anaconda environment

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


## Content based Recommender System
I have built a base Content-based recommender system for travel destinations using the wiki-travel database. The notebooks describing the implementation can be found [here](https://github.com/rbiswasfc/Recommender-System/blob/master/notebooks/wikiVoyageRecSys.ipynb).
I have used TF-iDF to extract document features.
* To-do: use spaCy to extract named entities and use them as additional features.

### Web scraping
I have scraped the Triposo website to build a point of interest database for countries in south-east Asia.
The [web scraping notebook](https://github.com/rbiswasfc/Recommender-System/blob/master/scraping/Triposo_Scrape.ipynb)
