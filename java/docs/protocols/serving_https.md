---
title: Serving HTTPS
meta_title: Using WireMock with HTTPS using self-signed or custom certificates | WireMock
---

<br>

WireMock can optionally accept requests over HTTPs. By default it will serve its own self-signed TLS certificate, but this can be
overridden if required by providing a keystore containing another certificate.

## Handling HTTPS requests

To enable HTTPS using WireMock's self-signed certificate just specify an
HTTPS port:

```java
@Rule
public WireMockRule wireMockRule = new WireMockRule(wireMockConfig().httpsPort(8443));
```

To use your own keystore you can specify its path and optionally its
password:

```java
@Rule
public WireMockRule wireMockRule = new WireMockRule(wireMockConfig()
    .httpsPort(8443)
    .keystorePath("/path/to/keystore.jks") // Either a path to a file or a resource on the classpath
    .keystorePassword("verysecret") // The password used to access the keystore. Defaults to "password" if omitted
    .keyManagerPassword("verysecret")); // The password used to access individual keys in the keystore. Defaults to "password" if omitted
```

The keystore type defaults to JKS, but this can be changed if you're using another keystore format e.g. Bouncycastle's BKS with Android:

```java
.keystoreType("BKS")
```

To allow only HTTPS requests, disable HTTP by adding:

```java
@Rule
public WireMockRule wireMockRule = new WireMockRule(wireMockConfig().httpsPort(8443).httpDisabled(true));
```

## Requiring client certificates

To make WireMock require clients to authenticate via a certificate you
need to supply a trust store containing the certs to trust and enable
client auth:

```java
@Rule
public WireMockRule wireMockRule = new WireMockRule(wireMockConfig()
    .httpsPort(8443)
    .needClientAuth(true)
    .trustStorePath("/path/to/truststore.jks") // Either a path to a file or a resource on the classpath
    .trustStorePassword("mostsecret")); // Defaults to "password" if omitted
```

If you using WireMock as a proxy onto another system which requires client certificate authentication, you will also need to
specify a trust store containing the certificate(s).

> **note**
>
> Jetty requires client certificates to contain Subject Alternative Names.
> See [this script](https://github.com/tomakehurst/wiremock/blob/master/scripts/create-client-cert.sh) for an example of how to build
> a truststore containing a valid certificate (you'll probably want to edit the client-cert.conf file before running this).

## Common HTTPS issues

`javax.net.ssl.SSLException: Unrecognized SSL message, plaintext connection?`: Usually means you've tried to connect to the
HTTP port with a client that's expecting HTTPS (i.e. has `https://` in the URL).

`org.apache.hc.core5.http.NoHttpResponseException: The target server failed to respond`: Could mean you've tried to connect to the HTTPS port with a
client expecting HTTP.

`javax.net.ssl.SSLHandshakeException: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target`: You are using WireMock's default (self-signed) TLS certificate or another certificate that isn't signed by a CA. In this case you need to specifically configure your HTTP client to trust the certificate being presented, or to trust all certificates. Here is an example of [how to do this with the Apache HTTP client](https://github.com/wiremock/wiremock/blob/3.4.2/src/main/java/com/github/tomakehurst/wiremock/http/HttpClientFactory.java#L207).
