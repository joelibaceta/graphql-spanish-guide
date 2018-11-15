---
layout: post
title:  Consultas y Mutaciones 
categories: "Aprender"
---

# Consultas y Mutaciones

En esta pagina. Aprenderas en detalle como consultar un servidor GraphQL.

## Campos

En su forma mas simple GraphQL trata sobre consultas por campos especificos. Empecemos por ver una consulta muy simple y el resultado que obtenemos.

```graphql
{
  hero {
    name
  }
}
```
Resultado
```json
{
  "data": {
    "hero": {
      "name": "R2-D2"
    }
  }
}
```

Puedes ver inmediatamente que la consulta tiene exactamente la misma forma que el resutlado. Esto es escencial en GraphQL, por que siempre obtendras lo que esperas y el servidor sabe cuales son los campos por los que el cliente esta consultando.

El campo `name`retorna un tipo `String`, en este caso el nombre del heroe principal de Star Wars, "R2-D2".

> Oh, una cosa mas, <del>la consulta anterior es interactiva. lo que significa que puedes modificarla y ver el nuevo resultado,</del> trata de agregar el campo `appearsIn` al objeto `hero` en la consulta y mira el nuevo resultado.

```graphql
{
  hero {
    name
    appearsIn
  }
}
```
Resultado
```json
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "appearsIn": [
        "NEWHOPE",
        "EMPIRE",
        "JEDI"
      ]
    }
  }
}
```
En el ejemplo anterior, consultamos por el nombre del heroe el cual retornaba un `String`, pero los campos tambien pueden refererirse a objetos. En este caso, puedes hacer una sub-seleccion de campos para este objeto. Las consultas GraphQL pueden realizarse a traves de objetos relacionados y sus campos, permitiendo que los clientes traigan mucha de la informacion relacionada en una unica consulta, en lugar de hacer multiples consultas de ida y vuelta como seria necesario en una estructura clasica REST.

```graphql
{
  hero {
    name
    # Queries can have comments!
    friends {
      name
    }
  }
}
```
Respuesta
```
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```
Note que en este ejemplo el campo `friends` retorna un arreglo de elementos. Las consultas GraphQL tienen el mismo aspecto tanto para elementos individuales o una lista de elementos. Sin embargo sabemos que resultado esperar basados en lo que indica el esquema.
