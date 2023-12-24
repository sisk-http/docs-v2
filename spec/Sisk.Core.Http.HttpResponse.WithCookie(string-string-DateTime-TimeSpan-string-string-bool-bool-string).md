<!--

Copyrights 2023 Sisk Framework - CypherPotato
Published under MIT license

!!! DO NOT EDIT THIS FILE !!!
This file was generated by a tool in the Sisk package. To edit the information in this documentation,
edit the XML documentation present in the Sisk source code.

-->


# WithCookie(string, string, DateTime?, TimeSpan?, string, string, bool?, bool?, string) method

Declaring type: [Sisk.Core.Http.HttpResponse](/read?q=/contents/spec/Sisk.Core.Http.HttpResponse.md) (from Sisk.Core)


Definition:

```cs
public HttpResponse WithCookie(string name, string value, DateTime? expires, TimeSpan? maxAge, string? domain, string? path, bool? secure, bool? httpOnly, string? sameSite)
```

Sets a cookie and sends it in the response to be set by the client.


# Parameters

<table>
    <tbody>
<tr>
    <td width="33%">name</td>
    <td>The cookie name.</td>
</tr>
<tr>
    <td width="33%">value</td>
    <td>The cookie value.</td>
</tr>
<tr>
    <td width="33%">expires</td>
    <td>The cookie expirity date.</td>
</tr>
<tr>
    <td width="33%">maxAge</td>
    <td>The cookie max duration after being set.</td>
</tr>
<tr>
    <td width="33%">domain</td>
    <td>The domain where the cookie will be valid.</td>
</tr>
<tr>
    <td width="33%">path</td>
    <td>The path where the cookie will be valid.</td>
</tr>
<tr>
    <td width="33%">secure</td>
    <td>Determines if the cookie will only be stored in an secure context.</td>
</tr>
<tr>
    <td width="33%">httpOnly</td>
    <td>Determines if the cookie will be only available in the HTTP context.</td>
</tr>
<tr>
    <td width="33%">sameSite</td>
    <td>The cookie SameSite parameter.</td>
</tr>
    </tbody>
</table>