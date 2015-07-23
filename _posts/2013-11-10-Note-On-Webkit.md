---
layout: post
title: Webkit revisited
---
[my implementation](https://github.com/longwei/toyBrowser)


“WebKit” is technically the binding layer between WebCore and the ports, though in casual conversation this distinction is mostly unimportant.

WebCore is a layout, rendering, and Document Object Model (DOM) library for HTML and SVG.

Webkit
* parsing
* layout
* text and graphic rendering
* image decoding
* GPU interaction
* network access
* hardware acceleration

So first, WebKit parses HTML the same way. Well, except Chromium is the only port so far to enable threaded HTML parsing support.


but once parsed, the DOM tree is constructed the same. Well, actually Shadow DOM is only turned on for the Chromium port, so DOM construction varies. Same goes for custom elements.

Okay, well WebKit creates a window object and document object for everyone. True, though the properties and constructors it exposes can be conditional on the feature flags enabled.

CSS parsing is the same, though. Slurping up your CSS and turning it into CSS DOM’s pretty standard. Yeah, though Chrome accepts just the -webkit- prefix whereas Apple and other ports accept legacy prefixes like -khtml- and -apple-.
… Layout.. positioning? Those are the bread and butter. Same, right? Come on! Sub-pixel layout and saturated layout arithmetic is part of WebKit but differs from port to port.


1. the DOM, window, document
   * more or less the same
2. the CSSDOM
3. CSS parsing, property/value handling
4. HYTML parsing and DOM construction
5. All layout and positioning
6. Features like contenteditable, pushState, File API, most SVG, CSS Transform math, Web Audio API, localStorage (different layer for localStorage, and diff audio API)

What is not shared?

1. Anything on the GPU, of course.
  *3D transforms
  *WebGL
  *Video decoding
2. 2D drawing to the screen
  *Antialiasing approaches
  *SVG & CSS gradient rendering
3. Text rendering and hyphenation
4. Network stack, SPDY, prerendering, WebSocket transport
5. JS engine
6. Rendering of form controls
7. video & audio element codec
8. image decoding
9. navigating back/forward, pushState()


how about 2D rendering?

```
Webkit
  |
GraphicContext
  |
  -------Qt->QPainter
  Network: QtNetwork
  Fonts: QtInternal
 ``` 

Fonts and Text rendering is a huge part of platform. There are 2 separate text paths in WebKit: Fast and Complex. Both require platform-specific (port-side) support, but Fast just needs to know how to blit glyphs (which WebKit caches for the platform) and complex actually hands the whole string off to the platform layer and says “draw this, plz”.

REF

* [how browser works](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)