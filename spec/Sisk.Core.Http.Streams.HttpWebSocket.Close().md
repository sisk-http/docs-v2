<!--

Copyrights 2023 Sisk Framework - CypherPotato
Published under MIT license

!!! DO NOT EDIT THIS FILE !!!
This file was generated by a tool in the Sisk package. To edit the information in this documentation,
edit the XML documentation present in the Sisk source code.

-->


# Close() method

Declaring type: [Sisk.Core.Http.Streams.HttpWebSocket](/read?q=/contents/spec/Sisk.Core.Http.Streams.HttpWebSocket.md) (from Sisk.Core)


Definition:

```cs
public HttpResponse Close()
```

Closes the connection between the client and the server and returns an Http resposne indicating that the connection has been terminated. This method will not throw an exception if the connection is already closed.
