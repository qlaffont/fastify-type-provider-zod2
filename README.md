[![Maintainability](https://api.codeclimate.com/v1/badges/9079044bcd955c2a6d59/maintainability)](https://codeclimate.com/github/qlaffont/fastify-type-provider-zod2/maintainability) [![Test Coverage](https://api.codeclimate.com/v1/badges/9079044bcd955c2a6d59/test_coverage)](https://codeclimate.com/github/qlaffont/fastify-type-provider-zod2/test_coverage) ![npm](https://img.shields.io/npm/v/fastify-type-provider-zod2) ![npm](https://img.shields.io/npm/dm/fastify-type-provider-zod2) ![Snyk Vulnerabilities for npm package](https://img.shields.io/snyk/vulnerabilities/npm/fastify-type-provider-zod2) ![NPM](https://img.shields.io/npm/l/fastify-type-provider-zod2)
# fastify-type-provider-zod2

Fastify Type provider for Zod.

Inspired by [turkerdev/fastify-type-provider-zod](https://github.com/turkerdev/fastify-type-provider-zod) who is not maintened anymore.

## Usage

```bash

pnpm install fastify-type-provider-zod2 zod

```


## How to use?

```js
import Fastify from "fastify";
import { serializerCompiler, validatorCompiler, ZodTypeProvider } from "fastify-type-provider-zod2";
import z from "zod";

const app = Fastify()

// Add schema validator and serializer
app.setValidatorCompiler(validatorCompiler);
app.setSerializerCompiler(serializerCompiler);

app.withTypeProvider<ZodTypeProvider>().route({
  method: "GET",
  url: "/",
  // Define your schema
  schema: {
    querystring: z.object({
      name: z.string().min(4),
    }),
    response: {
      200: z.string(),
    },
  },
  handler: (req, res) => {
    res.send(req.query.name);
  },
});

app.listen({ port: 4949 });
```

## How to use together with @fastify/swagger

```ts
import fastify from 'fastify';
import fastifySwagger from '@fastify/swagger';
import fastifySwaggerUI from '@fastify/swagger-ui';
import { z } from 'zod';

import {
  jsonSchemaTransform,
  createJsonSchemaTransform,
  serializerCompiler,
  validatorCompiler,
  ZodTypeProvider,
} from 'fastify-type-provider-zod2';

const app = fastify();
app.setValidatorCompiler(validatorCompiler);
app.setSerializerCompiler(serializerCompiler);

app.register(fastifySwagger, {
  openapi: {
    info: {
      title: 'SampleApi',
      description: 'Sample backend service',
      version: '1.0.0',
    },
    servers: [],
  },
  transform: jsonSchemaTransform,
  // You can also create transform with custom skiplist of endpoints that should not be included in the specification:
  //
  // transform: createJsonSchemaTransform({
  //   skipList: [ '/documentation/static/*' ]
  // })
});

app.register(fastifySwaggerUI, {
  routePrefix: '/documentation',
});

const LOGIN_SCHEMA = z.object({
  username: z.string().max(32).describe('Some description for username'),
  password: z.string().max(32),
});

app.after(() => {
  app.withTypeProvider<ZodTypeProvider>().route({
    method: 'POST',
    url: '/login',
    schema: { body: LOGIN_SCHEMA },
    handler: (req, res) => {
      res.send('ok');
    },
  });
});

async function run() {
  await app.ready();

  await app.listen({
    port: 4949,
  });

  console.log(`Documentation running at http://localhost:4949/documentation`);
}

run();
```

## Test

To test this package:

```bash
pnpm test
```

## Maintain

This package use [TSdx](https://github.com/jaredpalmer/tsdx). Please check documentation to update this package.
