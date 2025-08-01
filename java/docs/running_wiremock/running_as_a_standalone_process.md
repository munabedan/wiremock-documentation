---
title: Running as a Standalone Process
meta_title: Run an API Mock Server as a Standalone Process | WireMock
---


<br>


!!! wiremock-cloud "WireMock Cloud"

    [WireMock Cloud offers secure, publicly hosted mock APIs with nothing to install.](https://www.wiremock.io?utm_source=oss-docs&utm_medium=oss-docs&utm_campaign=cloud-callouts-java&utm_id=cloud-callouts&utm_term=cloud-callouts-java)


The WireMock server can be run in its own process, and configured via
the Java API, JSON over HTTP or JSON files.

Once you have [downloaded the standalone JAR](../getting_started/download_and_installation.md) you can run it simply by doing this:

```bash
$ java -jar wiremock-standalone-{{ wiremock_version }}.jar
```

## Command line options

The following can optionally be specified on the command line:

`--admin-api-basic-auth` : Require HTTP Basic authentication for admin API calls with the supplied credentials in `username:password` format

`--admin-api-require-https` : Require HTTPS to be used to access the admin API

`--port`: Set the HTTP port number e.g. `--port 9999`. Use `--port 0` to dynamically determine a port.

`--disable-http`: Disable the HTTP listener, option available only if HTTPS is enabled.

`--disable-http2-plain`: Disable HTTP/2 over plain text (HTTP).

`--disable-http2-tls`: Disable HTTP/2 over TLS (HTTPS).

`--https-port`: If specified, enables HTTPS on the supplied port.
Note: When you specify this parameter, WireMock will still, additionally, bind to an HTTP port (8080 by default). So when running multiple WireMock servers you will also need to specify the `--port` parameter in order to avoid conflicts.

`--bind-address`: The IP address the WireMock server should serve from. Binds to all local network adapters if unspecified.

`--https-keystore`: Path to a keystore file containing an SSL
certificate to use with HTTPS. Can be a path to a file or a resource on the classpath. The keystore must have a password of
"password". This option will only work if `--https-port` is specified.
If this option isn't used WireMock will default to its own self-signed
certificate.

`--keystore-type`: The HTTPS keystore type. Usually JKS or PKCS12.

`--keystore-password`: Password to the keystore, if something other than
"password".
Note: the behaviour of this changed in version 2.27.0. Previously this set Jetty's key manager password, whereas now it
sets the keystore password value. The key manager password can be set with the (new) parameter below.

`--key-manager-password`: The password used by Jetty to access individual keys in the store, if something other than
"password".

`--https-truststore`: Path to a keystore file containing client public
certificates, proxy target public certificates & private keys to use when
authenticate with a proxy target that require client authentication. Can be a path to a file or a resource on the classpath. See
[HTTPS configuration](../java_usage/java_configuration.md/#https-configuration)
and [Running as a browser proxy](../stubbing_and_verifying/proxying.md/#running-as-a-browser-proxy) for
details.

`--truststore-type`: The HTTPS trust store type. Usually JKS or PKCS12.

`--truststore-password`: Optional password to the trust store. Defaults
to "password" if not specified.

`--https-require-client-cert`: Force clients to authenticate with a
client certificate. See [HTTPS](../protocols/serving_https.md) for details.

`--verbose`: Turn on verbose logging to stdout

`--root-dir`: Sets the root directory, under which `mappings` and
`__files` reside. This defaults to the current directory.

`--record-mappings`: Record incoming requests as stub mappings. See
[Record and Playback](../record_and_playback/record_and_playback.md).

`--match-headers`: When in record mode, capture request headers with the
keys specified. See [Record and Playback](../record_and_playback/record_and_playback.md).

`--proxy-all`: Proxy all requests through to another base URL e.g.
`--proxy-all="http://api.someservice.com"` Typically used in conjunction
with `--record-mappings` such that a session on another service can be
recorded.

`--preserve-host-header`: When in proxy mode, it passes the Host header
as it comes from the client through to the proxied service. When this
option is not present, the Host header value is deducted from the proxy
URL. This option is only available if the `--proxy-all` option is
specified.

`--preserve-user-agent-proxy-header`: As of WireMock `3.7.0`, when in proxy mode, this option will transfer the 
original `User-Agent` header from the client to the proxied service.

`--proxy-via`: When proxying requests (either by using --proxy-all or by
creating stub mappings that proxy to other hosts), route via another
proxy server (useful when inside a corporate network that only permits
internet access via an opaque proxy). e.g.
`--proxy-via webproxy.mycorp.com` (defaults to port 80) or
`--proxy-via webproxy.mycorp.com:8080`. Also supports proxy authentication,
e.g. `--proxy-via http://username:password@webproxy.mycorp.com:8080/`.

`--supported-proxy-encodings`: The set of acceptable compression methods represented in the `accept-encoding` request header 
sent by WireMock when proxying or recording expressed as a comma-separated list e.g `gzip,deflate`. This is particularly useful if you want to avoid recording e.g. brotli compresssed responses
that can't then be viewed in the request log or served with a different compression scheme on playback, which can be achieved via `--supported-proxy-encodings=identity`.

`--enable-browser-proxying`: Run as a browser proxy. See
[Running as a browser proxy](../stubbing_and_verifying/proxying.md/#running-as-a-browser-proxy).

`--ca-keystore`: A key store containing a root Certificate Authority private key
and certificate that can be used to sign generated certificates when
browser proxying https. Defaults to `$HOME/.wiremock/ca-keystore.jks`.

`--ca-keystore-password`: Password to the ca-keystore, if something other than
"password".

`--ca-keystore-type`: Type of the ca-keystore, if something other than `jks`.

`--trust-all-proxy-targets`: Trust all remote certificates when running as a
browser proxy and proxying HTTPS traffic.

`--trust-proxy-target`: Trust a specific remote endpoint's certificate when
running as a browser proxy and proxying HTTPS traffic. Can be specified multiple
times. e.g. `--trust-proxy-target dev.mycorp.com --trust-proxy-target localhost`
would allow proxying to `https://dev.mycorp.com` or `https://localhost:8443`
despite their having invalid certificate chains in some way.

`--no-request-journal`: Disable the request journal, which records
incoming requests for later verification. This allows WireMock to be run
(and serve stubs) for long periods (without resetting) without
exhausting the heap. The `--record-mappings` option isn't available if
this one is specified.

`--container-threads`: The number of threads created for incoming
requests. Defaults to 10.

`--max-request-journal-entries`: Set maximum number of entries in
request journal (if enabled). When this limit is reached oldest entries
will be discarded.

`--max-http-client-connections`: Maximum connections for Http Client. Defaults to 1000.

`--jetty-acceptor-threads`: The number of threads Jetty uses for
accepting requests.

`--jetty-accept-queue-size`: The Jetty queue size for accepted requests.

`--jetty-header-buffer-size`: Deprecated, use `--jetty-header-request-size`. The Jetty buffer size for request headers,
e.g. `--jetty-header-buffer-size 16384`, defaults to 8192K.

`--jetty-header-request-size`: The Jetty buffer size for request headers,
e.g. `--jetty-header-request-size 16384`, defaults to 8192K.

`--jetty-header-response-size`: The Jetty buffer size for response headers,
e.g. `--jetty-header-response-size 16384`, defaults to 8192K.

`--jetty-idle-timeout` : Idle timeout in milliseconds for Jetty connections

`--jetty-stop-timeout` : Timeout in milliseconds for Jetty to stop

`--async-response-enabled`: Enable asynchronous request processing in Jetty.
Recommended when using WireMock for performance testing with delays, as it allows much more efficient use of container threads and therefore higher throughput. Defaults to `false`.

`--async-response-threads`: Set the number of asynchronous (background) response threads.
Effective only with `asynchronousResponseEnabled=true`. Defaults to 10.

`--extensions`: Extension class names e.g.
com.mycorp.HeaderTransformer,com.mycorp.BodyTransformer. See [Extending WireMock](../extensibility/extending_wiremock.md).

`--print-all-network-traffic`: Print all raw incoming and outgoing network traffic to console.

`--global-response-templating`: Render all response definitions using Handlebars templates.

`--local-response-templating`: Enable rendering of response definitions using Handlebars templates for specific stub mappings.

`--max-template-cache-entries`: Set the maximum number of compiled template fragments to cache. Only has any effect when response templating is enabled. As of WireMock `3.7.0`, this defaults to 1000
cache entries.  Before WireMock `3.7.0` the default was unlimited.

`--use-chunked-encoding`: Set the policy for sending responses with `Transfer-Encoding: chunked`. Valid values are `always`, `never` and `body_file`.
The last of these will cause chunked encoding to be used only when a stub defines its response body from a file.

`--disable-gzip`: Prevent response bodies from being gzipped.

`--disable-request-logging`: Prevent requests and responses from being sent to the notifier. Use this when performance testing as it will save memory and CPU even when info/verbose logging is not enabled.

`--disable-banner`: Prevent WireMock logo from being printed on startup

`--disable-connection-reuse`: Disable http connection reuse. Defaults to `true`

`--disable-extensions-scanning` : Prevent extensions from being scanned and loaded from the classpath

`--disable-optimize-xml-factories-loading` : Whether to disable optimize XML loading factories loading or not.

`--disable-response-templating` : Disable processing of responses with Handlebars templates

`--disable-strict-http-headers` : Whether to disable strict HTTP header handling of Jetty or not.

`--permitted-system-keys`: Comma-separated list of regular expressions for names of permitted environment variables and system properties accessible from response templates. Only has any effect when templating is enabled. Defaults to `wiremock.*`.

`--enable-stub-cors`: Enable automatic sending of cross-origin (CORS) response headers. Defaults to off.

`--logged-response-body-size-limit`: Set a limit in bytes beyond which response bodies in the log will be truncated. When enabled this helps avoid out of memory errors when serving large response bodies.

`--allow-proxy-targets`: Limit the permitted targets for proxying to and recording from the supplied addressess. This parameter takes a comma-separated list of single IP addresses, IP address ranges and hostname wildcards. See [this article](../java_usage/java_configuration.md/#preventing-proxying-to-and-recording-from-specific-target-addresses) for details.

`--deny-proxy-targets`: Prevent proxying to and recording from the supplied addressess. This parameter takes a comma-separated list of single IP addresses, IP address ranges and hostname wildcards. Note: if both `--allow-proxy-targets` and this parameter are set, the allow list will be evaluated first. See [this article](../java_usage/java_configuration.md/#preventing-proxying-to-and-recording-from-specific-target-addresses) for details.

`--proxy-timeout`: Set the timeout for requests to the proxy in milliseconds

`--proxy-pass-through`: Flag used in browser-caching in order to enable or disable pass through unmatched requests to the target indicated by the original requests. By default, this flag is enabled and let the requests pass through.

`--filename-template`: Set filename template in handlebar format. For endpoint: `GET /pets/{id}` using the format: `{% raw %}{{{method}}}-{{{url}}}.json{% endraw %}` output will be `get-pets-id.json`. Default format: `{% raw %}{{{method}}}-{{{path}}}-{{{id}}}.json{% endraw %}` hence by default template filename will be: `get-pets-id-1.json`.   
Note: introduced in [3.0.0-beta-8](https://github.com/wiremock/wiremock/releases/tag/3.0.0-beta-8).

`--timeout` : The default global timeout

`--version` : Prints wiremock version information and exits

`--webhook-threadpool-size`: The number of threads created for processing webhook requests.  This option is available as of WireMock version `3.13.0`. Defaults to 10

`--help`: Show command line help

## Configuring WireMock using the Java client

The WireMock Java API can be used against a running server on a different host if required. If you're only planning to configure a single remote instance from within your program you can configure the static DSL to point to it:

```java
WireMock.configureFor("my.remote.host", 8000);

// or for HTTPS
WireMock.configureFor("https", "my.remote.host", 8443);
```

Alternatively you can create an instance of the client (or as many as there are servers to configure):

```java
WireMock wireMock1 = new WireMock("1st.remote.host", 8000);
WireMock wireMock2 = new WireMock("https", "2nd.remote.host", 8001);
```

## Configuring via JSON over HTTP

You can create a stub mapping by posting to WireMock's HTTP API:

```bash
$ curl -X POST \
--data '{ "request": { "url": "/get/this", "method": "GET" }, "response": { "status": 200, "body": "Here it is!\n" }}' \
http://localhost:8080/__admin/mappings
```

And then fetch it back:

```bash
$ curl http://localhost:8080/get/this
Here it is!
```

The full stubbing API syntax is described in [Stubbing](../stubbing_and_verifying/stubbing.md).

## JSON file configuration

You can also use the JSON API via files. When the WireMock server starts
it creates two directories under the current one: `mappings` and
`__files`.

To create a stub like the one above by this method, drop a file with a
`.json` extension under `mappings` with the following content:

```json
{
    "request": {
        "method": "GET",
        "url": "/api/mytest"
    },
    "response": {
        "status": 200,
        "body": "More content\n"
    }
}
```

After restarting the server you should be able to do this:

```bash
$ curl http://localhost:8080/api/mytest
More content
```

See [stubbing](../stubbing_and_verifying/stubbing.md) and [verifying](../stubbing_and_verifying/verifying.md) for more on the JSON API.

### Multi-stub JSON files

JSON files containing multiple stub mappings can also be used. These are of the form:

```json
{
    "mappings": [
        {
            "request": {
                "method": "GET",
                "url": "/one"
            },
            "response": {
                "status": 200
            }
        },
        {
            "id": "8c5db8b0-2db4-4ad7-a99f-38c9b00da3f7",
            "request": {
                "url": "/two"
            },
            "response": {
                "body": "Updated"
            }
        }
    ]
}
```

> **note**
>
> Stubs loaded from multi-mapping files are read-only, so any attempt to update or remove (including remove all) will cause an error to be thrown.

## Pushing JSON files to a remote WireMock instance

You can push a collection of stub mappings and associated files to a remote WireMock or WireMock Cloud instance via the
Java API as follows:

```java
WireMock wireMock = WireMock.create()
    .scheme("http")
    .host("my-wiremock.example.com")
    .port(80)
    .build();

// The root directory of the WireMock project, under which the mappings and __files directories should be found
wireMock.loadMappingsFrom("/wiremock-stuff");
```

## File serving

When running the standalone JAR, files placed under the `__files` directory will
be served up as if from under the docroot, except if stub mapping
matching the URL exists. For example if a file exists
`__files/things/myfile.html` and no stub mapping will match
`/things/myfile.html` then hitting
`http://<host>:<port>/things/myfile.html` will serve the file.

## Packaging the stubs into a standalone JAR

If you want to package your stubs into the standalone JAR, so you can distribute an executable JAR with all the stubs intact, you can do this using the `--load-resources-from-classpath` option.

For example, let's say have the following directory structure:

```
src/main/resources
src/main/resources/wiremock-stuff
src/main/resources/wiremock-stuff/__files
src/main/resources/wiremock-stuff/mappings
```

You could then run the packaged JAR as:

```
java -jar custom-wiremock.jar --load-resources-from-classpath wiremock-stuff
```

Which will load your files and mappings from the packaged JAR.

Note that it is not currently possible to load from the root of the classpath.

## Securing The WireMock Admin API

You can start WireMock with the `--admin-api-basic-auth` command line option specifying your username and password in the standard `username:password` format:

```
java -jar wiremock-standalone.jar --admin-api-basic-auth my-username:my-super-secret-password
```

Any call made to the admin API after that will need the correct `Authorization` header included or a `401` will be 
returned. The correct call will have the `Authorization` header with the word `Basic` followed by the Base64 
representation of your `username:password` pair:

```
curl -X GET --location "http://localhost:8080/__admin/requests" \
    -H "Authorization: Basic bXktdXNlcm5hbWU6bXktc3VwZXItc2VjcmV0LXBhc3N3b3Jk"
```

## Shutting Down

To shutdown the server, either call `WireMock.shutdownServer()` or post
a request with an empty body to `http://<host>:<port>/__admin/shutdown`.
