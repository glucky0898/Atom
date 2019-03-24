initializationClass - To enable the creation of default expectations a class can be specified to initialize expectations in MockServer, this class must implement org.mockserver.client.initialize.ExpectationInitializer interface. The initializeExpectations(MockServerClient mockServerClient) method will be called once MockServer has been started. Note: that the plugin must be started during the process-test-classes to ensure that the initialization class has been compiled from either src/main/java or src/test/java locations. In addition the initializer can only be used with start and run goals, it will not work with the runForked goal as a JVM is forked with a separate classpath. (required: false, default: false)

The when running from the command line, Maven plugin, npm module or Grunt plugin the log is written to a file called mockserver.log in the current working directory where the MockServer is running

•	org.mockserver.client.MockServerClient - makes HTTP requests to a remote MockServer instance
•	org.mockserver.integration.ClientAndServer - starts a local MockServer instance and makes HTTP requests to it
```java
public void startMockServer() {
        mockServer = startClientAndServer(1080);
    }
    public void stopMockServer() {
        mockServer.stop();
    }
new ClientAndServer(1080)
    .when(
        request()
            .withMethod("GET")
            .withPath("/view/cart")
            .withCookies(
                cookie("session", "4930456C-C718-476F-971F-CB8E047AB349")
            )
            .withQueryStringParameters(
                param("cartId", "055CA455-1DF7-45BB-8535-4F83E7266092")
            )
    )
    .respond(
        response()
            .withBody("some_response_body")
    );

new MockServerClient("localhost", 1080)
    .when(
        request()
            .withMethod("GET")
            .withPath("/view/cart")
            .withCookies(
                cookie("session", "4930456C-C718-476F-971F-CB8E047AB349")
            )
            .withQueryStringParameters(
                param("cartId", "055CA455-1DF7-45BB-8535-4F83E7266092")
            )
    )
    .respond(
        response()
            .withBody("some_response_body")
    );
```
To ensure expectations are available as soon as MockServer is started it is possible to use an expectation initializer, there are three options:

initializer class loaded from the classpath to construct an array of expectations
initializer JSON file loaded from the filesystem containing a serialised array of expectations
maven plugin loaded from the classpath to add expectations using the MockServerClient
Note: all three options require the class or file to be available to the MockServer, i.e. in the local classpath or filesystem. To remotely initialise the MockServer a client is required to connect to the MockServer after it has started and submit one or more expectations
```java
class:
ConfigurationProperties.initializationClass(String initializationClass)
public class ExpectationInitializerExample implements ExpectationInitializer {
    @Override
    public Expectation[] initializeExpectations() {
        return new Expectation[]{
            new Expectation(
                request()
                    .withPath("/simpleFirst")
            )
                .thenRespond(
                response()
                    .withBody("some first response")
            ),
            new Expectation(
                request()
                    .withPath("/simpleSecond")
            )
                .thenRespond(
                response()
                    .withBody("some second response")
            )
        };
    }
}
jsonfile
ConfigurationProperties.initializationJsonPath(String initializationJsonPath)
java -Dmockserver.initializationJsonPath="org/mockserver/server/initialize/initializerJson.json" -jar ~/Downloads/mockserver-netty-5.5.1-jar-with-dependencies.jar -serverPort 1080 -logLevel INFO

// jsonfile
```json
"httpRequest": {
        "path": "/ws/rest/user/[0-9]+/icon/[0-9]+\\.png"
    },
    "httpResponse": {
        "statusCode": 200,
        //binary PNG body
        "headers": {
            "content-type": ["image/png"],
            "content-disposition": ["form-data; name=\"test.png\"; filename=\"test.png\""]
        },
        "body": {
            "type": "BINARY",
            "base64Bytes":""
        },
        //UTF16 body
        //"headers": {
        //    "content-type": ["text/plain; charset=utf-16"]
       // },
       //10 second delay
        "delay": {
            "timeUnit": "SECONDS",
            "value": 10
        },
        "connectionOptions" : {
            //  suppress headers
            "suppressContentLengthHeader" : true,
            "suppressConnectionHeader" : true,
            "closeSocket" : true
        }
        
    },
    "times": {
        "remainingTimes": 1,
        "unlimited": false
    },
    //class callback
    "httpResponseClassCallback": {
        "callbackClass": "org.mockserver.examples.mockserver.CallbackActionExamples$TestExpectationResponseCallback"
    }
    //forward
    "httpForward": {
        "host": "mock-server.com",
        "port": 80,
        "scheme": "HTTP"
    },
    //error action
    "httpError": {
        "dropConnection": true,
        "responseBytes": "eQqmdjEEoaXnCvcK6lOAIZeU+Pn+womxmg=="
    }
```

### Retrieving Recorded Requests

### Retrieving Recorded Expectations
```java
String recordedExpectations = new MockServerClient("localhost", 1080)
    .retrieveRecordedExpectations(
        request()
            .withPath("/some/path")
            .withMethod("POST"),
        Format.JSON
    );
```

配置文件：
```java
//log日志
 public static Level logLevel() {
        return Level.valueOf(readPropertyHierarchically("mockserver.logLevel", "INFO"));
    }

    public static void logLevel(String level) {
        if (!Arrays.asList("TRACE", "DEBUG", "INFO", "WARN", "ERROR", "OFF").contains(level)) {
            throw new IllegalArgumentException("log level \"" + level + "\" is not legal it must be one of \"TRACE\", \"DEBUG\", \"INFO\", \"WARN\", \"ERROR\", \"OFF\"");
        } else {
            System.setProperty("mockserver.logLevel", level);
        }
    }
```
// 处理方法
```java
/**
     * Clear expectations, logs or both that match the http
     *
     * @param httpRequest the http request that is matched against when deciding whether to clear each expectation if null all expectations are cleared
     * @param type        the type to clear, EXPECTATION, LOG or BOTH
     */
    public MockServerClient clear(HttpRequest httpRequest, ClearType type) {
        sendRequest(request().withMethod("PUT").withPath(calculatePath("clear")).withQueryStringParameter("type", type.name().toLowerCase()).withBody(httpRequest != null ? httpRequestSerializer.serialize(httpRequest) : "", StandardCharsets.UTF_8));
        return clientClass.cast(this);
    }
```