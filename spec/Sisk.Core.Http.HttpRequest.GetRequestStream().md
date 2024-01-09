<!--

Copyrights 2023 Sisk Framework - CypherPotato
Published under MIT license

!!! DO NOT EDIT THIS FILE !!!
This file was generated by a tool in the Sisk package. To edit the information in this documentation,
edit the XML documentation present in the Sisk source code.

-->


# GetRequestStream() method

Declaring type: [Sisk.Core.Http.HttpRequest](/spec/Sisk.Core.Http.HttpRequest.md) (from Sisk.Core)


Definition:

```cs
public Stream GetInputStream()
```

Gets the HTTP request content stream. This property is only available while the content has not been imported by the HTTP server and will invalidate the body content cached in this object.
