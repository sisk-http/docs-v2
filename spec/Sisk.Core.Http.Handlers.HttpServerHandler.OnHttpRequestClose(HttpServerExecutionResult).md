<!--

Copyrights 2023 Sisk Framework - CypherPotato
Published under MIT license

!!! DO NOT EDIT THIS FILE !!!
This file was generated by a tool in the Sisk package. To edit the information in this documentation,
edit the XML documentation present in the Sisk source code.

-->


# OnHttpRequestClose(HttpServerExecutionResult) method

Declaring type: [Sisk.Core.Http.Handlers.HttpServerHandler](/spec/Sisk.Core.Http.Handlers.HttpServerHandler.md) (from Sisk.Core)


Definition:

```cs
public virtual void OnHttpRequestClose(HttpServerExecutionResult result)
```

Method that is called when an <a href="/spec/Sisk.Core.Http.HttpRequest.md">HttpRequest</a> is closed in the Http server.


# Parameters

<table>
    <tbody>
<tr>
    <td width="33%">result</td>
    <td>The result of the execution of the request.</td>
</tr>
    </tbody>
</table>