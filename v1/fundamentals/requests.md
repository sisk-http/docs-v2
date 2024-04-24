# Requests

Requests are structures that represent an HTTP request message. The [HttpRequest](../specification/spec/Sisk.Core.Http.HttpRequest) object contains useful functions for handling HTTP messages throughout your application.

An HTTP request is formed by the method, path, version, headers and body.

In this document, we will teach you how to obtain each of these elements.

# Getting the request method

To obtain the method of the received request, you can use the Method property:

```cs
static HttpResponse Index(HttpRequest request)
{
    HttpMethod requestMethod = request.Method;
    ...
}
```

This property returns the request's method represented by an [HttpMethod](https://learn.microsoft.com/pt-br/dotnet/api/system.net.http.httpmethod) object.

> **Note:**
>
> Unlike route methods, this property does not serves the [RouteMethod.Any](../specification/spec/Sisk.Core.Routing.RouteMethod.Any) item. Instead, it returns the real request method.

# Getting request url components

You can get various component from a URL through certain properties of a request. For this example, let's consider the URL:

```
http://localhost:5000/user/login?email=foo@bar.com
```

| Component name | Description | Component value |
| --- | --- | --- |
| [Path](../specification/spec/Sisk.Core.Http.HttpRequest.Path) | Gets the request path. | `/user/login` |
| [FullPath](../specification/spec/Sisk.Core.Http.HttpRequest.FullPath) | Gets the request path and the query string. | `/user/login?email=foo@bar.com` |
| [FullUrl](../specification/spec/Sisk.Core.Http.HttpRequest.FullUrl) | Gets the entire URL request string. | `http://localhost:5000/user/login?email=foo@bar.com` |
| [Host](../specification/spec/Sisk.Core.Http.HttpRequest.Host) | Gets the request host. | `localhost` |
| [Authority](../specification/spec/Sisk.Core.Http.HttpRequest.Authority) | Gets the request host and port. | `localhost:5000` |
| [QueryString](../specification/spec/Sisk.Core.Http.HttpRequest.QueryString) | Gets the request query. | `?email=foo@bar.com` |
| [Query](../specification/spec/Sisk.Core.Http.HttpRequest.Query) | Gets the request query in a named value collection. | `{StringValueCollection object}` |
| [IsSecure](../specification/spec/Sisk.Core.Http.HttpRequest.IsSecure) | Determines if the request is using SSL (true) or not (false). | `false` |

# Getting the request body

Some requests include body such as forms, files, or API transactions. You can get the body of a request from the property:

```cs
// gets the request body as an string, using the request encoding as the encoder
string body = request.Body;

// or gets it in an byte array
byte[] bodyBytes = request.RawBody;

// or else, you can stream it
Stream requestStream = request.GetRequestStream();
```

It is also possible to determine if there is a body in the request and if it is loaded with the properties [HasContents](../specification/spec/Sisk.Core.Http.HttpRequest.HasContents), which determines if the request has contents and [IsContentAvailable](../specification/spec/Sisk.Core.Http.HttpRequest.IsContentAvailable) which indicates that the HTTP server fully received the content from the remote point.

It is not possible to read the request content through `GetRequestStream` more than once. If you read with this method, the values in `RawBody` and `Body` will also not be available.

> **Note:**
>
> Sisk follows the RFC 9110 "HTTP Semantics", which doens't allow certain requests methods to have body. These requests will immediately drop an 400 (Bad Request) with the `ContentServedOnIllegalMethod` status. Requests with bodies are not allowed in methods GET, OPTIONS, HEAD and TRACE. You can read the [RFC 9910](https://httpwg.org/spec/rfc9110.html) here.
>
> You can disable this feature by turning [ThrowContentOnNonSemanticMethods](../specification/spec/Sisk.Core.Http.HttpServerFlags) to `false`.

# Getting the request context

The HTTP Context is an exclusive Sisk object that stores HTTP server, route, router and request handler information. You can use it to be able to organize yourself in an environment where these objects are difficult to organize.

The [RequestBag](../specification/spec/Sisk.Core.Http.HttpContext.RequestBag) object contains stored information that is passed from an request handler to another point, and can be consumed at the final destination. This object can also be used by request handlers that run after the route callback.

> Tip: this property is also acessible by [HttpRequest.Bag](../specification/spec/Sisk.Core.Http.HttpRequest.Bag) property.

```cs
public class AuthenticateUserRequestHandler : IRequestHandler
{
    public string Identifier { get; init; } = Guid.NewGuid().ToString();
    public RequestHandlerExecutionMode ExecutionMode { get; init; } = RequestHandlerExecutionMode.BeforeResponse;

    public HttpResponse? Execute(HttpRequest request, HttpContext context)
    {
        if (request.Headers["Authorization"] != null)
        {
            context.RequestBag.Add("AuthenticatedUser", "Bob");
            return null;
        }
        else
        {
            return new HttpResponse(System.Net.HttpStatusCode.Unauthorized);
        }
    }
}
```

The above request handler will define `AuthenticatedUser` in the request bag, and can be consumed later in the final callback:

```cs
public class MyController
{
    [Route(RouteMethod.Get, "/")]
    [RequestHandler(typeof(AuthenticateUserRequestHandler))]
    static HttpResponse Index(HttpRequest request)
    {
        HttpResponse res = new HttpResponse();
        string authUser = request.Context.RequestBag["AuthenticatedUser"];
        res.Content = new StringContent($"Hello, {authUser}!");
        return res;
    }
}
```

You can also use the `Bag.Set()` and `Bag.Get()` helper methods to get or set objects by their type singletons.

```cs
public class Authenticate : RequestHandler
{
    public override HttpResponse? Execute(HttpRequest request, HttpContext context)
    {
        request.Bag.Set<User>(authUser);
    }
}

[RouteGet("/")]
[RequestHandler<Authenticate>]
public static HttpResponse Test(HttpRequest request)
{
    var user = request.Bag.Get<User>();
}
```

# Getting form data

You can get the values of a form data in an [NameValueCollection](https://learn.microsoft.com/pt-br/dotnet/api/system.collections.specialized.namevaluecollection) with the example below:

```cs
static HttpResponse Index(HttpRequest request)
{
    var form = request.GetFormContent();

    string? username = form["username"];
    string? password = form["password"];

    if (AttempLogin(username, password) == true)
    {
        ...
    }
}
```

# Getting multipart form data

Sisk's HTTP request lets you get uploaded multipart contents, such as a files, form fields, or any binary content.

```cs
static HttpResponse Index(HttpRequest request)
{
    var multipartFormDataObjects = request.GetMultipartFormContent();

    foreach (MultipartObject uploadedObject in multipartFormDataObjects) {
        // The name of the file provided by Multipart form data. Null is returned if the object is not a file.
        Console.WriteLine("File name       : " + uploadedObject.Filename);
        // The multipart form data object field name.
        Console.WriteLine("Field name      : " + uploadedObject.Name);
        // The multipart form data content length.
        Console.WriteLine("Content length  : " + uploadedObject.ContentLength);
        // Determine the image format based in the file header for each image content type.
        // If the content ins't an recognized image format, this method below will return
        // MultipartObjectImageFormat.Unknown
        Console.WriteLine("Image format    : " + uploadedObject.GetImageFormat());
    }
}
```

You can read more about Sisk [Multipart form objects](../specification/spec/Sisk.Core.Entity.MultipartObject) and it's methods, properties and functionalities.

# Server-sent events support

Sisk supports [Server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events), which allows sending chunks as an stream and keeping the connection between the server and the client alive.

Calling the [HttpRequest.GetEventSource](../specification/spec/Sisk.Core.Http.HttpRequest.GetEventSource()) method will put the HttpRequest in it's listener state. From this, the context of this HTTP request will not expect an HttpResponse as it will overlap the packets sent by server side events.

After sending all packets, the callback must return the [Close](../specification/spec/Sisk.Core.Http.HttpRequestEventSource.Close()) method, which will send the final response to the server and indicate that the streaming has ended.

It's not possible to predict what the total length of all packets that will be sent, so it is not possible to determine the end of the connection with `Content-Length` header.

By most browsers defaults, server-side events does not support sending HTTP headers or methods other than the GET method. Therefore, be careful when using request handlers with event-source requests that require specific headers in the request, as it probably they ins't going to have them.

Also, most browsers restart streams if the [EventSource.close](https://developer.mozilla.org/en-US/docs/Web/API/EventSource/close) method ins't called on the client side after receiving all the packets, causing infinite additional processing on the server side. To avoid this kind of problem, it's common to send an final packet indicating that the event source has finished sending all packets.

The example below shows how the browser can communicate with the server that supports Server-side events.

```html
<html>
    <body>
        <b>Fruits:</b>
        <ul></ul>
    </body>
    <script>
        const evtSource = new EventSource('/event-source');
        const eventList = document.querySelector('ul');

        evtSource.onmessage = (e) => {
            const newElement = document.createElement("li");

            newElement.textContent = `message: ${e.data}`;
            eventList.appendChild(newElement);

            if (e.data == "Tomato") {
                evtSource.close();
            }
        }
    </script>
</html>
```

And progressively send the messages to the client:

```cs
public class MyController
{
    [Route(RouteMethod.Get, "/event-source")]
    static HttpResponse ServerEventsResponse(HttpRequest request)
    {
        var serverEvents = request.GetEventSource();

        string[] fruits = new[] { "Apple", "Banana", "Watermelon", "Tomato" };

        foreach (string fruit in fruits)
        {
            serverEvents.Send(fruit);
            Thread.Sleep(1500);
        }

        return serverEvents.Close();
    }
}
```

When running this code, we expect a result similar to this:

<img src="/assets/img/server side events demo.gif" />

# Resolving proxied IPs and hosts

Sisk can be used with proxies, and therefore IP addresses can be replaced by the proxy endpoint in the transaction from a client to the proxy.

By default, most proxies send a header named `X-Forwarded-For` indicating the real IP of the connecting client. Sisk has properties that resolve these headers to the properties of a request.

You can also do this for the host if it is forwarded.

To activate this, when configuring your HTTP server, make sure this property is defined:

```cs
HttpServerConfiguration confg = new HttpServerConfiguration();
confg.ResolveForwardedOriginAddress = true; // will resolve the first X-Forwarded-For address entry
confg.ResolveForwardedOriginHost = true;
```

In case of [ResolveForwardedOriginHost](../specification/spec/Sisk.Core.Http.HttpServerConfiguration.ResolveForwardedOriginHost) and an `X-Forwarded-Host` header is present, the value of this header will be used for server-side DNS matching.

# Headers encoding

Header encoding can be a problem for some implementations. On Windows, UTF-8 headers are not supported, so ASCII is used. Sisk has a built-in encoding converter, which can be useful for decoding incorrectly encoded headers.

This operation is costly and disabled by default, but can be enabled under the [NormalizeHeadersEncodings](/specification/spec/Sisk.Core.Http.HttpServerFlags.NormalizeHeadersEncodings) flag.