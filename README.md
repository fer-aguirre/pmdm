# Political Misogynistic Discourse Monitor (PMDM)

`Political Misogynistic Discourse Monitor` is a [web application](https://turing.iimas.unam.mx/pmdm/) and API that detects misogynistic discourse in several languages. 

We would like to thank ***Iván Vladimir*** for all his help developing the software and the application programming interface and [IIMAS](https://www.iimas.unam.mx/) for hosting the website.

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
    - [Vocabulary statistics](#vocabulary-statistics)
    - [Vocabulary frequencies](#vocabulary-frequencies)
    - [Top 50 word frequencies](#top-50-word-frequencies)
    - [Histograms of length of tweets](#histograms-of-length-of-tweets)
    - [Wordcloud](#wordcloud)
- [System Architecture](#system-architecture)
  - [Pre-pocessing tweets](#pre-processing-tweets)
  - [Methodology](#methodology)
  - [Pre-trained models](#pre-trained-models)
- [API Documentation](#api-documentation)
  - [POST Request](#post-request)
  - [HTTP Status Code](#http-status-code)
  - [Classifying text](#classifying-text)
  - [Classifying files](#classifying-files)
  - [More examples](#more-examples)
- [Future Work](#future-work)
- [Related Work](#related-work)

## Introduction

The *Political Misogynistic Discourse Monitor* team is part of the [JournalismAI](https://www.lse.ac.uk/media-and-communications/polis/JournalismAI) Collab in the Americas from [LSE](https://www.lse.ac.uk/). This collaboration is an attempt to accelerate the development of [MonitorA](https://azmina.com.br/projetos/monitora/), a project by [Azmina](https://azmina.com.br/) with [InternetLab](https://www.internetlab.org.br/en/) and [Institute Update](https://www.institutoupdate.org.br/en/home/), which gathered evidence and insight of systematic misogynistic attacks of women candidates in Brazilian local elections of 2020. 

According to the report [*Violence Against Women in Politics*](https://www.unwomen.org/en/digital-library/publications/2014/6/violence-against-women-in-politics), this kind of violence is a deterrent to the participation of women in the political sphere, where women from marginalized communities are disproportionately affected. [UN Women](https://www.unwomen.org/en/what-we-do/leadership-and-political-participation/facts-and-figures) states that in Latin America’s context, women hold barely 30% of parliamentary seats. In addition, UN Women stands out that “gender equality in the highest positions of power will not be reached for another 130 years”. The above facts lead us to analyze how the violence against women is perpetrated and has an impact in their participation. We want to report this kind of disinformation and attacks throughout Latin America in an effort to motivate new narratives where women have a safe space in their involvement in politics. 

In our project, we support that the automation of detecting misogynistic discourse is just a tool to help identifying attacks against women among a large volume of data on Twitter, so the system highlights content that can be analyzed by a human moderator afterward.

## Data

Since the collaborators are from Latin American countries, this model was trained with Spanish and Portuguese tweets posted from 2020 to 2021. We retrieved 4179 tweets from Twitter in 'csv' format.

> There are missing 270 tweets from the database we used to train the model and the database we share in this repo since we couldn't recover the IDs from these tweets. All the following amounts belong to the database training.

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

![Vocabulary frequencies](/data-analysis/vocabulary_freq.png)

#### Top 50 word frequencies

![Top 50 word frequencies](/data-analysis/common_words.png)

#### Histograms of length of tweets

![Histograms of length of tokens](/data-analysis/lenght_token.png)
![Histograms of length of chars](/data-analysis/lenght_chars.png)

#### Wordcloud

![Wordcloud](/data-analysis/wordcloud.png)

## System Architecture

### Pre-processing tweets

There are several pre-processing steps on NLP that can be applied to the data:

- **Uncased:** All words are lowered. *(e.g., GitHub → github)*
- **Demojize:** Change emojis to textual representation. *(e.g., ☺️ → :smiling_face:)*
- **URLs:** Replace URLs with `$URL$` *(e.g., https://github.com/ → $URL$)*
- **Mentions:** Replace mentions with `$MENTION$` *(e.g., @github → $MENTION$)*
- **Hashtags:** Replace hashtags with `$HASHTAG$` *(e.g, #github → $HASHTAG$)*
- **Emojis:** Replace emojis with `$EMOJI$` *(e.g., 😃 → $EMOJI$)*
- **Smileys:** Replace smileys with `$SMILEY` *(e.g, :) → $SMILEY)*
- **Numbers:** Replace numbers with `$NUMBER$` *(e.g, 4 → $NUMBER$)*
- **Escaped characters:** Replace escaped characters with `$ESCAPE_CHAR$` *(e.g, char(2) → $ESCAPE_CHAR$)*

### Methodology

### Pre-trained models

Transformer model:

- [`bert-base-multilingual-cased`](https://huggingface.co/bert-base-multilingual-cased) and [`bert-base-multilingual-uncased`](https://huggingface.co/bert-base-multilingual-uncased)
- [`distilbert-base-multilingual-cased`](https://huggingface.co/distilbert-base-multilingual-cased)
- [`xlm-roberta-base`](https://huggingface.co/xlm-roberta-base) and [`xlm-roberta-large`](https://huggingface.co/xlm-roberta-large)

## API [Documentation](https://turing.iimas.unam.mx/pmdm/docs)

Usage examples with the Python library `requests`.

### Installing package with conda:

  ```python3
  conda install requests
  ```

### Installing package with pip:

  ```python3
  pip install requests
  ```

### POST Request

`requests.post(url, headers={key: value}, json={key: value}, data={key: value})`

#### Parameter Values

| Parameter | Description |
| :-: | --- |
| url | *Requiered:* The URL of the request |
| headers | *Optional:* A dict of HTTP to send to the url |
| json | *Optional:* A JSON to send to the url |
| files | *Optional:* A dict of files to send to the url |
| data | *Optional:* A dict, list of tuples, bytes of file object to send to the url|

### HTTP Status Code



### Classifying text 

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

### Classifying files

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

### More examples

For more examples, see this [Jupyter Notebook](https://nbviewer.org/)

## Future Work

## Related work
[violentometro-online](https://violentometro-online.herokuapp.com/) -> [Documentation](https://github.com/violentometro-online-team/violentometro-online)
