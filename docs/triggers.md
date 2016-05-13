# Triggers

A Trigger is a REST resource that binds a Script to a Predix Asset endpoint. 

### REST API

Clients can CRUD Triggers by sending requests to the `/system/triggers` endpoint.

| Method | Path                  | Content Type           | Description                      |
|--------|-----------------------|------------------------|----------------------------------|
| POST   | /system/triggers      | application/json       | Creates one or more Triggers     |
| PUT    | /system/triggers/:id  | application/json       | Creates a Trigger                |
| DELETE | /system/triggers/:id  |                        | Deletes a Trigger                |
| GET    | /system/triggers/:id  |                        | Gets a Trigger                   |
| GET    | /system/triggers      |                        | Gets a list of Triggers          |

### Trigger Properties

| Property Name    | Type          | Example                          | Description                                                                         |
|------------------|---------------|----------------------------------|-------------------------------------------------------------------------------------|
| uri              | string        | "/system/triggers/001"            | Unique resource identifier                                                          |
| onReferenceTo    | string        | "/assets" <br> "/custom/my-logic" | Rest collection or custom endpoint that triggers a script execution                 |
| methods          | Array<string> | ["POST", "PUT"]                   | HTTP methods that trigger a script execution when sent to "onReferenceTo"           |
| disabled         | boolean       | false                             | Enables or disables the Trigger                                                     |
| script           | string        | "/system/scripts/001"             | Uri of the script to execute                                                        |
| scriptParameters | object        | {"param1": "foo"}                 | Object map containing required configuration parameters for the script (optional)   |

### Examples

##### Freeze A Resource Collection

Given the following Script is created with uri `/system/scripts/cancel-request`:

```javascript
/**
 * Cancels an incoming request.
 * @execution pre-processing
 */
$.cancelRequest();   
```

We can create a Trigger that prevents a resource collection (e.g. `assets`) from being modified:

```json
{
  "uri":           "/system/triggers/freeze-assets",
  "onReferenceTo": "/assets",
  "methods":       ["DELETE", "POST", "PUT", "PATCH"],
  "disabled":      false,
  "script":        "/system/scripts/cancel-request"
}
```

##### Enforce a Schema

Given the following Script is created with uri `/system/scripts/require-properties`:

```javascript
/**
 * Cancels a request if one or more properties is missing.
 * @execution pre-processing
 * @methods   POST, PUT
 * @param     {Array<String>} properties - Required properties
 */
for (let p of parameters.properties) {
  if (!o[p]) {
    $.cancelRequest(o.uri + ' does not have a ' + p + ' property');
  }
}
```

We can create a Trigger that requires resources to have a "parent" and "state" property.

```json
{
  "uri":              "/system/triggers/enforce-asset-schema",
  "onReferenceTo":    "/assets",
  "methods":          ["POST", "PUT"],
  "disabled":         false,
  "script":           "/system/scripts/require-properties",
  "scriptParameters": {
    "properties": ["parent", "state"]
  }
}
```
