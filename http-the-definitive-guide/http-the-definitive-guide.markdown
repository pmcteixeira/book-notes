# HTTP: The Definitive Guide

by David Gourley and Brian Totty.

_These book notes were taken to further my own learning and for quick reference. If you enjoy these notes, please [purchase the book](http://www.amazon.com/HTTP-Definitive-Guide-Guides/dp/1565925092)!_

## Mind Maps

### Chapters 1 - 4

![Part 1 Mind Map (Chapters 1-4)](assets/part_1.png)

### Chapter 5

![Chapter 5](assets/chapter_5.png)

### Chapter 6

![Chapter 6](assets/chapter_6.png)

### Chapter 7

![Chapter 7](assets/chapter_7.png)

### Chapter 11

![Chapter 6](assets/chapter_11.png)

## Chapter 1: Overview of HTTP

_HTTP_ is the common language between _web browsers_, _web servers_ and related _web applications_.

Web servers host web content, also referred as _web resources_. The simplest kind of web resources is static files stored in the web server's filesystem. They can be simple text files, HTML files, JPEG image files, AVI movie files, or any other file. _Resources can also be software programs that generate content on demand_.

HTTP moves web content reliably from web servers to web browsers, without worry about the flaws of internet data transmission. Web servers provide web content upon request. The client sends a **_HTTP request_** and servers retrieve a **_HTTP response_**.

HTTP tags each data type being transferred with a label called _MIME type_. A MIME type is a text label, represented by a primary object type and a specific subtype, separated by a slash. There are hundreds of popular MIME types, here are a few:

Data Type       | MIME label
:-------------- | :--------------
HTML            | text/html
ASCII text      | text/plain
JPEG            | image/jpeg
GIF             | image/gif
QuickTime movie | video/quicktime

### URI, URL and URN

A web resource is uniquely identified and located by a _URI_ (_uniform resource identifier_). Given the URI, HTTP can retrieve the correspondent resource. URI come in two flavors, called URLs and URNs.

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

HTTP is the application layer protocol. HTTP doesn't worry about the nitty-gritty details of network communication; instead, it leaves the details of networking to TCP/IP, the popular reliable Internet transport protocol. Once a TCP connection is established, messages exchanged between the client and server computers will never be lost, damaged, or received out of order. In networking terms, the HTTP protocol is layered over TCP. HTTP uses TCP to transport its message data. Likewise, TCP is layered over IP.

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

The sockets API lets you create TCP endpoint data structures, connect these endpoints to remote server TCP endpoints, and read and write data streams. The TCP API hides all the details of the underlying network protocol handshaking and the segmentation and reassembly of the TCP data stream to and from IP packets.

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

## Chapter 5: Web Servers

The term "web server" can refer either to web server software or to the particular device or computer dedicated to serving the web pages. The web server logic implements the HTTP protocol, manages web resources, and provides web server administrative capabilities. The web server logic shares responsibilities for managing TCP connections with the operating system.

The underlying operating system manages the hardware details of the underlying computer system and provides TCP/IP network support, filesystems to hold web resources, and process management to control current computing activities. Web servers are available in many forms:

- **General-Purpose Software Web Servers** General-purpose software web servers run on standard, network-enabled computer systems. You can choose open source software or commercial software Web server software is available for just about every computer and operating system.
- **Web Server Appliances** Web server appliances are prepackaged software/hardware solutions.
- **Embedded Web Servers** Embedded servers are tiny web servers intended to be embedded into consumer products (e.g., printers or home appliances). Embedded web servers allow users to administer their consumer devices using a convenient web browser interface.

### What Real Web Servers Do

#### 1\. Accept Client Connections.

When a client requests a TCP connection to the web server, the web server establishes the connection and determines which client is on the other side of the connection, extracting the IP address from the TCP connection. Once a new connection is established and accepted, the server adds the new connection to its list of existing web server connections and prepares to watch for data on the connection. The web server is free to reject and immediately close any connection.

#### 2\. Receiving Request Messages.

As the data arrives on connections, the web server reads out the data from the network connection and parses out the pieces of the request message. Some web servers also store the request messages in internal data structures that make the message easy to manipulate. Web servers constantly watch for new web requests, because requests can arrive at any time. Different web server architectures to service requests: `Single-threaded web servers`, `Multiprocess and multithreaded web servers`, `Multiplexed I/O servers`, `Multiplexed multithreaded web servers`.

#### 3\. Processing Requests.

Once the web server has received a request, it can process the request using the method, resource, headers, and optional body.

#### 4\. Mapping and Accessing Resources.

Web servers are resource servers. They deliver pre-created content, such as HTML pages or JPEG images, as well as dynamic content from resource-generating applications running on the servers. Web servers support different kinds of resource mapping, but the simplest form of resource mapping uses the request URI to name a file in the web server's filesystem.

Typically, a special folder in the web server filesystem is reserved for web content. This folder is called the document root, or `docroot`. The web server takes the URI from the request message and appends it to the document root. Another common use of docroots gives people private web sites on a web server. A typical convention maps URIs whose paths begin with a slash and tilde (/~) followed by a username to a private document root for that user. The private docroot is often the folder called public_html inside that user's home directory, but it can be configured differently.

A web server can receive requests for directory URLs, where the path resolves to a directory, not a file. Most web servers can be configured to take a few different actions when a client requests a directory URL: _return an error_, _return a special, default, "index file" instead of the directory_, _Scan the directory, and return an HTML page containing the contents_.

Web servers also can map URIs to dynamic resources--that is, to programs that generate content on demand. In fact, a whole class of web servers called application servers connect web servers to sophisticated backend applications. The web server needs to be able to tell when a resource is a dynamic resource, where the dynamic content generator program is located, and how to run the program.

When a server receives a request for a URI with an executable path component, it attempts to execute a program in a corresponding server directory.

Many web servers also provide support for **_server-side includes_ (SSI)**. If a resource is flagged as containing server-side includes, the server processes the resource contents before sending them to the client. The contents are scanned for certain special patterns (often contained inside special HTML comments), which can be variable names or embedded scripts. The special patterns are replaced with the values of variables or the output of executable scripts. This is an easy way to create dynamic content.

#### 5\. Building Responses

If the transaction generated a response body, the content is sent back with the response message. If there was a body, the response message usually contains:

- A Content-Type header, describing the MIME type of the response body
- A Content-Length header, describing the size of the response body
- The actual message body content

The web server is responsible for determining the **MIME** type of the response body.

Web servers sometimes return redirection responses instead of success messages. A web server can redirect the browser to go elsewhere to perform the request. A redirection response is indicated by a 3XX return code. The Location response header contains a URI for the new or preferred location of the content. Redirects are useful for: _Permanently moved resources_, _Temporarily moved resources_, _URL augmentation_, _Load balancing_, _Server affinity_, _Canonicalizing directory names_.

#### 6\. Sending Responses

The server needs to keep track of the connection state and handle persistent connections with special care. For non persistent connections, the server is expected to close its side of the connection when the entire message is sent. For persistent connections, the connection may stay open, in which case the server needs to be extra cautious to compute the Content-Length header correctly, or the client will have no way of knowing when a response ends.

#### 7\. Logging

when a transaction is complete, the web server notes an entry into a log file, describing the transaction performed.

## Chapter 6: Proxies

Web proxy servers are intermediaries. Proxies sit between clients and servers and act as "middlemen," shuffling HTTP messages back and forth between the parties. HTTP proxy servers are both web servers and web clients.

### Proxies vs Gateways

Strictly speaking, proxies connect two or more applications that speak the same protocol, while gateways hook up two or more parties that speak different protocols. A gateway acts as a "protocol converter," allowing a client to complete a transaction with a server, even when the client and server speak different protocols.

In practice, the difference between proxies and gateways is blurry. Because browsers and servers implement different versions of HTTP, proxies often do some amount of protocol conversion. And commercial proxy servers implement gateway functionality to support SSL security protocols, SOCKS firewalls, FTP access, and web-based applications.

### Why Use Proxies?

#### Child filter

Elementary schools use filtering proxies to block access to adult content, while providing unhindered access to educational sites.

#### Document access controller

Provides different clients with different permissions.

#### Security firewall

Network security engineers often use proxy servers to enhance security. Proxy servers restrict which application-level protocols flow in and out of an organization, at a single secure point in the network. They also can provide hooks to scrutinize that traffic (Figure 6-5), as used by virus-eliminating web and email proxies.

#### Web cache

Proxy caches maintain local copies of popular documents and serve them on demand, reducing slow and costly Internet communication.

#### Surrogate

Proxies can masquerade as web servers. These so-called surrogates or reverse proxies receive real web server requests, but, unlike web servers, they may initiate communication with other servers to locate the requested content on demand.

#### Content router

Content routers also can be used to implement various service-level offerings. For example, content routers can forward requests to nearby replica caches if the user or content provider has paid for higher performance (Figure 6-8), or route HTTP requests through filtering proxies if the user has signed up for a filtering service. Many interesting services can be constructed using adaptive content- routing proxies.

#### Transcoder

Proxy servers can modify the body format of content before delivering it to clients. This transparent translation between data representations is called transcoding. Transcoding proxies can convert GIF images into JPEG images as they fly by, to reduce size.

#### Anonymizer

Anonymizer proxies provide heightened privacy and anonymity, by actively removing identifying characteristics from HTTP messages (e.g., client IP address, From header, Referer header, cookies, URI session IDs).

### Proxy Server Deployment

You can place proxies in all kinds of places, depending on their intended uses. A few ways proxy servers can be deployed:

#### Egress proxy

You can stick proxies at the exit points of local networks to control the traffic flow between the local network and the greater Internet. You might use egress proxies in a corporation to offer firewall protection against malicious hackers outside the enterprise or to reduce bandwidth charges and improve performance of Internet traffic.

#### Access (ingress) proxy

Proxies are often placed at ISP access points, processing the aggregate requests from the customers. ISPs use caching proxies to store copies of popular documents, to improve the download speed for their users (especially those with high-speed connections) and reduce Internet bandwidth costs.

#### Surrogates

Proxies frequently are deployed as surrogates (also commonly called reverse proxies) at the edge of the network, in front of web servers, where they can field all of the requests directed at the web server and ask the web server for resources only when necessary.

#### Network exchange proxy

With sufficient horsepower, proxies can be placed in the Internet peering exchange points between networks, to alleviate congestion at Internet junctions through caching and to monitor traffic flows.

### Proxy Hierarchies

Proxies can be cascaded in chains called proxy hierarchies. However, hierarchies do not have to be static. A proxy server can forward messages to a varied and changing set of proxy servers and origin servers, based on many factors.

Here are a few other examples of dynamic parent proxy selection:

- Load balancing
- Geographic proximity routing
- Protocol/type routing
- Subscription-based routing

### How Proxies Get traffic

Because clients normally talk directly to web servers, we need to explain how HTTP traffic finds its way to a proxy in the first place. There are four common ways to cause client traffic to get to a proxy:

![Chapter 6 Proxy traffic.)](assets/chapter_6_proxy_traffic.png)

## Chapter 7: Caching

Web caches are HTTP devices that automatically keep copies of popular documents. When a web request arrives at a cache, if a local "cached" copy is available, the document is served from the local storage instead of from the origin server.

A cache doesn't store a copy of every document in the world. Some requests that arrive at a cache can be served from an available copy. This is called a **_cache hit_**. Other requests arrive at a cache only to be forwarded to the origin server, because no copy is available. This is called a **_cache miss_**.

Because the origin server content can change, caches have to check every now and then that their copies are still up-to-date with the server. These "freshness checks" are called HTTP **_revalidations_**. To make revalidations efficient, HTTP defines special requests that can quickly check if content is still fresh, without fetching the entire object from the server.

A cache can revalidate a copy any time it wants, and as often as it wants. But because caches often contain millions of documents, and because network bandwidth is scarce, most caches revalidate a copy only when it is requested by a client and when the copy is old enough to warrant a check.

When a cache needs to revalidate a cached copy, it sends a small revalidation request to the origin server. If the content hasn't changed, the server responds with a tiny _304 Not Modified_ response. As soon as the cache learns the copy is still valid, it marks the copy temporarily fresh again and serves the copy to the client. This is called a **_revalidate hit_** or a **_slow hit_**. It's slower than a pure cache hit, because it does need to check with the origin server, but it's faster than a cache miss, because no object data is retrieved from the server.

HTTP gives us a few tools to revalidate cached objects, but the most popular is the _If-Modified-Since_ header. When added to a GET request, this header tells the server to send the object only if it has been modified since the time the copy was cached.

### Hit Rate

The fraction of requests that are served from cache is called the **_cache hit rate_** (or cache hit ratio), or sometimes the **_document hit rate_** (or document hit ratio). The hit rate ranges from 0 to 1 but is often described as a percentage, where 0% means that every request was a miss (had to get the document across the network), and 100% means every request was a hit (had a copy in the cache). Cache administrators would like the cache hit rate to approach 100%.

### Byte Hit Rate

Some large objects might be accessed less often but contribute more to overall data traffic, because of their size. For this reason, some people prefer the byte hit rate metric. The byte hit rate represents the fraction of all bytes transferred that were served from cache. Document hit rate and byte hit rate are both useful gauges of cache performance.

### Distinguishing Hits and Misses

Unfortunately, HTTP provides no way for a client to tell if a response was a cache hit or an origin server access. In both cases, the response code will be 200 OK, indicating that the response has a body.

### Cache Topologies

Caches can be dedicated to a single user or shared between thousands of users. Dedicated caches are called **_private caches_**. Private caches are personal caches, containing popular pages for a single user. Shared caches are called **_public caches_**.

#### Private Caches

Private caches don't need much horsepower or storage space, so they can be made small and cheap. Web browsers have private caches built right in, most browsers cache popular documents in the disk and memory of your personal computer.

#### Public Proxy Caches

Public caches are special, shared proxy servers called caching proxy servers or, more commonly, **_proxy caches_**. Proxy caches serve documents from the local cache or contact the server on the user's behalf. Because a public cache receives accesses from multiple users, it has more opportunity to eliminate redundant traffic.

#### Proxy Cache Hierarchies

In practice, it often makes sense to deploy hierarchies of caches, where cache misses in smaller caches are funneled to larger parent caches that service the leftover "distilled" traffic.

#### Cache Meshes, Content Routing, and Peering

Some network architects build complex **_cache meshes_** instead of simple cache hierarchies. Proxy caches in cache meshes talk to each other in more sophisticated ways, and make dynamic cache communication decisions, deciding which parent caches to talk to, or deciding to bypass caches entirely and direct themselves to the origin server. Such proxy caches can be described as **_content routers_**, because they make routing decisions about how to access, manage, and deliver content.

These more complex relationships between caches allow different organizations to **_peer_** with each other, connecting their caches for mutual benefit. Caches that provide selective peering support are called **_sibling caches_**.

### Cache Processing Steps

1. **_Receiving_**: Cache reads the arriving request message from the network.
2. **_Parsing_**: Cache parses the message, extracting the URL and headers.
3. **_Lookup_**: Cache checks if a local copy is available and, if not, fetches a copy (and stores it locally).
4. **_Freshness check_**: Cache checks if cached copy is fresh enough and, if not, asks server for any updates.
5. **_Response creation_**: Cache makes a response message with the new headers and cached body.
6. **_Sending_**: Cache sends the response back to the client over the network.
7. **_Logging_**: Optionally, cache creates a log file entry describing the transaction.

### Keeping Copies Fresh

HTTP includes simple mechanisms to keep cached data sufficiently consistent with servers, without requiring servers to remember which caches have copies of their documents. HTTP calls these simple mechanisms **_document expiration_** and **_server revalidation_**.

#### Document Expiration

HTTP lets an origin server attach an "expiration date" to each document, using special HTTP Cache-Control and Expires headers. Like an expiration date on a quart of milk, these headers dictate how long content should be viewed as fresh.

#### Server revalidation

Just because a cached document has expired doesn't mean it is actually different from what's living on the origin server; it just means that it's time to check. This is called "server revalidation," meaning the cache needs to ask the origin server whether the document has changed:

- If revalidation shows the content has changed, the cache gets a new copy of the document, stores it in place of the old data, and sends the document to the client.
- If revalidation shows the content has not changed, the cache only gets new headers, including a new expiration date, and updates the headers in the cache.

## Chapter 8: Integration Points: Gateways, Tunnels, and Relays

### Gateways

A gateway is the glue between resources and applications. An application can ask (through HTTP or some other defined interface) a gateway to handle the request, and the gateway can provide a response. The gateway can speak the query language to the database or generate the dynamic content, acting like a portal: a request goes in, and a response comes out.

#### Client-Side and Server-Side Gateways

Web gateways speak HTTP on one side and a different protocol on the other side. Gateways are described by their client- and server-side protocols, separated by a slash:

```
 <client-protocol>/<server-protocol>
```

We use the terms "server-side gateway" and "client-side gateway" to describe what side of the gateway the conversion is done for:

- Server-side gateways speak HTTP with clients and a foreign protocol with servers (HTTP/*).
- Client-side gateways speak foreign protocols with clients and HTTP with servers (*/HTTP).

##### HTTPS/HTTP: Client-Side Security Accelerator Gateways

These HTTPS/HTTP gateways sit in front of the web server, usually as an invisible intercepting gateway or a reverse proxy. They receive secure HTTPS traffic, decrypt the secure traffic, and make normal HTTP requests to the web server.

These gateways often include special decryption hardware to decrypt secure traffic much more efficiently than the origin server, removing load from the origin server. Because these gateways send unencrypted traffic between the gateway and origin server, you need to use caution to make sure the network between the gateway and origin server is secure.

#### Resource Gateways

The most common form of gateway, the application server, combines the destination server and gateway into a single server. Application servers are server-side gateways that speak HTTP with the client and connect to an application program on the server side.

The first popular API for application gateways was the **_Common Gateway Interface_** (CGI). CGI is a standardized set of interfaces that web servers use to launch programs in response to HTTP requests for special URLs, collect the program output, and send it back in HTTP responses.

The Common Gateway Interface was the first and probably still is the most widely used server extension. It is used throughout the Web for things like dynamic HTML, credit card processing, and querying databases.

#### Application Interfaces and Web Services

One of the trickier issues in wiring up applications is negotiating the protocol interface between the two applications so that they can exchange data, often this is done on an application-by-application basis.

The Internet community has developed a set of standards and protocols that allow web applications to talk to each other. These standards are loosely referred to **_as web services_**.

Web services are built on standard web technologies, such as HTTP. Web services exchange information using XML over SOAP. The Extensible Markup Language (XML) provides a way to create and interpret customized information about a data object. The Simple Object Access Protocol (SOAP) is a standard for adding XML information to HTTP messages.

### Tunnels

Web tunnels enable access to applications that speak non-HTTP protocols through HTTP applications. Web tunnels let you send non-HTTP traffic through HTTP connections, allowing other protocols to piggyback on top of HTTP. The most common reason to use web tunnels is to embed non-HTTP traffic inside an HTTP connection, so it can be sent through firewalls that allow only web traffic.

### Relays

HTTP relays are simple HTTP proxies that do not fully adhere to the HTTP specifications. Relays process enough HTTP to establish connections, then blindly forward bytes.

## Chapter 9: Web Robots

Web robots are software programs that automate a series of web transactions with- out human interaction. Many robots wander from web site to web site, fetching con- tent, following hyperlinks, and processing the data they find. These kinds of robots are given colorful names such as "crawlers," "spiders," "worms," and "bots" because of the way they automatically explore web sites, seemingly with minds of their own.

### Crawlers and Crawling

Web crawlers are robots that recursively traverse information webs, fetching first one web page, then all the web pages to which that page points, then all the web pages to which those pages point, and so on.

### Robotic HTTP

Robots are no different from any other HTTP client program. They too need to abide by the rules of the HTTP specification. A robot that is making HTTP requests and advertising itself as an HTTP/1.1 client needs to use the appropriate HTTP request headers.

## Chapter 10: HTTP-NG

No notes.

## Chapter 11: Client Identification and Cookies

HTTP began its life as an anonymous, stateless, request/response protocol. Modern web sites want to know more about users on the other ends of the connections and be able to keep track of those users as they browse. HTTP itself was not born with a rich set of identification features. Mechanisms to identify users:

### HTTP Headers

Seven HTTP request headers that most commonly carry information about the user:

Header name     | Header type | Description
:-------------- | :---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
From            | Request     | User's email address. Ideally, this would be a viable source of user identification, because each user would have a different email address. However, few browsers send From headers, due to worries of unscrupulous servers collecting email addresses and using them for junk mail distribution.
User-Agent      | Request     | The User-Agent header tells the server information about the browser the user is using, including the name and version of the program, and often information about the operating system. This sometimes is useful for customizing content to interoperate well with particular browsers and their attributes, but that doesn't do much to help identify the particular user in any meaningful way.
Referer         | Request     | The Referer header provides the URL of the page the user is coming from. The Referer header alone does not directly identify the user, but it does tell what page the user previously visited. You can use this to better understand user browsing behavior and user interests.
Authorization   | Request     | Username and password. If a server wants a user to register before providing access to the site, it can send back an HTTP 401 Login Required response code to the browser, and adds the WWW-Authenticate header. Once the user enters a username and a password the browser repeats the original request. This time it adds an Authorization header, specifying the username and password. The username and password are scrambled, to hide them from casual or accidental network observers. HTTP basic authentication username and password can easily be unscrambled by anyone who wants to go through a minimal effort. For future requests, the browser will automatically issue the stored username and password when asked and will often even send it to the site when not asked. This makes it possible to log in once to a site and have your identity maintained through the session, by having the browser send the Authorization header as a token of your identity on each request to the server.
Client-ip       | Extension   | Client's IP address
X-Forwarded-For | Extension   | Client's IP address
Cookie          | Extension   | Server-generated ID label

### Fat URLs

Some web sites keep track of user identity by generating special versions of each URL for each user. Typically, a real URL is extended by adding some state information to the start or end of the URL path. As the user browses the site, the web server dynamically generates hyperlinks that continue to maintain the state information in the URLs. URLs modified to include user state information are called fat URLs.

### Cookies

You can classify cookies broadly into two types: **_session cookies_** and **_persistent cookies_**. A session cookie is a temporary cookie that keeps track of settings and preferences as a user navigates a site. A session cookie is deleted when the user exits the browser. Persistent cookies can live longer; they are stored on disk and survive browser exits and computer restarts. Persistent cookies often are used to retain a configuration profile or login name for a site that a user visits periodically.

The only difference between session cookies and persistent cookies is when they expire. A cookie is a session cookie if its Discard parameter is set, or if there is no Expires or Max-Age parameter indicating an extended expiration time.

#### How Cookies Work

Cookies are like "Hello, My Name Is" stickers stuck onto users by servers. When a user visits a web site, the web site can read all the stickers attached to the user by that server.

The first time the user visits a web site, the web server doesn't know anything about the user. The web server expects that this same user will return again, so it wants to "slap" a unique cookie onto the user so it can identify this user in the future. The cookie contains an arbitrary list of name=value information, and it is attached to the user using the Set-Cookie or Set-Cookie2 HTTP response (extension) headers.

Cookies can contain any information, but they often contain just a unique identification number, generated by the server for tracking purposes. For example, the server slaps onto the user a cookie that says id="34294". The server can use this number to look up database information that the server accumulates for its visitors (purchase history, address information, etc.).

However, cookies are not restricted to just ID numbers. Many web servers choose to keep information directly in the cookies. For example:

```
Cookie: name="Brian Totty"; phone="555-1212"
```

The browser remembers the cookie contents sent back from the server in Set-Cookie or Set-Cookie2 headers, storing the set of cookies in a browser cookie database. When the user returns to the same site in the future, the browser will select those cookies slapped onto the user by that server and pass them back in a Cookie request header.

#### Cookie Jar: Client-Side State

The basic idea of cookies is to let the browser accumulate a set of server-specific information, and provide this information back to the server each time you visit.

Because the browser is responsible for storing the cookie information, this system is called client-side state. The official name for the cookie specification is the HTTP State Management Mechanism.

#### Different Cookies for Different Sites

A browser can have hundreds or thousands of cookies in its internal cookie jar, but browsers don't send every cookie to every site. In fact, they typically send only two or three cookies to each site. Here's why:

- Moving all those cookie bytes would dramatically slow performance. Browsers would actually be moving more cookie bytes than real content bytes!
- Most of these cookies would just be unrecognizable gibberish for most sites, because they contain server-specific name/value pairs.
- Sending all cookies to all sites would create a potential privacy concern, with sites you don't trust getting information you intended only for another site.

##### Cookie Domain attribute

A server generating a cookie can control which sites get to see that cookie by adding a Domain attribute to the Set-Cookie response header. For example, the following HTTP response header tells the browser to send the cookie user="mary17" to any site in the domain .airtravelbargains.com:

```
Set-cookie: user="mary17"; domain="airtravelbargains.com"
```

If the user visits www.airtravelbargains.com, specials.airtravelbargains.com, or any site ending in .airtravelbargains.com, the following Cookie header will be issued:

```
Cookie: user="mary17"
```

##### Cookie Path attribute

The cookie specification even lets you associate cookies with portions of web sites. This is done using the Path attribute, which indicates the URL path prefix where each cookie is valid. For example, one web server might be shared between two organizations, each having separate cookies. The site www.airtravelbargains.com might devote part of its web site to auto rentals--say, [http://www.airtravelbargains.com/autos/--using](http://www.airtravelbargains.com/autos/—using) a separate cookie to keep track of a user's preferred car size. A special auto-rental cookie might be generated like this:

```
Set-cookie: pref=compact; domain="airtravelbargains.com"; path=/autos/
```

If the user goes to <http://www.airtravelbargains.com/specials.html>, she will get only this cookie:

```
Cookie: user="mary17"
```

But if she goes to <http://www.airtravelbargains.com/autos/cheapo/index.html>, she will get both of these cookies:

```
Cookie: user="mary17"
Cookie: pref=compact
```

So, cookies are pieces of state, slapped onto the client by the servers, maintained by the clients, and sent back to only those sites that are appropriate.

#### Cookie Ingredients

There are two different versions of cookie specifications in use: Version 0 cookies (sometimes called "Netscape cookies"), and Version 1 ("RFC 2965") cookies. Version 1 cookies are a less widely used extension of Version 0 cookies.

Neither the Version 0 or Version 1 cookie specification is documented as part of the HTTP/1.1 specification. There are two primary adjunct documents that best describe the use of cookies.

##### Version 0 (Netscape) Cookies

The initial cookie specification was defined by Netscape. These "Version 0" cookies defined the Set-Cookie response header, the Cookie request header, and the fields available for controlling cookies. Version 0 cookies look like this:

```
Set-Cookie: name=value [; expires=date] [; path=path] [; domain=domain] [; secure]

Cookie: name1=value1 [; name2=value2] ...
```

##### Version 1 (RFC 2965) Cookies

An extended version of cookies is defined in RFC 2965 (previously RFC 2109). This Version 1 standard introduces the Set-Cookie2 and Cookie2 headers, but it also interoperates with the Version 0 system.

The major changes of RFC 2965 cookies are:

- Associate descriptive text with each cookie to explain its purpose
- Support forced destruction of cookies on browser exit, regardless of expiration
- Max-Age aging of cookies in relative seconds, instead of absolute dates
- Ability to control cookies by the URL port number, not just domain and path
- The Cookie header carries back the domain, port, and path filters (if any)
- Version number for interoperability
- $ prefix in Cookie header to distinguish additional keywords from usernames

For example, assume the client has received these five Set-Cookie2 responses in the past from the www.joes-hardware.com web site:

```
Set-Cookie2: ID="29046"; Domain=".joes-hardware.com"
Set-Cookie2: color=blue
Set-Cookie2: support-pref="L2"; Domain="customer-care.joes-hardware.com"
Set-Cookie2: Coupon="hammer027"; Version="1"; Path="/tools"
Set-Cookie2: Coupon="handvac103"; Version="1"; Path="/tools/cordless"
```

If the client makes another request for path /tools/cordless/specials.html, it will pass along a long Cookie2 header like this:

```
Cookie: $Version="1";
        ID="29046"; $Domain=".joes-hardware.com";
        color="blue";
        Coupon="hammer027"; $Path="/tools";
        Coupon="handvac103"; $Path="/tools/cordless"
```

#### Cookies, Security, and Privacy

The biggest misuse comes from third-party web sites using persistent cookies to track users. This practice, combined with IP addresses and information from the Referer header, has enabled these marketing companies to build fairly accurate user profiles and browsing patterns.

In spite of all the negative publicity, the conventional wisdom is that the session handling and transactional convenience of cookies outweighs most risks, if you use caution about who you provide personal information to and review sites' privacy policies.
