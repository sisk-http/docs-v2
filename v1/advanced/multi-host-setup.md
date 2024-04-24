# Multiple listening hosts per server

The Sisk Framework has always supported the use of more than one host per server, that is, a single HTTP server can listen on multiple ports and each port has its own router and its own service running on it.

This way, it is easy to separate responsibilities and manage services on a single HTTP server with Sisk. The example below shows the creation of two ListeningHosts, each listening to a different port, with different routers and actions.

Read [manually creating your app](/v1/getting-started.md#manually-creating-your-app) to understand the details about this abstraction.

```cs
static void Main(string[] args)
{
    // create two listening hosts, which each one has it's own router and
    // listens to it's own port
    //
    ListeningHost hostA = new ListeningHost();
    hostA.Ports = [new ListeningPort(12000)];
    hostA.Router = new Router();
    hostA.Router.SetRoute(RouteMethod.Get, "/", request => new HttpResponse().WithContent("Hello from the host A!"));

    ListeningHost hostB = new ListeningHost();
    hostB.Ports = [new ListeningPort(12001)];
    hostB.Router = new Router();
    hostB.Router.SetRoute(RouteMethod.Get, "/", request => new HttpResponse().WithContent("Hello from the host B!"));
    
    // create an server configuration and adds both
    // listening hosts on it
    //
    HttpServerConfiguration configuration = new HttpServerConfiguration();
    configuration.ListeningHosts.Add(hostA);
    configuration.ListeningHosts.Add(hostB);

    // creates an http server which uses the specified
    // configuration
    //
    HttpServer server = new HttpServer(configuration);

    // starts the server
    server.Start();

    Console.WriteLine("Try to reach host A in {0}", server.ListeningPrefixes[0]);
    Console.WriteLine("Try to reach host B in {0}", server.ListeningPrefixes[1]);

    Thread.Sleep(-1);
}
```