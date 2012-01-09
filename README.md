# Introduction #
RestExpress is a thin wrapper on the [JBOSS Netty HTTP](http://www.jboss.org/netty) 
stack to provide a simple and easy way to create RESTful services in Java that support 
massive Internet Scale and performance.

Born to be simple, only three things are required to wire up a service.

1. The main class which utilizes the RestExpress DSL to create a server instance.
2. A RouteDeclaration extender (much like routes.rb in a Rails app), which uses a DSL for the declaration of supported URLs and HTTP methods of the service(s) in its defineRoutes() method.
3. Service implementation(s), which is/are a simple POJO--no interface or super class implementation.

RestExpress supports both [JSEND](http://labs.omniti.com/labs/jsend)-style and raw responses.  Meaning that it can wrap responses so
AJAX clients can always process the responses easily.  Or it can simply marshal the service return
value directly into JSON or XML. 

### To build:

    ant 

### To prepare a release build:

    ant release

# A quick tutorial #
Please see the [examples/kickstart](RestExpress/tree/master/examples/kickstart) for 
a complete, running example.

HTTP Methods, if not changed in the fluent (DSL) interface, map to the following:

- GET --> read(Request, Response)
- PUT --> update(Request, Response)
- POST --> create(Request, Response)
- DELETE --> delete(Request, Response)

You can choose to return objects from the methods, if desired, which will be returned to the client
as body of the response.  The object will be marshaled into JSON or XML, depending on the default or
based on the format in the request (e.g. '.xml' or '?format=xml').

If you choose to not return a value from the method (void methods) and using raw responses, then
call response.setResponseNoContent() before returning to set the response HTTP status code to 204
(no content).  Wrapped responses (JSEND style) are the default.  So if you're using wrapped
responses, there will always be a response returned to the client--therefore, you don't need to set
the response.setResponseNoContent().  Just return your objects--or not.  RestExpress will handle
things on your behalf!

On successful creation, call response.setResponseCreated() to set the returning HTTP status code to
201.

# Change History/Release Notes #
Release notes are [available here](RestExpress/tree/master/ReleaseNotes.md)
 