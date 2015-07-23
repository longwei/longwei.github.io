---
layout: post
title: Linux Graphic Stack and Wayland
---

3D rendering with OpenGL
===
* A library, “Mesa”, implements the OpenGL API. It uses card-specific drivers to translate the API into a hardware-specific form. If the driver uses Gallium internally, there’s a shared component that turns the OpenGL API into a common intermediate representation, TGSI. The API is passed through Gallium, and all the device-specific driver does is translate from TGSI into hardware commands,
* libdrm uses special secret card-specific ioctls to talk to the Linux kernel
* The Linux kernel, having special permissions, can allocate memory on and for the card.
* Back out at the Mesa level, Mesa uses DRI2 to talk to Xorg to make sure that buffer flips and window positions, etc. are synchronized.

2D rendering with cairo1.
===
* You draw some circles using a gradient. cairo decomposes the circles into trapezoids, and sends these trapezoids and gradients to the X server using the XRender extension. In the case where the X server doesn’t support the XRender extension, cairo draws locally using libpixman, and uses some other method to send the rendered pixmap to the X server.
* The X server acknowledges the XRender request. Xorg can use multiple specialized drivers to do the drawing. A.In a software fallback case, or in the case the graphics driver isn’t up to the task, Xorg will use pixman to do the actual drawing, similar to how cairo does it in its case.
B.In a hardware-accelerated case, the Xorg driver will speak libdrm to the kernel, and send textures and commands to the card in the same way.


As to how Xorg gets things on the screen, Xorg itself will set up a framebuffer to draw into using KMS and card-specific drivers.


X11
===
X11 isn’t just related to graphics; it has an event delivery system, the concept of properties attached to windows, and more. Lots of other non-graphical things are built on top of it (clipboard, drag and drop).

X11 is the wire protocol used by X windows system
Xlib: the reference impl of client side of the X11 system, used by Qt
XCB: Xlib built on it now, has lower level API
Xorg the reference impl of server side

X11 was also designed to be “network transparent”. When the server and client are on the same machine, instead of going over the network, they’ll go over a UNIX socket, and the kernel doesn’t have to copy data around.

cairo: a drawing library that could be used by application or framework. API looks like canvas 
the vector drawing model is from PostScript.

XRender: a extendtion that adds support for anti-aliased drawing primitives, gradients, matrix transforms and more

pixman: pixel level maniplulate. rasterization, gradients, matrices


Modern hardware Acceleration
===
libGL.so comes from Mesa.  Each vendor is supposed to provide its own libGL.so
gallium : state trackers that implement some form of API (OpenGL, GLSL, Direct3D), transform that state to an intermediate representation TGSi, and the backends take that intermediate representation and convert it to the operations that would be consumed by the hardware itself.

TGSI: Tungsten Graphics Shader Infrastructure
GLES: GL Embedded System
GLX: the binding glue between the differences of OpenGL and X11, because OpenGL has no concept of platform and window systems on its own


Xorg Drivers, DRM, DRI
Xorg has the ability to do GPU acceleration.
How? by X11 and Mesa both generate car-specific command, upload the kernel DRM,
libdrm uses a set of generic, private ioctls with the kernel to allocate things on the card, and stuffs the commands and textures and things it needs in there.

app=>Mesa=>libdrm->..GEM...->kernel

KMS: Inside of libdrm and the kernel, there’s a special subsystem that does exactly that, called KMS, which stands for “Kernel Mode Setting”.

exposed: ExposeEvent will sent to program, and it will redraw the contents
redirect, create backing pixmaps for each windows, instead of drawing directly to the backbuffer.
but this mean pixmaps all not showing be default, who will standup to mange which pixels will showing?
This is how compositor come in, set up a composite overlay window,( for example. Compiz, GNOME are using OpenGL to display the redirected windows on the screen).
TFP: It lets an OpenGL program use an X11 Pixmap as if it were an OpenGL Texture.

Compositing window managers grab the pixmap from the X server using TFP, and then render it on to the OpenGL scene at just the right place, giving the illusion that the thing that you think you’re clicking on is the actual X11 window

AIGLX: Accelerated Indirect GLX

(Game FullScreen)copying (or more accurately, sampling) the window texture contents so that it can be painted as an OpenGL texture requires copying data. As such, most window managers have a feature to turn redirection off for a window that’s full-screen. It may sound a bit silly to describe this as unredirection, as that’s the initial state a window is in.

a lot of the input device handling has moved into the kernel with evdev, and things like device hotplug support has been moved back into udev.

Wayland
===
Wayland can use almost the entire stack as detailed above to get a framebuffer on your monitor up and running. without large hacks to support Drag & Drop, clipboard for network transparency.
wayland still have a protocol, but based on Unix sockets and local resources.
Wayland follows the modern desktop’s advice and moves all of this into the window manager process instead. These window managers, more accurately called “compositors” in Wayland terms, are actually in charge of pulling events from the kernel with a system like evdev, setting up a frame buffer using KMS and DRM, and displaying windows on the screen with whatever drawing stack they want, including OpenGL


Wayland clients talk to the compositor and request a buffer. The compositor will hand them back a buffer that they can draw into, using OpenGL, cairo, or whatever. The compositor is at the discretion to do whatever it wants with that buffer
The compositor is also in control of input and event handling
The X server keeps track of where windows are, and it’s up to the compositing window manager to display them where the X server thinks it is, otherwise, confusion happens


Systemd
===
systemd is a system and service manager for Linux, compatible with SysV and LSB init scripts. systemd provides aggressive parallelization capabilities, uses socket and D-Bus activation for starting services, offers on-demand starting of daemons, keeps track of processes using Linux control groups, supports snapshotting and restoring of the system state, maintains mount and automount points and implements an elaborate transactional dependency-based service control logic

REF
===
http://blog.mecheye.net/2012/06/the-linux-graphics-stack/

http://magcius.github.io/xplain/article/
