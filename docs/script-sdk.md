# Script Utility Methods

Every Script is injected with the `$` global variable, which contains a set of utility methods. The utility methods available to a Script may change depending on its execution mode and/or the REST method of the request it is processing. All utility methods are documented below:

### `$.cancelRequest(message)`

Cancels an incoming request, sending back a message with status `422` to the client.

**Accessibility**

This method is only accessible in pre-processing Scripts.

**Arguments**

| Name       | Type          | Description                                               |
|------------|---------------|-----------------------------------------------------------|
| message    | string        | Message to send back to the client                        |

**Return Value**

This method has no return value.



### `$.deepDiff(lhs, rhs)`

Calculates the difference between two objects with [deep-diff](https://github.com/flitbit/diff/blob/master/Readme.md).

**Accessibility**

This method is accessible in all Scripts.

**Arguments**

| Name       | Type          | Description                                               |
|------------|---------------|-----------------------------------------------------------|
| lhs        | object        | Origin object                                             |
| rhs        | object        | Object being compared with the origin object              |

**Return Value**

See the [deep-diff](https://github.com/flitbit/diff/blob/master/Readme.md) documentation (v0.2.0 and above).



### `$.del(uri)`

Sends a DELETE request to Predix Asset and returns the response.

**Accessibility**

This method is only accessible in post-processing and custom endpoint Scripts.

**Arguments**

| Name       | Type          | Description                                               |
|------------|---------------|-----------------------------------------------------------|
| uri        | string        | The URI of the Predix Asset REST resource to delete       |

**Return Value**

Returns a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) that will be resolved with the following value:

| Name                | Type          | Description                                      |
|---------------------|---------------|--------------------------------------------------|
| response            | object        | Response object                                  |
| response.statusCode | number        | Response status code                             |
| response.body       | any           | Response body                                    |



### `$.get(uri)`

Sends a GET request to Predix Asset and returns the response.

**Accessibility**

This method is accessible in all Scripts.

**Arguments**

| Name       | Type          | Description                                               |
|------------|---------------|-----------------------------------------------------------|
| uri        | string        | The URI of a Predix Asset REST collection or resource     |

**Return Value**

Returns a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) that will be resolved with the following value:

| Name                | Type          | Description                                      |
|---------------------|---------------|--------------------------------------------------|
| response            | object        | Response object                                  |
| response.statusCode | number        | Response status code                             |
| response.body       | any           | Response body                                    |



### `$.getPropertyValue(object, path)`

Retrieves an object's property at the specified path.

**Accessibility**

This method is accessible in all Scripts.

**Arguments**

| Name       | Type          | Description                                               |
|------------|---------------|-----------------------------------------------------------|
| object     | object        | The object from which to retrieve a property              |
| path       | string        | Period-delimited path of the property (e.g. `.foo.bar`)   |

**Return Value**

Returns the value of the object's property at the specified path.



### `$.log(message)`

Logs a [system message](./system-messages.md).

**Accessibility**

This method is accessible in all Scripts.

**Arguments**

| Name       | Type          | Description                                               |
|------------|---------------|-----------------------------------------------------------|
| message    | string        | The message to log.                                       |

**Return Value**

This method has no return value.



### `$.patch(uri, body)`

Sends a PATCH request to Predix Asset and returns the response.

**Accessibility**

This method is only accessible in post-processing and custom endpoint Scripts.

**Arguments**

| Name       | Type           | Description                                              |
|------------|----------------|----------------------------------------------------------|
| uri        | string         | The URI of a Predix Asset REST resource                  |
| body       | Array\<Object> | Array of PATCH operations to apply to the REST resource  |

**Return Value**

Returns a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) that will be resolved with the following value:

| Name                | Type          | Description                                      |
|---------------------|---------------|--------------------------------------------------|
| response            | object        | Response object                                  |
| response.statusCode | number        | Response status code                             |
| response.body       | any           | Response body                                    |



### `$.patchObject(uri, body)`

Applies a set of PATCH operations to an object.

**Accessibility**

This method is accessible in all Scripts.

**Arguments**

| Name       | Type           | Description                                              |
|------------|----------------|----------------------------------------------------------|
| object     | object         | The object to PATCH                                      |
| operations | Array\<Object> | Array of PATCH operations to apply to the object         |

**Return Value**

Returns the input object (with PATCH operation applied).



### `$.post(uri, body)`

Sends a POST request to Predix Asset and returns the response.

**Accessibility**

This method is only accessible in post-processing and custom endpoint Scripts.

**Arguments**

| Name       | Type           | Description                                              |
|------------|----------------|----------------------------------------------------------|
| uri        | string         | The URI of a Predix Asset REST collection                |
| body       | Array\<Object> | Array of REST resources                                  |

**Return Value**

Returns a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) that will be resolved with the following value:

| Name                | Type          | Description                                      |
|---------------------|---------------|--------------------------------------------------|
| response            | object        | Response object                                  |
| response.statusCode | number        | Response status code                             |
| response.body       | any           | Response body                                    |



### `$.put(uri, body)`

Sends a PUT request to Predix Asset and returns the response.

**Accessibility**

This method is only accessible in post-processing and custom endpoint Scripts.

**Arguments**

| Name       | Type           | Description                                              |
|------------|----------------|----------------------------------------------------------|
| uri        | string         | The URI of a Predix Asset REST resource                  |
| body       | Object         | The Predix Asset REST resource body                      |

**Return Value**

Returns a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) that will be resolved with the following value:

| Name                | Type          | Description                                      |
|---------------------|---------------|--------------------------------------------------|
| response            | object        | Response object                                  |
| response.statusCode | number        | Response status code                             |
| response.body       | any           | Response body                                    |



### `$.removeEntity(message)`

Removes an entity from a POST or PUT request and logs a system message.

**Accessibility**

This method is only accessible in pre-processing Scripts handling PUT and POST requests.

**Arguments**

| Name       | Type           | Description                                              |
|------------|----------------|----------------------------------------------------------|
| message    | string         | A message explaining why the entity was removed          |

**Return Value**

This method has no return value.



### `$.setResponse(response)`

Sets the response to be returned by a custom endpoint Script.

**Accessibility**

This method is only accessible in custom endpoint Scripts.

**Arguments**

| Name            | Type      | Description                                              |
|-----------------|-----------|----------------------------------------------------------|
| response        | object    | Object containing response body and status               |
| response.body   | any       | Response body                                            |
| response.status | number    | Response status code (defaults to `200`)                 |

**Return Value**

This method has no return value.



### `$.updateBody(body)`

Updates the body of a PATCH request.

**Accessibility**

This method is only accessible in pre-processing Scripts handling PATCH requests.

**Arguments**

| Name       | Type           | Description                                              |
|------------|----------------|----------------------------------------------------------|
| body       | Array\<object> | Array of PATCH operations                                |

**Return Value**

This method has no return value.



### `$.updateEntity(body)`

Updates an entity in the body of a POST or PUT request.

**Accessibility**

This method is only accessible in pre-processing Scripts handling POST and PUT requests.

**Arguments**

| Name       | Type           | Description                                              |
|------------|----------------|----------------------------------------------------------|
| entity     | object         | The updated entity object (uri must not change)          |

**Return Value**

This method has no return value.



### `$.uuid.v1()`

Generates a UUID (v1) with [node-uuid](https://github.com/broofa/node-uuid).

**Accessibility**

This method is accessible in all Scripts.

**Arguments**

See [node-uuid](https://github.com/broofa/node-uuid) for more information.

**Return Value**

Returns a Universally Unique Identifier (v1) string.



### `$.uuid.v4()`

Generates a UUID (v4) with [node-uuid](https://github.com/broofa/node-uuid).

**Accessibility**

This method is accessible in all Scripts.

**Arguments**

See [node-uuid](https://github.com/broofa/node-uuid) for more information.

**Return Value**

Returns a Universally Unique Identifier (v4) string.
