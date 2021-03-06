<!--
title: Serverless Framework - Google Cloud Functions Guide - Functions
menuText: Functions
menuOrder: 5
description: How to configure Google Cloud Functions in the Serverless Framework
layout: Doc
-->

<!-- DOCS-SITE-LINK:START automatically generated  -->
### [Read this on the main serverless docs site](https://www.serverless.com/framework/docs/providers/google/guide/functions)
<!-- DOCS-SITE-LINK:END -->

# Google - Functions

If you are using Google Cloud Functions as a provider, all *functions* inside the service are Google Cloud Functions.

## Configuration

All of the Google Cloud Functions in your serverless service can be found in `serverless.yml` under the `functions` property.

```yml
# serverless.yml
service: my-gcloud-service

provider:
  name: google

plugins:
  - serverless-google-cloudfunctions

functions:
  first:
    handler: http
    events:
      - http: path
```

## Handler

The `handler` property should be the function name you've exported in your entrypoint file.

When you e.g. export a function with the name `http` in `index.js` your `handler` should be `handler: http`.

```javascript
// index.js
exports.http = (request, response) => {}
```

**A note about index.js and the entrypoint file**

Google Cloud Functions assumes that you have a `index.js` or `function.js` file with the exported handler functions in the root of your project (you can read more about that in [their docs](https://cloud.google.com/functions/docs/deploying/)).

However you can overwrite this by specifying the entrypoint file with the help of the `main` config parameter in the projects `package.json` file.

Our recommendation is to have an `index.js` file with all the function handlers in the projects root directory.

## Memory size and timeout

The `memorySize` and `timeout` for the functions can be specified on the provider or function level. The provider wide definition causes all functions to share this config, whereas the function wide definition means that this configuration is only valid for the function.

The default `memorySize` is 256 and the default timeout is `60s` if not specified.

```yml
# serverless.yml

provider:
  memorySize: 1024
  timeout: 90s

functions:
  first:
    handler: first
  second:
    handler: second
    memorySize: 512
    timeout: 120s
```

## Handler signatures

Google Cloud Functions have different handler signatures dependent on the event type which will trigger them.

### `http` events

```javascript
exports.http = (request, response) => {
  response.status(200).send('Hello World!');
};
```

### `event` events

```javascript
exports.event = (event, callback) => {
  console.log('Hello World!');
  callback();
};
```

## Labels

Google Cloud Platform supports [labels to assist in organizing resources](https://cloud.google.com/resource-manager/docs/creating-managing-labels).
Serverless allows you to define labels to be applied to functions upon deploy.
Labels are defined as key-value pairs.

Labels can be applied globally, to all functions in your configuration file, and to specific functions.

```yml
# serverless.yml

provider:
  name: google
  labels:
    application: Serverless Example

functions:
  first:
    handler: httpFirst
    events:
      - http: path
    labels:
      team: GCF Team
  second:
    handler: httpSecond
    events:
      - http: path
    labels:
      application: Serverless Example - Documentation
```

With the above configuration the `httpFirst` function would have two labels applied, `application` and `team`.
The value of the `application` label would be `Serverless Example`, the value of the `team` label would be `GCF Team`.

The `httpSecond` function would have only one label applied, `application`, and it would have a value of `Serverless Example - Documentation`.

Labels defined under the `provider` object are applied to all functions in the file.
Labels defined under specific functions only apply to that function.
The labels defined under a function's object will override global labels for that function.
