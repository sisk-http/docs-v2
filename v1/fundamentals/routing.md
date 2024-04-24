# Routing

The router refers to what redirects requests to their proper places. A [Router](../specification/spec/Sisk.Core.Routing.Router) is responsible for routing [Route](../specification/spec/Sisk.Core.Routing.Route) to its responsible controllers, methods or callbacks. What the Router uses to match a route with a Route is the request path and its method. These two items are responsible for assigning a request to a route.

```cs
mainRouter.SetRoute(RouteMethod.Get, "/hey/<name>", (request) =>
{
    var name = request.Query["name"].GetString();
    return new HttpResponse() { Content = new StringContent("Hello, " + name) };
});

// alternative way using the + operator
mainRouter += new Route(RouteMethod.Get, "/", (req) =>
{
    return new HttpResponse()
        .WithContent("Hello, world!");
});
```

To understand what a route is capable of doing, we need to understand what a request is capable of doing. An [HttpRequest](../specification/spec/Sisk.Core.Http.HttpRequest) will contain everything you need. Sisk also includes some extra features that speed up the overral development.

# Creating routes using paths

You can define routes using various `SetRoute` methods.

```cs
mainRouter.SetRoute(RouteMethod.Get, "/hey/<name>", (request) =>
{
    string name = request.Query["name"]!; // name will never be null in this context
    return request.CreateOkResponse($"Hello, {name}");
});

// or multiple parameters
mainRouter.SetRoute(RouteMethod.Get, "/hey/<name>/surname/<surname>", (request) =>
{
    string name = request.Query["name"]!;
    string surname = request.Query["surname"]!;
    return request.CreateOkResponse($"Hello, {name} {surname}!");
});
```

The HTTP request [Query](../specification/spec/Sisk.Core.Http.HttpRequest.Query) property also stores the content of an original query, but if there are parameters in the route with the same name as a query, it will be replaced by what is in the route. The path that is matched with an request URI is always the path as explained in [RFC 3986](https://www.rfc-editor.org/rfc/rfc3986#section-3.3).

You can also get an query parameter, including an route parameter, casting it to the desired type, with [GetQueryValue](../specification/spec/Sisk.Core.Http.HttpRequest.GetQueryValue(string).md):

```cs
mainRouter.SetRoute(RouteMethod.Get, "/user/<id>", (request) =>
{
    Guid id = request.GetQueryValue<Guid>("id");
});
```

Internally, the implementation of this method differs from .NET 6 to newer versions of .NET that did not yet implement `IParsable`, so to make it compatible, some converters were implemented manually. You can see the [supported types here](https://github.com/sisk-http/core/blob/main/src/Internal/Parseable.cs).

> **Note:**
>
> Paths have their trailing `/` ignored in both request and route path, that is, if you try to access a route defined as `/index/page` you'll be able to access using `/index/page/` too.
>
> You can also force URLs to terminate with `/` enabling the [ForceTrailingSlash](../specification/spec/Sisk.Core.Http.HttpServerFlags.ForceTrailingSlash) flag.

# Creating routes using class instances

You can also define routes dynamically using reflection with the attribute [RouteAttribute](../specification/spec/Sisk.Core.Routing.RouteAttribute). This way, the instance of a class in which its methods implement this attribute will have their routes defined in the target router.

Methods marked with the route attribute must be static.

```cs
public class MyController
{
    // will be reached as an instance form
    [Route(RouteMethod.Get, "/")]
    HttpResponse Index(HttpRequest request)
    {
        HttpResponse res = new HttpResponse();
        res.Content = new StringContent("Index!");
        return res;
    }

    // static methods only be reached when setting the object type
    [Route(RouteMethod.Get, "/hello")]
    static HttpResponse Hello(HttpRequest request)
    {
        HttpResponse res = new HttpResponse();
        res.Content = new StringContent("Hello world!");
        return res;
    }
}
```

Each route method (with the exception of Any) also contains its own attribute, such as:

```cs
public class MyController
{
    [RouteGet("/")]
    HttpResponse Index(HttpRequest request)
    {
        return new HttpResponse()
            .WithContent(new HtmlContent("<h1>Hello</h1>"));
    }
}
```

Then you can define the routes of the MyController instance:

```cs
var myController = new MyController();
mainRouter.SetObject(myController);
```

Alternatively, you can define your members statically, without an instance, by passing the class's type as a parameter:

```cs
mainRouter.SetObject(typeof(MyController));
```

The diferences between `SetObject(object)` and `SetObject(type)` is the way the router searchs for route methods. The first searches all instance methods of the object. Functions will be called on a singleton of the parameter object. The second one searchs for static methods, public or not. Both methods searchs for private and public methods through reflection.

Since Sisk version 0.16, it is possible to enable AutoScan, which will search for user-defined classes that implement `RouterModule` and will automatically associate it with the router. This method is experimental and is not supported with AOT compilation.

```cs
mainRouter.AutoScanModules<ApiController>();
```

The above instruction will search for all types which implements `ApiController` but not the type itself. The two optional parameters indicate how the method will search for these types. The first argument implies the Assembly where the types will be searched and the second indicates the way in which the types will be instantiated, whether by instance activation or by searching for static methods.

# Regex routes

Instead of using the default HTTP path matching methods, you can mark a route to be interpreted with Regex.

```cs
Route indexRoute = new Route(RouteMethod.Get, @"\/[a-z]+\/", "My route", IndexPage, null);
indexRoute.UseRegex = true;
mainRouter.SetRoute(indexRoute);
```

You can also capture groups from the regex pattern into the [Request.Query](../specification/spec/Sisk.Core.Http.HttpRequest.Query.md) contents:

```cs
[RegexRoute(RouteMethod.Get, @"/uploads/(?<filename>.*\.(jpeg|jpg|png))")]
static HttpResponse RegexRoute(HttpRequest request)
{
    string filename = request.Query["filename"].GetString();
    return new HttpResponse().WithContent($"Acessing file {filename}");
}
```

# Any method routes

You can define a route to be matched only by its path and skip the HTTP method. This can be useful for you to do method validation inside the route callback.

```cs
// will match / on any HTTP method
mainRouter.SetRoute(RouteMethod.Any, "/", callbackFunction);
```

# Ignore case route matching

By default, the interpretation of routes with requests are case-sensitive. To make it ignore case, enable this option:

```cs
mainRouter.MatchRoutesIgnoreCase = true;
```

This will also enable the option `RegexOptions.IgnoreCase` for routes where it's regex-matching.

# Not Found (404) callback handler

You can create a custom callback for when a request doesn't match any known routes.

```cs
mainRouter.NotFoundErrorHandler = () =>
{
    return new HttpResponse(404)
    {
        // Since v0.14
        Content = new HtmlContent("<h1>Not found</h1>")
        // older versions
        Content = new StringContent("<h1>Not found</h1>", Encoding.UTF8, "text/html")
    };
};
```

# Method not allowed (405) callback handler

You can also create a custom callback for when a request matches it's path, but doens't match the method.

```cs
mainRouter.MethodNotAllowedErrorHandler = (context) =>
{
    return new HttpResponse(405)
    {
        Content = new StringContent($"Method not allowed for this route.")
    };
};
```

# Internal error handler

Route callbacks can throw errors during server execution. If not handled correctly, the overall functioning of the HTTP server can be terminated. The router has a callback for when a route callback fails and prevents service interruption.

This method is only reacheable when [ThrowExceptions](../specification/spec/Sisk.Core.Http.HttpServerConfiguration.ThrowExceptions.md) is set to false.

```cs
mainRouter.CallbackErrorHandler = (ex, context) =>
{
    return new HttpResponse(500)
    {
        Content = new StringContent($"Error: {ex.Message}")
    };
};
```