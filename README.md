# Mock Api

This module is a middleware generating function that generates an API based on a [OpenAPI 3](https://swagger.io/docs/specification/basic-structure/) compatible YAML or JSON file. All data returned is generated by [chance](https://chancejs.com/) module.

- [Mock Api](#)
  - [Install](#)
  - [Usage](#)
    - [Options](#)
    - [Specifying custom Chance options](#)
    - [A note on types](#)
    - [Returning Fixed Values](#)

## Install

`yarn add openapi-mocker`
or
`npm install openapi-mocker`

## Usage

When installed use it as normal middleware in your express application.

```ts
const openApiMockerMiddleware = await openApiMocker({
  openApiFile: "path/to/file",
});

/*...*/
app.use(openApiMockerMiddleware);
```

### Options

```ts
export interface Config {
  openApiFile?: string;
  openApi?: OpenAPIV3.Document;
  ignorePaths?: string[];
  mockPaths?: string[];
  format400?: () => {};
  format404?: (next: Function) => {};
}
```

### Specifying custom Chance options

Swagger specifies only a few primitive types; for scenarios where specific chance methods are needed, use the `x-chance-type` field.

```yaml
components:
  schemas:
    NewPet:
      properties:
      name:
        type: string
        x-chance-type: name
      tag:
        type: string
        x-chance-type: guid
```

Most of the chance methods allow some fine-tuning of the returned data. For example, the [integer](https://chancejs.com/basics/integer.html) method allows specification of minimum and maximum output values. These options can be configured in the Swagger YAML file with the `x-chance-options` block:

```yaml
components:
  schemas:
    Pet:
      allOf:
        - $ref: "#/components/schemas/NewPet"
        - required:
            - id
            properties:
            id:
              type: integer
              format: int64
              x-type-options:
                min: 1
                max: 1000
```

### A note on types

All of the primitive types defined in the [OpenApi specification](https://swagger.io/docs/specification/data-models/data-types/) are supported except for `file` and `password`. Currently, the `format` property is ignored; use `x-chance-type` instead. The server will error on any request with a type other than one of the primitive types if there is no valid x-chance-type also defined.

### Returning Fixed Values

Although not a chance method, support has been added for returning fixed values using `x-chance-type: fixed`. Any value given for the custom tag `x-type-value` will be returned; below is an example where an object is returned:

```yaml
status:
  type: object
  x-chance-type: fixed
  x-type-value:
    type: "adopted"
```
