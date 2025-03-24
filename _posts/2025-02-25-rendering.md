---
layout: post
title: rendering random thoughts
---

I haven't work on rendering for a while, and I always on the Qt ship.
I am keep getting question about build embedded application for startup.

let's get to the basic of the GUI rendering.

1. layout engine
where should I put that box? not only that one box, but also all the children box and its sibilings.
box size, or calculated from flex
open source: yoga

so the GUI can render the logic layout to the actual layout

2. rendering engine
GPU: skia. but it have the common issue with GPU.
CPU: blend2D

the dependecies: webp, jpeg freetype, shader..etc

3. DSL
QML, HTML/CSS

4. scripe engine
why? because seperatation of static and dynamic part.
Qt v4

5. message engine
signal/slot and cross-thread

