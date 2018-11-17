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

## Argumentos

Si lo unico que pudiesemos hacer fuese consultar por objetos y campos, GraphQL ya seria un lenguaje muy util para obtener datos. Pero cuando le agremas la posibilidad de usar argumentos a los campos, las cosas se ponen mas interesantes.

```graphql
{
  human(id: "1000") {
    name
    height
  }
}
```
Resultado
```json
{
  "data": {
    "human": {
      "name": "Luke Skywalker",
      "height": 1.72
    }
  }
}
```
En un sistema como REST, solo puedes pasar un unico conjunto de argumentos: los parametros de consulta y los segmentos de url en la solicitud, pero en GraphQL, cada campo y objeto anidado puede tener su propio conjunto de argumentos, haciendo GraphQL un reemplazo completo para consultas multiples a APIs para la obtencion de informacion, Incluso puedes pasar argumentos en campos escalares para realizar transformaciones de datos en el servidor en lugar de realizarlo en cada cliente por separado. 

```graphql
{
  human(id: "1000") {
    name
    height(unit: FOOT)
  }
}
```
Respuesta
```json
{
  "data": {
    "human": {
      "name": "Luke Skywalker",
      "height": 5.6430448
    }
  }
}
```
Los argumentos puede ser de muchos tipos distintos. En el ejemplo anterior nosotros usamos un tipo `Enumeration` el cual representa un numero finito de opciones (En este caso, unidades de distancia `METER` o `FOOT`).

[Leer mas sobre el esquemas y tipos en GraphQL aqui](https://joelibaceta.github.io/graphql-guide-spanish/aprender/schemas-and-types)

## Alias

Si tienes una vista aguda, puedes haber notado que los campos del objeto resultado coinciden con el nombre del campo en la consulta pero no incluyen argumentos, por lo que no puedes consultar directamente el mismo campo con diferentes argumentos. Es por este motivo que se necesitan alias - Estos te permiten renombrar un campo del resultado.

```graphql
{
  empireHero: hero(episode: EMPIRE) {
    name
  }
  jediHero: hero(episode: JEDI) {
    name
  }
}
```
Resultado
```json
{
  "data": {
    "empireHero": {
      "name": "Luke Skywalker"
    },
    "jediHero": {
      "name": "R2-D2"
    }
  }
}
```
En el ejemplo anterior, los dos campos `hero` podrian tener conflictos, pero como podemos asignar diferentes nombres (alias) y obtenemos ambos resultados en una misma solciitud.

## Fragmentos

Digamos que tenemos una pagina relativamente completa en tu aplicacion, la cual nos permite ver a los dos heroes lado a lado junto con sus amigos, puedes imaginar que una consulta de este tipo podria complicarse rapidamente, por que tendriamos que repetir los campos al menos una vez, uno para cada lado de la comparación.

Es por eso que GraphQL incluye unidades reutilizables llamadas fragmentos. Los fragmentos permiten construir conjuntos de campos y luego incluirlos en las consultas donde sea necesario. Aquí hay un ejemplo de cómo podría resolver la situación anterior utilizando fragmentos:

```graphql
{
  leftComparison: hero(episode: EMPIRE) {
    ...comparisonFields
  }
  rightComparison: hero(episode: JEDI) {
    ...comparisonFields
  }
}

fragment comparisonFields on Character {
  name
  appearsIn
  friends {
    name
  }
}
```
Resultado
```json
{
  "data": {
    "leftComparison": {
      "name": "Luke Skywalker",
      "appearsIn": [
        "NEWHOPE",
        "EMPIRE",
        "JEDI"
      ],
      "friends": [
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        },
        {
          "name": "C-3PO"
        },
        {
          "name": "R2-D2"
        }
      ]
    },
    "rightComparison": {
      "name": "R2-D2",
      "appearsIn": [
        "NEWHOPE",
        "EMPIRE",
        "JEDI"
      ],
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
Puedes ver como la consulta anterior podria ser bastante reptitiva si los campos fuesen repetidos. El concepto de fragmentos es frecuentemente usado para separar requerimeintos de datos de aplicaciones complejas, en partes mas pequeñas, especialmente cuando necesita combinar muchos componentes de la interfaz de usuario con diferentes fragmentos en una consulta de datos.

### Usando variables en los fragmentos

Es posible que los fragmentos accedan a variables declaradas en la consulta o mutación. [Ver variables.](https://joelibaceta.github.io/graphql-guide-spanish/aprender/queries-and-mutations)

```graphql
query HeroComparison($first: Int = 3) {
  leftComparison: hero(episode: EMPIRE) {
    ...comparisonFields
  }
  rightComparison: hero(episode: JEDI) {
    ...comparisonFields
  }
}

fragment comparisonFields on Character {
  name
  friendsConnection(first: $first) {
    totalCount
    edges {
      node {
        name
      }
    }
  }
}
```
Resultado
```
{
  "data": {
    "leftComparison": {
      "name": "Luke Skywalker",
      "friendsConnection": {
        "totalCount": 4,
        "edges": [
          {
            "node": {
              "name": "Han Solo"
            }
          },
          {
            "node": {
              "name": "Leia Organa"
            }
          },
          {
            "node": {
              "name": "C-3PO"
            }
          }
        ]
      }
    },
    "rightComparison": {
      "name": "R2-D2",
      "friendsConnection": {
        "totalCount": 3,
        "edges": [
          {
            "node": {
              "name": "Luke Skywalker"
            }
          },
          {
            "node": {
              "name": "Han Solo"
            }
          },
          {
            "node": {
              "name": "Leia Organa"
            }
          }
        ]
      }
    }
  }
}
```
## Nombre de Operacion

Hasta ahora, hemos estado usando una sintaxis abreviada donde omitimos la palabra clave `query` y el nombre de la consulta, pero en aplicaciones de producción es útil usarlas para hacer que nuestro código sea menos ambiguo.

Aquí hay un ejemplo que incluye la palabra clave `query` como _tipo de operación_ y `HeroNameAndFriends` como nombre de operación:

```graphql
query HeroNameAndFriends {
  hero {
    name
    friends {
      name
    }
  }
}
```
Resultado
```json
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
El _tipo de operación_ es cualquier consulta, mutación o suscripción y describe qué tipo de operación pretende realizar. El tipo de operación es obligatorio a menos que esté usando la sintaxis abreviada de la consulta, en cuyo caso no puede proporcionar un nombre o definicion de variables.

El nombre de la operación es un nombre significativo y explícito para la operación. Solo se requiere en documentos de operaciones múltiples, pero se recomienda su uso porque es muy útil para la depuración y el registro del lado del servidor. Cuando algo sale mal, es más fácil identificar una consulta en su código, registros de red o en su servidor GraphQL por nombre en lugar de intentar descifrar el contenido. Piense en esto como el nombre de una función en su lenguaje de programación favorito. Por ejemplo, en JavaScript podemos trabajar fácilmente solo con funciones anónimas, pero cuando asignamos un nombre a una función, es más fácil rastrearlo, depurar nuestro código y registrar cuando se llama. De la misma manera, los nombres de consulta y mutación de GraphQL, junto con los nombres de los fragmentos, pueden ser una herramienta útil de depuración en el lado del servidor para identificar diferentes solicitudes de GraphQL.

## Variables

Hasta ahora, hemos estado escribiendo todos nuestros argumentos dentro de la consulta. Pero en la mayoría de las aplicaciones, los argumentos de los campos serán dinámicos: por ejemplo, podría haber un menú desplegable que te permita seleccionar el episodio de Star Wars en el que estás interesado, un campo de búsqueda o un conjunto de filtros.

No sería una buena idea pasar estos argumentos dinámicos directamente en la consulta, porque entonces nuestro código del lado del cliente necesitaría manipular dinámicamente la  consulta en tiempo de ejecución y serializarla en un formato específico de GraphQL. En su lugar, GraphQL tiene una forma de _primera clase_ para factorizar los valores dinámicos de la consulta y pasarlos como un diccionario separado. Estos valores se denominan variables.

Cuando comenzamos a trabajar con variables, necesitamos hacer tres cosas:

1. Reemplace el valor estático en la consulta con $nombreDeVariable
2. Declare $nombreDeVariable como una de las variables aceptadas por la consulta
3. Pasar `nombreDeVariable: valor` en el diccionario de variables separado, especifico del transporte (Normalmente JSON)

Asi se veria todo junto:
```graphql
query HeroNameAndFriends($episode: Episode) {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}
```
Variables
```json
{
  "episode": "JEDI"
}
```
Resultado
```json
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
Ahora, en nuestro código del cliente, podemos pasar una variable diferente en lugar de tener que construir una consulta completamente nueva. En general, esta es también una buena práctica para denotar qué argumentos de nuestra consulta se espera que sean dinámicos: nunca deberíamos hacer interpolación de cadenas para construir consultas a partir de valores proporcionados por el usuario.

### Variable definitions

La definicion de variables es la parte que se ve como `($episode: Episode)` en la consulta anterior. Funciona igual que las definiciones de argumentos para una función en un lenguaje escrito. Enumera todas las variables, prefijadas por el simbolo `$`, seguidas por su tipo, en este caso `Episode`.

Todas las variables declaradas deben ser escalares, enumeraciones o tipos de objetos de entrada. Entonces, si quiere pasar un objeto complejo a un campo, necesita saber qué tipo de entrada coincide con el servidor. Obtenga más información sobre los tipos de objetos de entrada en la página Esquema.

La definicion de variables puede ser opcional o requerido. En el caso anterior, ya que no hay un `!` Junto al tipo Episodio, es opcional. Pero si el campo al que le está pasando la variable requiere un argumento no nulo, la variable también debe ser requerida.

Para obtener más información sobre la sintaxis de estas definiciones de variables, es útil aprender el lenguaje del esquema GraphQL. El lenguaje del esquema se explica en detalle en la página Esquema.

### Variables por defecto

Tambien se pueden asignar valores predeterminados a las variables en la consulta agregando el valor después de la declaración de tipo.

```graphql
query HeroNameAndFriends($episode: Episode = JEDI) {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}
```

Cuando se asignan valores predeterminados para todas las variables, se puede llamar a la consulta sin pasar ninguna variable. Si se pasa alguna variable como parte del diccionario de variables, estas sobrescribiran los valores predeterminados.

## Directivas

Discutimos anteriormente cómo las variables nos permiten evitar la interpolación de cadenas de forma manual para construir consultas dinámicas. Pasar variables en argumentos resuelve una buena cantidad de problemas, pero también es posible que necesitemos una forma de cambiar dinámicamente la estructura y la forma de nuestras consultas utilizando variables. Por ejemplo, podemos imaginar un componente UI que tiene una vista resumida y detallada, donde una incluye más campos que el otro.

Construyamos una consulta para este componente:

```graphql
query Hero($episode: Episode, $withFriends: Boolean!) {
  hero(episode: $episode) {
    name
    friends @include(if: $withFriends) {
      name
    }
  }
}
```
Variables
```json
{
  "episode": "JEDI",
  "withFriends": false
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
Pasando `true` para la variable `withFriends`
```json
{
  "episode": "JEDI",
  "withFriends": true
}
```
Resultado
```json
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
Necesitábamos usar una nueva característica en GraphQL llamada directiva. Una directiva puede ser adjuntada a un campo o fragmento y puede afectar la ejecución de la consulta de la forma que el servidor desee. La especificación básica de GraphQL incluye exactamente dos directivas, que deben ser compatibles con cualquier implementación de servidor GraphQL:

- `@include(if: Boolean)` Solo incluya este campo en el resultado si el argumento es `true`.
- `@skip(if: Boolean)` Omita este campo si el argumento es `true`.

Las directivas pueden ser útiles para salir de situaciones en las que de otro modo necesitaría realizar manipular las cadenas para agregar y eliminar campos en su consulta. Las implementaciones de servidor también pueden agregar características experimentales al definir directivas completamente nuevas.

## Mutaciones

La mayoría de las discusiones sobre GraphQL se centran en la obtención de datos, pero cualquier plataforma de datos completa también necesita una forma de modificar los datos del lado del servidor.

En REST, cualquier solicitud puede terminar causando algunos efectos secundarios de lado del servidor, pero por convención se sugiere que no se utilicen las solicitudes GET para modificar los datos. GraphQL es similar: técnicamente cualquier consulta podría implementarse para provocar una escritura de datos. Sin embargo, es útil establecer una convención de que todas las operaciones que causan escrituras deben enviarse explícitamente a través de una mutación.

Al igual que en las consultas, si el campo de mutación devuelve un tipo objeto, puede consultar por campos anidados. Esto puede ser útil para obtener el nuevo estado de un objeto después de una actualización. Veamos un ejemplo simple de mutación:

```graphql
mutation CreateReviewForEpisode($ep: Episode!, $review: ReviewInput!) {
  createReview(episode: $ep, review: $review) {
    stars
    commentary
  }
}
```
Variables
```json
{
  "ep": "JEDI",
  "review": {
    "stars": 5,
    "commentary": "This is a great movie!"
  }
}
```
Resultado
```json
{
  "data": {
    "createReview": {
      "stars": 5,
      "commentary": "This is a great movie!"
    }
  }
}
```
Observe cómo el campo `createReview` devuelve las estrellas (`stars`) y los comentarios (`commentary`) de la revisión (`review`) recién creada. Esto es especialmente útil al mutar datos existentes, por ejemplo, al incrementar un campo, ya que podemos mutar y consultar el nuevo valor del campo con una sola solicitud.

También puede notar que, en este ejemplo, la variable `review` que pasamos no es un escalar. Es un tipo de objeto de entrada, un tipo especial de objeto que se puede pasar como un argumento. Obtenga más información sobre los tipos de entrada en la página Esquema.

### Múltiples campos en las mutaciones

Una mutación puede contener varios campos, al igual que una consulta. Hay una diferencia importante entre consultas y mutaciones, aparte del nombre:

**Mientras que los campos de consulta se ejecutan en paralelo, los campos de mutación se ejecutan en serie, uno tras otro.**

Esto significa que si enviamos dos mutaciones `incrementCredits` en una solicitud, se garantiza que la primera finalice antes de que comience la segunda, asegurando que no terminemos con una de carrera entre ellas.