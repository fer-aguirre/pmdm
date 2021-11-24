# Monitor de Discurso Político Misógino (PMDM en inglés)

`Monitor de Discurso Político Misógino` es una [aplicación web](https://turing.iimas.unam.mx/pmdm/)  y API que detecta el discurso de odio contra las mujeres en varios idiomas.

Queremos agradecer a ***Iván Vladimir*** por toda su ayuda en el desarrollo de software y la aplicación web. También queremos agradecer a [IIMAS](https://www.iimas.unam.mx/) por alojar el proyecto.

**Miembrxs del equipo:**
- Bárbara Libório, [AzMina](https://azmina.com.br/)
- Marina Gama, [AzMina](https://azmina.com.br/)
- Helena Bertho, [AzMina](https://azmina.com.br/)
- Gaby Bouret, [La Nación Data](https://www.lanacion.com.ar/data/)
- Jose Luis Peñarredonda, [CLIP](https://www.elclip.org/)
- Fer Aguirre, [DataCrítica](https://datacritica.org/)

## Contenidos
- [Introducción](#introducción)
- [Datos](#datos)
  - [Creación de Corpus](#creación-de-corpus)
  - [Etiquetado](#etiquetado)
  - [Acuerdo entre Anotadores](#acuerdo-entre-anotadores)
- [Metodología](#metodología)
  - [Pre-procesamiento de Tweets](#pre-procesameinto-de-tweets)
  - [Análisis de Datos](#análisis-de-datos)
    - [Estadísticas del Vocabulario](#estadísticas-del-vocabulario)
    - [Frecuencias del Vocabulario](#frecuencias-del-vocabulario)
    - [50 Palabras más Frecuentes](#50-palabras-más-frecuentes)
    - [Histogramas de Longitud de los Tweets](#histogramas-de-longitud-de-los-tweets)
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
