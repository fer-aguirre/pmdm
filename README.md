# Political Misogynistic Discourse Monitor (PMDM)

`Political Misogynistic Discourse Monitor` is a [web application](https://turing.iimas.unam.mx/pmdm/) and API that detects hate speech against women in several languages.

We would like to thank ***Iván Vladimir*** for all his help developing the software, the website and the application programming interface. We also want to acknowledge [IIMAS](https://www.iimas.unam.mx/) for hosting the project.

**Team members:**
- Bárbara Libório
- Marina Gama
- Helena Bertho
- Gaby Bouret
- Jose Luis Peñarredonda
- Fer Aguirre

## Contents
- [Introduction](#introduction)
- [Data](#data)
  - [Corpus Creation](#corpus-creation)
  - [Labelling](#labelling)
  - [Inter Annotator Agreement](#inter-annotator-agreement)
  - [Data Analysis](#data-analysis)
    - [Pre-pocessing tweets](#pre-processing-tweets)
    - [Vocabulary statistics](#vocabulary-statistics)
    - [Vocabulary frequencies](#vocabulary-frequencies)
    - [Top 50 word frequencies](#top-50-word-frequencies)
    - [Histograms of length of tweets](#histograms-of-length-of-tweets)
    - [Wordcloud](#wordcloud)
- [System Architecture](#system-architecture)
  - [Methodology](#methodology)
  - [Pre-trained Models](#pre-trained-models)
- [API Documentation](#api-documentation)
  - [Requests Library](#requests-library)
  - [POST Request](#post-request)
  - [Status Code](#status-code)
  - [Classifying Text](#classifying-text)
  - [Classifying Files](#classifying-files)
  - [More Examples](#more-examples)
- [Future Work](#future-work)
- [Related Work](#related-work)

## Introduction

The *Political Misogynistic Discourse Monitor* team is part of the [JournalismAI](https://www.lse.ac.uk/media-and-communications/polis/JournalismAI) Collab in the Americas from [LSE](https://www.lse.ac.uk/). This collaboration is an attempt to accelerate the development of [MonitorA](https://azmina.com.br/projetos/monitora/), a project by [Azmina](https://azmina.com.br/) with [InternetLab](https://www.internetlab.org.br/en/) and [Institute Update](https://www.institutoupdate.org.br/en/home/), which gathered evidence and insight of systematic misogynistic attacks of women candidates in Brazilian local elections of 2020. 

According to the report [*Violence Against Women in Politics*](https://www.unwomen.org/en/digital-library/publications/2014/6/violence-against-women-in-politics), this kind of violence is a deterrent to the participation of women in the political sphere, where women from marginalized communities are disproportionately affected. [UN Women](https://www.unwomen.org/en/what-we-do/leadership-and-political-participation/facts-and-figures) states that in Latin America’s context, women hold barely 30% of parliamentary seats. In addition, UN Women stands out that “gender equality in the highest positions of power will not be reached for another 130 years”. The mentioned facts lead us to analyze how the violence against women is perpetrated and has an impact in their participation. We want to report this kind of disinformation and attacks throughout Latin America in an effort to motivate new narratives where women have a safe space in their involvement in politics. 

For the above reason, although this AI model is able to identify violence against women in general, we want to focus on misogyny in political discourse as a case study in Latin America. In our project, we support that the automation of detecting misogynistic discourse is just a tool to help identifying attacks against women among a large volume of data on Twitter, so the system highlights content that can be analyzed by a human moderator afterward.

## Data

Since the collaborators are from Latin American countries, this model was trained with Spanish and Portuguese tweets posted from 2020 to 2021. We retrieved 4179 tweets from Twitter in 'csv' format.

> There are missing 270 tweets from the database we used to train the model and the database we share in this repository since we couldn't recover the IDs from these tweets. All the following amounts belong to the database training.

| database training | database repository |
| :-: | :-: |
| 4179 | 3909 |

- ### Corpus Creation:

We created a dictionary on Spanish and one on Portuguese with misogynistic terms and phrases. Along with that, we made two lists of usernames: one for trendy politicians and a second for black, indigenous and trans women politicians, journalists and activists from Brazil, Argentina, Colombia and Mexico. Therefore tweets mentioning those usernames were collected from Twitter using [Meltwater](https://www.meltwater.com/en) and filtered by the dictionaries with regular expressions.

- ### Labelling:

The [data file](https://github.com/fer-aguirre/pmdm/blob/master/data/tweets.csv) includes three columns:

1. **ID**: As Twitter's policy prevents from sharing tweets messages, we only included the ID from each tweet, considering that IDs are allowed to be downloadable.


2. **Classification**: Tweets are annotated with the label `1` if they are misogynistic or `0` if they are not. Misogynistic discourse were positive in 2637 tweets and negative in 1542 tweets.

![Tuits per classes](/data-analysis/classification.png)

3. **Language**: There's a label for the language of the tweet, `es` for Spanish and `pt` for Portuguese. There are 2087 tweets on Spanish and 2092 on Portuguese.

![Tuits per language](/data-analysis/language.png)

- ### Inter Annotator Agreement:

The annotation for this database to detect misogyny was performed by 6 human annotators (5 women and 1 man) which first language are Spanish or Portuguese and that are based on the country of each dataset (Brazil, Argentina, Colombia and Mexico). In order to validate the annotation, all the classification labels had a checker different from the first annotator. If the checker agreed on the label, the classification remained. Otherwise, the tweet was removed from the database.

- ### Data Analysis

#### Pre-processing tweets

There are several pre-processing steps on Natural Language Processing that can be applied to the data:

- **Lowers:** All words are lowered. *(e.g., GitHub → github)*
- **Stop words:** Remove words that are very common but don’t provide useful information. *(e.g., prepositions)*
- **Demojize:** Change emojis to textual representation. *(e.g., ☺️ → :smiling_face:)*
- **URLs:** Replace URLs with `$URL$` *(e.g., https://github.com/ → $URL$)*
- **Mentions:** Replace mentions with `$MENTION$` *(e.g., @github → $MENTION$)*
- **Hashtags:** Replace hashtags with `$HASHTAG$` *(e.g, #github → $HASHTAG$)*
- **Emojis:** Replace emojis with `$EMOJI$` *(e.g., 😃 → $EMOJI$)*
- **Smileys:** Replace smileys with `$SMILEY` *(e.g, :) → $SMILEY)*
- **Numbers:** Replace numbers with `$NUMBER$` *(e.g, 4 → $NUMBER$)*
- **Escaped characters:** Replace escaped characters with `$ESCAPE_CHAR$` *(e.g, char(2) → $ESCAPE_CHAR$)*

#### Vocabulary statistics

| | Frequency | Description |
| :-: | :-: | :-: |
| count | 19063 | Number of different words |
| mean | 3.444841 | The average number words appear |
| std | 13.935922 | The standard deviation associated to the words |
| min | 1 | The minimum number that a word appears |
| 25% | 1 | Up to 25% of the words appear |
| 50% | 1 | Up to 50% of the words appear |
| 75% | 2 | Up to 75% of the words appear |
| max | 1062 | The maximum number that a word appears |

#### Vocabulary frequencies

This graph shows the full vocabulary of the data:

![Vocabulary frequencies](/data-analysis/vocabulary_frequency.png)

#### Top 50 word frequencies

This graph shows the fifty most common words in the data: 

![Top 50 word frequencies](/data-analysis/common_words.png)

#### Histograms of length of tweets

These graphs show the number of tweets with a certain length:

![Histograms of length of tokens](/data-analysis/lenght_token.png)
![Histograms of length of chars](/data-analysis/lenght_chars.png)

#### Wordcloud

This is a wordcloud with the most common words.

![Wordcloud](/data-analysis/wordcloud.png)

## System Architecture

![System Architecture](/architecture/diagram.png)

### Methodology

We followed a machine learning [methodology](https://en.wikipedia.org/wiki/Training,_validation,_and_test_sets) in which we used part of the labelled data to train a model which then is tested on another part of the data. During training we validated the progress of the model using a third part of the data.

| Split | Percentage |
| :-: | :-: |
| Train | 80% |
| Test | 10% |
| Validation | 10% |

### Pre-trained Models

We tested several Transformer and Adapters models. Nevertheless, [`xlm-roberta-base`](https://huggingface.co/xlm-roberta-base) was the one with the better performance on [F1 score](https://en.wikipedia.org/wiki/F-score):

| Model | Type | F1 (both) | es | pt |
| :-: | :-: | :-: | :-: | :-: |
| xlm-roberta-base | multilingual | ? | ? | ? |

For more information about all the model performances, checkout this [technical report](link).

## API [Documentation](https://turing.iimas.unam.mx/pmdm/docs)

To enable communication with the API, we need a [HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP) library to make a request-response. There are a few libraries to make HTTP requests in python. However, we'll make use of `requests` due to it is well-documented and simple.

### Requests Library

Installing package with conda:

  ```python3
  conda install requests
  ```

Installing package with pip:

  ```python3
  pip install requests
  ```

### POST Request

The POST method is used when we want to submit data to be processed to the server. Here's an example of the syntax:

`requests.post(url, headers={key: value}, json={key: value}, data={key: value})`

For more information about HTTP request methods, checkout this [guide](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods).

#### Parameter Values

| Parameter | Description |
| :-: | --- |
| url | The url of the request |
| headers | A dict to send to the url |
| json | A dict to send to the url |
| files | A dict of files to send to the url |
| data | A dict or list of tuples to send to the url|

### Status Code

The status code method shows the result when a request is sent. Responses can be grouped in five categories:

1. Informational `100`-`199`
2. Succesful `200`-`299`
4. Redirection `300`-`399`
5. Client error `400`-`499`
6. Server error `500`-`599`

For more information about HTTP response status codes, checkout this [guide](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status).

### Classifying Text 

  ```python3 
  import requests
  
  url = 'https://turing.iimas.unam.mx/pmdm/api/classify'
  
  headers = {'access-token': 'token'}
  
  tweet = {'tweet': 'text to classify'}

  response = requests.post(url, headers=headers, json=tweet)

  print(response.status_code)

  response.json()
  ``` 
Default tweet arguments:
  
  ```python3
  {
    'tweet': 'string',
    'use_lower': 'false',
    'demojize': 'true',
    'process_urls': 'true',
    'process_mentions': 'true',
    'process_hashtags': 'true',
    'process_emojis': 'false',
    'process_smileys': 'false',
    'process_numbers': 'false',
    'process_escaped_chars': 'false'
  }
```

### Classifying Files

  ```python3 
  import requests
  
  url = 'https://turing.iimas.unam.mx/pmdm/api/classify_file'
  
  headers = {'access-token': 'token'}
  
  files = {'uploaded_file': open('filename.csv', 'rb')}
  
  # Tweet parameters are required
  data = {'use_lower': 'false', 'demojize': 'true', 'process_urls': 'true', 
  'process_mentions': 'true', 'process_hashtags': 'true', 'process_emojis': 'false', 
  'process_smileys': 'false', 'process_numbers': 'false', 'process_escaped_chars': 'false'}

  response = requests.post(url, headers=headers, files=files, data=data)

  print(response.status_code)

  response.json()
  ``` 

### More Examples

For more examples, see this [Jupyter Notebook](https://nbviewer.org/github/fer-aguirre/pmdm/blob/master/notebooks/examples.ipynb)

## Future Work

For future work we would like to create datasets from Latin American countries not included at this point in the interest of keeping the model's training. Furthermore, we will use the API to streamline the detection and to analyze instances of misogynistic discourse on social media.

Since we are aware that the management of an API is still not very accessible for a lot of newsrooms in the region due to technical requirements, we want to document and methodize use applications that hopefully inspire and help other organizations to work with this tool. 

## Related work

[violentometro-online](https://violentometro-online.herokuapp.com/) -> [Documentation](https://github.com/violentometro-online-team/violentometro-online)
