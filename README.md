# okhttp-digest
A digest authenticator for okhttp. Most of the code is 
ported from Apache Http Client.

Damn

# Usage

```java
            final DigestAuthenticator authenticator = new DigestAuthenticator(new Credentials("username", "pass"));

            final Map<String, CachingAuthenticator> authCache = new ConcurrentHashMap<>();
            final OkHttpClient client = new OkHttpClient.Builder()
                    .authenticator(new CachingAuthenticatorDecorator(authenticator, authCache))
                    .addInterceptor(new AuthenticationCacheInterceptor(authCache))
                    .build();

            String url = "http://www.google.com";
            Request request = new Request.Builder()
                    .url(url)
                    .get()
                    .build();
            Response response = client.newCall(request).execute();
```

If you want to support multiple authentication schemes (including auth caching) then this should
work:

```java
            OkHttpClient.Builder builder = new OkHttpClient.Builder();
            final Map<String, CachingAuthenticator> authCache = new ConcurrentHashMap<>();

            Credentials credentials = new Credentials("username", "pass");
            final BasicAuthenticator basicAuthenticator = new BasicAuthenticator(credentials);
            final DigestAuthenticator digestAuthenticator = new DigestAuthenticator(credentials);

            // note that all auth schemes should be registered as lowercase!
            DispatchingAuthenticator authenticator = new DispatchingAuthenticator.Builder()
                    .with("digest", digestAuthenticator)
                    .with("basic", basicAuthenticator)
                    .build();

            client = builder
                    .authenticator(new CachingAuthenticatorDecorator(authenticator, authCache))
                    .addInterceptor(new AuthenticationCacheInterceptor(authCache))
                    .addNetworkInterceptor(logger)
                    .build();
```

[ ![Download](https://api.bintray.com/packages/rburgst/android/okhttp-digest/images/download.svg) ](https://bintray.com/rburgst/android/okhttp-digest/_latestVersion)
[![Build Status](https://travis-ci.org/rburgst/okhttp-digest.svg?branch=master)](https://travis-ci.org/rburgst/okhttp-digest)

## Use via gradle

```groovy
compile 'com.burgstaller:okhttp-digest:1.17'
```
