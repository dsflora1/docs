---

copyright:
  years: 2016, 2017
lastupdated: "2017-03-16"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Web Actions
{: #openwhisk_webactions}

Web actions are OpenWhisk actions annotated to quickly enable you to build web based applications. This allows you to program backend logic which your web application can access anonymously without requiring an OpenWhisk authentication key. It is up to the action developer to implement their own desired authentication and authorization (i.e. OAuth flow).
{: shortdesc}

Web action activations will be associated with the user that created the action. This actions defers the cost of an action activation from the caller to the owner of the action.

Let's take the following JavaScript action `hello.js`,
```javascript
function main({name}) {
  var msg = 'you did not tell me who you are.';
  if (name) {
    msg = `hello ${name}!`
  }
  return {body: `<html><body><h3>${msg}</h3></body></html>`}
}
```
{: codeblock}

You may create a _web action_ `hello` in the package `demo` for the namespace `guest` using the annotation `web-export`:
```
wsk package create demo
```
{: pre}
```
wsk action create /guest/demo/hello hello.js -a web-export true
```
{: pre}

The `web-export` annotation allows the action to be accessible as a web action via a new REST interface. The URL that is structured as follows: `https://openwhisk.ng.bluemix.net/api/v1/web/{QUALIFIED ACTION NAME}.{EXT}`. The fully qualified name of an action consists of three parts: the namespace, the package name, and the action name.

*The fully qualified name of the action must include its package name, which is `default` if the action is not in a named package.*

An example is `guest/demo/hello`. The last part of the URI called the `extension` which is typically `.http` although other values are permitted as described later. The web action API path may be used with `curl` or `wget` without an API key. It may even be entered directly in your browser.

Try opening [https://openwhisk.ng.bluemix.net/api/v1/web/guest/demo/hello.http?name=Jane](https://openwhisk.ng.bluemix.net/api/v1/web/guest/demo/hello.http?name=Jane) in your web browser. Or try invoking the action via `curl`:
```
curl https://openwhisk.ng.bluemix.net/api/v1/web/guest/demo/hello.http?name=Jane
```
{: pre}

Here is an example of a web action that performs an HTTP redirect:
```javascript
function main() {
  return { 
    headers: { location: 'http://openwhisk.org' },
    statusCode: 302
  }
}
```
{: codeblock}  

Or sets a cookie:
```javascript
function main() {
  return { 
    headers: { 
      'Set-Cookie': 'UserID=Jane; Max-Age=3600; Version=',
      'Content-Type': 'text/html'
    }, 
    statusCode: 200,
    body: '<html><body><h3>hello</h3></body></html>' }
}
```
{: codeblock}

Or returns an `image/png`:
```javascript
function main() {
    let png = <base 64 encoded string>
    return { headers: { 'Content-Type': 'image/png' },
             statusCode: 200,
             body: png };
}
```
{: codeblock}

Or returns `application/json`:
```javascript
function main(params) { 
    return {
        statusCode: 200,
        headers: { 'Content-Type': 'application/json' },
        body: new Buffer(JSON.stringify(params)).toString('base64'),
    };
}
```
{: codeblock}

It is important to be aware of the [response size limit](./openwhisk_reference.html) for actions since a response that exceeds the predefined system limits will fail. Large objects should not be sent inline through OpenWhisk, but instead deferred to an object store, for example.

## Handling HTTP requests with actions
{: #openwhisk_webactions_http}

An OpenWhisk action that is not a web action requires both authentication and must respond with a JSON object. In contrast, web actions may be invoked without authentication, and may be used to implement HTTP handlers that respond with _headers_, _statusCode_, and _body_ content of different types. The web action must still return a JSON object, but the OpenWhisk system (namely the `controller`) will treat a web action differently if its result includes one or more of the following as top level JSON properties:

- `headers`: a JSON object where the keys are header-names and the values are string values for those headers (default is no headers).
- `statusCode`: a valid HTTP status code (default is 200 OK).
- `body`: a string which is either plain text or a base64 encoded string (for binary data).

The controller will pass along the action-specified headers, if any, to the HTTP client when terminating the request/response. Similarly the controller will respond with the given status code when present. Lastly, the body is passed along as the body of the response. Unless a `content-type header` is declared in the action result’s `headers`, the body is passed along as is if it’s a string (or results in an error otherwise). When the `content-type` is defined, the controller will determine if the response is binary data or plain text and decode the string using a base64 decoder as needed. Should the body fail to decoded correctly, an error is returned to the caller.

_Note_: A JSON object or array is treated as binary data and must be base64 encoded as shown in the example above.

## HTTP Context

All web actions, when invoked, receives additional HTTP request details as parameters to the action input argument. They are:

- `__ow_method` (type: string). the HTTP method of the request.
- `__ow_headers` (type: map string to string): A the request headers.
- `__ow_path` (type: string): the unmatched path of the request (matching stops after consuming the action extension).
- `__ow_user` (type: string): the namespace identifying the OpenWhisk authenticated subject
- `__ow_body` (type: string): the request body entity, as a base64 encoded string when content is binary, or plain string otherwise
- `__ow_query` (type: string): the query parameters from the request as an unparsed string

A request may not override any of the named `__ow_` parameters above; doing so will result in a failed request with status equal to 400 Bad Request.

The `__ow_user` is only present when the web action is [annotated to require authentication](./openwhisk_annotations.html#openwhisk_annotations_webactions) and allows a web action to implement its own authorization policy. The `__ow_query` is available only when a web action elects to handle the ["raw" HTTP request](#raw-http-handling). It is a string containing the query parameters parsed from the URI (separated by `&`). The `__ow_body` property is present either when handling "raw" HTTP requests, or when the HTTP request entity is not a JSON object or form data. Web actions otherwise receive query and body parameters as first class properties in the action arguments with body parameters taking precedence over query parameters, which in turn take precedence over action and package parameters.

## Additional features

Web actions bring some additional features that include:

- `Content extensions`: the request must specify its desired content type as one of `.json`, `.html`, `.http`, `.svg` or `.text`. This is done by adding an extension to the action name in the URI, so that an action `/guest/demo/hello` is referenced as `/guest/demo/hello.http` for example to receive an HTTP response back. For convenience, the `.http` extension is assumed when no extension is detected.
- `Projecting fields from the result`: the path that follows the action name is used to project out one or more levels of the response. For example, 
`/guest/demo/hello.html/body`. This allows an action which returns a dictionary `{body: "..." }` to project the `body` property and directly return its string value instead. The projected path follows an absolute path model (as in XPath).
- `Query and body parameters as input`: the action receives query parameters as well as parameters in the request body. The precedence order for merging parameters is: package parameters, action parameters, query parameter, body parameters with each of these overriding any previous values in case of overlap . As an example `/guest/demo/hello.http?name=Jane` will pass the argument `{name: "Jane"}` to the action.
- `Form data`: in addition to the standard `application/json`, web actions may receive URL encoded from data `application/x-www-form-urlencoded data` as input.
- `Activation via multiple HTTP verbs`: a web action may be invoked via any of these HTTP methods: `GET`, `POST`, `PUT`, `PATCH`, and `DELETE`, as well as `HEAD` and `OPTIONS`.
- `Non JSON body and raw HTTP entity handling`: A web action may accept an HTTP request body other than a JSON object, and may elect to always receive such values as opaque values (plain text when not binary, or base64 encoded string otherwise).

The example below briefly sketches how you might use these features in a web action. Consider an action `/guest/demo/hello` with the following body:
```javascript
function main(params) { 
    return { response: params };
}
```

When this action is invoked as a web action, you can alter the response of the web action by projecting different paths from the result.
For example, to return the entire object, and see what arguments the action receives:

```
curl https://openwhisk.ng.bluemix.net/api/v1/web/guest/demo/hello.json
```
{: pre}
```json
{
  "response": {
    "__ow_method": "get",
    "__ow_headers": {
      "accept": "*/*",
      "connection": "close",
      "host": "172.17.0.1",
      "user-agent": "curl/7.43.0"
    },
    "__ow_path": ""
  }
}
```

and with a query parameter:
```
curl https://openwhisk.ng.bluemix.net/api/v1/web/guest/demo/hello.json?name=Jane
```
{: pre}
```json
{
  "response": {
    "name": "Jane",
    "__ow_method": "get",
    "__ow_headers": {
      "accept": "*/*",
      "connection": "close",
      "host": "172.17.0.1",
      "user-agent": "curl/7.43.0"
    },
    "__ow_path": ""
  }
}
```

or form data:
```
curl https://openwhisk.ng.bluemix.net/api/v1/web/guest/demo/hello.json -d "name":"Jane"
```
{: pre}
```json
{
  "response": {
    "name": "Jane",
    "__ow_method": "post",
    "__ow_headers": {
      "accept": "*/*",
      "connection": "close",
      "content-length": "10",      
      "content-type": "application/x-www-form-urlencoded",      
      "host": "172.17.0.1",
      "user-agent": "curl/7.43.0"
    },
    "__ow_path": ""
  }
}
```

or JSON object:
```
curl https://openwhisk.ng.bluemix.net/api/v1/web/guest/demo/hello.json -H 'Content-Type: application/json' -d '{"name":"Jane"}'
```
{: pre}
```json
{
  "response": {
    "name": "Jane",
    "__ow_method": "post",
    "__ow_headers": {
      "accept": "*/*",
      "connection": "close",
      "content-length": "15",      
      "content-type": "application/json",
      "host": "172.17.0.1",
      "user-agent": "curl/7.43.0"
    },
    "__ow_path": ""
  }
}
```

and to project just the name (as text):
```
curl https://openwhisk.ng.bluemix.net/api/v1/web/guest/demo/hello.text/response/name?name=Jane
```
{: pre}
```
Jane
```

You see above that for convenience, query parameters, form data, and JSON object body entities are all treated as dictionaries are their values are directly accessible as action input properties. This is not the case for web actions which opt to instead handle HTTP request entities more directly, or when the web action receives an entity that is not a JSON object.

Here is an example of using a "text" content-type with the same example shown above:
```
curl https://openwhisk.ng.bluemix.net/api/v1/web/guest/demo/hello.json -H 'Content-Type: text/plain' -d "Jane"
```
{: pre}
```json
{
  "response": {
    "__ow_method": "post",
    "__ow_headers": {
      "accept": "*/*",
      "connection": "close",
      "content-length": "4",      
      "content-type": "text/plain",
      "host": "172.17.0.1",
      "user-agent": "curl/7.43.0"
    },
    "__ow_path": "",
    "__ow_body": "Jane"
  }
}
```


## Content extensions
{: #openwhisk_webactions_extensions}

A content extension is generally required when invoking a web action; the absence of an extension assumes `.http` as the default. The `.json` and `.http` extensions do not require a projection path. The `.html`, `.svg` and `.text` extensions do, however for convenience, the default path is assumed to match the extension name. So to invoke a web action and receive an `.html` response, the action must respond with a JSON object that contains a top level property called `html` (or the response must be in the explicitly given path). In other words, `/guest/demo/hello.html` is equivalent to projecting the `html` property explicitly, as in `/guest/demo/hello.html/html`. The fully qualified name of the action must include its package name, which is `default` if the action is not in a named package.


## Protected parameters
{: #openwhisk_webactions_protected}

Action parameters may also be protected and treated as immutable. To finalize parameters, and to make an action web accessible, two [annotations](openwhisk_annotations.html) must be attached to the action: `final` and `web-export` either of which must be set to `true` to have affect. Revisiting the action deployment earlier, we add the annotations as follows:

```
wsk action create /guest/demo/hello hello.js \
      --parameter name Jane \
      --annotation final true \
      --annotation web-export true
```
{: pre}

The result of these changes is that the `name` is bound to `Jane` and may not be overridden by query or body parameters because of the final annotation. This secures the action against query or body parameters that try to change this value whether by accident or intentionally. 

## Disabling web actions
{: #openwhisk_webactions_disable}

To disable a web action from being invoked via the new API (`https://`openwhisk.<span class="keyword" data-hd-keyref="DomainName">DomainName</span>`/api/v1/web/`), it’s enough to remove the annotation or set it to `false`.

```
wsk action update /guest/demo/hello hello.js \
      --annotation web-export false
```

## Raw HTTP handling
{: #raw-http-handling}

A web action may elect to interpret and process an incoming HTTP body directly, without the promotion of a JSON object to first class properties available to the action input (e.g., `args.name` vs parsing `args.__ow_query`). This is done via a `raw-http` [annotations](openwhisk_annotations.html). Using the same example show earlier, but now as a "raw" HTTP web action receiving `name` both as a query parameters and as JSON value in the HTTP request body:
```
curl https://openwhisk.ng.bluemix.net/api/v1/web/guest/demo/hello.json?name=Jane -X POST -H "Content-Type: application/json" -d '{"name":"Jane"}'
```
{: pre}
```json 
{
  "response": {
    "__ow_method": "post",
    "__ow_query": "name=Jane",
    "__ow_body": "eyJuYW1lIjoiSmFuZSJ9",
    "__ow_headers": {
      "accept": "*/*",
      "connection": "close",
      "content-length": "15",
      "content-type": "application/json",
      "host": "172.17.0.1",
      "user-agent": "curl/7.43.0"      
    },
    "__ow_path": ""
  }
}
```

Notice in this case the JSON content is base64 encoded because it is treated as a binary value. The action must base64 decode and JSON parse this value to recover the JSON object. OpenWhisk uses the [Spray](https://github.com/spray/spray) framework to [determine](https://github.com/spray/spray/blob/master/spray-http/src/main/scala/spray/http/MediaType.scala#L282) which content types are binary and which are plain text.


### Enabling raw HTTP handling

Raw HTTP web actions are enabled via the annotation `raw-http` [annotations](openwhisk_annotations.html) with a value of `true`.

```
wsk action create /guest/demo/hello hello.js \
      --annotation web-export true
      --annotation raw-http true
```
{: pre}

**Note:** Since `raw-http` implies `web-export`, we plan to improve the CLI to provide a more convenient way to add (and remove) these annotations in the future.


### Disabling raw HTTP handling

Disabling raw HTTP is accomplished by setting the `raw-http` [annotations](openwhisk_annotations.html) value to `false`.

```
wsk update create /guest/demo/hello hello.js \
      --annotation web-export true
      --annotation raw-http false
```
{: pre}

**Note:**  All annotations for a single action must be set at the same time, either when creating or updating the action. This is due to a current limitation on the API and CLI. Failure to do so will result is removal of any previously attached annotations.


## Error Handling
{: #openwhisk_webactions_errors}

When an OpenWhisk action fails, there are two different failure modes. The first is known as an _application error_ and is analogous to a caught exception: the action returns a JSON object containing a top level `error` property. The second is a _developer error_ which occurs when the action fails catastrophically and does not produce a response (this is similar to an uncaught exception). For web actions, the controller handles application errors as follows:

- Any specified path projection is ignored and the controller projects the `error` property instead.
- The controller applies the content handling implied by the action extension to the value of the `error` property.

Developers should be aware of how web actions might be used and generate error responses accordingly. For example, a web action that is used with the `.http` extension
should return an HTTP response, for example: `{error: { statusCode: 400 }`. Failing to do so will in a mismatch between the implied content-type from the extension and the action content-type in the error response. Special consideration must be given to web actions that are sequences, so that components that make up a sequence can generate adequate errors when necessary.

