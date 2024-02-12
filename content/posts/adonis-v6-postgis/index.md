---
title: AdonisJS v6 Postgis
description: AdonisJS v6 guide to setting up Postgis
date: 2024-02-11T12:01:42-05:00
lastMod: 2024-02-11T12:01:42-05:00
tags: ["adonis", "lucid orm", "knex", "postgis", "knex-postgis", "adonis-v6", "geospatial", "postgres", "postgresql"]
categories: ["adonis"]
images: ['adonis_v6_postgis.png']
---

Adonis v6 is a significant upgrade with a [fantastic list of improvements](https://adonisjs.com/blog/adonisjs-v6-announcement). If you're using Postgis with your Adonis application, you may have come across a [cookbook](https://v5-docs.adonisjs.com/cookbooks/using-knex-postgis-with-lucid) that demonstrates how to set up an Adonis v5 application with the [knex-postgis](https://github.com/jfgodoy/knex-postgis) library. However, this guide no longer works with v6. In this post, I'll walk you through the process of adapting the cookbook to work with Adonis v6.



## Getting Started

First, we'll need to install `knex-postgis` as a dependency in our project.

```bash
npm install knex-postgis
```


## Creating an Extension

To link the knex-postgis library with Adonis' database service, we need to create an extension. Follow these steps:

1. Create a folder called `extensions` and add an empty TypeScript file named `postgis.ts`. You can choose a different name if you prefer.
2. In the `postgis.ts` file, add the following code:


```typescript
// extensions/postgis.ts

import { Database } from '@adonisjs/lucid/database'
import knexPostgis, { KnexPostgis } from 'knex-postgis'

declare module '@adonisjs/lucid/database' {
  interface Database {
    st(): KnexPostgis
  }
}

Database.macro('st', function (this: Database, connectionName?: string) {
  connectionName = connectionName || this.primaryConnectionName
  this.manager.connect(connectionName)

  const connection = this.getRawConnection(connectionName)!.connection!

  /**
   * Ensure we are dealing with a PostgreSQL connection
   */
  if (connection.dialectName !== 'postgres') {
    throw new Error('The "st" function can only be used with PostgreSQL')
  }

  /**
   * Configure extension if not already configured
   */
  if (!connection.client!['postgis']) {
    knexPostgis(connection.client!)
    if (connection.hasReadWriteReplicas) {
      knexPostgis(connection.readClient!)
    }
  }

  return connection.client!['postgis']
})
```

Let's break down what's happening in this file. First, we tap into the [Macroable](https://docs.adonisjs.com/guides/extend-adonisjs#macros-and-getters) features of Adonis to add the `st` function to the `Database` class, so that Typescript can understand it. We then implement the `st` function to configure the extension if it hasn't been configured yet, just like in the original cookbook.


## Linking it up

All that's left to do is implement the extension via a provider that runs on application boot.

```typescript
// providers/postgis_provider.ts

import type { ApplicationService } from '@adonisjs/core/types'

export default class PostgisProvider {
  constructor(protected app: ApplicationService) {}

  /**
   * The container bindings have booted
   */
  async boot() {
    await import('../extensions/postgis.js')
  }
}

```

Finally, add it to your `adonisrc.ts` configuration file:


```typescript
// adonisrc.ts
// ...
providers : [
  // all other providers
  () => import('#providers/postgis_provider'),
]
```

## That's it!

Now you can access geospatial knex-postgis functions in your Adonis v6 application, like so:

```typescript
import db from '@adonisjs/lucid/services/db'

const point = db.st().geomFromText('POINT(1 1)')
// yields -> ST_geomFromText('Point(1 1)')
```


## Example Github Repo

I've created a demo repo on [github](https://github.com/mattstrayer/adonis-v6-postgis-demo). You can clone it, create an `.env` file with the required database credentials, and then run the `node ace postgis:test` command to test it out. If successful, it should print out the following.

```log
[ info ] ST_geomFromText('Point(0 0)')
```
