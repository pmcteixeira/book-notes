# HTTP: The Definitive Guide

by David Gourley and Brian Totty.

_These book notes were taken to further my own learning and for quick reference. If you enjoy these notes, please [purchase the book](http://www.amazon.com/HTTP-Definitive-Guide-Guides/dp/1565925092)!_

## Part 1 Mind Map (Chapters 1-4)

![Part 1 Mind Map (Chapters 1-4)](assets/part_1.png)

## Chapter 1: Overview of HTTP

_HTTP_ is the common language between _web browsers_, _web servers_ and related _web applications_. HTTP moves data reliably from web servers to web browsers, without worry about the flaws of internet data transmission.

Web servers host web content, also referred as _web resources_. Web servers provide web content upon request. The client sends a HTTP request and servers retrieve a HTTP response.

The simplest kind of web resources is static files stored in the web server's filesystem. They can be simple text files, _HTML_ files, _JPEG_ image files, _AVI_ movie files, or any other file. **Resources can also be software programs that generate content on demand**.

HTTP tags each data type being transferred with a label called _MIME type_. A MIME type is a text label, represented by a primary object type and a specific subtype, separated by a slash. There are hundreds of popular MIME types, here are a few:

Data Type       | MIME label
:-------------- | :--------------
HTML            | text/html
ASCII text      | text/plain
JPEG            | image/jpeg
GIF             | image/gif
QuickTime movie | video/quicktime

### URI, URL and URN

A resource is uniquely identified and located by a _URI_ (_uniform resource identifier_). Given the URI, HTTP can retrieve the correspondent resource. URI come in two flavors, called URLs and URNs.

The **uniform resource locator** (URL) is the most common form or URI. URL describes the **specific location** of a resource on a particular server. Most URL follow a standardized format in three main parts:

- _scheme_ (describes the protocol used)
- _server address_
- _resource_ name.

1\. Use HTTP protocol | 2\. Go to www.joes-hardware.com | 3\. Grab the resource called /specials/saw-blade.gif
:-------------------- | :------------------------------ | ----------------------------------------------------
http://               | www.joes-hardware.com           | /specials/saw-blade.gif

The second flavor is _uniform resource name_ or URN. A URN is a unique name to identify a resource, independently of where the resource is located. URN allows the resource to move from place to place, and being accessed by multiple protocols while maintaining the same name. The "RFC 2141" URN is:

> urn:ietf:rfc:2141

URNs are still experimental and not yet widely adopted. To work effectively, URNs need a supporting infrastructure to resolve resource locations.

### Transactions

HTTP messages between client and server are simple, line-oriented sequences of characters. HTTP messages sent from clients to servers are called **request messages**. Messages from servers to clients are called **response messages**. _There are no other kinds of HTTP messages_. An **HTTP transaction** consists of a request message and a response message.

HTTP supports several different request commands, called **HTTP methods**. Every HTTP request message has a method. The method tells the server what action to perform.

HTTP method | Description
:---------- | :-------------------------------------------------------------------
GET         | Send named resource from the server to the client.
PUT         | Store data from client into a named server resource.
DELETE      | Delete the named resource from a server.
POST        | Send client data into a server gateway application.
HEAD        | Send just the HTTP headers from the response for the named resource.

Every HTTP response message comes back with a status code. The status code is a numeric code that tells the client if the request succeeded, or if other actions are required. Some common HTTP status codes:

HTTP status code | Description
:--------------- | :-----------------------------------------------
200              | Ok. Document returned correctly.
302              | Redirect. Go someplace else to get the resource.
404              | Not found. Can't find this resource.

Request message example:

```
GET /test/hi-there.txt HTTP/1.0
Accept: text/*
Accept-Language: en,fr
```

Response message example:

```
HTTP/1.0 200 OK
Content-type: text/plain
Content-length: 19

Hi! I’m a message!
```

HTTP messages consist of three parts:

- **Start line** The first line of the message is the start line, indicating what to do for a request or what happened for a response.
- **Header fields** Zero or more header fields follow the start line. Each header field consists of a name and a value, separated by a colon (:) for easy parsing. The headers end with a blank line. Adding a header field is as easy as adding another line.
- **Body** After the blank line is an optional message body containing any kind of data. Request bodies carry data to the web server; response bodies carry data back to the client. Unlike the start lines and headers, which are textual and structured, the body can contain arbitrary binary data (e.g., images, videos, audio tracks, software applications). Of course, the body can also contain text.

### TCP/IP

HTTP is an application layer protocol. HTTP doesn't worry about the nitty-gritty details of network communication; instead, it leaves the details of networking to TCP/IP, the popular reliable Internet transport protocol. Once a TCP connection is established, messages exchanged between the client and server computers will never be lost, damaged, or received out of order. In networking terms, the HTTP protocol is layered over TCP. HTTP uses TCP to transport its message data. Likewise, TCP is layered over IP.

Before an HTTP client can send a message to a server, it needs to establish a TCP/IP connection between the client and server using Internet protocol (IP) addresses and port numbers. In TCP, you need the IP address of the server computer and the TCP port number associated with the specific software program running on the server. Hostnames can easily be converted into IP addresses through a facility called the Domain Name Service (DNS). When the port number is missing from an HTTP URL, you can assume the default value of port 80.

### Architectural Components of the Web

There are many other web applications that you interact with on the Internet, besides web browsers and web servers. Here is an outline:

- **Proxies** HTTP intermediaries that sit between clients and servers, receiving all of the client's HTTP requests and relaying the requests to the server (perhaps after modifying the requests). Proxy accesses the server on the user's behalf.
- **Caches** A special type of HTTP proxy server that keeps copies of popular documents that pass through the proxy. The next client requesting the same document can be served from the cache's personal copy.
- **Gateways** Special web servers that connect to other applications. They are often used to convert HTTP traffic to another protocol. For example, an HTTP/FTP gateway receives requests for FTP URIs via HTTP requests but fetches the documents using the FTP protocol. The resulting document is packed into an HTTP message and sent to the client.
- **Tunnels** Tunnels are HTTP applications that, after setup, blindly relay raw data between two connections. HTTP tunnels are often used to transport non-HTTP data over one or more HTTP connections, without looking at the data. One popular use of HTTP tunnels is to carry encrypted Secure Sockets Layer (SSL) traffic through an HTTP connection, allowing SSL traffic through corporate firewalls that permit only web traffic.
- **Agents** User agents (or just agents) are client programs that make HTTP requests on the user's behalf. Any application that issues web requests is an HTTP agent.

## Chapter 2: URLs and Resources

URLs provide a means of locating any resource on the Internet, but these resources can be accessed by different schemes (e.g., HTTP, FTP, SMTP), and URL syntax varies from scheme to scheme. Most URLs adhere to a general URL syntax, and there is significant overlap in the style and syntax between different URL schemes.

Most URL schemes base their URL syntax on this nine-part general format:

```
<scheme>://<user>:<password>@<host>:<port>/<path>;<params>?<query>#<frag>
```

Almost no URLs contain all these components. The three most important parts of a URL are the scheme, the host, and the path.

Component | Description
:-------- | :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
scheme    | Which protocol to use when accessing a server to get a resource.
user      | The username some schemes require to access a resource.
password  | The password that may be included after the username, separated by a colon (:).
host      | The hostname or dotted IP address of the server hosting the resource.
port      | The port number on which the server hosting the resource is listening. Many schemes have default port numbers (the default port number for HTTP is 80).
path      | The local name for the resource on the server, separated from the previous URL components by a slash (/). The syntax of the path component is server- and scheme-specific. The path component for HTTP URLs can be divided into path segments separated by "/" characters. Each path segment can have its own params component.
params    | Used by some schemes to specify input parameters. Params are name/value pairs. A URL can contain multiple params fields, separated from themselves and the rest of the path by semicolons (;). Example of param `type=d` used in URL: `ftp://prep.ai.mit.edu/pub/gnu;type=d`. Also, each segment can have its own params. For example: `<http://www.joes-hardware.com/hammers;sale=false/index.html;graphics=true>`
query     | Used by some schemes to pass parameters to active applications (such as databases, search engines, and other Internet gateways). There is no common format for the contents of the query component. It is separated from the rest of the URL by the "?" character. E.g: `http://www.joes-hardware.com/inventory-check.cgi?item=12731`. By convention, query strings are expected to be formatted as a series of "name=value" pairs, separated by "&" characters: `http://www.joes-hardware.com/inventory-check.cgi?item=12731&color=blue`.
frag      | A name for a piece or part of the resource. The frag field is not passed to the server when referencing the object; it is used internally by the client. It is separated from the rest of the URL by the "#" character.

### URL shortcuts

URLs come in two flavors: _absolute_ and _relative_. With an absolute URL, you have all the information you need to access a resource. On the other hand, relative URLs are incomplete. To get all the information needed to access a resource from a relative URL, you must interpret it relative to another URL, called its base.

An example HTML document with an embedded relative URL:

```
<HTML>
<HEAD><TITLE>Joe's Tools</TITLE></HEAD>
<BODY>
<H1> Tools Page </H1>
<H2> Hammers <H2>
<P> Joe's Hardware Online has the largest selection of <A HREF="./hammers.html">hammers
</A> on earth.
</BODY>
</HTML>
```

In the HTML document, there is a hyperlink containing the URL `./hammers.html`. This URL is a legal relative URL. The absolute URL can be resolved using the base URL of the resource they are in. It is also worth noting that relative URLs provide a convenient way to keep a set of resources (such as HTML pages) portable. If you use relative URLs, you can move a set of documents around and still have their links work, because they will be interpreted relative to the new base. This allows for things like mirroring content on other servers.

### Shady Characters

URLs are permitted to contain only characters from a relatively small, universally safe alphabet. An escape mechanism was added, allowing unsafe characters to be encoded into safe characters for transport. The encoding simply represents the unsafe character by an "escape" notation, consisting of a percent sign (%) followed by two hexadecimal digits that represent the ASCII code of the character.

Applications need to walk a fine line. It is best for client applications to convert any unsafe or restricted characters before sending any URL to any other application. Once all the unsafe characters have been encoded, the URL is in a **_canonical form_** that can be shared between applications;

Character | ASCII code | Example URL
:-------- | :--------- | ------------------------------------------------
~         | 126 (0x7E) | <http://www.joes-hardware.com/%7Ejoe>
SPACE     | (0x20)     | <http://www.joes-hardware.com/more%20tools.html>

### Most popular schemes

Scheme | Description
:----- | :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
http   | Basic form: `http://<host>:<port>/<path>?<query>#<frag>`
https  | The https scheme is a twin to the http scheme. The only difference is that the https scheme uses Secure Sockets Layer (SSL), which provides end-to-end encryption of HTTP connections. Basic form: `https://<host>:<port>/<path>?<query>#<frag>`
mailto | Mailto URLs refer to email addresses. Basic form: `mailto:<RFC-822-addr-spec>`
ftp    | File Transfer Protocol URLs. Basic forms: `ftp://<user>:<password>@<host>:<port>/<path>;<params>`
file   | The file scheme denotes files directly accessible on a given host machine (by local disk, a network filesystem). Basic form: `file://<host>/<path>`

## Chapter 3: HTTP Messages

HTTP messages are the blocks of data sent between HTTP applications. These blocks of data begin with some text meta-information describing the message con- tents and meaning, followed by optional data. These messages flow between clients, servers, and proxies. The terms **inbound**, **outbound**, **upstream**, and **downstream** describe message direction.

HTTP uses the terms inbound and outbound to describe transactional direction. Messages travel inbound to the origin server, and when their work is done, they travel outbound back to the user agent.

All messages flow downstream, regardless of whether they are request messages or response messages. The sender of any message is upstream of the receiver.

HTTP messages consist of three parts: a start line describing the message, a block of headers containing attributes, and an optional body containing data. The start line and headers are just ASCII text, broken up by lines. The entity body or message body (or just plain "body") is simply an optional chunk of data. Unlike the start line and headers, the body can contain text or binary data or can be empty.

### Message Syntax

All HTTP messages fall into two types: **request messages** and **response messages**. Request messages request an action from a web server. Response messages carry results of a request back to a client. Here's the format for a request message:

```
<method> <request-URL> <version>
<headers>
<entity-body>
```

Here's the format for a response message (note that the syntax differs only in the start line):

```
<version> <status> <reason-phrase>
<headers>
<entity-body>
```

Name          | Description
:------------ | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
method        | The action that the client wants the server to perform on the resource. It is a single word, like "GET," "HEAD," or "POST".
request-URL   | A complete URL naming the requested resource, or the path component of the URL. If you are talking directly to the server, the path component of the URL is usually okay as long as it is the absolute path to the resource, the server can assume itself as the host/port of the URL.
version       | The version of HTTP that the message is using.
status-code   | A three-digit number describing what happened during the request.
reason-phrase | A human-readable version of the numeric status code.
headers       | Zero or more headers, each of which is a name, followed by a colon (:), followed by optional whitespace, followed by a value, followed by a CRLF. The headers are terminated by a blank line (CRLF), marking the end of the list of headers and the beginning of the entity body.
entity-body   | The entity body contains a block of arbitrary data. Not all messages contain entity bodies.

### Methods

The method begins the start line of requests, telling the server what to do.

Methods | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Has message body
:------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------
GET     | GET is the most common method. It usually is used to ask a server to send a resource.                                                                                                                                                                                                                                                                                                                                                                          | NO
HEAD    | The HEAD method behaves exactly like the GET method, but the server returns only the headers in the response.                                                                                                                                                                                                                                                                                                                                                  | NO
POST    | The POST method was designed to send input data to the server.* In practice, it is often used to support HTML forms. The data from a filled-in form typically is sent to the server, which then marshals it off to where it needs to go (e.g., to a server gateway program, which then processes it). POST is used to send data to a server. PUT is used to deposit data into a resource on the server (e.g., a file).                                         | YES
PUT     | The PUT method writes documents to a server, in the inverse of the way that GET reads documents from a server. The semantics of the PUT method are for the server to take the body of the request and either use it to create a new document named by the requested URL or, if that URL already exists, use the body to replace it.                                                                                                                            | YES
TRACE   | When a client makes a request, that request may have to travel through firewalls, proxies, gateways, or other applications. Each of these has the opportunity to modify the original HTTP request. The TRACE method allows clients to see how its request looks when it finally makes it to the server. The TRACE method is used primarily for diagnostics. It's also a good tool for see- ing the effects of proxies and other applications on your requests. | NO
OPTIONS | The OPTIONS method asks the server to tell us about the various supported capabilities of the web server.                                                                                                                                                                                                                                                                                                                                                      | NO
DELETE  | The DELETE method does just what you would think - it asks the server to delete the resources specified by the request URL.                                                                                                                                                                                                                                                                                                                                    | NO

Not all servers implement all seven of the methods. Furthermore, because HTTP was designed to be easily extensible, other servers may implement their own request methods in addition to these. These additional methods are called **_extension methods_**.

Note that not all methods are implemented by every server. To be compliant with HTTP Version 1.1, a server need implement only the GET and HEAD methods for its resources.

### Status codes

As methods tell the server what to do, status codes tell the client what happened.

Overall range | Defined range | Category
:------------ | :------------ | -------------
100-199       | 100-101       | Informational
200-299       | 200-206       | Successful
300-399       | 300-305       | Redirection
400-499       | 400-415       | Client error
500-599       | 500-505       | Server error

### Headers

HTTP header fields add additional information to request and response messages. They are basically just lists of name/value pairs. The HTTP specification defines several header fields. Applications also are free to invent their own home-brewed headers. HTTP headers are classified into:

- **General headers** can appear in both request and response messages. For example, the Date header is a general-purpose header that allows both sides to indicate the time and date at which the message was constructed: `Date: Tue, 3 Oct 1974 02:16:00 GMT`.
- **Request headers** provide more information about the request. For example, the following Accept header tells the server that the cli- ent will accept any media type that matches its request: `Accept: */*`.
- **Response headers** Provide more information about the response. For example, the follow- ing Server header tells the client that it is talking to a Version 1.0 Tiki-Hut server: `Server: Tiki-Hut/1.0`.
- **Entity headers** Describe body size and contents, or the resource itself. For example, the following Content-Type header lets the application know that the data is an HTML document in the iso-latin-1 character set: `Content-Type: text/html; charset=iso-latin-1`.
- **Extension headers** New headers that are not defined in the specification.

Each HTTP header has a simple syntax: a name, followed by a colon (:), followed by optional whitespace, followed by the field value, followed by a CRLF. Long header lines can be made more readable by breaking them into multiple lines, preceding each extra line with at least one space or tab character. For example:

```
HTTP/1.0 200 OK
Content-Type: image/gif
Content-Length: 8572
Server: Test Server
    Version 1.0
```

In this example, the response message contains a Server header whose value is bro- ken into continuation lines. The complete value of the header is "Test Server Version 1.0".

### Entity Bodies

The third part of an HTTP message is the optional entity body. Entity bodies are the payload of HTTP messages. They are the things that HTTP was designed to transport. HTTP messages can carry many kinds of digital data: images, video, HTML documents, software applications, credit card transactions, electronic mail, and so on.

## Chapter 4: Connection Management

HTTP connections are mainly TCP connections, plus a few rules about how to use them. TCP gives HTTP a reliable bit pipe. Bytes stuffed in one side of a TCP connection come out the other side correctly, and in the right order. A secure variant, HTTPS, inserts a cryptographic encryption layer (called TLS or SSL) between HTTP and TCP.

When given a URL, a browser performs the following steps:

1. The browser extracts the hostname.
2. The browser looks up the IP address for this hostname (DNS).
3. The browser gets the port number (80).
4. The browser makes a TCP connection to IP address port 80.
5. The browser sends an HTTP GET request message to the server.
6. The browser reads the HTTP response message from the server.
7. The browser closes the connection.

TCP sends its data in little chunks called IP packets (or IP datagrams). When HTTP wants to transmit a message, it streams the contents of the message data, in order, through an open TCP connection. TCP takes the stream of data, chops up the data stream into chunks called segments, and transports the segments across the Internet inside envelopes called IP packets. This is all handled by the TCP/IP software; **_the HTTP programmer sees none of it_**. Each TCP segment is carried by an IP packet from one IP address to another IP address.

A computer might have several TCP connections open at any one time. TCP keeps all these connections straight through port numbers. A TCP connection is distinguished by four values:

```
<source-IP-address, source-port, destination-IP-address, destination-port>
```

Together, these four values **_uniquely define_** a connection.

### TCP Sockets

Operating systems provide different facilities for manipulating their TCP connections. **Sockets API**, the TCP programming interface firstly developed for the Unix operating system, hides all the details of TCP and IP from the HTTP programmer.

The sockets API lets you create TCP endpoint data structures, connect these end- points to remote server TCP endpoints, and read and write data streams. The TCP API hides all the details of the underlying network protocol handshaking and the segmentation and reassembly of the TCP data stream to and from IP packets.

Establishing a connection can take a while, depending on how far away the server is, the load on the server, and the congestion of the Internet. Once the connection is set up, the client sends the HTTP request and the server reads it. Once the server gets the entire request message, it processes the request, performs the requested action, and writes the data back to the client.

Because HTTP is layered directly on TCP, the performance of HTTP transactions depends critically on the performance of the underlying TCP plumbing.

### TCP Performance Considerations

Delay                                      | Description
:----------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
TCP Connection Handshake                   | When you set up a new TCP connection, even before you send any data, the TCP software exchanges a series of IP packets to negotiate the terms of the connection.
Delayed Acknowledgments                    | Internet itself does not guarantee reliable packet delivery. TCP implements its own acknowledgment scheme to guarantee successful data delivery.
TCP Slow Start                             | TCP connections "tune" themselves over time, initially limiting the maximum speed of the connection and increasing the speed over time as data is transmitted successfully.
Nagle's Algorithm                          | TCP sends large numbers of packets containing small amounts of data. Nagle's algorithm attempts to bundle up a large amount of TCP data before sending a packet, aiding network efficiency, but also causes several performance problems. E.g: messages, that don't fill a packet, may be delayed waiting for additional data that will never arrive.
TIME_WAIT Accumulation and Port Exhaustion | When a TCP endpoint closes a TCP connection, it maintains in memory a small control block recording the IP addresses and port numbers of the recently closed connection. This information is maintained for a short time. In practice, this algorithm prevents two connections with the exact same IP addresses and port numbers from being created, closed, and recreated within two minutes. Each time the client connects to the server, it gets a new source port in order to have a unique connection. But because a limited number of source ports are available (say, 60,000) and no connection can be reused for short period of time (say, 120 seconds), this limits the connect rate.
Serial Transaction Delays                  | If each transaction requires a new connection, the connection and slow-start delays can add up.

### HTTP connection performance improvement techniques

Name                    | Description
:---------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Parallel connections    | HTTP allows clients to open multiple connections and perform multiple HTTP transactions in parallel. Even though parallel connections may be faster, however, they are not always faster, as this can cause significant server slowdown.
Persistent connections  | By reusing an idle, persistent connection that is already open to the target server, you can avoid the slow connection setup. In addition, the already open connection can avoid the slow-start congestion adaptation phase, allowing faster data transfers delays.
Pipelined connections   | Concurrent HTTP requests across a shared TCP connection. While the first request is streaming across the network to a server, a second and third requests can get underway. This can improve performance in high-latency network conditions, by reducing network round trips.
Multiplexed connections | Interleaving chunks of requests and responses (experimental)

### Connection close

Any HTTP client, server, or proxy can close a TCP transport connection at any time.

TCP connections are bidirectional. Each side of a TCP connection has an input queue and an output queue, for data being read or written. Data placed in the output of one side will eventually show up on the input of the other side.

An application can close either or both of the TCP input and output channels. A close( ) sockets call closes both the input and output channels of a TCP connection. This is called a **full close**.
