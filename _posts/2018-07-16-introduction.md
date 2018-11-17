---
layout: post
title:  Introducción a GraphQL
categories: "Aprender"
--- 

> Aprenderas sobre GraphQL, como funciona y como usarlo en esta serie de articulos. ¿Buscas documentacion sobre como construir un Servicio GraphQL? Estas librerias te ayudaran a implementar GraphQL en [muchos diferentes lenguajes.](https://graphql.org/code/) Para un aprendizaje produndo con tutoriales praticos, visite el sitio [How to GraphQL](https://www.howtographql.com/)(En inglés)

GraphQL es un lenguaje de consultas para tu API y un entorno de ejecucion de consultas usando un sistema de tipos definidos desde tus datos, no esta atado a ninguna base de datos especifica o un motor de almacenamiento en su lugar esta respaldado por tu codigo y tus datos.

Un servicio GrapbQL es creado definiendo tipos (`types`) y campos (`fields`) en esos tipos, luego se proveen funciones para cada campo en cada tipo, por ejemplo, un servicio GraphQL que nos dice quien es el usuario registrado (`me`) asi como el nombre de este usuario, podria verse mas o menos asi.

```graphql
type Query {
  me: User
}

type User {
  id: ID
  name: String
}
```

Junto con las funciones para cada campo en cada tipo.

```js
function Query_me(request) {
  return request.auth.user;
}

function User_name(user) {
  return user.getName();
}
```

Una vez que el servicio GrapQL se esta ejecutando (Tipicamente en una URL o un WebService), se pueden enviar consultas GraphQL para validar y ejecutar. Una consulta recibida es primero verificada para asegurarse que solo se haga referencia a tipos y campos definidos luego se ejecutan las funciones para producir un resultado.

Por ejemplo la consulta


```graphql
{
  me {
    name
  }
}
```

Podria producir el resultado JSON:

```JS
{
  "me": {
    "name": "Luke Skywalker"
  }
}
```

Aprenda mas sobre GraphQL - El lenguaje de consulta, el sistema de tipos, como funciona el servicio GraphQL y cuales son las mejores practicas para usar GraphQL para resolver problemas comunes - in los articulos escritos en esta seccion. 

>[Seguir leyendo -> Consultas y Mutaciones ](queries-and-mutations.md)