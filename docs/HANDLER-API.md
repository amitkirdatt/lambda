# Handler Object
The handler object has two attributes:
 - *validate* - `optional` validation object for path parameters, query parameters and body
 - *handler* - `required` function that performs the business logic

### validate

#### `handler.validate.params`
A [Joi](https://github.com/hapijs/joi) validation object for incoming request path parameters. After validation, the path params are available in `request.params`

*Note: The original path parameters are untouched and available in `event.pathParameters`*

```
exports.get = Lambda.define({
  validate: {
    params: Joi.object().keys({
      a: Joi.string().required(),
      b: Joi.string().required()
    })
  },
  handler: async function(request, h) {
    const {a, b} = request.params;

    return {myParams: {a, b}};
  }
});
```

#### `handler.validate.query`
A [Joi](https://github.com/hapijs/joi) validation object for incoming request query parameters. After validation, the query params are available in `request.query`

*Note: The original query parameters are untouched and available in `event.queryStringParameters`*

```
exports.get = Lambda.define({
  validate: {
    query: Joi.object().keys({
      a: Joi.string(),
      b: Joi.string()
    })
  },
  handler: async function(request, h) {
    const {a, b} = request.query;

    return {myQuery: {a, b}};
  }
});
```

#### `handler.validate.payload`
A [Joi](https://github.com/hapijs/joi) validation object for incoming request payload. After validation, the payload is available in `request.payload`

*Note: The original payload is untouched and available in `event.body`*

```
exports.get = Lambda.define({
  validate: {
    body: Joi.object().keys({
      a: Joi.string().required,
      b: Joi.string()
    })
  },
  handler: async function(request, h) {
    const {a, b} = request.payload;

    return {myPayload: {a, b}};
  }
});
```


### `handler`
The handler function that performs the business logic

The function takes in two parameters:
 - `request` - This is the `event` object that the Lambda is invoked with
 - `h` - The response toolkit. You can access the `context` object at `h.context`. The `context` here is the `context` object that the lambda is invoked with.

More information on the [AWS Lambda handler](https://docs.aws.amazon.com/lambda/latest/dg/nodejs-handler.html)

The response toolkit `h` is useful in settting specific HTTP status codes

```
const Lambda = require('@medial/lambda');
const Boom = require('@hapi/boom');
const Joi = require('@hapi/joi');


exports.get = Lambda.define({
  validate: {
    query: Joi.object().keys({
      error: Joi.boolean().default(false)
    })
  },
  handler: async function(request, h) {
    const isError = request.query.error;

    if (isError) {
      throw Boom.notFound('boo boo');
    }

    return h.response({hello: 'world'}).code(200);
  }
});
```