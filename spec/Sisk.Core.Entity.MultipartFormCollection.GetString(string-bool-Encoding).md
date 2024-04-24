<!--

Copyrights 2023 Sisk Framework - CypherPotato
Published under MIT license

!!! DO NOT EDIT THIS FILE !!!
This file was generated by a tool in the Sisk package. To edit the information in this documentation,
edit the XML documentation present in the Sisk source code.

-->


# GetString(string, bool, Encoding) method

Declaring type: [Sisk.Core.Entity.MultipartFormCollection](/spec/Sisk.Core.Entity.MultipartFormCollection.md) (from Sisk.Core)


Definition:

```cs
public string? GetString(string name, bool ignoreCase = false, Encoding? encoding = null)
```

Reads an form item contents by it's name and returns their content as string.


# Parameters

<table>
    <tbody>
<tr>
    <td width="33%">name</td>
    <td>The form item name.</td>
</tr>
<tr>
    <td width="33%">ignoreCase</td>
    <td>Optional. Determines if this method should use an case-insensitive search to find the specified item.</td>
</tr>
<tr>
    <td width="33%">encoding</td>
    <td>Optional. Specifies the  used to read the content.</td>
</tr>
    </tbody>
</table>