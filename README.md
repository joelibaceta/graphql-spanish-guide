

# GraphQL una Guia en Espanol
Una traducción al español de la guía propuesta por Facebook para GraphQL en https://graphql.org

## Objetivos

Crear un aporte mas a la generacion de contenido tecnico en español para acercar el conocmiento de tecnologias emergentes a personas que no comprenden ingles.

## Estructura del proyecto

La pagina final esta generada por Jekyll (https://jekyllrb.com) el cual nos permite redactar la documentacion en markdown automatizar la generacion de contenido estatico segun el template para el proyecto y mantenerlo siempre actualizado.

Todos los archivos del contenido se encuentran en la carpeta `_posts` ordenados de forma inversa segun fecha de creación de forma intencional para lograr un orden especifico en el menu lateral de la página.

**Por ejemplo**

- `2018-07-16-introduction.md` corresponde al primer archivo de introduccion del sitio.
- `2018-07-05-caching.md` corresponde al ultimo archivo, sobre el uso del cache como buena practica.

> Sugerimos no alterar los nombres de los archivos para mantener la estructura del sidebar.

## Estructura de un archivo de contenido.

Los archivos de contenido estan escritos en formato markdown para facilitar su edicion ([ver referencia sobre formato markdown](https://guides.github.com/features/mastering-markdown/)).

Este esta dividio en 2 secciones, el encabezado y el contenido en si, el encabezado nos permite definir propiedades generales que seran interpretadas por el generador de contenido estatico, por ejemplo:

En el archivo `2018-07-16-introduction.md`

```
---
layout: post
title:  Introducción a GraphQL
categories: "Aprender"
--- 

> Aprenderas sobre GraphQL, como funciona y como usarlo en esta serie de articulos. ¿Buscas documentacion sobre como construir un Servicio GraphQL? Estas librerias te ayudaran a implementar GraphQL en [muchos diferentes lenguajes.](https://graphql.org/code/) Para un aprendizaje produndo con tutoriales praticos, visite el sitio [How to GraphQL](https://www.howtographql.com/)(En inglés)
```

Encontramos un par de propiedades:
- **layout:** Que nos define cual sera el template de la pagina que albergue el contenido de este archivo.
- **title:** El titulo que identifica el contenido de archivo.
- **categories:** La/s categoria/s a por las cuales este archivo puede ser identificado (_No es necesario crearlas previamente, se crean la primera vez que son nombradas_)

## ¿Como Contribuir?

1. En la seccion [issues](https://github.com/joelibaceta/graphql-guide-spanish/issues), encontraras una lista de tareas pendientes en las que puedes colaborar, dejanos un comentario para mostrarnos tu intencion de tomar la tarea o parte de ella y seras asignado al `issue` correspondiente.
2. Haz un `fork` del proyecto para tener tu propia copia.
3. Una vez terminado el trabajo, debes enviar un `Pull Request` con la descripcion del trabajo realizado para su revision.
4. Cuando la revision se ha terminado, esta sera añadida a la rama principal y gracias a la automatizacion del proyecto, se publicara de forma inmediata en la pagina publica de proyecto.

## ¿Como ejecutar una copia local?

El proyecto tiene 2 componentes, una gema personalizada para el tema y el proyecto jekyll local.

### Pre requisitos

- rvm o similar
- Ruby 2.3 o superior

Para instalar el entorno de desarrollo ejecutar 

```
bundle install
```

y luego para iniciar el proyecto 

```
jekyll serve
```

Podras visualizar el sitio en la url local `http://127.0.0.1:4000/`


## Tengo una idea o sugerencia

Crea un issue con tu idea o sugerencia para poder llevarla adelante.

