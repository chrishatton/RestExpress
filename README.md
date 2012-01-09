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
 
## Release 0.7.2 - in development (build 'master') ##
* Introduced DateHeaderPostprocessor which adds a Date header to responses to GET requests.
* Introduced CacheHeaderPostprocessor which support Cache-Control and other caching-related
  response header best-practices by setting Parameters.Cache.MAX_AGE or Flags.Cache.DONT_CACHE on
  a route.

## Release 0.7.1 ##
* Added rootCause to ResultWrapper data area.
* Exposed the XStream object from DefaultXmlProcessor.
* Renamed Link to XLink.
* Renamed LinkUtils to XLinkUtils, adding asXLinks() method that utilizes XLinkFactory callback
  to create the XLink instances.
* Changed URL Matching to support additional characters: '[', ']', '&' which more closely follows
  W3C the specification.
* Added ability to return query string parameters as a Map from the Request.
* Introduced Request.getBaseUrl() which returns protocol and host, without URL path information.
* Introduced query criteria capability: filter, order, range (for pagination).
* Introduced the concept of Plugins.
* Refactored the console routes to use the new plugin concept.
* Updated Netty jars (to 3.2.5).
* Added ability to set the number of worker threads via call to RestExpress.setWorkerThreadCount()
  before calling bind().

## Release 0.7.0 ##
* Added gzip request/response handling. On by default. Disable it via call to
  RestExpress.noCompression() and supportCompression().
* Added chunked message handling. On by default. Chunking settings are managed via
  RestExpress.noChunkingSupport(), supportChunking(), and setMaxChunkSize(int).

## Release 0.6.1 ##
* Bug fix to patch erroneously writing to already closed channel in
  DefaultRequestHandler.exceptionCaught().

## Release 0.6.1 ##
* Stability release.
* Fixed issue when unable to URL Decode query string parameters or URL.
* Introduced SerializationResolver that defines a getDefault() method. Implemented
  SerializationResolver in DefaultSerializationResolver.
* Changed UrlPattern to match format based on non-whitespace vs. word characters.
* Refactored Request.getHeader() into getRawHeader(String) and getUrlDecodedHeader(String), along
  with corresponding getRawHeader(String,String) and getUrlDecodedHeader(String,String).
* Renamed realMethod property to effectiveHttpMethod, along with appropriate accessor/mutator.
* Removed Request from Response constructor signature.
* Added FieldNamingPolicy to DefaultJsonProcessor (using LOWER_CASE_WITH_UNDERSCORES).
* getUrlDecodedHeader(String) throws BadRequestException if URL decoding fails.

## Release 0.6.0.2 ##
* Fixed issue with 'connection reset by peer' causing unresponsive behavior.
* Utilized Netty logging behavior to add logging capabilities to RestExpress.
* Made socket-level settings externally configurable:  tcpNoDelay, KeepAlive, reuseAddress,
  soLinger, connectTimeoutMillis, receiveBufferSize.
* Merged in 0.5.6.1 path release changes.
* Added enforcement of some HTTP 1.1 specification rules: Content-Type, Content-Length and body
  content for 1xx, 204, 304 are not allow.  Now throws HttpSpecificationException if spec. is
  not honored.
* Added ability to add 'flags' and 'parameters' to routes, in that, uri().flag("name") on Route
  makes test request.isFlagged("name") return true. Also, uri().parameter("name", "value")
  makes request.getParamater("name") return "value". Not returned/marshaled in the response.
  Useful for setting internal values/flags for preprocessors, controllers, etc.
* Added .useRawResponse() and .useWrappedResponse() to fluent route DSL.  Causes that particular
  route to wrap the response or not, independent of global response wrap settings.
* Parameters parsed from the URL and query string arguments are URL decoded before being placed
  as Request headers.

## Release 0.6.0.1 ##
* Issue #7 - Fixed issue with invalid URL requested where serialization always occurred to the
             default (JSON).  Now serializes to the requested format, if applicable.
* Issue #11 - Feature enhancement for Kickstart.  Now utilizes Rails-inspired configuration
              environements (e.g. dev, testing, prod, etc.).
* Issue #12 - Parse URL parameter names out of the URL pattern and include them in the route
              metadata output.

## Release 0.6.0 ##
* Routes now defined in descendant of RouteDeclaration.
* Refactored everything into RestExpress object, using builder pattern for configuration.
* Implemented RestExpress DSL to declare REST server in main().
* Added supported formats and default format to RouteBuilder.
* Added JSEND-style response wrapping (now default).  Call RestExpress.useWrappedResponses() to use.
* Add ability to support raw response return.  Call RestExpress.useRawResponses() to use.
* Implemented /console/routes.{format} route which return metadata about the routes in this
  service suite. To use, call RestExpress.supportConsoleRoutes().
* Exceptions occurring now return in the requested format with the message wrapped and using the
  appropriate mime type (e.g. application/json or application/xml).
* Kickstart application has complete build with 'dist' target that builds a runnable jar file and
  'run' target that will run the services from the command line.
* Kickstart application now handles JVM shutdown correctly using JVM shutdown hooks.  The method
  RestExpress.awaitShutdown() uses the DefaultShutdownHook class.  RestExpress.shutdown() allows
  programs to use their own shutdown hooks, calling RestExpress.shutdown() upon shudown to release
  all resource.

## Release 0.5.6.1 ##
* Patch release to fix issue with HTTP response status of 204 (No Content) and 304 (Not Modified)
  where they would return a body of an empty string and content length of 2 ('\r\n').  No longer
  serializes for 204 or 304.  Also no longer serializes for null body response unless a JSONP header
  is passed in on the query string.

## Release 0.5.6 ##
* Upgraded to Netty 3.2.3 final.
* Added getProtocol(), getHost(), getPath() to Request
* Functionality of getUrl() is now getPath() and getUrl() now returns the entire URL string,
  including protocol, host and port, and path.

## Release 0.5.5 ##
* Added regex URL matching with RouteMapping.regex(String) method.
* Refactored Route into an abstract class, moving previous functionality into ParameterizedRoute.
* Added KickStart release artifact to get projects going quickly--simply unzip the kickstart file.
* Added SimpleMessageObserver which performs simple timings and outputs to System.out.

## Release 0.5.4 ##
* Added alias() capability to DefaultTxtProcessor to facilitate custom text serialization.
* Updated kickstart application to illustrate latest features.
* Minor refactoring of constants and their locations (moved to RestExpress.java).

## Release 0.5.3 ##
* Fixed issue with JSON date/timestamp parsing.
* Fixed issues with XML date/timestamp parsing.
* Upgraded to GSON 1.6 release.
* Added correlation ID to Request to facilitate timing, etc. in pipeline.
* Added alias(String, Class) to DefaultXmlProcessor.
* By default, alias List and Link in DefaultXmlProcessor.

## Release 0.5.2 ##
* Introduced DateJsonProcessor (sibling to DefaultJsonProcessor) which parses dates vs. time points.
* Refactored ExceptionMapping.getExceptionFor() signature from Exception to Throwable.
* Introduced MessageObserver, which accepts notifications of onReceived(), onSuccess(), onException(), onComplete() to facilitate logging, auditing, timing, etc.
* Changed RouteResolver.resolve() to throw NotFoundException instead of BadRequestException for unresolvable URI.

## Release 0.5.1 ##
* Enhanced support for mark, unreserved and some reserved characters in URL. Specifically, added
  $-+*()~:!' and %.  Still doesn't parse URLs with '.' within the string itself--because of the
  support for .{format} URL section.

## Release 0.5 ##
* Renamed repository from RestX to RestExpress.
* Repackaged everything from com.strategicgains.restx... to com.strategicgains.restexpress...
* Changed DefaultHttpResponseWriter to output resonse headers correctly.
* Updated javadoc on RouteBuilder to provide some documentation on route DSL.

## Release 0.4 ##
* Fixed error in "Connection: keep-alive" processing during normal and error response writing.
* Can now create route mappings for OPTIONS and HEAD http methods.
* Added decoding to URL when Request is constructed.
* Improved pre-processor implementation, including access to resolved route in request.
* Better null handling here and there to avoid NullPointerException, including serialization resolver.
* Improved UT coverage.
* KickStart application builds and is a more accurate template.

## Release 0.3 ##
* Added support for "method tunneling" in POST via query string parameter (e.g. _method=PUT or _method=DELETE)
* Added JSONP support. Use jsonp=<method_name> in query string.
* Utilized Builder pattern in DefaultPipelineFactory, which is now PipelineBuilder.
* Externalized DefaultRequestHandler in PipelineBuilder and now supports pre/post processors (with associated interfaces).
