<!--

Copyrights 2023 Sisk Framework - CypherPotato
Published under MIT license

!!! DO NOT EDIT THIS FILE !!!
This file was generated by a tool in the Sisk package. To edit the information in this documentation,
edit the XML documentation present in the Sisk source code.

-->

# LogOutput enum
Assembly: Sisk.Core

Namespace: Sisk.Core.Routing

Definition:

```cs
[Flags]
public enum LogOutput
```

Determines the way the server can write log messages. This enumerator is for giving permissions for certain contexts to be able or not to write to the server logs, such as <a href="/spec/Sisk.Core.Http.HttpServerConfiguration.md">AccessLogsStream</a> and <a href="/spec/Sisk.Core.Http.HttpServerConfiguration.md">ErrorsLogsStream</a>.


# Field list

<table>
    <tbody>
<tr>
    <td style="width: 33%">
        <img class="icon" src="/assets/img/icons/field.svg">
        <a href="/spec/Sisk.Core.Routing.LogOutput.AccessLog.md">
            AccessLog
        </a>
    </td>
    <td>
        Determines that the context or the route can write log messages only to the access logs through <a href="/spec/Sisk.Core.Http.HttpServerConfiguration.md">AccessLogsStream</a>.
    </td>
</tr>
<tr>
    <td style="width: 33%">
        <img class="icon" src="/assets/img/icons/field.svg">
        <a href="/spec/Sisk.Core.Routing.LogOutput.Both.md">
            Both
        </a>
    </td>
    <td>
        Determines that the context or the route can write log messages to both error and access logs.
    </td>
</tr>
<tr>
    <td style="width: 33%">
        <img class="icon" src="/assets/img/icons/field.svg">
        <a href="/spec/Sisk.Core.Routing.LogOutput.ErrorLog.md">
            ErrorLog
        </a>
    </td>
    <td>
        Determines that the context or the route can write error messages only to the error logs through <a href="/spec/Sisk.Core.Http.HttpServerConfiguration.md">ErrorsLogsStream</a>.
    </td>
</tr>
<tr>
    <td style="width: 33%">
        <img class="icon" src="/assets/img/icons/field.svg">
        <a href="/spec/Sisk.Core.Routing.LogOutput.None.md">
            None
        </a>
    </td>
    <td>
        Determines that the context or the route cannot write any log messages.
    </td>
</tr>
    </tbody>
</table>