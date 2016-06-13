# HTTP: The Definitive Guide

by David Gourley and Brian Totty.

_These book notes were taken to further my own learning and for quick reference. If you enjoy these notes, please [purchase the book](http://www.amazon.com/HTTP-Definitive-Guide-Guides/dp/1565925092)!_

- [HTTP: The Definitive Guide](#http-the-definitive-guide)

  - [Chapter 1: Overview of HTTP](#chapter-1-overview-of-http)

    - [URI, URL and URN](#uri-url-and-urn)
    - [Transactions](#transactions)
    - [TCP/IP](#tcpip)
    - [Architectural Components of the Web](#architectural-components-of-the-web)

  - [Chapter 2: URLs and Resources](#chapter-2-urls-and-resources)

    - [URL shortcuts](#url-shortcuts)
    - [Shady Characters](#shady-characters)
    - [Most popular schemes](#most-popular-schemes)

  - [Chapter 3: HTTP Messages](#chapter-3-http-messages)

    - [Message Syntax](#message-syntax)
    - [Methods](#methods)
    - [Status codes](#status-codes)
    - [Headers](#headers)
    - [Entity Bodies](#entity-bodies)

  - [Chapter 4: Connection Management](#chapter-4-connection-management)

    - [TCP Sockets](#tcp-sockets)
    - [TCP Performance Considerations](#tcp-performance-considerations)
    - [HTTP connection performance improvement techniques](#http-connection-performance-improvement-techniques)
    - [Connection close](#connection-close)

  - [Chapter 5: Web Servers](#chapter-5-web-servers)

    - [What Real Web Servers Do](#what-real-web-servers-do)

      - [1\. Accept Client Connections.](#1-accept-client-connections)
      - [2\. Receiving Request Messages.](#2-receiving-request-messages)
      - [3\. Processing Requests.](#3-processing-requests)
      - [4\. Mapping and Accessing Resources.](#4-mapping-and-accessing-resources)
      - [5\. Building Responses](#5-building-responses)
      - [6\. Sending Responses](#6-sending-responses)
      - [7\. Logging](#7-logging)

  - [Chapter 6: Proxies](#chapter-6-proxies)

    - [Proxies vs Gateways](#proxies-vs-gateways)
    - [Why Use Proxies?](#why-use-proxies)

      - [Child filter](#child-filter)
      - [Document access controller](#document-access-controller)
      - [Security firewall](#security-firewall)
      - [Web cache](#web-cache)
      - [Surrogate](#surrogate)
      - [Content router](#content-router)
      - [Transcoder](#transcoder)
      - [Anonymizer](#anonymizer)

    - [Proxy Server Deployment](#proxy-server-deployment)

      - [Egress proxy](#egress-proxy)
      - [Access (ingress) proxy](#access-ingress-proxy)
      - [Surrogates](#surrogates)
      - [Network exchange proxy](#network-exchange-proxy)

    - [Proxy Hierarchies](#proxy-hierarchies)

    - [How Proxies Get traffic](#how-proxies-get-traffic)

  - [Chapter 7: Caching](#chapter-7-caching)

    - [Hit Rate](#hit-rate)
    - [Byte Hit Rate](#byte-hit-rate)
    - [Distinguishing Hits and Misses](#distinguishing-hits-and-misses)
    - [Cache Topologies](#cache-topologies)

      - [Private Caches](#private-caches)
      - [Public Proxy Caches](#public-proxy-caches)
      - [Proxy Cache Hierarchies](#proxy-cache-hierarchies)
      - [Cache Meshes, Content Routing, and Peering](#cache-meshes-content-routing-and-peering)

    - [Cache Processing Steps](#cache-processing-steps)

    - [Keeping Copies Fresh](#keeping-copies-fresh)

      - [Document Expiration](#document-expiration)
      - [Server revalidation](#server-revalidation)

  - [Chapter 8: Integration Points: Gateways, Tunnels, and Relays](#chapter-8-integration-points-gateways-tunnels-and-relays)

    - [Gateways](#gateways)

      - [Client-Side and Server-Side Gateways](#client-side-and-server-side-gateways)

        - [HTTPS/HTTP: Client-Side Security Accelerator Gateways](#httpshttp-client-side-security-accelerator-gateways)

      - [Resource Gateways](#resource-gateways)

      - [Application Interfaces and Web Services](#application-interfaces-and-web-services)

    - [Tunnels](#tunnels)

    - [Relays](#relays)

  - [Chapter 9: Web Robots](#chapter-9-web-robots)

    - [Crawlers and Crawling](#crawlers-and-crawling)
    - [Robotic HTTP](#robotic-http)

  - [Chapter 10: HTTP-NG](#chapter-10-http-ng)

  - [Chapter 11: Client Identification and Cookies](#chapter-11-client-identification-and-cookies)

    - [HTTP Headers](#http-headers)
    - [Fat URLs](#fat-urls)
    - [Cookies](#cookies)

      - [How Cookies Work](#how-cookies-work)
      - [Cookie Jar: Client-Side State](#cookie-jar-client-side-state)
      - [Different Cookies for Different Sites](#different-cookies-for-different-sites)

        - [Cookie Domain attribute](#cookie-domain-attribute)
        - [Cookie Path attribute](#cookie-path-attribute)

      - [Cookie Ingredients](#cookie-ingredients)

        - [Version 0 (Netscape) Cookies](#version-0-netscape-cookies)
        - [Version 1 (RFC 2965) Cookies](#version-1-rfc-2965-cookies)

      - [Cookies, Security, and Privacy](#cookies-security-and-privacy)

  - [Chapter 12: Basic Authentication](#chapter-12-basic-authentication)

    - [Security Realms](#security-realms)
    - [Basic Authentication](#basic-authentication)
    - [Base-64 Username/Password Encoding](#base-64-usernamepassword-encoding)
    - [Proxy Authentication](#proxy-authentication)
    - [The Security Flaws of Basic Authentication](#the-security-flaws-of-basic-authentication)

  - [Chapter 13: Digest Authentication](#chapter-13-digest-authentication)

    - [Using Digests to Keep Passwords Secret](#using-digests-to-keep-passwords-secret)
    - [One-Way Digests](#one-way-digests)
    - [Using Nonces to Prevent Replays](#using-nonces-to-prevent-replays)

  - [Chapter 14: Secure HTTP](#chapter-14-secure-http)

    - [Digital Cryptography](#digital-cryptography)
    - [HTTPS: The Details](#https-the-details)

      - [HTTPS Overview](#https-overview)
      - [HTTPS Schemes](#https-schemes)
      - [Secure Transport Setup](#secure-transport-setup)
      - [SSL Handshake](#ssl-handshake)
      - [Server Certificates](#server-certificates)
      - [Site Certificate Validation](#site-certificate-validation)

        - [Date check](#date-check)
        - [Signer trust check](#signer-trust-check)
        - [Signature check](#signature-check)
        - [Site identity check](#site-identity-check)

      - [A Real HTTPS Client](#a-real-https-client)

        - [OpenSSL](#openssl)

    - [Tunneling Secure Traffic Through Proxies](#tunneling-secure-traffic-through-proxies)

  - [Chapter 15: Entities and Encodings](#chapter-15-entities-and-encodings)

    - [Messages Are Crates, Entities Are Cargo](#messages-are-crates-entities-are-cargo)
    - [Entity Bodies](#entity-bodies)
    - [Content-Length: The Entity's Size](#content-length-the-entitys-size)
    - [Content-Length and Persistent Connections](#content-length-and-persistent-connections)
    - [Content Encoding](#content-encoding)
    - [Entity Digests](#entity-digests)
    - [Media Type and Charset](#media-type-and-charset)
    - [Character Encodings for Text Media](#character-encodings-for-text-media)
    - [Multipart Media Types](#multipart-media-types)

      - [Multipart Form Submissions](#multipart-form-submissions)
      - [Multipart Range Responses](#multipart-range-responses)

    - [Content Encoding](#content-encoding)

      - [Content-Encoding Types](#content-encoding-types)
      - [Accept-Encoding Headers](#accept-encoding-headers)

    - [Range Requests](#range-requests)

    - [Delta Encoding](#delta-encoding)

  - [Chapter 16: Internationalization](#chapter-16-internationalization)

    - [Charset Is a Character-to-Bits Encoding](#charset-is-a-character-to-bits-encoding)
    - [How Character Sets and Encodings Work](#how-character-sets-and-encodings-work)
    - [The Wrong Charset Gives the Wrong Characters](#the-wrong-charset-gives-the-wrong-characters)
    - [Standardized MIME Charset Values](#standardized-mime-charset-values)
    - [Content-Type Charset Header and META Tags](#content-type-charset-header-and-meta-tags)
    - [The Accept-Charset Header](#the-accept-charset-header)
    - [Language Tags and HTTP](#language-tags-and-http)
    - [The Accept-Language Header](#the-accept-language-header)

  - [Chapter 17: Content Negotiation and Transcoding](#chapter-17-content-negotiation-and-transcoding)

  - [Chapter 18: Web hosting](#chapter-18-web-hosting)

    - [Virtual Hosting](#virtual-hosting)

      - [Virtual hosting by Host header](#virtual-hosting-by-host-header)

    - [HTTP/1.1 Host Headers](#http11-host-headers)

      - [Syntax and usage](#syntax-and-usage)

    - [Making Web Sites Reliable](#making-web-sites-reliable)

      - [Mirrored Server Farms](#mirrored-server-farms)

    - [Making Web Sites Fast](#making-web-sites-fast)

  - [Chapter 19: Publishing systems](#chapter-19-publishing-systems)

  - [Chapter 20: Redirection and Load Balancing](#chapter-20-redirection-and-load-balancing)

    - [Why Redirect?](#why-redirect)
    - [Where to Redirect](#where-to-redirect)
    - [Overview of Redirection Protocols](#overview-of-redirection-protocols)

  - [Chapter 21: Logging and Usage tracking](#chapter-21-logging-and-usage-tracking)

    - [Log Formats](#log-formats)

      - [Common Log Format](#common-log-format)

## Mind Maps

![Part 1 Mind Map (Chapters 1-4)](assets/part_1.png)

![Chapter 5](assets/chapter_5.png)

![Chapter 6](assets/chapter_6.png)

![Chapter 7](assets/chapter_7.png)

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

## Chapter 12: Basic Authentication

HTTP provides a native challenge/response framework to make it easy to authenticate users.

Whenever a web application receives an HTTP request message, instead of acting on the request, the server can respond with an "authentication challenge," challenging the user to demonstrate who she is by providing some secret information. The user needs to attach the secret credentials (username and password) when she repeats the request. If the credentials don't match, the server can challenge the client again or generate an error. If the credentials do match, the request completes normally.

HTTP defines two official authentication protocols: basic authentication and digest authentication.

Phase             | Headers             | Description                                                                                                                                                                                                                                                                                                                                                                                         | Method/Status
:---------------- | :------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------
Request Challenge | WWW-Authenticate    | The first request has no authentication. The server rejects the request with a 401 status, indicating that the user needs to provide his username and password. Because the server might have different areas, each with its own password, the server describes the protection area in the WWW-Authenticate header. Also, the authentication algorithm is specified in the WWW-Authenticate header. | GET / 401 Unauthorized
Authorization     | Authorization       | The client retries the request, but this time attaching an Authorization header specifying the authentication algo- rithm, username, and password.                                                                                                                                                                                                                                                  | GET
Success           | Authentication-Info | If the authorization credentials are correct, the server returns the document. Some authorization algorithms return some additional information about the authorization session in the optional Authentication-Info header.                                                                                                                                                                         | 200 OK

### Security Realms

Before we discuss the details of basic authentication, we need to explain how HTTP allows servers to associate different access rights to different resources. WWW-Authenticate challenge included a realm directive. Web servers group protected documents into **_security realms_**. Each security realm can have different sets of authorized users.

For example, suppose a web server has two security realms established: one for corporate financial information and another for personal family documents. Different users will have different access to the realms. The CEO of your company probably should have access to the sales forecast, but you might not give her access to your family vacation photos!

Here's a hypothetical basic authentication challenge, with a realm specified:

```
HTTP/1.0 401 Unauthorized
WWW-Authenticate: Basic realm="Corporate Financials"
```

A realm should have a descriptive string name, like "Corporate Financials," to help the user understand which username and password to use.

### Basic Authentication

In basic authentication, a web server can refuse a transaction, challenging the client for a valid username and password. The server initiates the authentication challenge by returning a 401 status code instead of 200 and specifies the security realm being accessed with the WWW-Authenticate response header. When the browser receives the challenge, it opens a dialog box requesting the username and password for this realm. The username and password are sent back to the server in a slightly scrambled format inside an Authorization request header.

The HTTP basic authentication WWW-Authenticate and Authorization headers summarized:

Challenge/Response           | Header syntax and description
:--------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Challenge (server to client) | There may be different passwords for different parts of the site. The realm is a quoted string that names the set of documents being requested, so the user knows which password to use. `WWW-Authenticate: Basic realm=quoted-realm`                                                                                                                                   |
Response (client to server)  | The username and password are joined together by a colon (:) and then converted to base-64 encoding, making it a bit easier to include international characters in usernames and passwords and making it less likely that a cursory examination will yield usernames and passwords while watching network traffic. `Authorization: Basic base64-username-and-password`.

### Base-64 Username/Password Encoding

HTTP basic authentication packs the username and password together (separated by a colon), and encodes them using the base-64 encoding method.

Base-64 encoding was invented to take strings of binary, text, and international character data (which caused problems on some systems) and convert them temporarily into a portable alphabet for transmission. The original strings could then be decoded on the remote end without fear of transmission corruption.

Base-64 encoding can be useful for usernames and passwords that contain international characters or other characters that are illegal in HTTP headers (such as quotation marks, colons, and carriage returns). Also, because base-64 encoding trivially scrambles the username and password, it can help prevent administrators from accidentally viewing usernames and passwords while administering servers and networks.

### Proxy Authentication

The steps involved in proxy authentication are identical to that of web server identification. However, the headers and status codes are different.

Web server versus proxy authentication:

Web server                    | Proxy server
:---------------------------- | :----------------------------
Unauthorized status code: 401 | Unauthorized status code: 407
WWW-Authenticate              | Proxy-Authenticate
Authorization                 | Proxy-Authorization
Authentication-Info           | Proxy-Authentication-Info

### The Security Flaws of Basic Authentication

Basic authentication is simple and convenient, but it is not secure. It should only be used to prevent unintentional access from nonmalicious parties or used in combination with an encryption technology such as SSL.

Consider the following security flaws:

1. Basic authentication sends the username and password across the network in a form that can trivially be decoded. In effect, the secret password is sent in the clear, for anyone to read and capture. Base-64 encoding obscures the username and password, making it less likely that friendly parties will glean passwords by accidental network observation. However, given a base 64–encoded username and password, the decoding can be performed trivially by reversing the encoding process. Decoding can even be done in seconds, by hand, with pencil and paper! Base 64–encoded passwords are effectively sent "in the clear." Assume that motivated third parties will intercept usernames and passwords sent by basic authentication. If this is a concern, send all your HTTP transactions over SSL encrypted channels, or use a more secure authentication protocol, such as digest authentication.
2. Even if the secret password were encoded in a scheme that was more complicated to decode, a third party could still capture the garbled username and pass- word and replay the garbled information to origin servers over and over again to gain access. No effort is made to prevent these replay attacks.
3. Even if basic authentication is used for noncritical applications, such as corpo- rate intranet access control or personalized content, social behavior makes this dangerous. Many users, overwhelmed by a multitude of password-protected services, share usernames and passwords. A clever, malicious party may capture a username and password in the clear from a free Internet email site, for example, and find that the same username and password allow access to critical online banking sites!
4. Basic authentication offers no protection against proxies or intermediaries that act as middlemen, leaving authentication headers intact but modifying the rest of the message to dramatically change the nature of the transaction.
5. Basic authentication is vulnerable to spoofing by counterfeit servers. If a user can be led to believe that he is connecting to a valid host protected by basic authentication when, in fact, he is connecting to a hostile server or gateway, the attacker can request a password, store it for later use, and feign an error.

Basic authentication can be made secure by combining it with encrypted data transmission (such as SSL) to conceal the username and password from malicious individuals. This is a common technique.

## Chapter 13: Digest Authentication

Digest authentication is an alternate HTTP authentication protocol that tries to fix the most serious flaws of basic authentication. In particular, digest authentication:

- Never sends secret passwords across the network in the clear
- Prevents unscrupulous individuals from capturing and replaying authentication handshakes

### Using Digests to Keep Passwords Secret

The motto of digest authentication is "never send the password across the network." Instead of sending the password, the client sends a "fingerprint" or "digest" of the password, which is an irreversible scrambling of the password. The client and the server both know the secret password, so the server can verify that the digest pro- vided a correct match for the password. Given only the digest, a bad guy has no easy way to find what password it came from, other than going through every password in the universe, trying each one!

### One-Way Digests

One popular digest function, MD5, converts any arbitrary sequence of bytes, of any length, into a 128-bit digest.

### Using Nonces to Prevent Replays

One-way digests save us from having to send passwords in the clear. We can just send a digest of the password instead, and rest assured that no malicious party can easily decode the original password from the digest.

Unfortunately, obscured passwords alone do not save us from danger, because a bad guy can capture the digest and replay it over and over again to the server, even though the bad guy doesn't know the password. The digest is just as good as the password.

To prevent such replay attacks, the server can pass along to the client a special token called a nonce, which changes frequently (perhaps every millisecond, or for every authentication). The client appends this nonce token to the password before computing the digest.

Mixing the nonce in with the password causes the digest to change each time the nonce changes. This prevents replay attacks, because the recorded password digest is valid only for a particular nonce value, and without the secret password, the attacker cannot compute the correct digest.

Digest authentication requires the use of nonces, because a trivial replay weakness would make unnonced digest authentication effectively as weak as basic authentication. Nonces are passed from server to client in the WWW-Authenticate challenge.

## Chapter 14: Secure HTTP

When using HTTPS, all the HTTP request and response data is encrypted before being sent across the network. HTTPS works by providing a transport-level crypto- graphic security layer--using either the Secure Sockets Layer (SSL) or its successor, Transport Layer Security (TLS)--underneath HTTP.

Because most of the hard encoding and decoding work happens in the SSL libraries, web clients and servers don't need to change much of their protocol processing logic to use secure HTTP. For the most part, they simply need to replace TCP input/out- put calls with SSL calls and add a few other calls to configure and manage the security information.

### Digital Cryptography

Concept                      | Description
:--------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Ciphers                      | Algorithms for encoding text to make it unreadable to voyeurs
Keys                         | Numeric parameters that change the behavior of ciphers. You needed to enter the right key into the cipher machine to get the decoding process to work correctly. Cipher keys make a single cipher machine act like a set of many virtual cipher machines, each of which behaves differently because they have different key values.
Symmetric-key cryptosystems  | Algorithms that use the same key for encoding and decoding
Asymmetric-key cryptosystems | Algorithms that use different keys for encoding and decoding
Public-key cryptography      | Instead of a single encoding/decoding key for every pair of hosts, public-key cryptography uses two asymmetric keys: one for encoding messages for a host, and another for decoding the host's messages. The encoding key is publicly known to the world (thus the name public-key cryptography), but only the host knows the private decoding key. This makes key establishment much easier, because every- one can find the public key for a particular host. But the decoding key is kept secret, so only the recipient can decode messages sent to it.
Digital signatures           | Checksums that verify that a message has not been forged or tampered with. Signatures Are Cryptographic Checksums. Signatures prove the author wrote the message. Because only the author has the author's top-secret private key, only the author can compute these checksums. The checksum acts as a personal "signature" from the author.
Digital certificates         | Identifying information, verified and signed by a trusted organization

### HTTPS: The Details

HTTPS combines the HTTP protocol with a powerful set of symmetric, asymmetric, and certificate-based cryptographic techniques.

#### HTTPS Overview

HTTPS is just HTTP sent over a secure transport layer. Instead of sending HTTP messages unencrypted to TCP and across the world-wide Internet. HTTPS sends the HTTP messages first to a security layer that encrypts them before sending them to TCP. Today, the HTTP security layer is implemented by SSL and its modern replacement, TLS.

#### HTTPS Schemes

Today, secure HTTP is optional. Thus, when making a request to a web server, we need a way to tell the web server to perform the secure protocol version of HTTP. This is done in the scheme of the URL.

In normal, nonsecure HTTP, the scheme prefix of the URL is http, as in:

```
http://www.joes-hardware.com/index.html
```

In the secure HTTPS protocol, the scheme prefix of the URL is https, as in:

```
https://cajun-shop.securesites.com/Merchant2/merchant.mv?Store_Code=AGCGS
```

When a client (such as a web browser) is asked to perform a transaction on a web resource, it examines the scheme of the URL:

- If the URL has an http scheme, the client opens a connection to the server on port 80 (by default) and sends it plain-old HTTP commands.
- If the URL has an https scheme, the client opens a connection to the server on port 443 (by default) and then "handshakes" with the server, exchanging some SSL security parameters with the server in a binary format, followed by the encrypted HTTP commands

Because SSL traffic is a binary protocol, completely different from HTTP, the traffic is carried on different ports (SSL usually is carried over port 443). If both SSL and HTTP traffic arrived on port 80, most web servers would interpret binary SSL traffic as erroneous HTTP and close the connection. A more integrated layering of security services into HTTP would have eliminated the need for multiple destination ports, but this does not cause severe problems in practice.

#### Secure Transport Setup

In HTTPS, the client first opens a connection to port 443 (the default port for secure HTTP) on the web server. Once the TCP connection is established, the client and server initialize the SSL layer, negotiating cryptography parameters and exchang- ing keys. When the handshake completes, the SSL initialization is done, and the cli- ent can send request messages to the security layer. These messages are encrypted before being sent to TCP.

#### SSL Handshake

Before you can send encrypted HTTP messages, the client and server need to do an SSL handshake, where they:

- Exchange protocol version numbers.
- Select a cipher that each side knows.
- Authenticate the identity of each side.
- Generate temporary session keys to encrypt the channel.

Before any encrypted HTTP data flies across the network, SSL already has sent a bunch of handshake data to establish the communication.

#### Server Certificates

SSL supports mutual authentication, carrying server certificates to clients and carry- ing client certificates back to servers. But today, client certificates are not commonly used for browsing. Most users don't even possess personal client certificates. A web server can demand a client certificate, but that seldom occurs in practice.

On the other hand, secure HTTPS transactions always require server certificates. When you perform a secure transaction on a web server, such as posting your credit card information, you want to know that you are talking to the organization you think you are talking to. Server certificates, signed by a well-known authority, help you assess how much you trust the server before sending your credit card or personal information.

#### Site Certificate Validation

SSL itself doesn't require you to examine the web server certificate, but most modern browsers do some simple sanity checks on certificates and provide you with the means to do more thorough checks. One algorithm for web server certificate validation, proposed by Netscape, forms the basis of most browser's validation techniques. The steps are:

##### Date check

First, the browser checks the certificate's start and end dates to ensure the certificate is still valid. If the certificate has expired or has not yet become active, the certificate validation fails and the browser displays an error.

##### Signer trust check

Every certificate is signed by some certificate authority (CA), who vouches for the server. There are different levels of certificate, each requiring different levels of background verification. For example, if you apply for an e-commerce server certificate, you usually need to provide legal proof of incorporation as a business.

Anyone can generate certificates, but some CAs are well-known organizations with well-understood procedures for verifying the identity and good business behavior of certificate applicants. For this reason, browsers ship with a list of signing authorities that are trusted. If a browser receives a certificate signed by some unknown (and possibly malicious) authority, the browser usually displays a warning. Browsers also may choose to accept any certificates with a valid signing path to a trusted CA. In other words, if a trusted CA signs a certificate for "Sam's Signing Shop" and Sam's Signing Shop signs a site certificate, the browser may accept the certificate as deriving from a valid CA path.

##### Signature check

Once the signing authority is judged as trustworthy, the browser checks the cer- tificate's integrity by applying the signing authority's public key to the signature and comparing it to the checksum.

##### Site identity check

**_To prevent a server from copying someone else's certificate or intercepting their traffic, most browsers try to verify that the domain name in the certificate matches the domain name of the server they talked to. Server certificates usually contain a single domain name, but some CAs create certificates that contain lists of server names or wildcarded domain names, for clusters or farms of servers. If the host- name does not match the identity in the certificate, user-oriented clients must either notify the user or terminate the connection with a bad certificate error._**

#### A Real HTTPS Client

SSL is a complicated binary protocol. Unless you are a crypto expert, you shouldn't send raw SSL traffic directly. Thankfully, several commercial and open source libraries exist to make it easier to program SSL clients and servers.

##### OpenSSL

OpenSSL is the most popular open source implementation of SSL and TLS. The OpenSSL Project is a collaborative volunteer effort to develop a robust, commercial- grade, full-featured toolkit implementing the SSL and TLS protocols, as well as a full- strength, general-purpose cryptography library. You can get information about OpenSSL, and download the software, from <http://www.openssl.org>.

### Tunneling Secure Traffic Through Proxies

Many corporations place a proxy at the security perimeter of the corporate network and the public Internet. The proxy is the only device permitted by the firewall routers to exchange HTTP traffic, and it may employ virus checking or other content controls.

But once the client starts encrypting the data to the server, using the server's public key, the proxy no longer has the ability to read the HTTP header! And if the proxy cannot read the HTTP header, it won't know where to forward the request.

To make HTTPS work with proxies, a few modifications are needed to tell the proxy where to connect. One popular technique is the HTTPS SSL tunneling protocol.

Using the HTTPS tunneling protocol, the client first tells the proxy the secure host and port to which it wants to connect. It does this in plaintext, before encryption starts, so the proxy can read this information.

HTTP is used to send the plaintext endpoint information, using a new extension method called CONNECT. The CONNECT method tells the proxy to open a connection to the desired host and port number and, when that's done, to tunnel data directly between the client and server. The CONNECT method is a one-line text command that provides the hostname and port of the secure origin server, separated by a colon. The host:port is followed by a space and an HTTP version string followed by a CRLF. After that there is a series of zero or more HTTP request header lines, followed by an empty line. After the empty line, if the handshake to establish the connection was successful, SSL data transfer can begin. Here is an example:

```
CONNECT home.netscape.com:443 HTTP/1.0
User-agent: Mozilla/1.1N
<raw SSL-encrypted data would follow here...>
```

After the empty line in the request, the client will wait for a response from the proxy. The proxy will evaluate the request and make sure that it is valid and that the user is authorized to request such a connection. If everything is in order, the proxy will make a connection to the destination server and, if successful, send a 200 Connection Established response to the client.

```
HTTP/1.0 200 Connection established
Proxy-agent: Netscape-Proxy/1.1
```

## Chapter 15: Entities and Encodings

### Messages Are Crates, Entities Are Cargo

If you think of HTTP messages as the crates of the Internet shipping system, then HTTP entities are the actual cargo of the messages.

HTTP message. HTTP/1.1 defines 10 primary entity header fields:

Header           | Description
:--------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Content-Type     | The kind of object carried by the entity.
Content-Length   | The length or size of the message being sent.
Content-Language | The human language that best matches the object being sent.
Content-Encoding | Any transformation (compression, etc.) performed on the object data.
Content-Location | An alternate location for the object at the time of the request.
Content-Range    | If this is a partial entity, this header defines which pieces of the whole are included.
Content-MD5      | A checksum of the contents of the entity body.
Last-Modified    | The date on which this particular content was created or modified at the server.
Expires          | The date and time at which this entity data will become stale.
Allow            | What request methods are legal on this resource; e.g., GET and HEAD.
ETag             | A unique validator for this particular instance of the document. The ETag header is not defined formally as an entity header, but it is an important header for many operations involving entities.
Cache-Control    | Directives on how this document can be cached. The Cache-Control header, like the ETag header, is not defined formally as an entity header.

### Entity Bodies

The entity body just contains the raw cargo. Any other descriptive information is contained in the headers. Because the entity body cargo is just raw data, the entity headers are needed to describe the meaning of that data. For example, the Content- Type entity header tells us how to interpret the data (image, text, etc.), and the Content-Encoding entity header tells us if the data was compressed or otherwise recoded.

The raw content begins immediately after the blank CRLF line that marks the end of the header fields. Whatever the content is--text or binary, document or image, com- pressed or uncompressed, English or French or Japanese--it is placed right after the CRLF.

### Content-Length: The Entity's Size

The Content-Length header indicates the size of the entity body in the message, **_in bytes_**. The size includes any content encodings (the Content-Length of a gzip-compressed text file will be the compressed size, not the original size). The Content-Length header is mandatory for messages with entity bodies, unless the message is transported using chunked encoding.

### Content-Length and Persistent Connections

Content-Length is essential for persistent connections. If the response comes across a persistent connection, another HTTP response can immediately follow the current response. The Content-Length header lets the client know where one message ends and the next begins. Because the connection is persistent, the client cannot use connection close to identify the message's end. Without a Content-Length header, HTTP applications won't know where one entity body ends and the next message begins.

### Content Encoding

HTTP lets you encode the contents of an entity body, perhaps to make it more secure or to compress it to take up less space. If the body has been content-encoded, the Content-Length header specifies the length, in bytes, of the encoded body, not the length of the original, unencoded body.

Some HTTP applications have been known to get this wrong and to send the size of the data before the encoding.

### Entity Digests

Although HTTP typically is implemented over a reliable transport protocol such as TCP/IP, parts of messages may get modified in transit for a variety of reasons, such as noncompliant transcoding proxies or buggy intermediary proxies. To detect unintended (or undesired) modification of entity body data, the sender can generate a checksum of the data when the initial entity is generated, and the receiver can sanity check the checksum to catch any unintended entity modification.

The Content-MD5 header is used by servers to send the result of running the MD5 algorithm on the entity body. Only the server where the response originates may compute and send the Content-MD5 header.

The Content-MD5 header contains the MD5 of the content after all content encodings have been applied to the entity body and before any transfer encodings have been applied to it. Clients seeking to verify the integrity of the message must first decode the transfer encodings, then compute the MD5 of the resulting unencoded entity body. As an example, if a document is compressed using the gzip algorithm, then sent with chunked encoding, the MD5 algorithm is run on the full gripped body.

### Media Type and Charset

The Content-Type header field describes the MIME type of the entity body.* The MIME type is a standardized name that describes the underlying type of media carried as cargo. Client applications use the MIME type to properly decipher and process the content.

The Content-Type values are standardized MIME types, registered with the Internet Assigned Numbers Authority (IANA). MIME types consist of a primary media type (e.g., text, image, audio), followed by a slash, followed by a subtype that further specifies the media type.

It is important to note that the Content-Type header specifies the media type of the original entity body. If the entity has gone through content encoding, for example, the Content-Type header will still specify the entity body type before the encoding.

### Character Encodings for Text Media

The Content-Type header also supports optional parameters to further specify the content type. The "charset" parameter is the primary example, specifying the mechanism to convert bits from the entity into characters in a text file:

```
Content-Type: text/html; charset=iso-8859-4
```

### Multipart Media Types

MIME "multipart" email messages contain multiple messages stuck together and sent as a single, complex message. Each component is self-contained, with its own set of headers describing its content; the different components are concatenated together and delimited by a string.

#### Multipart Form Submissions

HTTP sends such requests with a Content-Type: multipart/form-data header or a Content-Type: multipart/mixed header and a multipart body, like this:

```
Content-Type: multipart/form-data; boundary=[abcdefghijklmnopqrstuvwxyz]
```

where the boundary specifies the delimiter string between the different parts of the body.

If the user enters "Sally" in the text-input field and selects the text file "essayfile.txt," the user agent might send back the following data:

```
Content-Type: multipart/form-data; boundary=AaB03x
--AaB03x
Content-Disposition: form-data; name="submit-name"
Sally
--AaB03x
Content-Disposition: form-data; name="files"; filename="essayfile.txt" Content-Type: text/plain
...contents of essayfile.txt...
--AaB03x--
```

#### Multipart Range Responses

HTTP responses to range requests also can be multipart. Such responses come with a Content-Type: multipart/byteranges header and a multipart body with the different ranges. Here is an example of a multipart response to a request for different ranges of a document:

```
 HTTP/1.0 206 Partial content
Server: Microsoft-IIS/5.0
Date: Sun, 10 Dec 2000 19:11:20 GMT
Content-Location: http://www.joes-hardware.com/gettysburg.txt
Content-Type: multipart/x-byteranges; boundary=--[abcdefghijklmnopqrstuvwxyz]--
Last-Modified: Sat, 09 Dec 2000 00:38:47 GMT

--[abcdefghijklmnopqrstuvwxyz]--
Content-Type: text/plain
Content-Range: bytes 0-174/1441

Fourscore and seven years ago our fathers brough forth on this continent
a new nation, conceived in liberty and dedicated to the proposition that
all men are created equal.
--[abcdefghijklmnopqrstuvwxyz]--
Content-Type: text/plain
Content-Range: bytes 552-761/1441

But in a larger sense, we can not dedicate, we can not consecrate,
we can not hallow this ground. The brave men, living and dead who
struggled here have consecrated it far above our poor power to add
or detract.
--[abcdefghijklmnopqrstuvwxyz]--
Content-Type: text/plain
Content-Range: bytes 1344-1441/1441

and that government of the people, by the people, for the people shall
not perish from the earth.

--[abcdefghijklmnopqrstuvwxyz]--
```

### Content Encoding

HTTP applications sometimes want to encode content before sending it. These types of encodings are applied to the content at the sender. Once the content is content-encoded, the encoded data is sent to the receiver in the entity body as usual. The content-encoding process is:

1. A web server generates an original response message, with original Content- Type and Content-Length headers.
2. A content-encoding server (perhaps the origin server or a downstream proxy) creates an encoded message. The encoded message has the same Content-Type but (if, for example, the body is compressed) a different Content-Length. The content-encoding server adds a Content-Encoding header to the encoded message, so that a receiving application can decode it.
3. A receiving program gets the encoded message, decodes it, and obtains the original.

#### Content-Encoding Types

HTTP defines a few standard content-encoding types and allows for additional encodings to be added as extension encodings. Encodings are standardized through the IANA, which assigns a unique token to each content-encoding algorithm. The Content-Encoding header uses these standardized token values to describe the algorithm used in the encoding.

Content-encoding value | Description
:--------------------- | :-------------------------------------------------------------------------------------------------------------------------------
gzip                   | Indicates that the GNU zip encoding was applied to the entity.
compress               | Indicates that the Unix file compression program has been run on the entity.
deflate                | Indicates that the entity has been compressed into the zlib format.
identity               | Indicates that no encoding has been performed on the entity. When a Content-Encoding header is not present, this can be assumed.

The gzip, compress, and deflate encodings are lossless compression algorithms used to reduce the size of transmitted messages without loss of information. Of these, gzip typically is the most effective compression algorithm and is the most widely used.

#### Accept-Encoding Headers

Of course, we don't want servers encoding content in ways that the client can't decipher. To prevent servers from using encodings that the client doesn't support, the client passes along a list of supported content encodings in the Accept-Encoding request header. If the HTTP request does not contain an Accept-Encoding header, a server can assume that the client will accept any encoding.

### Range Requests

HTTP allows clients to actually request just part or a range of a document.

Imagine if you were three-fourths of the way through downloading the latest hot soft- ware across a slow modem link, and a network glitch interrupted your connection. You would have been waiting for a while for the download to complete, and now you would have to start all over again, hoping the same thing does not happen again.

With range requests, an HTTP client can resume downloading an entity by asking for the range or part of the entity it failed to get (provided that the object did not change at the origin server between the time the client first requested it and its subsequent range request). For example:

```
GET /bigfile.html HTTP/1.1
Host: www.joes-hardware.com
Range: bytes=4000-
User-Agent: Mozilla/4.61 [en] (WinNT; I)
...
```

In this example, the client is requesting the remainder of the document after the first 4,000 bytes (the end bytes do not have to be specified, because the size of the document may not be known to the requestor). Range requests of this form can be used for a failed request where the client received the first 4,000 bytes before the failure. The Range header also can be used to request multiple ranges (the ranges can be specified in any order and may overlap)--for example, imagine a client connecting to multiple servers simultaneously, requesting different ranges of the same document from different servers in order to speed up overall download time for the document. In the case where clients request multiple ranges in a single request, responses come back as a single entity, with a multipart body and a Content-Type: multipart/byteranges header.

Not all servers accept range requests, but many do. Servers can advertise to clients that they accept ranges by including the header Accept-Ranges in their responses. The value of this header is the unit of measure, usually bytes. For example:

```
 HTTP/1.1 200 OK
Date: Fri, 05 Nov 1999 22:35:15 GMT
Server: Apache/1.2.4
Accept-Ranges: bytes
```

Range headers are used extensively by popular peer-to-peer file-sharing client software to download different parts of multimedia files simultaneously, from different peers.

Note that range requests are a class of instance manipulations, because they are exchanges between a client and a server for a particular instance of an object. That is, a client's range request makes sense only if the client and server have the same version of a document.

### Delta Encoding

Rather than sending it the entire new page, the client would get the page faster if the server sent just the changes to the client's copy of the page (provided that the number of changes is small). Delta encoding is an extension to the HTTP protocol that optimizes transfers by communicating changes instead of entire objects. Delta encoding is a type of instance manipulation, because it relies on clients and servers exchanging information about particular instances of an object.

## Chapter 16: Internationalization

HTTP applications use character set encodings to request and display text in different alphabets, and they use language tags to describe and restrict content to languages the user understands.

HTTP messages can carry content in any language, just as it can carry images, movies, or any other kind of media. To HTTP, the entity body is just a box of bits.

To support international content, servers need to tell clients about the alphabet and languages of each document, so the client can properly unpack the document bits into characters and properly process and present the content to the user.

Servers tell clients about a document's alphabet and language with the HTTP Content-Type charset parameter and Content-Language headers. These headers describe what's in the entity body's "box of bits," how to convert the contents into the proper characters that can be displayed onscreen, and what spoken language the words represent.

At the same time, the client needs to tell the server which languages the user under- stands and which alphabetic coding algorithms the browser has installed. The client sends Accept-Charset and Accept-Language headers to tell the server which character set encoding algorithms and languages the client understands, and which of them are preferred.

### Charset Is a Character-to-Bits Encoding

The HTTP charset values tell you how to convert from entity content bits into characters in a particular alphabet. Each charset tag names an algorithm to translate bits to characters (and vice versa). The charset tags are standardized in the MIME character set registry, maintained by the IANA.

The following Content-Type header tells the receiver that the content is an HTML file, and the charset parameter tells the receiver to use the iso-8859-6 Arabic character set decoding scheme to decode the content bits into characters:

```
Content-Type: text/html; charset=iso-8859-6
```

The iso-8859-6 encoding scheme maps 8-bit values into both the Latin and Arabic alphabets, including numerals, punctuation and other symbols.

Some character encodings (e.g., UTF-8 and iso-2022-jp) are more complicated, variable-length codes, where the number of bits per character varies. This type of coding lets you use extra bits to support alphabets with large numbers of characters (such as Chinese and Japanese), while using fewer bits to support standard Latin characters.

### How Character Sets and Encodings Work

We want to convert from bits in a document into characters that we can display onscreen. But because there are many different alphabets, and many different ways of encoding characters into bits (each with advantages and disadvantages), we need a standard way to describe and apply the bits-to-character decoding algorithm.

HTTP concerns itself only with transporting the character data and the associated language and charset labels. The presentation of the character shapes is handled by the user's graphics display software.

### The Wrong Charset Gives the Wrong Characters

If the client uses the wrong charset parameter, the client will display strange, bogus characters.

### Standardized MIME Charset Values

The combination of a particular character encoding and a particular coded character set is called a MIME charset. HTTP uses standardized MIME charset tags in the Con- tent-Type and Accept-Charset headers. MIME charset values are registered with the IANA.

### Content-Type Charset Header and META Tags

Web servers send the client the MIME charset tag in the Content-Type header, using the charset parameter:

```
Content-Type: text/html; charset=iso-2022-jp
```

If no charset is explicitly listed, the receiver may try to infer the character set from the document contents. For HTML content, character sets might be found in

<meta http-equiv="Content-Type">

tags that describe the charset.

Character encoding can be specified in HTML META tags:

```
<HEAD>
  <META HTTP-EQUIV="Content-Type" CONTENT="text/html; charset=iso-2022-jp"> <META LANG="jp">
  <TITLE>A Japanese Document</TITLE>
</HEAD>
```

### The Accept-Charset Header

HTTP clients can tell servers precisely which character systems they support, using the Accept-Charset request header.

For example, the following HTTP request header indicates that a client accepts the Western European iso-8859-1 character system as well as the UTF-8 variable-length Unicode compatibility system. A server is free to return content in either of these character encoding schemes.

HTTP Accept-Charset header and the Content-Type charset parameter carry character-encoding information from the client and server.

### Language Tags and HTTP

There are language tags for English (en), German (de), Korean (ko), and many other languages. Language tags can describe regional variants and dialects of languages, such as Brazilian Portuguese (pt-BR), U.S. English (en-US), and Hunan Chinese (zh-xiang). There is even a standard language tag for Klingon (i-klingon)!

The Content-Language entity header field describes the target audience languages for the entity. If the content is intended primarily for a French audience, the Content- Language header field would contain:

```
Content-Language: fr
```

If the content is intended for multiple audiences, you can list multiple languages:

```
Content-Language: mi, en
```

### The Accept-Language Header

```
Accept-Language: es
```

Language tags can be used to represent:

- General language classes (as in "es" for Spanish)
- Country-specific languages (as in "en-GB" for English in Great Britain)
- Dialects of languages (as in "no-bok" for Norwegian "Book Language")
- Regional languages (as in "sgn-US-MA" for Martha's Vineyard sign language)
- Standardized nonvariant languages (e.g., "i-navajo")
- Nonstandard languages (e.g., "x-snowboarder-slang")

## Chapter 17: Content Negotiation and Transcoding

No notes.

## Chapter 18: Web hosting

The collective duties of storing, brokering, and administering content resources is called **_web hosting_**. If you don't want to manage the required hardware and software yourself, you need a hosting service, or hoster. Hosters rent you serving and web-site administration services and provide various degrees of security, reporting, and ease of use. Hosters typically pool web sites on heavy-duty web servers for cost-efficiency, reliability, and performance.

Many levels of service are available, from physical facilities management (providing space, air conditioning, and wiring) to full-service web hosting, where all the customer does is provide the content.

### Virtual Hosting

Many folks want to have a web presence but don't have high-traffic web sites. For these people, providing a dedicated web server may be a waste, because they're pay- ing many hundreds of dollars a month to lease a server that is mostly idle!

Many web hosters offer lower-cost web hosting services by sharing one computer between several customers. This is called shared hosting or virtual hosting. Each web site appears to be hosted by a different server, but they really are hosted on the same physical server. From the end user's perspective, virtually hosted web sites should be indistinguishable from sites hosted on separate dedicated servers.

For cost efficiency, space, and management reasons, a virtual hosting company wants to host tens, hundreds, or thousands of web sites on the same server--but this does not necessarily mean that 1,000 web sites are served from only one PC. Hosters can create banks of replicated servers (called server farms) and spread the load across the farm of servers. Because each server in the farm is a clone of the others, and hosts many virtual web sites, administration is much easier.

Unfortunately, there is a design flaw in HTTP/1.0 that makes virtual hosters pull their hair out. The HTTP/1.0 specification didn't give any means for shared web servers to identify which of the virtual web sites they're hosting is being accessed.

Because the early specifications did not make provisions for virtual hosting, web hosters needed to develop workarounds and conventions to support shared virtual hosting. The problem could have been solved simply by requiring all HTTP request messages to send the full URL instead of just the path component. HTTP/1.1 does require servers to handle full URLs in the request lines of HTTP messages, but it will be a long time before all legacy applications are upgraded to this specification. In the meantime, four techniques have emerged:

Technique                      | Description
:----------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Virtual hosting by URL path    | Adding a special path component to the URL so the server can determine the site.
Virtual hosting by port number | Assigning a different port number to each site, so requests are handled by separate instances of the web server.
Virtual hosting by IP address  | Dedicating different IP addresses for different virtual sites and binding all the IP addresses to a single machine. This allows the web server to identify the site name by IP address.
Virtual hosting by Host header | Many web hosters pressured the HTTP designers to solve this problem. Enhanced versions of HTTP/1.0 and the official version of HTTP/1.1 define a Host request header that carries the site name. The web server can identify the virtual site from the Host header.

#### Virtual hosting by Host header

To avoid excessive address consumption and virtual IP limits, we'd like to share the same IP address among virtual sites, but still be able to tell the sites apart. But as we've seen, because most browsers send just the path component of the URL to servers, the critical virtual hostname information is lost.

To solve this problem, browser and server implementors extended HTTP to provide the original hostname to servers. But browsers couldn't just send a full URL, because that would break many servers that expected to receive only a path component. Instead, the hostname (and port) is passed in a Host extension header in all requests.

Host headers are required for HTTP/1.1 compliance.

### HTTP/1.1 Host Headers

#### Syntax and usage

The Host header specifies the Internet host and port number for the resource being requested, as obtained from the original URL:

```
Host = "Host" ":" host [ ":" port ]
```

### Making Web Sites Reliable

#### Mirrored Server Farms

A server farm is a bank of identically configured web servers that can cover for each other. The content on each server in the farm can be mirrored, so that if one has a problem, another can fill in.

Often, mirrored servers follow a hierarchical relationship. One server might act as the "content authority"--the server that contains the original content (perhaps a server to which the content authors post). This server is called the master origin server. The mirrored servers that receive content from the master origin server are called replica origin servers.

### Making Web Sites Fast

Many of the technologies mentioned in the previous section also help web sites load faster. Server farms and distributed proxy caches or surrogate servers distribute net- work traffic, avoiding congestion. Distributing the content brings it closer to end users, so that the travel time from server to client is lower. The key to speed of resource access is how requests and responses are directed from client to server and back across the Internet.

Another approach to speeding up web sites is encoding the content for fast transportation. This can mean, for example, compressing the content, assuming that the receiving client can uncompress it.

## Chapter 19: Publishing systems

How do you create web pages and get them onto a web server? In the dark ages of the Web (let's say, 1995), you might have hand-crafted your HTML in a text editor and manually uploaded the content to the web server using FTP. This procedure was painful, difficult to coordinate with coworkers, and not particularly secure.

Modern-day publishing tools make it much more convenient to create, publish, and manage web content. Today, you can interactively edit web content as you'll see it on the screen and publish that content to servers with a single click, while being notified of any files that have changed.

## Chapter 20: Redirection and Load Balancing

HTTP does not walk the Web alone. The data in an HTTP message is governed by many protocols on its journey. HTTP cares only about the endpoints of the journey-- the sender and the receiver--but in a world with mirrored servers, web proxies, and caches, the destination of an HTTP message is not necessarily straightforward.

Redirection technologies usually determine whether the message ends up at a proxy, a cache, or a particular web server in a server farm. Redirection technologies may send your messages to places a client didn't explicitly request.

### Why Redirect?

Redirection is a fact of life in the modern Web because HTTP applications always want to do three things:

- Perform HTTP transactions reliably
- Minimize delay
- Conserve network bandwidth

For these reasons, web content often is distributed in multiple locations. This is done for reliability, so that if one location fails, another is available; it is done to lower response times, because if clients can access a nearer resource, they receive their requested content faster; and it's done to lower network congestion, by spreading out target servers. You can think of redirection as a set of techniques that help to find the "best" distributed content.

### Where to Redirect

Servers, proxies, caches, and gateways all appear to clients as servers, in the sense that a client sends them an HTTP request, and they process it. Web servers handle requests on a per-IP basis. Distributing requests to duplicate servers means that each request for a specific URL should be sent to an optimal web server (the one nearest to the client, or the least-loaded one, or some other optimization). Redirecting to a server is like sending all drivers in search of gasoline to the nearest gas station.

Proxies tend to handle requests on a per-protocol basis. Ideally, all HTTP traffic in the neighborhood of a proxy should go through the proxy. For instance, if a proxy cache is near various clients, all requests ideally will flow through the proxy cache, because the cache will store popular documents and serve them directly, avoiding longer and more expensive trips to the origin servers.

### Overview of Redirection Protocols

- The browser application that creates the client's message could be configured to send it to a proxy server.
- DNS resolvers choose the IP address that is used for addressing the message. This IP address can be different for different clients in different geographical locations.
- As the message passes through networks, it is divided into addressed packets; switches and routers examine the TCP/IP addressing on the packets and make decisions about routing the packets on that basis.
- Web servers can bounce requests back to different web servers with HTTP redirects.

Browser configuration, DNS, TCP/IP routing, and HTTP all provide mechanisms for redirecting messages. Notice that some methods, such as browser configuration, make sense only for redirecting traffic to proxies, while others, such as DNS redirection, can be used to send traffic to any server.

General redirection methods:

Mechanism             | How it works                                                                                                                                                                                                                             | Basis for rerouting                                                                                  | Limitations
:-------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
HTTP redirection      | Initial HTTP request goes to a first web server that chooses a "best" web server to serve the content. The first web server sends the client an HTTP redirect to the chosen server. The client resends the request to the chosen server. | Many options, from round-robin load balancing, to minimizing latency, to choosing the shortest path. | Can be slow--every transaction involves the extra redirect step. Also, the first server must be able to handle the request load.
DNS redirection       | DNS server decides which IP address, among several, to return for the host- name in the URL.                                                                                                                                             | Many options, from round-robin load balancing, to minimizing latency, to choosing the shortest path. | Need to configure DNS server.
Anycast addressing    | Several servers use the same IP address. Each server masquerades as a backbone router. The other routers send packets addressed to the shared IP to the nearest server (believing they are sending packets to the nearest router).       | Routers use built-in shortest-path routing capabilities.                                             | Need to own/configure routers. Risks address conflicts. Established TCP connections can break if routing changes and packets associated with a connection get sent to different servers.
IP MAC forwarding     | A network element such as a switch or router reads a packet's destination address; if the packet should be redirected, the switch gives the packet the destination MAC address of a server or proxy.                                     | Save bandwidth and improve QOS. Load balance.                                                        | Server or proxy must be one hop away.
IP address forwarding | Layer-4 switch evaluates a packet's destination port and changes the IP address of a redirect packet to that of a proxy or mirrored server.                                                                                              | Save bandwidth and improve QOS. Load balance.                                                        | IP address of the client can be lost to the server/proxy.

## Chapter 21: Logging and Usage tracking

Almost all servers and proxies log summaries of the HTTP transactions they process.

You could log all of the headers in an HTTP transaction, but for servers and proxies that process millions of transactions per day, the sheer bulk of all of that data quickly would get out of hand.

Typically, just the basics of a transaction are logged. A few examples of commonly logged fields are:

- HTTP method
- HTTP version of client and server
- URL of the requested resource
- HTTP status code of the response
- Size of the request and response messages (including any entity bodies)
- Timestamp of when the transaction occurred
- Referer and User-Agent header values

### Log Formats

#### Common Log Format

One of the most common log formats in use today is called, appropriately, the Common Log Format. Originally defined by NCSA, many servers use this log format as a default. Most commercial and open source servers can be configured to use this format, and many commercial and freeware tools exist to help parse common log files.
