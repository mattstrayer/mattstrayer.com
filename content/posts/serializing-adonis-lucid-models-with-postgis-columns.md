---
title: "Serializing Adonis Lucid Models with Postgis Columns"
description: A quick and easy way to fetch postgis columns with Adonis' Lucid models
date: 2023-05-15T17:19:03-04:00
lastMod: 2023-05-15T17:19:03-04:00
tags: ["adonis", "lucid orm", "vandal", "postgis", "point", "geospatial"]
categories: ["adonis", "building vandal"]
---
## why

I'm currently in the middle of backend rewrite for [vandal](https://www.vandal.app) from [Django](https://www.djangoproject.com/) to [Adonisjs](https://adonisjs.com/). Overall, things have gone swimmingly and the developer experience has been wonderful -- so has the type-safety. My biggest pain point has been around the lack of searchable historical knowledge, which makes sense because Adonis is still a _new_ framework. This is the _why_ behind this series of blog posts. I aim to add to that searchable knowledge bit by bit & encourage you to do the same!

## intro

Adonis doesn't have first-class support for geospatial database columns, but they do offer a [cookbook](https://docs.adonisjs.com/cookbooks/using-knex-postgis-with-lucid#document) that shows you how to leverage [knex-postgis](https://github.com/jfgodoy/knex-postgis) in your Lucid ORM models. To keep this post brief, I'll assume you've followed the cookbook and set up your postgis database already.

## what's the POINT

When querying for a `geo-point` column in our database I kept receiving a string of characters like this (`point: '0101000020E6100000276893C327A25EC0D881734694F24240',`) instead of the value that was in the database (`POINT(10 -20)`).

This was a serialization issue. I needed to explicitly tell my query about the point field I was returning with the query so it could be properly serialized. The cookbook references a method for doing this leveraging `knex-postgis`:

```typescript
import Database from '@ioc:Adonis/Lucid/Database'

await Database
  .from('points')
  .select(
    'id',
    Database.st().asText('point')
  )
```

This method works, but I want to be able to leverage the power of Lucid ORM models! After some digging around in the documentation, I found two very useful lifecycle hooks & cooked up my own solution.

## lucid orm + postgis

Lucid exposes two critical lifecycle hooks that we'll use here to properly serialize postgis point columns: `beforeFind` & `beforeFetch`. The first applies to all ORM queries that find a single record & the latter is used for all other queries.

Below is the simple solution to the problem. Before every ORM query gets sent off, we append a select statement to get everything along with the properly formatted geopoint field `point`.

```typescript
import { BaseModel, beforeCreate, column } from '@ioc:Adonis/Lucid/Orm'
import Database from '@ioc:Adonis/Lucid/Database'
import {
  ModelQueryBuilderContract,
  beforeFetch,
  beforeFind,
  column,
} from '@ioc:Adonis/Lucid/Orm'

type ModelQuery = ModelQueryBuilderContract<typeof Model>

export default class Model extends BaseModel {

  // query hooks
  // add properly formatted Geo point to every query
  @beforeFetch()
  @beforeFind()
  public static fetchWithFormattedPoint(query: ModelQuery) {
    query.select('*', Database.st().asText('point')) // point is the name of the geo-point column
  }

  @column()
  public point: any
}

```

That's it! Now you can query for a model through the ORM and access the exact database value via `point`.
