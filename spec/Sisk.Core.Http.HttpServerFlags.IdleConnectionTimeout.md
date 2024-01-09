<!--

Copyrights 2023 Sisk Framework - CypherPotato
Published under MIT license

!!! DO NOT EDIT THIS FILE !!!
This file was generated by a tool in the Sisk package. To edit the information in this documentation,
edit the XML documentation present in the Sisk source code.

-->


# IdleConnectionTimeout field

Declaring type: [Sisk.Core.Http.HttpServerFlags](/spec/Sisk.Core.Http.HttpServerFlags.md) (from Sisk.Core)


Definition:

```cs
public TimeSpan IdleConnectionTimeout;
```

Determines the maximum amount of time an connection can keep alive without sending or receiving any data.


<p>
                    Default value: <code>TimeSpan.FromSeconds(120)</code></p>
