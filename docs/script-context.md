# Script Context

The following global variables are injected into every Script at runtime:

| Variable                 | Type               | Description                                                               |
|--------------------------|--------------------|---------------------------------------------------------------------------|
| $                        | object             | [Script SDK](./script-sdk.md)                                             |
| parameters               | object             | Object map of parameters provided by Trigger                              |
| Promise                  | function\|object   | The [bluebird](https://github.com/petkaantonov/bluebird) Promise library  |
| req                      | object             | The request being processed                                               |
| req.clientData           | object             | Information relating to the client                                        |
| req.clientData.locale    | string             | Client locale code                                                        |
| req.clientData.tenantId  | string             | Client tenant ID                                                          |
| req.clientData.requestId | string             | ID of the request being processed                                         |
| req.headers              | object             | Request headers                                                           |
| req.method               | string             | Request REST method                                                       |
| req.protocol             | string             | Request REST protocol: http or https                                      |
| req.queryParameters      | object             | Request query parameter object map                                        |
| script                   | object             | Script entity that is being executed                                      |
| script.execution         | string             | The Script's execution mode                                               |
| script.methods           | Array\<string>     | REST methods that the Script supports                                     |
| script.uri               | string             | URI of the Script                                                         |
| trigger                  | object             | Trigger entity that caused the Script to execute                          |
| trigger.onReferenceTo    | string             | REST collection that the Trigger is bound to (e.g. `/assets`)             |
| trigger.methods          | Array\<string>     | REST methods that cause the Trigger to fire                               |
| trigger.uri              | string             | URI of the Trigger                                                        |

### Post-processing Scripts

Post-processing Scripts receive the following additional global variables at runtime.

| Variable                 | Type               | Description                                                               |
|--------------------------|--------------------|---------------------------------------------------------------------------|
| res                      | object             | The response sent back to the client (i.e. from Predix Asset)             |
| res.statusCode           | number             | Response status code                                                      |

### POST Requests

A Script handling a POST request receives the following additional global variables:

| Variable                 | Type               | Description                                       |
|--------------------------|--------------------|---------------------------------------------------|
| o                        | object             | A single entity from the body of the POST request |

### PUT Requests

A Script handling a PUT request receives the following additional global variables:

| Variable                 | Type               | Description                                       |
|--------------------------|--------------------|---------------------------------------------------|
| o                        | object             | The entity in the body of the PUT request         |

### PATCH Requests

A Script handling a PATCH request receives the following additional global variables:

| Variable                 | Type               | Description                                       |
|--------------------------|--------------------|---------------------------------------------------|
| o                        | Array\<object>     | The request body (array of PATCH operations)      |
