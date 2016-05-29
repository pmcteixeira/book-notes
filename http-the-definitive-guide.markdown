# HTTP: The Definitive Guide

by David Gourley and Brian Totty.

_These book notes were taken to further my own learning and for quick reference. If you enjoy these notes, please [purchase the book](http://www.amazon.com/HTTP-Definitive-Guide-Guides/dp/1565925092)!_

## Chapter 1: Overview of HTTP

_HTTP_ is the common language between _web browsers_, _web servers_ and related _web applications_. HTTP moves data reliably from web servers to web browsers, without worry about the flaws of internet data transmission.

Web servers are also called _HTTP servers_ as they use HTTP protocol. They host web content: _web resources_. HTTP servers provide their content upon request. The client sends a HTTP request and servers retrieve a HTTP response.

The simplest kind of web resource is static files stored in the web server's filesystem. These can be simple text files, _HTML_ files, _JPEG_ image files, _AVI_ movie files, or any other file. **Resources can also be software programs that generate content on demand**.

HTTP tags each data type being transported with a label called _MIME type_. A MIME type is a text label, represented by a primary object type and a specific subtype, separated by a slash. There are hundreds of popular MIME types, here are a few:

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
http   | Item Two Basic form: `http://<host>:<port>/<path>?<query>#<frag>`
https  | The https scheme is a twin to the http scheme. The only difference is that the https scheme uses Secure Sockets Layer (SSL), which provides end-to-end encryption of HTTP connections. Basic form: `https://<host>:<port>/<path>?<query>#<frag>`
mailto | Mailto URLs refer to email addresses. Basic form: `mailto:<RFC-822-addr-spec>`
ftp    | File Transfer Protocol URLs. Basic forms: `ftp://<user>:<password>@<host>:<port>/<path>;<params>`
file   | The file scheme denotes files directly accessible on a given host machine (by local disk, a network filesystem). Basic form: `file://<host>/<path>`
