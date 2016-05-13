# Scripts

A Script is a snippet of JavaScript code that contains client business logic. 

### REST API

Clients can CRUD Scripts by sending requests to the `/system/scripts` endpoint.

| Method | Path                  | Content Type           | Description                      |
|--------|-----------------------|------------------------|----------------------------------|
| PUT    | /system/scripts/:id   | application/javascript | Creates a Script                 |
| DELETE | /system/scripts/:id   |                        | Deletes a Script                 |
| GET    | /system/scripts/:id   |                        | Gets a Script                    |
| GET    | /system/scripts       |                        | Gets a JSON listing of Scripts   |

### Example Script

Below is an example which highlights the core components of a Script.

```javascript
(1)  'use strict';

     /**
(2)   * Cancels a request if one or more properties do not link to an entity.
      *
(3)   * @name      ValidateLinksExist
(4)   * @author    John Doe
(5)   * @execution pre-processing
(6)   * @methods   POST, PUT
      *
(7)   * @param {Array<String>} properties - Properties that must link to an entity.
      */
     
(8)  for (let p of parameters.properties) {
       let linkUri = o[p];
       if (!linkUri) {
(9)      $.cancelRequest(o.uri + ' does not have a ' + p + ' property');
       }
       else {
(10)     let response = yield $.get(linkUri);
         if (response.body.length < 1) {
           $.cancelRequest(o.uri + ' ' + p + ' property does not link to entity');
         }
       }
     }
```

The core components of a Script are as follows:

1. The first line of the Script uses the `use strict` directive to enable [ECMAScript 5 strict mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode), which makes several changes to normal JavaScript semantics. All Scripts execute with strict mode enabled regardless of whether or not the `use strict` directive is present.

2. All Scripts start with a header comment, which is used to (a) provide metadata describing the Script and (b) configure how the Script is executed. The header comment is similar to a [JSDoc](http://usejsdoc.org/about-getting-started.html) comment, but only accepts a narrow set of specialized tags.
  
3. The `@name` tag can be used to provide a name for the Script.
  
4. The `@author` tag can be used to identify the author of the Script.

5. The `@execution` tag is used to specify the Script's execution mode (see below).

6. The `@methods` tag provides a comma-separated list of REST methods that the Script supports. Triggers that reference the Script may only fire on  supported REST methods. This list may contain any of the following methods: GET, DELETE, POST, PUT, PATCH.

7. The `@param` tag provides the name, type, and description of a Script parameter. Triggers that reference the Script must provide all defined parameters (with correct types) in their `scriptParameters` property.
 
8. Scripts access parameters passed in a Trigger's `scriptParameters` property using the global `parameters` object.

9. The Scripting Engine provides an [SDK](./script-context.md) that can be accessed using the global `$` object.

10. Scripts are executed in a [coroutine](http://bluebirdjs.com/docs/api/promise.coroutine.html), and can therefore `yield` to promises.

### Execution Mode

A Script can be configured to execute in three different ways:

| Execution Mode  | Description                                             |
|-----------------|---------------------------------------------------------|
| pre-processing  | Executes before a request is sent to Predix Asset       |
| post-processing | Executes after the response has been sent to the client |
| custom          | Executes when a request is sent to a custom endpoint    |

##### Pre-processing Scripts

A script with `@execution pre-processing` will be executed before the Scripting Engine forwards a request to Predix Asset. The Script may modify the body of the request or cancel the request altogether. A pre-processing Script may also issue GET requests to Predix Asset in case more information is needed to make a decision on how to process the request.

##### Post-processing Scripts

A script with `@execution post-processing` will be executed after a request has been processed by Predix Asset and had its response sent to the client. A post-processing Script may issue additional GET, PUT, POST, DELETE, and PATCH requests to Predix Asset. Since post-processing Scripts are executed after a response has been sent to the client, there is no way to alert the client directly should a failure occur. Instead, the Scripting Engine will create a [system message](./system-messages.md) with the reason for the failure. It is important that clients actively monitor their system messages for post-processing failures.

##### Custom Endpoint Scripts

A script with `@execution custom` will be executed when a request is sent to a custom endpoint (e.g. `/custom/my-logic`). A custom endpoint Script may issue GET, PUT, POST, DELETE, and PATCH requests to Predix Asset. Custom endpoint Scripts may send a custom response back to the client. 

### `@inject` Tag

When triggered by a POST, PUT, or PATCH request to a REST collection or resource, a post-processing Script will execute after the resource(s) have been updated. However, it is sometimes necessary for post-processing Scripts to have access to the pre-modified version of a resource. For example, a post-processing Script may need to perform a diff between the old and new versions of a resource.

If the `@inject current` tag is present in a Script's header comment, the Scripting Engine will GET the pre-modified value of the resource and inject it into the Script as a global variable named `current`. This tag can be used in both pre-processing and post-processing Scripts. Be aware that if a Script with this tag is triggered by a large POST request, the Scripting Engine will issue a separate GET request for each entity in the request before sending the request to Predix Asset.

### Resource Limitations

Each Script is provisioned a limited amount of processing time and memory. Exceeding these limits will cause the Script to fail. If a pre-processing Script fails, the request being processed will also fail and the failure will be returned to the client. If a custom endpoint Script fails, the failure will also be returned to the client. If a post-processing Script fails, a [system message](./system-messages.md) will be created with the reason for failure.

| Execution Mode  | Time   | Memory     |
|-----------------|--------|------------|
| pre-processing  | 2s     | 20 MB      |
| post-processing | 20s    | 20 MB      |
| custom          | 20s    | 20 MB      |

In addition to time and memory limits, the Scripting Engine is also limited in terms of how many Scripts it can execute concurrently for a single tenant. If a tenant sends many concurrent requests that trigger Script executions, the Scripting Engine may not be able to process them all. If this occurs, the Scripting Engine will begin to reject incoming requests from the tenant until enough resources have been freed to process their requests.

### Number of Executions

The number of times a Script is executed depends on its execution mode and the REST method of the request it is processing. Pre-processing and post-processing Scripts handling a POST request are executed once for each entity in the request body. On each execution, the Script processes a different entity in the request body. This is done to provide large POST requests with enough time and resources to process the entire request, yet still limit the execution time and memory usage of individual Script executions. In all other cases, the Script is executed once, and processes the entire request body.
 
| Execution Mode  | REST Method                   | Number of Executions        |
|-----------------|-------------------------------|-----------------------------|
| pre-processing  | GET, DELETE, PUT, PATCH       | 1                           |
| pre-processing  | POST                          | 1 per entity in the request |
| post-processing | GET, DELETE, PUT, PATCH       | 1                           |
| post-processing | POST                          | 1 per entity in the request |
| custom          | GET, DELETE, PUT, POST, PATCH | 1                           |

### ES6 Support

The Scripting Engine provides access to some ES6 features including:

* [Generators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)
* [Arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
* [Binary and octal literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Lexical_grammar)
* [let variables](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)
* String methods:
    * [.contains()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/includes) (replaced by `includes()` in the ES6 specification)
    * [.startsWith()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/startsWith)
    * [.endsWith()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/endsWith)
    * [.codePointAt()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/codePointAt)
    * [.repeat()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/repeat)
    * [.normalize()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/normalize)
    * [String.fromCodePoint](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/fromCodePoint)
