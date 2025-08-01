---
title: Extending WireMock
meta_title: Extending WireMock via custom code | WireMock
---

<br>

!!! wiremock-cloud "WireMock Cloud"

    [Chaos testing, RBAC, dynamic state and more with WireMock Cloud.](https://www.wiremock.io?utm_source=oss-docs&utm_medium=oss-docs&utm_campaign=cloud-callouts-extending&utm_id=cloud-callouts&utm_term=cloud-callouts-extending)


WireMock can be customised via a variety of extension points.

Each extension point is defined by an interface that extends from `Extension` and extension implementations are loaded at startup time.

At present, the following extension interfaces are available:

* `RequestFilterV2`/`AdminRequestFilterV2`/`StubRequestFilterV2`: Intercept requests, modifying them or taking alternative actions based on their content.
* `ResponseDefinitionTransformerV2`: Modify the response definition used to generate a response. See [Transforming responses](../extensibility/transforming_responses.md).
* `ResponseTransformerV2`: Modify the response served to the client. See [Transforming responses](../extensibility/transforming_responses.md).
* `ServeEventListener`: Listen for events at various points in the request processing lifecycle. See [Listening for Serve Events](../extensibility/listening_for_serve_events.md).
* `AdminApiExtension`: Add admin API functions. See [Admin API Extensions](../extensibility/admin_api_extensions.md).
* `RequestMatcherExtension`: Implement custom request matching logic. See [Custom matching](../extensibility/custom_matching.md).
* `GlobalSettingsListener`: Listen for changes to the settings object. See [Listening for Settings Changes](../extensibility/listening_for_setting_changes.md).
* `StubLifecycleListener`: Listen for changes to the stub mappings. See [Listening for Stub Changes](../extensibility/listening_for_stub_changes.md).
* `TemplateHelperProviderExtension`: Provide custom Handlebars helpers to the template engine. See [Adding Template Helpers](../extensibility/adding_template_helpers.md).
* `TemplateModelDataProviderExtension`: Provide additional data to the model passed to response templates. See [Adding Template Model Data](../extensibility/adding_template_model_data.md).
* `MappingsLoaderExtension`: Provide additional source to load the stub mappings. See [Adding Mappings Loader](../extensibility/mapping_loader_extensions.md).

The interfaces in this list ending with `V2` supercede deprecated equivalents with an older, more restrictive interface. Additionally `ServeEventListener` deprecates `PostServeAction`.

As of WireMock version `3.6.0`, the `Extension` interface has two new lifecycle methods called `start()` and `stop()`. 
The `start()` method is called on each extension when the WireMock server first starts (just before the stub mappings 
are loaded) and the `stop()` method is called when the server is stopped. This allows extensions to perform any
initialisation or cleanup tasks.

## Registering Extensions

You can directly register the extension programmatically via its class name,
class or an instance:

```java
new WireMockServer(wireMockConfig()
  .extensions("com.mycorp.BodyContentTransformer", "com.mycorp.HeaderMangler"));

new WireMockServer(wireMockConfig()
  .extensions(BodyContentTransformer.class, HeaderMangler.class));

new WireMockServer(wireMockConfig()
  .extensions(new BodyContentTransformer(), new HeaderMangler()));
```

See [Running as a Standalone Process](../running_wiremock/running_as_a_standalone_process.md) for details on running with extensions from the command line.

### Factories

You can also register an extension factory, which allows an extension to be created with various core WireMock services passed to the constructor:

```java
new WireMockServer(wireMockConfig()
  .extensions(services ->
                    List.of(
                        new MiscInfoApi(
                            services.getAdmin(),
                            services.getOptions(),
                            services.getStores(),
                            services.getFiles(),
                            services.getExtensions()
                        ))));
```

Services currently available to extension factories are:
* `Admin`: the main WireMock functional interface for stubbing, verification and configuration tasks.
* `Options`: the configuration object built at startup.
* `Stores`: the root interface for gaining access to the various stores of WireMock's state and creating/using custom stores.
* `FileSource`: the `__files` directory where larger response body files are often kept.
* `Extensions`: the service for creating and providing extension implementations.
* `TemplateEngine`: the Handlebars template engine.

## Extension registration via service loading

Extensions that are packaged with the relevant [Java service loader framework](https://docs.oracle.com/javase/8/docs/api/java/util/ServiceLoader.html) metadata
will be loaded automatically if they are placed on the classpath.

See [https://github.com/wiremock/wiremock/tree/master/test-extension](https://github.com/wiremock/wiremock/tree/master/test-extension) for an example of such an extension.


## Attaching sub-events during request processing

Sub-events are a used to report interesting/useful information during request processing. WireMock attaches the diff report generated when a request is not matched as a sub-event, and custom extension can exploit this approach to surface e.g. diagnostic and validation data in the serve event log, where it can be retrieved later via the API or exported to monitoring/observability tools via listeners.


Several types of extension act on WireMock's request processing: `RequestFilterV2` (and its stub/admin sub-interfaces), `ResponseDefinitionTransformer`, `ResponseTransformer` and `ServeEventListener`.

The primary method in each of these takes the current `ServeEvent` as a parameter and sub-events can be attached to this:

```java
serveEvent.appendSubEvent(
  "JSON_PARSE_WARNING",
  Map.of("message", "Single quotes are not permitted")
);
```

The second parameter to `appendSubEvent()` can be a Map or object containing any data required.