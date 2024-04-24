# Logging

You can configure Sisk to write access and error logs automatically. It is possible to define log rotation, extensions and frequency.

The [LogStream](../specification/spec/Sisk.Core.Http.LogStream) class provides an asynchronous way of writing logs and keeping them in an awaitable write queue.

In this article we will show you how to configure logging for your application.

# File based access logs

Logs to files open the file, write the line text, and then close the file for every line written. This procedure was adopted to maintain write responsiveness in the logs.

```cs
config.AccessLogsStream = new LogStream("logs/access.log");
```

The above code will write all incoming requests to the `logs/access.log` file. Note that, the file is created automatically if it does not exist, however the folder before it does not. It's not necessary to create the `logs/` directory as the LogStream class automatically creates it.

# Stream based logging

You can write log files to TextWriter objects instances, such as `Console.Out`, by passing an TextWriter object in the constructor:

```cs
config.AccessLogsStream = new LogStream(Console.Out);
```

For every message written in the stream-based log, the `TextWriter.Flush()` method is called.

# Access log formatting

You can customize the access log format by predefined variables. Consider the following line:

```cs
config.AccessLogsFormat = "%dd/%dmm/%dy %tH:%ti:%ts %tz %ls %ri %rs://%ra%rz%rq [%sc %sd] %lin -> %lou in %lmsms [%{user-agent}]";
```

It will write an message like:

    29/mar./2023 15:21:47 -0300 Executed ::1 http://localhost:5555/ \[200 OK\] 689B -> 707B in 84ms \[Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/111.0.0.0 Safari/537.36\]

You can format your log file by the format described by the table:

|Variable|Description|Example|
|--- |--- |--- |
|%dd|The current timestamp's day, in 00 format.|25|
|%dm|The current timestamp's month, in 00 format.|03|
|%dmm|The current timestamp's month, in abreviated name format.|mar.|
|%dmmm|The current timestamp's month, in full name format.|March|
|%dy|The current timestamp's year, in 0000 format.|2023|
|%th|The current timestamp's hour, in 12-hours format.|03|
|%tH|The current timestamp's hour, in 24-hours format.|15|
|%ti|The current timestamp's minutes, in 00 format.|25|
|%ts|The current timestamp's seconds, in 00 format.|32|
|%tm|The current timestamp's millisecond, in 000 format.|633|
|%tz|The current timezone difference, in +/- 0000 format.|+0300, -0500, +0000|
|%ri|The requesting user IP address (may be IPv4 or IPv6).|192.168.0.1|
|%rm|The request method in upper case.|GET|
|%rs|The requesting user URL scheme.|https, http|
|%ra|The requesting user URL authority.|my.contorso.com:8080|
|%rh|The requesting user URL host.|my.contorso.com|
|%rp|The requesting user URL port.|8080|
|%rz|The requesting user URL absolute path.|/index.html|
|%rq|The requesting user URL query string.|?foo=bar&aaa=bbb|
|%sc|The response status code, in 000 format.|404|
|%sd|The response status description.|Not Found|
|%lin|The incoming request content size, in an human readable form.|12,5kb|
|%lou|The outcoming response content size, in an human readable form.|65,8kb|
|%lms|The server processing time of the request and deliver of the response, in milliseconds format (000).|18|
|%{header}|Gets the value of an HTTP header, where header is the header name, or an empty value if the header ins't present. This field is case-insensitive.|%{user-agent}|


# Rotating logs

> **Tip:**
>
> In Sisk 0.15 and older, this function is only available with the Sisk.ServiceProvider package. In Sisk 0.16 and above, this function is implemented on it's core package.

You can configure the HTTP server to rotate the log files to a compressed .gz file when they reach a certain size. The size is checked periodically by the limiar you define.

```cs
config.AccessLogsStream = new LogStream("access.log");

var rotater = new RotatingLogPolicy(config.AccessLogsStream);
rotater.Configure(1024 * 1024, TimeSpan.FromHours(6));
```

The above code will check every six hours if the LogStream's file has reached it's 1MB limit. If so, the file is compressed to an .gz file and it then `access.log` is cleaned.

During this process, writing to the file is locked until the file is compressed and cleaned. All lines that enter to be written in this period will be in a queue waiting for the end of compression.

This function only works with file-based LogStreams.

# Error logging

When a server is not throwing errors to the debugger, it forwards the errors to log writing when there are any. You can configure error writing with:

```cs
config.ThrowExceptions = false;
config.ErrorsLogsStream = new LogStream("error.log");
```

This property will only write something to the log if the error is not captured by the callback or the [Router.CallbackErrorHandler](../specification/spec/Sisk.Core.Routing.Router.CallbackErrorHandler) property.

The error written by the server always writes the date and time, the request headers (not the body), the error trace, and the inner exception trace, if theres any.

# Other logging instances

Your application can have zero or multiple LogStreams, there is no limit on how many log channels it can have. Therefore, it is possible to direct your application's log to a file other than the default AccessLog or ErrorLog.

```cs
LogStream appMessages = new LogStream("messages.log");
appMessages.WriteLine("Application started at {0}", DateTime.Now);
```

# Extending LogStream

You can extend the `LogStream` class to write custom formats, compatible with the current Sisk log engine. The example below allows to write colorful messages into the Console through Spectre.Console library:

```cs
public class SpectreLogStream : LogStream
{
    public enum MessageLevel
    {
        Warn,
        Error,
        Info
    }

    // instance the LogStream base class with an output pointing to "info.log", which will
    // write unformatted messages
    public SpectreLogStream() : base("info.log") {}

    public void WriteInformation(MessageLevel level, string message)
    {
        string color = level switch
        {
            MessageLevel.Warn => "gold3_1",
            MessageLevel.Error => "darkorange3_1",
            MessageLevel.Info => "darkseagreen1",
            _ => "white"
        };

        AnsiConsole.MarkupLine($"[{color}]{level}[/] {message}");

        // also writes the message to the default stream
        base.WriteLine("[{0}] {1}", level, message);
    }
}
```

Another way to automatically write custom logs for each request/response is to create an HttpServerHandler. The example below is a little more complete. It writes the body of the request and response in JSON to the Console. It can be useful for debugging requests in general. This example makes use of ContextBag and HttpServerHandler.

```cs
class Program
{
    static async Task Main(string[] args)
    {
        var app = HttpServer.CreateBuilder(host =>
        {
            host.UseListeningPort(5555);
            host.UseHandler<JsonMessageHandler>();
        });

        app.Router += new Route(RouteMethod.Any, "/json", request =>
        {
            return new HttpResponse()
                .WithContent(JsonContent.Create(new
                {
                    method = request.Method.Method,
                    path = request.Path,
                    specialMessage = "Hello, world!!"
                }));
        });

        await app.StartAsync();
    }
}

class JsonMessageHandler : HttpServerHandler
{
    protected override void OnHttpRequestOpen(HttpRequest request)
    {
        if (request.Method != HttpMethod.Get && request.Headers["Content-Type"]?.Contains("json", StringComparison.InvariantCultureIgnoreCase) == true)
        {
            // At this point, the connection is open and the client has sent the header specifying
            // that the content is JSON.The line below reads the content and leaves it stored in the request.
            //
            // If the content is not read in the request action, the GC is likely to collect the content
            // after sending the response to the client, so the content may not be available after the response is closed.
            //
            _ = request.RawBody;

            // add hint in the context to tell that this request has an json body on it
            request.Bag.Add("IsJsonRequest", true);
        }
    }

    protected override async void OnHttpRequestClose(HttpServerExecutionResult result)
    {
        string? requestJson = null,
                responseJson = null,
                responseMessage;

        if (result.Request.Bag.ContainsKey("IsJsonRequest"))
        {
            // reformats the JSON using the CypherPotato.LightJson library
            var content = result.Request.Body;
            requestJson = JsonValue.Deserialize(content, new JsonOptions() { WriteIndented = true }).ToString();
        }

        if (result.Response is { } response)
        {
            var content = response.Content;
            responseMessage = $"{(int)response.Status} {HttpStatusInformation.GetStatusCodeDescription(response.Status)}";

            if (content is HttpContent httpContent &&
                // check if the response is JSON
                httpContent.Headers.ContentType?.MediaType?.Contains("json", StringComparison.InvariantCultureIgnoreCase) == true)
            {
                string json = await httpContent.ReadAsStringAsync();
                responseJson = JsonValue.Deserialize(json, new JsonOptions() { WriteIndented = true }).ToString();
            }
        }
        else
        {
            // gets the internal server handling status
            responseMessage = result.Status.ToString();
        }

        StringBuilder outputMessage = new StringBuilder();

        if (requestJson != null)
        {
            outputMessage.AppendLine("-----");
            outputMessage.AppendLine($">>> {result.Request.Method} {result.Request.Path}");

            if (requestJson is not null)
                outputMessage.AppendLine(requestJson);
        }

        outputMessage.AppendLine($"<<< {responseMessage}");

        if (responseJson is not null)
            outputMessage.AppendLine(responseJson);

        outputMessage.AppendLine("-----");

        await Console.Out.WriteLineAsync(outputMessage.ToString());
    }
}
```