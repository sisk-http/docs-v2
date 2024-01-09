<!--

Copyrights 2023 Sisk Framework - CypherPotato
Published under MIT license

!!! DO NOT EDIT THIS FILE !!!
This file was generated by a tool in the Sisk package. To edit the information in this documentation,
edit the XML documentation present in the Sisk source code.

-->

# HttpServerHandler class
Assembly: Sisk.Core

Namespace: Sisk.Core.Http.Handlers

Definition:

```cs
public class HttpServerHandler
```

Represents a handler for the <a href="/spec/Sisk.Core.Http.HttpServer.md">HttpServer</a>, router, and related modules.


# Method list

<table>
    <tbody>
<tr>
    <td style="width: 33%">
        <img class="icon" src="/assets/img/icons/method.svg">
        <a href="/spec/Sisk.Core.Http.Handlers.HttpServerHandler.OnContextBagCreated(HttpContextBagRepository).md">
            OnContextBagCreated(HttpContextBagRepository)
        </a>
    </td>
    <td>
        Method that is called when an <a href="/spec/Sisk.Core.Http.HttpContextBagRepository.md">HttpContextBagRepository</a> is created within an <a href="/spec/Sisk.Core.Http.HttpRequest.md">HttpRequest</a> object.
    </td>
</tr>
<tr>
    <td style="width: 33%">
        <img class="icon" src="/assets/img/icons/method.svg">
        <a href="/spec/Sisk.Core.Http.Handlers.HttpServerHandler.OnException(Exception).md">
            OnException(Exception)
        </a>
    </td>
    <td>
        Method that is called when an exception is caught in the Http server.
    </td>
</tr>
<tr>
    <td style="width: 33%">
        <img class="icon" src="/assets/img/icons/method.svg">
        <a href="/spec/Sisk.Core.Http.Handlers.HttpServerHandler.OnHttpRequestClose(HttpServerExecutionResult).md">
            OnHttpRequestClose(HttpServerExecutionResult)
        </a>
    </td>
    <td>
        Method that is called when an <a href="/spec/Sisk.Core.Http.HttpRequest.md">HttpRequest</a> is closed in the Http server.
    </td>
</tr>
<tr>
    <td style="width: 33%">
        <img class="icon" src="/assets/img/icons/method.svg">
        <a href="/spec/Sisk.Core.Http.Handlers.HttpServerHandler.OnHttpRequestOpen(HttpRequest).md">
            OnHttpRequestOpen(HttpRequest)
        </a>
    </td>
    <td>
        Method that is called when an <a href="/spec/Sisk.Core.Http.HttpRequest.md">HttpRequest</a> is received in the Http server.
    </td>
</tr>
<tr>
    <td style="width: 33%">
        <img class="icon" src="/assets/img/icons/method.svg">
        <a href="/spec/Sisk.Core.Http.Handlers.HttpServerHandler.OnServerStarted(HttpServer).md">
            OnServerStarted(HttpServer)
        </a>
    </td>
    <td>
        Method that is called immediately after starting the <a href="/spec/Sisk.Core.Http.HttpServer.md">HttpServer</a>, when it's ready and listening.
    </td>
</tr>
<tr>
    <td style="width: 33%">
        <img class="icon" src="/assets/img/icons/method.svg">
        <a href="/spec/Sisk.Core.Http.Handlers.HttpServerHandler.OnServerStarting(HttpServer).md">
            OnServerStarting(HttpServer)
        </a>
    </td>
    <td>
        Method that is called immediately before starting the <a href="/spec/Sisk.Core.Http.HttpServer.md">HttpServer</a>.
    </td>
</tr>
<tr>
    <td style="width: 33%">
        <img class="icon" src="/assets/img/icons/method.svg">
        <a href="/spec/Sisk.Core.Http.Handlers.HttpServerHandler.OnSetupRouter(Router).md">
            OnSetupRouter(Router)
        </a>
    </td>
    <td>
        Method that is called when an <a href="/spec/Sisk.Core.Routing.Router.md">Router</a> is binded to the Http server.
    </td>
</tr>
    </tbody>
</table>