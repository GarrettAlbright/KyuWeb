# KyuWeb v0.0.1 Alpha 1 (incomplete)

## Introduction

KyuWeb is a proposal for a document-oriented web. It describes using existing technologies in a simplified manner for the purpose of creating a network of primarily text-based documents, interlinked hypertextually in a manner similar to the earliest forms of the World Wide Web. The KyuWeb can be effectively browsed by standard modern web browsers, but by using web technologies in a limited and/or simplified manner, it can also be browsed by dedicated KyuWeb browsers which can be much less resource-intensive than modern browsers have become, and browsing the KyuWeb effectively on memory-limited 8-bit home computers of the late-'70s and early-'80s should be possible.

KyuWeb uses HTTP/1.1 as a transport mechanism (with some additional non-mandatory HTTP headers and minor changes in behavior), and primarily CommonMark (a strictly-defined successor to Markdown) for document markup.

### Why not use the standard Web?

The current standards for the World Wide Web have brought features and improvements that broaden its scope well beyond the concept of sharing and displaying text documents. HTML 5 has features for playing audio and video and displaying paintable canvases and 3D graphics. HTTP/2.0 demands levels of encryption which are beyond the capabilities of older and more inexpensive hardware, and WebSockets offer levels of networked interactivity which are useless for the needs of simply displaying documents. All of these standards are far more complex to implement than the standards that KyuWeb proposes. However, things weren't always that way. The HTTP protocol prior to version 2 is quite easy to understand and implement.

Then there's HTML. HTML, especially prior to XHTML and HTML 5, has traditionally been kind of loosey-goosey in how things work. Later versions became more explicit on how things should work, but also added so many new features that it became quite complex; HTML 5 standardizes well over 100 tags. While using a strict subset of HTML 5 would be a good option, HTML's basic syntax is still somewhat complex and not fun to write. CommonMark provides a clear and useful list of document-oriented elements which is pleasant to both write and read - even if the client does not actually parse and render the Markdown document and just displays the raw text instead (which is a valid, albeit not recommended, behavior for a KyuWeb browser).

That CommonMark does not support video, audio, canvases, and so on is to be considered a feature, not a bug, as these complications detract from the goal of a document-oriented web.

### Why not use Gopher or Gemini?

KyuWeb can be thought of as a successor to the concept of Gopher. However, Gopher's "line-oriented" markup status is limited and doesn't lend itself well to long-form documents; its documents lack headings and proper inline links or styling. In addition, the protocol is a little too simple, not allowing for the metadata in both requests and responses that HTTP headers provide.

Gemini also aims to be a successor to Gopher and has had some success in following through on that promise. However, Gemini defines its own network protocols and markup standards. KyuWeb proposes leveraging existing standards, thus avoiding "reinventing wheels." One major benefit to this is that, particularly on newer operating systems, developers can largely leverage existing libraries for serving and retrieving files via HTTP and parsing documents with CommonMark. Even in the cases where a developer will have to create their own implementations for these standards due to lack of support for existing libraries for the operating system and/or hardware they are targeting, the standards KyuWeb uses have been around a very long time and are likely to be familiar to many developers already, and documentation for them is boundless.

## KyuWeb Specification: Transport

### Basics

KyuWeb uses HTTP/1.1 for communication between clients and servers. KyuWeb browsers make HTTP requests to servers, which respond with HTTP responses. With the exception of the custom headers below, the questions of "Does KyuWeb support X?" or "How does KyuWeb support Y?" in terms of data transport are answered with "Refer to the HTTP/1.1 spec."

### The `Accept-KyuWeb` request and `KyuWeb` response headers

A dedicated KyuWeb browser *must* send a `Accept-KyuWeb` header. The value of this header will correspond to the major and minor versions of the KyuWeb specification that the browser wishes to communicate with; for example, as of the writing of this document, `Accept-KyuWeb: 0.0` (although the first actual software which implements these standards will probably use `Accept-KyuWeb: 0.1`).

A KyuWeb server which is sending a response intended to be interpreted by a KyuWeb response by clients capable of handling such *must* send a `KyuWeb` response header with the version of the KyuWeb standards the response corresponds to; eg, `KyuWeb: 0.1`.

If a server sees that a request includes an `Accept-KyuWeb` header, it *must* send a KyuWeb response if it is capable of doing so. If it does *not* see an `Accept-KyuWeb` header, it *may* send a standard HTTP web response.

### The `Accept-Length` request header

A KyuWeb browser *may* send an `Accept-Length` header the value of which is the maximum size of a file, in bytes, that it is capable of receiving. If such a header is sent, the server *must* respond with a response with a body smaller than or equal to the size specified in this header, or respond with an error as outlined in the "Not acceptable responses" section below.

For example, a browser which only has 2 MB of RAM to work with may wish to send an `Accept-Length: 524288` header to ensure responses are no larger than .5 MB in size, ensuring the browser has sufficient memory in which to store, parse, and process the response.

If a KyuWeb browser sends an `Accept-Length` header *and* a `Range` header, the `Accept-Length` header *must* be ignored if the server is fulfilling the `Range` header request and only sending a limited range of a document. If the server is ignoring the header and sending the entire document instead, the `Accept-Length` header *must* be respected as normal.

### The `Title` response header

A server *should* provide a document title by way of the `Title` response header. A client *may* use this title in a manner similar to the `<title>` tag in HTML, such as placing it in the title bar of windows; using it to identify the document in bookmark and history lists; etc.

### Content types

All KyuWeb responses *must* have a Content-Type header.

KyuWeb browsers *must* accept and display content of the types `text/plain` and `text/markdown`. Content of other types may be displayed, downloaded and/or opened with a helper application. Note that this means that KyuWeb browsers *may,* but also may not, support images in Markdown documents. See the "KyuWeb specification: Markup" section below for more information on how KyuWeb browsers are expected to handle Markdown documents.

KyuWeb browsers *may* accept a `text/html` document and attempt to find and display any Markdown wrapped within it according to the process described in the "HTML-wrapped Markdown Documents" section below.

### Not acceptable responses

If a server cannot fulfill a response *strictly* according to the `Accept` request headers, it *must* send a `406 Not Acceptable` response, in addition to whatever other format of response it is capable of (eg, a server which can send a KyuWeb response, but not one corresponding to the requested KyuWeb version, *should* send a KyuWeb response in whatever version it is capable of doing so). The contents and format of the body of the response is undefined, but a plain text or Markdown response that can be shown by a KyuWeb browser is recommended.

## KyuWeb Specification: Documents

### Markdown

KyuWeb documents are comprised of text marked up with Markdown according to the current  [CommonMark specification](https://spec.commonmark.org), with one exception; any HTML in the document *must not* be interpreted as HTML, but rather as plain text. KyuWeb browsers *should* parse and display these documents as close to specification as possible, but *may* display raw unparsed markup if it does not yet (or never will) support some particular Markdown/CommonMark feature.

KyuWeb browsers *may* support auto-linked URLs and tables as per the [GitHub Flavored Markdown specification](https://github.github.com/gfm/).

Documents *should* be written with the expectation that a browser may not be able to display images directly in documents (for example, it may be a CLI-based browser, or the user may have optionally disabled images) and/or the user may be visually impaired. Thus, all images in documents should contain helpful descriptions.

When a document served by a KyuWeb server contains only markup, it *must* be served with the `text/markdown` value for the `Content-Type` header.

### HTML-wrapped Markdown documents

In order to improve interoperability between KyuWeb and the standard web, it is also possible to wrap KyuWeb documents in HTML. The intent here is that the HTML page will link to a JavaScript-powered client-side Markdown interpreter to parse the KyuWeb page body with, as well as CSS files to style said page. However, KyuWeb-exclusive browsers will ignore the HTML parts of the document and only use the Markdown part of the document.

A HTML-wrapped Markdown document *must* be served with a `text/html` value for the `Content-Type` header. This will indicate to the client that it should used the wrapped HTML approach for extracting the document content.

To find the Markdown wrapped in an HTML document, the KyuWeb client *must* use the following rules;

1. Find a line which begins with the sequence `<!-- KyuWeb Doc Start /-->`. Ignore the entire content of that line up to and including the next line ending (as defined in the [CommonMark spec](https://spec.commonmark.org/0.30/#line-ending)). The Markdown document begins on the next line (the byte following the line ending.
2. Find a line which ends with the sequence `<!-- KyuWeb Doc End /-->` followed by a line ending. Ignore the entire content of that line. The Markdown document ended at the beginning of that line (the bytes up to and including the line ending that proceeded that line.)

All bytes outside of the sequence captured by the rules above are ignored. The captured bytes are then interpreted as per the rules above.

What follows is an example of a HTML-wrapped Markdown document.

```html
<!doctype html>
<html>
  <head>
    <title>An Example HTML-wrapped Markdown Document</title>
    <script type="text/javascript" src="/scripts/commonmark-parser.js"></script>
    <script type="text/javascript" src="/scripts/kyuweb-parser.js"></script>
    <link type="text/css" href="/styles/kyuweb-styles.css" rel="stylesheet" />
  </head>
  <body>
    <h1> Hello, world! A KyuWeb document starts below.</h1>

<!-- KyuWeb Doc Start /--><pre id="kyuweb-doc">
*Hello, world!* This is a KyuWeb document.

Thanks for checking it out!
</pre><!-- KyuWeb Doc End /-->

    <p>Wow. That was an interesting experience, wasn't it?</p>
  </body>
</html>
```

The JavaScript included in the HTML document could then find the Markdown embedded in the element with the `kyubweb-doc` element and pass it through a CommonMark parser. It could then remove the `<pre>` element and replace it with the HTML that the parser has created. If the web browser does not support JavaScript, then the Markdown document source would be visible within the page and be as readable as a marked-up plain text document.

To a KyuWeb browser, once the HTML parts of the document are "trimmed away" using the rules above, the document is identical to the following:

```
*Hello, world!* This is a KyuWeb document.

Thanks for checking it out!

```

With this approach, a web server can easily output documents readable both by standard web browsers and KyuWeb browsers.

All this being said, a server *should,* when presented with a `Accept-KyuWeb` header, respond with a `text/markdown` document whenever possible. Furthermore, If a client does not list the `text/html` type in its `Accept-Type` request header, a server *may not* respond with an HTML-wrapped Markdown document.

### Text encoding

As with HTTP and HTML, KyuWeb documents may be provided in any given text encoding. Thus, a server *should* provide a `charset` parameter on the `Content-Type` header, such as `text/markdown;charset=US-ASCII`), such that clients can make a best effort to display the document correctly if it is capable of doing so.

## KyuWeb and standard web browsers

## Appendix

### Etymology

The "Kyu" in "KyuWeb" is meant to symbolize the middle part of the word "document," as well as being similar to the *onyomi* pronunciation of the Japanese character 急 (キュウ, *kyu̅*), meaning "sudden" or "fast."

The name "DocWeb" was initially going to be used for this proposal, but was abandoned for fear it could be confused with "Dark Web" (web sites and services used for sketchy if not outright illegal purposes) in verbal contexts.

### History

I first started fleshing out the ideas for KyuWeb in a conversation on the #lua channel on the Libera IRC network on the 10th of February, 2022. I thank the other people in that channel for entertaining my rambling and giving me their ideas and criticism to consider.

### On metadata (YAML sucks)

Recently, some systems have developed and adapted the "front matter" file format which consists of a Markdown/CommonMark document prepended by metadata in [YAML](https://yaml.org) format. While there is some need for metadata to go along with Markdown documents as KyuWeb describes, front matter is not an appropriate format for this purpose. YAML is a [very poor choice](https://noyaml.com) for the purpose of serializing data due to, among other things, its ambiguities and, more relevant to our case, its complexity.

In the current specification, such as it is, any metadata is implemented via HTTP headers, such as the Title header mentioned above. I find this satisfactory for now. However, if I manage to be convinced that in-document metadata is necessary in the future, I propose "headers" are in the same format as HTML headers at the beginning of the document, with two line breaks separating the block of headers from the body of the document. Thus, at the byte level, the document would appear to have two blocks of identically-formatted headers before the body, but the first block would be consumed by the HTTP handling code, whereas the second block would be consumed by the KyuWeb parser. This format will allow headers to be parsed with the same code that is used to parse the HTTP headers.

However, for the time being, I find using solely HTTP headers to be sufficient for the time being.
