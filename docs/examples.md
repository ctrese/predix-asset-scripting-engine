# Examples

The following examples show how to create different kinds of [Scripts](./scripts.md) and [Triggers](./triggers.md).

### Pre-processing

Given the following Script is created with uri `/system/scripts/cancel-request`:

```
URL:          https://<script-engine>/system/scripts/cancel-request
Method:       PUT
Content-Type: application/javascript
Body:       
/**
 * Cancels any request sent to an endpoint.
 * @execution pre-processing
 */
$.cancelRequest("You shall not pass!");
```

And the following Trigger that binds the Script to `DELETE /assets`:

```
URL:          https://<script-engine>/system/triggers/prevent-delete-assets
Method:       PUT
Content-Type: application/json
Body:  
{
  "uri":           "/system/triggers/prevent-delete-assets",
  "onReferenceTo": "/assets",
  "methods":       ["DELETE"],
  "disabled":      false,
  "script":        "/system/scripts/cancel-request"
}
```
   
When a request is sent to `DELETE /assets/:id`, the following response will be returned:
    
```
[
  {
    "code":       "SE_REQUEST_CANCELLED_BY_SCRIPT",
    "message":    "Request cancelled by a script: You shall not pass!",
    "suggestion": "Ensure the request satisfies all constraints enforced by the script",
    "requestId":  "cde44545-813c-41cb-827d-ec41f63e8207",
    "script":     "/system/scripts/cancel-request",
    "trigger":    "/system/triggers/prevent-delete-assets"
  }
]
```

### Post-processing

Given the following Script is created with uri `/system/scripts/create-event`:

```
URL:          https://<script-engine>/system/scripts/create-event
Method:       PUT
Content-Type: application/javascript
Body:       
/**
 * Creates an event entity when a resource is created or updated.
 * @execution post-processing
 * @methods   POST, PUT
 */
if (res.statusCode === 204) {
  let event = {
    uri:       '/events/' + $.uuid.v4(),
    timestamp: (new Date()).toISOString(),
    resource:  o.uri,
    method:    req.method
  };

  $.post('/events', [event]);
}
```

And the following Trigger that binds the Script to `POST /assets` and `PUT /assets/:id`:

```
URL:          https://<script-engine>/system/triggers/record-asset-modification
Method:       PUT
Content-Type: application/json
Body:  
{
  "uri":           "/system/triggers/record-asset-modification",
  "onReferenceTo": "/assets",
  "methods":       ["POST", "PUT"],
  "disabled":      false,
  "script":        "/system/scripts/create-event"
}
```
   
When a request is sent to `PUT /assets/001`, the following entity will be created:
    
```
[
  {
    "uri": "/events/adcfd169-2b32-4300-a60c-a20a9b4c80b6",
    "timestamp": "2016-05-02T23:40:19.764Z",
    "resource": "/assets/001",
    "method": "PUT"
  }
]
```
 
### Custom Endpoint

Given the following Script is created with uri `/system/scripts/add-two-numbers`:

```
URL:          https://<script-engine>/system/scripts/add-two-numbers
Method:       PUT
Content-Type: application/javascript
Body:       
/**
 * Adds two numbers and returns the result.
 * @execution custom
 */
let result = o.num1 + o.num2;
$.setResponse({status: 200, body: result});
```

And the following Trigger that binds the Script to `GET /custom/add-two-numbers`:

```
URL:          https://<script-engine>/system/triggers/add-two-numbers
Method:       PUT
Content-Type: application/json
Body:  
{
  "uri":           "/system/triggers/add-two-numbers",
  "onReferenceTo": "/custom/add-two-numbers",
  "methods":       ["PUT"],
  "disabled":      false,
  "script":        "/system/scripts/add-two-numbers"
}
```
   
When a request is sent to `PUT /custom/add-two-numbers` with the following body:

```
{
  "num1": 2, 
  "num2": 2
}
```

The following response will be returned:

```
4
```
