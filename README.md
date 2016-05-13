# Scripting Engine

##### *Powering Your Business Logic*
----------

The Predix Asset Scripting Engine allows clients to add custom business logic to Asset queries:

1. Before a request is processed by Predix Asset.
2. After a request has been processed by Predix Asset.
3. When a request is sent to a custom endpoint such as, `/custom/my-logic`.

This business logic can perform tasks such as:

- Validate and accept/reject requests.
- Modify the contents of incoming requests.
- Perform additional operations after a request has been processed.
- Execute ad hoc processing and return a custom response.

More specific examples:

- Reject requests containing resources that do not match a schema.
- Prevent resources from being created, read, updated, or deleted.
- Create, update, or delete resources after processing a request.
- Read from multiple Predix Asset resources and generate a report. 

## How the Scripting Engine Works

Client applications that bind to the Predix Asset service may send their requests to either the Scripting Engine or directly to Predix Asset. When a request is sent to the Scripting Engine, it checks to see if the client has configured any business logic to be executed for that request. If no business logic needs to be executed, the Scripting Engine will issue an [HTTP redirect](https://en.wikipedia.org/wiki/HTTP_302), telling the client application to send its request directly to Predix Asset. If some business logic needs to be executed, the Scripting Engine will perform the following steps:

1. Execute any pre-processing logic that has been configured to execute.
2. Forward the client request to Predix Asset and send the result back to the client.
3. Execute any post-processing logic that has been configured to execute.

The Scripting Engine allows clients to [decorate](https://sourcemaking.com/design_patterns/decorator) the Predix Asset REST interface with their own custom business logic. This approach is quite flexible, but may not be able to handle all of a client's business logic needs. Therefore, the Scripting Engine also allows clients to define custom endpoints that can issue one or more requests to Predix Asset, perform ad hoc processing, and return a custom response. 

## Scripts and Triggers

[Scripts](./docs/scripts.md) and [Triggers](./docs/triggers.md) are the building blocks that allow clients to define and apply their custom business logic. Scripts contain the client's business logic and Triggers determine when that business logic is executed. Both Scripts and Triggers are REST resources that can be created, read, updated, and deleted by clients. Clients first create a set of Scripts containing their business logic and then create one or more Triggers to bind that business logic to either the Predix Asset REST interface or a custom endpoint.

## How to Use the Scripting Engine

Upon binding to the Predix Asset service, a client application receives the URL of the Scripting Engine via the [VCAP_SERVICES](https://docs.run.pivotal.io/devguide/deploy-apps/environment-variable.html#VCAP-SERVICES) environment variable. Client applications may issue requests to the Scripting Engine using the same credentials that are required by Predix Asset.

```json
"VCAP_SERVICES": {
  "predix-asset-dev": [{
    "credentials": {
      "scriptEngine_uri": "http://predix-script-engine.grc-apps.svc.ice.ge.com"
    }
  }]
}
```

The Scripting Engine is capable of accepting any request that can be handled by Predix Asset. In addition, the Scripting Engine also provides the `/system/scripts` and `/system/triggers` endpoints for CRUD of [Scripts](./docs/scripts.md) and [Triggers](./docs/triggers.md).

## More Information

The following links contain additional information about the Scripting Engine:

- Read about the [global variables](./docs/script-context.md) injected into Scripts at runtime.
- Read about the [Script SDK](./docs/script-sdk.md) and the utility methods it provides.
- Read about [system messages](./docs/system-messages.md) and how to interpret them.
- See end-to-end [examples](./docs/examples.md) of how to use the Scripting Engine.
