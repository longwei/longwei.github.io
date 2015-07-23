---
layout: post
title: Random note for Linux 
---

1. Describe direct memory access (DMA). Can a user level buffer / pointer be used by kernel or drivers?

   why DMA? because some time, we just need move data around, don't need to go through CPU to put data in memory.

	HOW?
	DMA controller. send DMA request
	CPU grants access by asserting DMA acknowledge signal
	DMA reads/writes
	DMA asserting bus release signal to notify CPU


2. What is a Branch Target buffer? Explain how it can be used in reducing bubble cycles in cases of branch misprediction.
	BTB reduces the penalty by caching brach target address.
	it is like a hashmap with {pc: conditional address}. if hit, the address could be known at fetch stage


3. Explain the following terms: virtual memory, page fault, thrashing.
	knowleage-based 的问题就不隐藏了
	process 需要的memory 和真实 memory物理存储介质之间的abstract layer. 有个这个抽象之后，具体的address可以被mapping到各种奇奇怪怪的地方，而不必限制于RAM。
	page fault： accesses a page that is mapped in address space, but not loaded in physical memory。 出现这种错基本是指针指出了界。
	trashing: Thrash means that increasing resources are used to do a decreasing amount of work.



* read memory from another program  by get all the usused memor
	the hardware provide privilege levels. the central piece of it is kernel. application obtain RAM by pages. if application want to access a page belonging to another. here comes the wrath of kernel(!segmentation fault!).
	when application exit, the page is no longer need, the kernel takes control of that page, wipe it and put it back to pool. (ptrace() come to rescue.)
	the RAM the application see is not have to be the true memory. the illustion is maintained by swapping RAM contents with a dedicated space on disk. (virtual meomory).
	The keneral promise to bring back this page when needed. 


* Write a step by step execution of things that happen after a user presses a key on the keyboard. Use as much detail as possible.

follow up, what happen if user is browing internet, how events like onClick() will be trigged?

[what happen when you googling](https://github.com/alex/what-happens-when/blob/master/README.rst)

1. keyboard controller received a scan code.
2. the keyboard controller interprets the code and stores it in buffer.
3. the keyboard controller send a hw interrupt to cpu. (via putting signal on "interrupt request line: IRQ1")
4. the interrupt controller unmask signal to maps IRQ1 into INT 9.
5. the process stop current task and invoke interrupt handler, fetch address of ISR from interrupt vector table.
6. ISR read scan code from port 60h, and decide whether to process it or pass the control to program for taking action.

hw and os part done, continue on sw stack

7. GUI toolkit(Qt/GTK/MFC)capature this event.
8. Event passed to Webkit(Webcore)
9. Webcore delegate this event to javascript engine's DOM document.
Javascript Engine(part of Webkit) finally receive this Event.
(when Webkit build DOM tree and Render tree. if it meets onClick() attribute, it will register a unique eventlistener on document 
document handle all eventlisteners(jsLazyEventListener).)
10. windows.document is singleton that handle all eventlisteners.
11. pasing the Render Tree to find the eventTargetNode(in DOM) which contains all the event tree nodes.
12. get attributes of onClick() on those html nodes.
13. parse this String into a AST tree to intepret it. function definiton, parameters, var....
14. exec() it.






