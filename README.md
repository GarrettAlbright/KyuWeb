# KyuWeb v0.0.1 Alpha 1 (incomplete)

## Introduction

KyuWeb is a proposal for a document-oriented web. It describes using existing technologies in a simplified manner for the purpose of creating a network of primarily text-based documents, interlinked hypertextually in a manner similar to the earliest forms of the World Wide Web. The KyuWeb can be effectively browsed by standard modern web browsers, but by using web technologies in a limited and/or simplified manner, it can also be browsed by dedicated KyuWeb browsers which can be much less resource-intensive than modern browsers have become, and browsing the web effectively on memory-limited 8-bit home computers of the late-'70s and early-'80s should be possible.

KyuWeb uses HTTP/1.1 as a transport mechanism, with some additional HTTP headers, and primarily CommonMark (a strictly-defined successor to Markdown) for document markup.

### Why not use the standard Web?

The current standards for the World Wide Web have brought features and improvements that broaden its scope well beyond the concept of sharing and displaying text documents. HTML 5 has features for playing audio and video and displaying paintable canvases and 3D graphics. HTTP/2.0 demands levels of encryption which are beyond the capabilities of older and more inexpensive hardware, and WebSockets offer levels of networked interactivity which are useless for the needs of simply displaying documents. All of these standards are far more complex to implement than the standards that KyuWeb proposes.

However, things weren't always that way. The HTTP protocol prior to version 2 is quite easy to understand and implement. HTML, especially prior to XHTML and HTML 5, has traditionally been kind of loosey-goosey in how things work; later versions became more explicit on how things should work but also added so many new features that it became quite complex; HTML 5 standardizes well over 100 tags. While using a strict subset of these would be a good option, HTML's basic syntax is still somewhat complex and not fun to write. CommonMark provides a clear and useful list of document-oriented elements which is pleasant to both write, and read - even if the client does not actually parse and render the Markdown document and just displays the raw text instead (which is a valid, albeit not recommended, behavior for a KyuWeb browser).

That CommonMark does not support video, audio, canvases, and so on is to be considered a feature, not a bug, as these complications detract from the goal of a document-oriented web.

### Why not use Gopher or Gemini?

KyuWeb can be thought of as a successor to the concept of Gopher. However, Gopher's "line-oriented" markup status is limited and doesn't lend itself well to long-form documents; it lacks headings and proper inline links, bolding, or italics. In addition, the protocol is a little too simple, not allowing for the metadata in both requests and responses that HTTP headers provide.

Gemini also aims to be a successor to Gopher and has had some success in following through on that promise. However, Gemini defines its own network protocols and markup standards. KyuWeb proposes leveraging existing standards, thus avoiding "reinventing wheels." One major benefit to this is that, particularly on newer operating systems, developers can largely leverage existing libraries for serving and retrieving files via HTTP and parsing documents with CommonMark. Even in the cases where a developer will have to create their own implementations for these standards due to lack of support for existing libraries for the operating system and/or hardware they are targeting, the standards KyuWeb uses have been around a very long time and are likely to be familiar to many developers already, and documentation for them is boundless.

## HTTP/1.1 as KyuWeb knows it

(incomplete)

## CommonMark as KyuWeb knows it

(incomplete)

## KyuWeb browser behavior

(incomplete)

## KyuWeb and standard modern web browsers

(incomplete)

## Appendix

### Etymology

The "Kyu" in "KyuWeb" is meant to symbolize the middle part of the word "document," as well as being similar to the *onyomi* pronunciation of the Japanese character 急 (キュウ, *kyu̅*), meaning "sudden" or "fast."

The name "DocWeb" was initially going to be used for this proposal, but was abandoned for fear it could be confused with "Dark Web" (web sites and services used for sketchy if not outright illegal purposes) in verbal contexts.

### History

I first started fleshing out the ideas for KyuWeb in a conversation on the #lua channel on the Libera IRC network on the 10th of February, 2022. I thank the other people in that channel for entertaining my rambling and giving me their ideas and criticism to consider.
