# hapi-swagger

This project has been forked from and inspired by [glennjones/hapi-swagger](https://github.com/glennjones/hapi-swagger).

The key differences from glennjones/hapi-swager is the support for [wurst](https://github.com/felixheck/wurst) and modifcations in usage making consumption of the library easy and straight forward. 

This is a [OpenAPI (aka Swagger)](https://openapis.org/) plug-in for [Hapi](https://hapi.dev/) When installed it will self document the API interface
in a project.

This plugin has been built specifically to work well with [wurst](https://github.com/felixheck/wurst) and to work with swagger ui out of the box.

However it is compatible and works out of the box with all hapi projects.

If you're using hapi version 18.4.0 then use the following versions of inert and vision
@hapi/inert    - 5.2.2
@hapi/vision   - 5.5.4
## Installation

You can add the module to your Hapi using:

```bash
> yarn add hapi-swaggerui
```

If you want to view the documentation from your API you will also need to install the `inert` and `vision` plugs-ins which support templates and static
content serving.

```bash
> yarn add @hapi/inert --save
> yarn add @hapi/vision --save
```

## Documentation

-   [Options Reference](optionsreference.md)
-   [Usage Guide](usageguide.md)

## Quick start

In your Hapi apps main JavaScript file add the following code to created a Hapi `server` object. You will also add the routes for you API as describe on hapijs.com site.

```Javascript
const Hapi = require('@hapi/hapi');
const Inert = require('@hapi/inert');
const Vision = require('@hapi/vision');
const HapiSwagger = require('hapi-swagger');
const Pack = require('./package');

(async () => {
    const server = await new Hapi.Server({
        host: 'localhost',
        port: 3000,
    });

    const swaggerOptions = {
        info: {
                title: 'Test API Documentation',
                version: Pack.version,
            },
        };

    await server.register([
        Inert,
        Vision,
        {
            plugin: HapiSwagger,
            options: swaggerOptions
        }
    ]);

    try {
        await server.start();
        console.log('Server running at:', server.info.uri);
    } catch(err) {
        console.log(err);
    }

    server.route(Routes);
})();
```

### Tagging your API routes

As a project may be a mixture of web pages and API endpoints you need to tag the routes you wish Swagger to
document. Simply add the `tags: ['api']` property to the route object for any endpoint you want documenting.

You can even specify more tags and then later generate tag-specific documentation. If you specify
`tags: ['api', 'foo']`, you can later use `/documentation?tags=foo` to load the documentation on the
HTML page (see next section).

```Javascript
{
    method: 'GET',
    path: '/todo/{id}/',
    options: {
        handler: handlers.getToDo,
        description: 'Get todo',
        notes: 'Returns a todo item by the id passed in the path',
        tags: ['api'], // ADD THIS TAG
        validate: {
            params: Joi.object({
                id : Joi.number()
                        .required()
                        .description('the id for the todo item'),
            })
        }
    },
}
```

Once you have tagged your routes start the application. **The plugin adds a page into your site with the route `/documentation`**,
so the the full URL for the above options would be `http://localhost:3000/documentation`.

### Typescript

**hapi-swagger** exports its own typescript definition file that can be used when registering the plugin with **Hapi**. See example below:

#### Install Typescript Definition Files

```sh
npm i @types/hapi__hapi @types/hapi__inert @types/hapi__joi @types/hapi__vision @types/node hapi-swagger --save-dev
```

#### Register Plugin with Typescript

```typescript
import * as Hapi from '@hapi/hapi';
import * as HapiSwagger from 'hapi-swagger';

// code omitted for brevity

const swaggerOptions: HapiSwagger.RegisterOptions = {
    info: {
        title: 'Test API Documentation'
    }
};

const plugins: Array<Hapi.ServerRegisterPluginObject<any>> = [
    {
        plugin: Inert
    },
    {
        plugin: Vision
    },
    {
        plugin: HapiSwagger,
        options: swaggerOptions
    }
];

await server.register(plugins);
```

## Contributing

Read the [contributing guidelines](./.github/CONTRIBUTING.md) for details.

## Thanks
