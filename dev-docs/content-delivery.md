---
layout: wiki
title: Content Delivery
---
## Content Delivery

[work in progress]

The **content delivery** flow looks like this:

```
_Browser Request -> Check if page can be found in KeyValue Store_  
_-> if yes, combine prerendered content snippets_  
_-> if no, 404 (or fall back to alternative application to server request)_  
```

![](http://www.websequencediagrams.com/cgi-bin/cdraw?lz=IyBUbyBiZSB1c2VkIHdpdGggaHR0cHM6Ly93d3cud2Vic2VxdWVuY2VkaWFncmFtcy5jb20vCgp0aXRsZSBIVFRQIFJlcXVlc3QgRmxvdwoKV2ViRnJvbnQtPgACCDogcmVnaXN0ZXJGYWN0b3JpZXMoKQALHVJvdXRlcgAYDitIdHRwABMGQ2hhaW46IHJvdXRlKAASBQB8BikKbG9vcAoAGg8ALg0AIRUAUAoAXwgAgUwGSGFuZGxlcjogY2FuUHJvY2VzcwAmEwAfDS0-LQBhDGJvb2wKZW5kAFYNABcLAIFDBwBiEgCBMxItAIJFCgAcEwCCGBAAgTIPcACBFSkAVhBzcG9uc2UAg0sLAAsMOiBzZW5kKCk&s=default)

Right click and open in a new tab to make it readable.

-----

## Diagram Source Data

### HTTP Request Flow

```
# To be used with https://www.websequencediagrams.com/

title HTTP Request Flow

WebFront->WebFront: registerFactories()
WebFront->WebFront: registerRouters()
WebFront->+HttpRouterChain: route(HttpRequest)
loop
HttpRouterChain->+HttpRouter: route(HttpRequest)
HttpRouter->+HttpRequestHandler: canProcess(HttpRequest)
HttpRequestHandler->-HttpRouter: bool
end
HttpRouter->-HttpRouterChain: HttpRequestHandler
HttpRouterChain->-WebFront: HttpRequestHandler
WebFront->+HttpRequestHandler: process(HttpRequest)
HttpRequestHandler->-WebFront: HttpResponse
WebFront->HttpResponse: send()
```
