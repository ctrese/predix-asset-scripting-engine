# System Messages

A System Message is a REST resource containing information about the [Scripts](./scripts.md) executed for a particular request. The Scripting Engine will generate a System Message when a Script:
                                                                                                                                                                
- Cancels or modifies an incoming request.                                                                                                                      
- Encounters an unhandled exception.
- Exceeds its time or memory usage [limits](./scripts.md).
- Invokes the [$.log](./script-sdk.md) method.

### REST API

Clients can read and delete System Messages by sending requests to `/system/messages`.

| Method | Path                  | Description                      |
|--------|-----------------------|----------------------------------|
| GET    | /system/messages      | Gets a list of System Messages   |
| GET    | /system/messages/:id  | Gets a System Message            |
| DELETE | /system/messages/:id  | Deletes a System Message         |

### Example

Given a pre-processing Script with uri `/system/scripts/abc`:

```javascript
/**
 * Logs two messages.
 * @execution pre-processing
 */
$.log("Hello post-processing script!");
$.log("How was your day?");   
```

And a post-processing Script with uri `/system/scripts/xyz`:

```javascript
/**
 * Logs a message and throws an error.
 * @execution post-processing
 */
$.log("Not so good pre-processing Script.");
throw new Error("I have a bug");
```

And the following [Triggers](./triggers.md) that bind the two Scripts to `GET /assets`.

```json
[
  {
    "uri":           "/system/triggers/abc",
    "onReferenceTo": "/assets",
    "methods":       ["GET"],
    "disabled":      false,
    "script":        "/system/scripts/abc"
  },
  {
    "uri":           "/system/triggers/xyz",
    "onReferenceTo": "/assets",
    "methods":       ["GET"],
    "disabled":      false,
    "script":        "/system/scripts/xyz"
  }
]
```

Issuing a request to `GET /assets` will generate the following System Message:

```json
[
  {
    "uri": "/system/messages/733b3ccd-53f5-4c12-b6ab-0be0da5eef59",
    "type": "Script Engine Execution Report",
    "datetime": "2016-05-02T23:03:54.931Z",
    "request": {
      "id": "0e32247e-d663-4806-a7ec-0e677e25e6f7",
      "method": "GET",
      "url": "/assets"
    },
    "reports": {
      "preProcessing": [
        {
          "trigger": "/system/triggers/abc",
          "script": "/system/scripts/abc",
          "timeElapsedMs": 378,
          "messages": [
            "Hello post-processing script!",
            "How was your day?"
          ]
        }
      ],
      "postProcessing": [
        {
          "trigger": "/system/triggers/xyz",
          "script": "/system/scripts/xyz",
          "timeElapsedMs": 244,
          "messages": [
            "Not so good pre-processing Script.",
            "I have a bug"
          ]
        }
      ]
    }
  }
]
```
