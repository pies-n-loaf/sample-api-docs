# Sample API Documentation
> by sandy for sandy's own needs.

[![Deploy sample API docs](https://github.com/pies-n-loaf/sample-api-docs/actions/workflows/deploy.yml/badge.svg)](https://github.com/pies-n-loaf/sample-api-docs/actions/workflows/deploy.yml)

~~API documentation is the eyes that are the windows of the soul.~~ API documentation is a crucially important part of ensuring your API is useful and usable. Every time a developer finds exactly what they need in an API's reference, a puppy gets adopted. 

This repo's purpose is to provide an extensible and broadly applicable example for how to generate, customize, and deploy API documentation in as few steps as possible. Additionally, this solution has to work within the following constraints:

* **Must be compatible with OpenAPI 2.0 specifications.** _(That's what I gotta use at work, and I wanna make that bread.)_

* **Must be free.** _(I wanna make that bread, not spend that bread.)_

* **Must be generated using the base API definition.** Docs should be seamlessly updated with development, as opposed to being an additional maintenance burden. 

* **Must be easily customizable.** _(I want custom branding! Themes! Fonts!)_

* **Must be automatically regenerated and redeployed whenever the default branch is updated.** The deployed documentation should always be up-to-date, but no one should have to remember to do this.

To this end...

ASIDE:
For work, I have a number of other, extremely specific requirements to keep in mind. Most of these were secondary to the pursuit of the simplest way to deploy API docs, but one of them requires me to validate the fully compiled spec to ensure that a third party app can still use it. I have been using the online [Swagger Parser](https://apitools.dev/swagger-parser/online/) to check this.

. . .

- why this repo
  - define the problem needing to be solved (incl. being able to automatically deploy changes into a minimal branch, custom name, etc)
  - tools (openapi, redocly, github pages)

  - what is openapi
    - EXTREMELY brief blurb
    - link to reference docs

  - what is redoc/redocly
    - less brief blurb
    - navigating free/premium features (fuck this shit)
    - links

  - what is github pages
    - normal brief blurb

- how to view (gh pages link)
  - complex API version, with references, code examples, etc
  - simplest API definition
    - why openapi 2.0 (if anything it'll be easier in 3)

- how to do this yourself
  - STEP BY STEP

- contributing (lol don't)

- license (mit dunno why)

- TODO: recompile bundled api files on merge to main _in_ main?

---

## Working on your OpenAPI Definition

### Install

1. Install [Node JS](https://nodejs.org/).
2. Clone this repo and run `npm install` in the repo root.

### Usage

#### `npm start`
Starts the reference docs preview server.

#### `npm run build`
Bundles the definition to the dist folder.

#### `npm test`
Validates the definition.

## Contribution Guide

Below is a sample contribution guide. The tools
in the repository don't restrict you to any
specific structure. Adjust the contribution guide
to match your own structure. However, if you
don't have a structure in mind, this is a
good place to start.

Update this contribution guide if you
adjust the file/folder organization.

The `.redocly.yaml` controls settings for various
tools including the lint tool and the reference
docs engine.  Open it to find examples and
[read the docs](https://redoc.ly/docs/cli/configuration/)
for more information.


### Schemas

#### Adding Schemas

1. Navigate to the `openapi/components/schemas` folder.
2. Add a file named as you wish to name the schema.
3. Define the schema.
4. Refer to the schema using the `$ref` (see example below).

##### Example Schema
This is a very simple schema example:
```yaml
type: string
description: The resource ID. Defaults to UUID v4
maxLength: 50
example: 4f6cf35x-2c4y-483z-a0a9-158621f77a21
```
This is a more complex schema example:
```yaml
type: object
properties:
  id:
    description: The customer identifier string
    readOnly: true
    allOf:
      - $ref: ./ResourceId.yaml
  websiteId:
    description: The website's ID
    allOf:
      - $ref: ./ResourceId.yaml
  paymentToken:
    type: string
    writeOnly: true
    description: |
      A write-only payment token; if supplied, it will be converted into a
      payment instrument and be set as the `defaultPaymentInstrument`. The
      value of this property will override the `defaultPaymentInstrument`
      in the case that both are supplied. The token may only be used once
      before it is expired.
  defaultPaymentInstrument:
    $ref: ./PaymentInstrument.yaml
  createdTime:
    description: The customer created time
    allOf:
      - $ref: ./ServerTimestamp.yaml
  updatedTime:
    description: The customer updated time
    allOf:
      - $ref: ./ServerTimestamp.yaml
  tags:
    description: A list of customer's tags
    readOnly: true
    type: array
    items:
      $ref: ./Tags/Tag.yaml
  revision:
    description: >
      The number of times the customer data has been modified.

      The revision is useful when analyzing webhook data to determine if the
      change takes precedence over the current representation.
    type: integer
    readOnly: true
  _links:
    type: array
    description: The links related to resource
    readOnly: true
    minItems: 3
    items:
      anyOf:
        - $ref: ./Links/SelfLink.yaml
        - $ref: ./Links/NotesLink.yaml
        - $ref: ./Links/DefaultPaymentInstrumentLink.yaml
        - $ref: ./Links/LeadSourceLink.yaml
        - $ref: ./Links/WebsiteLink.yaml
  _embedded:
    type: array
    description: >-
      Any embedded objects available that are requested by the `expand`
      querystring parameter.
    readOnly: true
    minItems: 1
    items:
      anyOf:
        - $ref: ./Embeds/LeadSourceEmbed.yaml

```

##### Using the `$ref`

Notice in the complex example above the schema definition itself has `$ref` links to other schemas defined.

Here is a small excerpt with an example:

```yaml
defaultPaymentInstrument:
  $ref: ./PaymentInstrument.yaml
```

The value of the `$ref` is the path to the other schema definition.

You may use `$ref`s to compose schema from other existing schema to avoid duplication.

You will use `$ref`s to reference schema from your path definitions.

#### Editing Schemas

1. Navigate to the `openapi/components/schemas` folder.
2. Open the file you wish to edit.
3. Edit.

### Paths

#### Adding a Path

1. Navigate to the `openapi/paths` folder.
2. Add a new YAML file named like your URL endpoint except replacing `/` with `@` and putting path parameters into curly braces like `{example}`.
3. Add the path and a ref to it inside of your `openapi.yaml` file inside of the `openapi` folder.

Example addition to the `openapi.yaml` file:
```yaml
'/customers/{id}':
  $ref: './paths/customers@{id}.yaml'
```

Here is an example of a YAML file named `customers@{id}.yaml` in the `paths` folder:

```yaml
get:
  tags:
    - Customers
  summary: Retrieve a list of customers
  operationId: GetCustomerCollection
  description: |
    You can have a markdown description here.
  parameters:
    - $ref: ../components/parameters/collectionLimit.yaml
    - $ref: ../components/parameters/collectionOffset.yaml
    - $ref: ../components/parameters/collectionFilter.yaml
    - $ref: ../components/parameters/collectionQuery.yaml
    - $ref: ../components/parameters/collectionExpand.yaml
    - $ref: ../components/parameters/collectionFields.yaml
  responses:
    '200':
      description: A list of Customers was retrieved successfully
      headers:
        Rate-Limit-Limit:
          $ref: ../components/headers/Rate-Limit-Limit.yaml
        Rate-Limit-Remaining:
          $ref: ../components/headers/Rate-Limit-Remaining.yaml
        Rate-Limit-Reset:
          $ref: ../components/headers/Rate-Limit-Reset.yaml
        Pagination-Total:
          $ref: ../components/headers/Pagination-Total.yaml
        Pagination-Limit:
          $ref: ../components/headers/Pagination-Limit.yaml
        Pagination-Offset:
          $ref: ../components/headers/Pagination-Offset.yaml
      content:
        application/json:
          schema:
            type: array
            items:
              $ref: ../components/schemas/Customer.yaml
        text/csv:
          schema:
            type: array
            items:
              $ref: ../components/schemas/Customer.yaml
    '401':
      $ref: ../components/responses/AccessForbidden.yaml
  x-code-samples:
    - lang: PHP
      source:
        $ref: ../code_samples/PHP/customers/get.php
post:
  tags:
    - Customers
  summary: Create a customer (without an ID)
  operationId: PostCustomer
  description: Another markdown description here.
  requestBody:
    $ref: ../components/requestBodies/Customer.yaml
  responses:
    '201':
      $ref: ../components/responses/Customer.yaml
    '401':
      $ref: ../components/responses/AccessForbidden.yaml
    '409':
      $ref: ../components/responses/Conflict.yaml
    '422':
      $ref: ../components/responses/InvalidDataError.yaml
  x-code-samples:
    - lang: PHP
      source:
        $ref: ../code_samples/PHP/customers/post.php
```

You'll see extensive usage of `$ref`s in this example to different types of components including schemas.

You'll also notice `$ref`s to code samples.

### Code samples

1. Navigate to the `openapi/code_samples` folder.
2. Navigate to the `<language>` (e.g. PHP) sub-folder.
3. Navigate to the `path` folder, and add ref to the code sample.

You can add languages by adding new folders at the appropriate path level.

More details inside the `code_samples` folder README.
