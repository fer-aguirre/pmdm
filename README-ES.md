### [Português](README-PT.md) | [English](README.md)
---
# Monitor de Discurso Misógino Político (PMDM en inglés)

El `Monitor del Discurso Político Misógino` es una [aplicación web](https://turing.iimas.unam.mx/pmdm/) y una API que detecta el discurso de odio contra mujeres en español y portugués. 

Este proyecto forma parte del [2021 JournalismAI Collab Challenges](https://blogs.lse.ac.uk/polis/2021/03/23/journalismai-collab-challenges/), una iniciativa global que reúne a organizaciones de medios de comunicación para explorar soluciones innovadoras para mejorar el periodismo mediante el uso de tecnologías de IA. Se desarrolló como parte del grupo de **Américas** del Collab Challenges que se centró en [*¿Cómo podríamos utilizar las tecnologías de IA para innovar la recopilación de noticias y las técnicas de reportaje e investigación?*](https://www.youtube.com/watch?v=wBJ9KbR-yWQ) en colaboración entre [AzMina](https://azmina.com.br/) (Brasil), [La Nación](https://www.lanacion.com.ar/data/) (Argentina), [CLIP](https://www.elclip.org/) (Colombia) y [Data Crítica](https://datacritica.org/) (México) con el apoyo del [Knight Lab](https://knightlab.northwestern.edu/) de la Universidad Northwestern.

[JournalismAI](https://www.lse.ac.uk/media-and-communications/polis/JournalismAI) es un proyecto de [Polis](https://www.lse.ac.uk/media-and-communications/polis) -el grupo de reflexión sobre periodismo de la London School of Economics and Political Science- y está patrocinado por [Google News Initiative](https://newsinitiative.withgoogle.com/). Si quieres saber más sobre los Collab Challenges y otras actividades de JournalismAI, [suscríbete al newsletter](https://mailchi.mp/lse.ac.uk/journalismai) o pónte en contacto con el equipo a través de hello@journalismai.info

![API ejemplo de uso](./assets/webapp.gif)

## Contenidos
- [Introducción](#introducción)
- [Datos](#datos)
  - [Creación de Corpus](#creación-de-corpus)
  - [Etiquetado](#etiquetado)
  - [Acuerdo entre Anotadores](#acuerdo-entre-anotadores)
- [Metodología](#metodología)
  - [Pre-procesamiento de Tuits](#pre-procesameinto-de-tuits)
  - [Análisis de Datos](#análisis-de-datos)
    - [Estadísticas del Vocabulario](#estadísticas-del-vocabulario)
    - [Frecuencia del Vocabulario](#frecuencia-del-vocabulario)
    - [50 Palabras más Frecuentes](#50-palabras-más-frecuentes)
    - [Histogramas de Longitud de los Tuits](#histogramas-de-longitud-de-los-tweets)
    - [Nube de Palabras](#nube-de-palabras)
  - [Modelos Pre-entrenados](#modelos-pre-entrenados)
  - [Arquitectura del Sistema](#arquitectura-del-sistema)
- [Documentación de la API](#documentación-de-la-api)
  - [Librería Requests](#librería-requests)
  - [Solicitudes POST](#solicitudes-post)
  - [Código de Estado](#código-de-estado)
  - [Clasificación de Texto](#clasificación-de-texto)
  - [Clasificación de Archivos](#clasificación-de-archivos)
  - [Más Ejemplos](#más-ejemplos)
- [Trabajo Futuro](#trabajo-futuro)
- [Proyectos Relacionados](#proyectos-relacionados)
- [Bibliografía](#bibliografía)

---

## Introducción

Esta colaboración es un intento de acelerar el desarrollo de [MonitorA](https://azmina.com.br/projetos/monitora/), un proyecto de [AzMina](https://azmina.com.br/) con [InternetLab](https://www.internetlab.org.br/en/) e [Institute Update](https://www.institutoupdate.org.br/en/home/), que reunió pruebas y conocimientos sobre los ataques misóginos sistemáticos a las mujeres candidatas en las elecciones locales brasileñas de 2020. 

Según el informe [*Violencia contra las mujeres en la política*](https://www.unwomen.org/en/digital-library/publications/2014/6/violence-against-women-in-politics), este tipo de violencia es un elemento disuasorio para la participación de las mujeres en la esfera política, donde las mujeres de comunidades marginadas se ven afectadas de forma desproporcionada. [ONU Mujeres](https://www.unwomen.org/en/what-we-do/leadership-and-political-participation/facts-and-figures) afirma que en el contexto latinoamericano, las mujeres apenas ocupan el 30% de los asientos parlamentarios. Además, ONU Mujeres destaca que "la igualdad de género en los más altos puestos de poder no se alcanzará hasta dentro de 130 años". Los hechos mencionados nos llevan a analizar cómo la violencia contra las mujeres se perpetra y repercute en su participación. Queremos denunciar este tipo de desinformación y ataques en toda América Latina, en un esfuerzo por motivar nuevas narrativas donde las mujeres tengan un espacio seguro en su participación en la política. 

Por lo anterior, aunque este modelo de IA es capaz de identificar la violencia contra las mujeres en general, queremos centrarnos en la misoginia en el discurso político como caso de estudio en América Latina. En nuestro proyecto, apoyamos que la automatización de la detección del discurso misógino es sólo una herramienta para ayudar a identificar los ataques contra las mujeres entre un gran volumen de datos en Twitter, por lo que el sistema destaca el contenido que puede ser analizado por un moderador humano después.

---

## Datos

Dado que las colaboradoras somos de países latinoamericanos, este modelo se entrenó con tuits en español y portugués publicados entre 2020 y 2021. Recuperamos 4179 tuits de Twitter en formato 'csv'.

> Faltan 270 tuits de la base de datos que usamos para entrenar el modelo y de la base de datos que compartimos en este repositorio ya que no pudimos recuperar los IDs de esos tuits. Todas las cantidades del análisis de datos pertenecen a la base de datos de entrenamiento.

| entrenamiento de la base de datos | repositorio de la base de datos |
| :-: | :-: |
| 4179 | 3909 |

1. ### Creación de Corpus:

Creamos un diccionario en español y otro en portugués con términos y frases misóginas. Junto con eso, hicimos una lista de nombres de usuario de políticos actuales. Sin embargo, consideramos que esas cuentas no serían lo suficientemente inclusivas, así que decidimos hacer una segunda lista exclusivamente para mujeres diversas (negras, indígenas y LGBTQIA+) políticas, periodistas y activistas de Brasil, Argentina, Colombia y México. Por lo tanto, los tweets que mencionaban ambas listas de nombres de usuario se recopilaron de Twitter utilizando [Meltwater](https://www.meltwater.com/en) y se filtraron por los diccionarios con expresiones regulares.

2. ### Etiquetado:

  El [archivo de datos](https://raw.githubusercontent.com/fer-aguirre/pmdm/main/data/pmdm_data.csv) incluye tres columnas:

  - **ID**: Dado que la [política de Twitter](https://developer.twitter.com/en/developer-terms/agreement-and-policy) impide compartir los mensajes de los tuits, sólo incluimos el ID de cada uno de ellos, teniendo en cuenta que los IDs pueden descargarse y pueden transformarse en el texto original utilizando herramientas disponibles.

  - **Clasificación**: Los tuits se anotan con la etiqueta "1" si son misóginos o "0" si no lo son. El discurso misógino fue positivo en 2.637 tuits y negativo en 1.542.

  ![Tuits por clase](./assets/classification.png)

  - **Idioma**: Hay una etiqueta para el idioma del tuit, `es` para el español y `pt` para el portugués. Hay 2087 tweets en español y 2092 en portugués.

  ![Tuits per idioma](./assets/language.png)

3. ### Acuerdo entre Anotadores:

La anotación de esta base de datos para detectar misoginia fue realizada por seis anotadoras humanas (cinco mujeres y un hombre) cuyas lenguas maternas son el español o el portugués y que radican en el país de cada conjunto de datos (Brasil, Argentina, Colombia y México). Para validar la anotación, todas las etiquetas de clasificación contaron con una verificadora diferente de la primera anotadora. Si la verificadora estaba de acuerdo con la etiqueta, la clasificación se mantenía. En caso contrario, el tweet se eliminaba de la base de datos.

---

## Metodología

Para crear el clasificador, hemos hecho uso de cinco [Python Colaboratory Notebooks](https://drive.google.com/drive/folders/11PWsMQz1IsbttRyf90Ym37--0VU-O8r2):

- **Análisis de datos:** Análisis y estadísticas básicas de los datos.
- **Entrenar y evaluar el modelo (2 versiones):** Entrena un modelo y lo evalúa, uno para Transformers y otro para Adapters.
- **Etiquetado de datos (2 versiones):** Etiqueta los datos desde el formulario de entrada del cuaderno o desde un archivo, una para Transformers y otra para Adapters.

---

1. ### Preprocesamiento de Tuits

Hay varios pasos de preprocesamiento en el Procesamiento del Lenguaje Natural que se pueden aplicar a los datos:

- **Lowers:** Todas las palabras en minúsculas. *(p.ej. GitHub → github)*
- **Stop words:** Elimina las palabras que son muy comunes pero que no aportan información útil. *(p.ej. preposiciones)*
- **Demojize:** Cambia los emojis por una representación textual. *(p.ej. ☺️ → :smiling_face:)*
- **URLs:** Sustituye las URL por `$URL$` *(p.ej. https://github.com/ → $URL$)*
- **Mentions:** Sustituye las menciones por `$MENTION$` *(p.ej. @github → $MENTION$)*
- **Hashtags:** Sustituye los hashtags por `$HASHTAG$` *(p.ej. #github → $HASHTAG$)*
- **Emojis:** Sustituye los emojis por `$EMOJI$` *(p.ej. 😃 → $EMOJI$)*
- **Smileys:** Sustituye los smileys por `$SMILEY` *(p.ej. :) → $SMILEY)*
- **Numbers:** Sustituye los números por `$NUMBER$` *(p.ej. 4 → $NUMBER$)*
- **Escaped characters:** Sustituye los caracteres escapados por `$ESCAPE_CHAR$` *(p.ej. char(2) → $ESCAPE_CHAR$)*

Cabe mencionar que obtuvimos mejores resultados con el texto en minúsculas.

Además, seguimos una [metodología] de aprendizaje automático (https://en.wikipedia.org/wiki/Training,_validation,_and_test_sets) en la que utilizamos una parte de los datos etiquetados para entrenar un modelo que luego se prueba con otra parte de los datos. Durante el entrenamiento validamos el progreso del modelo utilizando una tercera parte de los datos.

| División | Porcentaje | Tuits |
| :-: | :-: | :-: |
| Entrenamiento | 80% | 3,343 (1673 pt, 1669 es) |
| Prueba | 10% | 418 (210 pt, 209 es) |
| Validación | 10% | 418 (209 pt, 209 es) |

---

2. ### Análisis de datos

Esta sección muestra algunas estadísticas y gráficos de los datos etiquetados.

- #### Estadísticas de vocabulario:

| | Frecuencia | Descripción |
| :-: | :-: | :-: |
| count | 19063 | Número de palabras diferentes |
| mean | 3.444841 | Media de palabras que aparecen |
| std | 13.935922 | Desviación estándar asociada a las palabras |
| min | 1 | Número mínimo en que aparece una palabra |
| 25% | 1 | Hasta el 25% de las palabras que aparecen |
| 50% | 1 | Hasta el 50% de las palabras que aparecen |
| 75% | 2 | Hasta el 75% de las palabras que aparecen |
| max | 1062 | Número máximo que aparece una palabra |

- #### Frecuencia del vocabulario

Este gráfico muestra el vocabulario completo de los datos:

![Frecuencia del vocabulario](./assets/wordFrequency.png)

- #### 50 palabras más frecuentes

Este gráfico muestra las cincuenta palabras más comunes en los datos: 

![50 palabras más frecuentes](./assets/topWordFrequency.png)

- #### Histogramas de la longitud de los tuits

Estos gráficos muestran el número de tuits con una longitud determinada:

![Histogramas de la longitud de los tokens](./assets/lenghtTokens.png)
![Histogramas de la longitud de los caracteres](./assets/lenghtChars.png)

- #### Nube de Palabras

Esta es una nube de palabras con las palabras más comunes:

![Nube de Palabras](./assets/wordcloud.png)

---

3. ### Modelos Pre-entrenados

Probamos varios modelos de Transformers y Adapters. Sin embargo, `cardiffnlp/twitter-xlm-roberta-base` fue el que obtuvo el mejor rendimiento en [F1 score](https://en.wikipedia.org/wiki/F-score):

| Modelo | Tipo | Ambos| es | pt |
| :-: | :-: | :-: | :-: | :-: |
| [cardiffnlp/twitter-xlm-roberta-base](https://huggingface.co/cardiffnlp/twitter-xlm-roberta-base-sentiment) | Multilingual | ***0.8728*** | ***0.9191*** | 0.8235 |
| [neuralmind/bert-base-portuguese-cased](https://huggingface.co/neuralmind/bert-base-portuguese-cased) | Portugués | - | - | ***0.875*** |
| [dccuchile/bert-base-spanish-wwm-uncased](https://huggingface.co/dccuchile/bert-base-spanish-wwm-uncased) | Español | - | 0.8985 | - |
| [mudes/multilingual-base](https://huggingface.co/mudes/multilingual-base) | Multilingual |  0.8641 | 0.8929 | 0.8339 |
| [neuralmind/bert-base-portuguese-cased](https://huggingface.co/neuralmind/bert-base-portuguese-cased) | Portugués | - | - | 0.8496 |
| [PlanTL-GOB-ES/roberta-base-bne](https://huggingface.co/PlanTL-GOB-ES/roberta-base-bne) | Español | - | 0.9027 | - |

Para más información sobre el desempeño de los modelos, consula este [informe técnico](https://docs.google.com/document/d/1VbeUCLYFrvT02A8GIBeL_VufjtkJRgNGK54CURho8R0/).

---

4. ### Arquitectura del Sistema

Esta es la estructura del flujo de trabajo que seguimos para el proyecto:

![Arquitectura del sistema](./assets/diagram.png)

---

## [Documentación de la API](https://gitlab.com/l52mas/political-misogynistic-discourse-monitor/-/tree/main/)

Para permitir la comunicación con la [API](https://turing.iimas.unam.mx/pmdm/docs), necesitamos una biblioteca [HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP) para hacer una petición-respuesta. Hay unas cuantas bibliotecas para hacer peticiones HTTP en Python. Sin embargo, haremos uso de `requests` debido a que está bien documentada y es sencilla.

- ### Requests Library

Instalando el paquete conda:

  ```python3
  conda install requests
  ```

Instalación del paquete con pip:

  ```python3
  pip install requests
  ```

- ### POST Request

El método POST se utiliza cuando queremos enviar datos para ser procesados al servidor. Este es un ejemplo de la sintaxis:

`requests.post(url, headers={key: value}, json={key: value}, data={key: value})`

Para más información sobre los métodos de petición HTTP, consulta esta [guía](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods).

- #### Valores de los parámetros

| Parámetro | Descripción |
| :-: | --- |
| url | Una cadena con el parámetro |
| headers | Un dictado para enviar a la url |
| json | Un dictado para enviar a la url |
| files | Un dictado de archivos para enviar a la url |
| data | Un dict o una lista de tuplas para enviar a la url |

- ### Código de estado

El método de código de estado muestra el resultado cuando se envía una solicitud. Las respuestas pueden agruparse en cinco categorías:

1. Informativa `100`-`199`
2. Exitoso `200`-`299`
3. Redirección `300`-`399`
4. Error del cliente `400`-`499`
5. Error del servidor `500`-`599`

Para más información sobre los códigos de estado de respuesta HTTP, consulta esta [guía](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status).

### Clasificación de texto 

  ```python3 
  import requests
  
  url = 'https://turing.iimas.unam.mx/pmdm/api/classify'
  
  headers = {'access-token': 'token'}
  
  tweet = {'tweet': 'text to classify'}

  response = requests.post(url, headers=headers, json=tweet)

  print(response.status_code)

  response.json()
  ``` 

Argumentos por defecto de los tuits:
  
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

### Clasificación de archivos

  ```python3 
  import requests
  
  url = 'https://turing.iimas.unam.mx/pmdm/api/classify_file'
  
  headers = {'access-token': 'token'}
  
  files = {'uploaded_file': open('filename', 'rb')}
  
  # Tweet arguments required
  data = {
          'model': 'es',
          'use_lower': 'false', 
          'demojize': 'true', 
          'process_urls':'true', 
          'process_mentions': 'true', 
          'process_hashtags': 'true', 
          'process_emojis': 'false', 
          'process_smileys': 'false', 
          'process_numbers': 'false', 
          'process_escaped_chars': 'false'}

  response = requests.post(url, headers=headers, files=files, data=data)

  print(response.status_code)

  response.json()
  ``` 

### Más ejemplos

Para más ejemplos, consulta este [Jupyter Notebook](https://nbviewer.org/github/fer-aguirre/pmdm/blob/main/notebooks/examples.ipynb)

---

## Trabajo futuro

Para trabajo futuro nos gustaría crear conjuntos de datos de países latinoamericanos no incluidos en este momento con el fin de mantener el entrenamiento del modelo. Además, utilizaremos la API para agilizar la detección y analizar los casos de discurso misógino en las redes sociales.

Como somos conscientes de que el manejo de una API es aún poco accesible para muchas redacciones de la región debido a los requerimientos técnicos, queremos documentar y metodizar aplicaciones de uso que esperamos inspiren y ayuden a otras organizaciones a trabajar con esta herramienta. 

---

## Contacto

Si quieres colaborar o simplemente saber más sobre el proyecto, pónte en contacto con nosotras: 
- barbara@azmina.com.br
- faguirre@datacritica.org 

---

## Proyectos Relacionados

[violentometro-online](https://violentometro-online.herokuapp.com/) -> [Documentation](https://github.com/violentometro-online-team/violentometro-online)

## Bibliografía

- [Datasheets for Datasets](https://arxiv.org/pdf/1803.09010.pdf)
- [Ethical and technical challenges of AI in tackling hate speech](https://informationethics.ca/index.php/irie/article/view/416/390)
- [Detección de Discurso de Odio en Redes Sociales mediante Transformers y Natural Language Processing](https://medium.com/saturdays-ai/detecci%C3%B3n-de-discurso-de-odio-en-redes-sociales-mediante-transformers-y-natural-language-processing-6c07a88b2e5f)
- [Violência Política de Gênero: as diferenças entre os ataques recebidos por mulheres e seus oponentes](https://azmina.com.br/reportagens/violencia-politica-de-genero-as-diferencas-entre-os-ataques-recebidos-por-mulheres-e-seus-oponentes/)
- [Tackling Online Abuse and Disinformation Targeting Women in Politics](https://carnegieendowment.org/2020/11/30/tackling-online-abuse-and-disinformation-targeting-women-in-politics-pub-83331)
- [#ShePersisted: why gendered disinformation](https://www.she-persisted.org/why)