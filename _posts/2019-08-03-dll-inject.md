---
layout: post
title:  "What is manual dll inject"
categories: windows
tag: dllinject
---

* toc
{:toc}


When trying to get my copypasta CS:GO internal hacking to work, I need to figure out how
to inject my freshly compiled DLL into the game process. Googling around and ppl say 
manual ddl inject is the better way of doing dll injection. Hmm, but how better? Compared with what?
So I digged a bit further about the topic and here is wat I learned. Before we touched on the manual dll inject, let's first see what
is `Loadlibrary`.

## Loadlibrary(A)
Doing a google serch and the top result would probably be a link to the official windows
api page. That is right, LoadLibraryA is an API exposed by the windows kernel. Citing from
the api documentation, it will:
> Loads the specified module into the address space of the calling process. The specified module may cause other modules to be loaded.

If all we need is to load a dll into a process, all we need to do is to call `LoadlibraryA`.
So why don't all the injectors simple use this method given how simple it is?  
Well, some injectors do. But others prefer a more stealth way of inject, as it is kinda "loud" by using `LoadLibrary`. When calling `LoadLibraryA`, the DLL will be registered in the PEB's
module list. Thus, a monitoring can easily iterate through every DLL loaded into the target
process and find out something potentially bad is there. Entering manual dll injection.


## Manual DLL Inject
Now we know we want to inject a DLL into the target process, but sliently.
This is where we can use the manual DLL inject. As the name suggests, it will inject a DLL
manually, bypassing the need to invoke `LoadLibrary`. The main benefit of doing this is of
course not to register the DLL we load into the moudle list, making it harder to spot. But
it does come with additional cost, as we now need to do all the manual work that is done by
`LoadLibrary`.

Here is how manual DLL inject works roughly:
1. Open the DLL file and read its content.
2. Allocate space for the DLL content and the shellcode (resolve imports and excute the 
entrypoint)
3. Copy the DLL content into the allocated space.
4. Copy the shellcode into the allocated space.
5. excute the shellcode (in a new thread).


## Conclusion
Cool, so now we have some understanding of what is manual dll inject and why we need it in some
cases, now we want to actually use it. There are lots of injectors you can find online, if you
don't really want to write one your own. [GH injector](https://guidedhacking.com/resources/guided-hacking-dll-injector.4/)
and [Xenos](https://github.com/DarthTon/Xenos) are two pretty famous ones. But of course, other
injectors would work as well. Just be sure that you pay attention to whether the injector you
use employs `LoadLibaray` or manual dll inject.


In the [next post]({% post_url 2019-08-05-dll-inject2 %}) I will walk through how to create a manual dll injector from sketch.


## Reference
https://www.elitepvpers.com/forum/c-c/4175990-how-does-manual-mapping-work-example.html

https://www.securitynewspaper.com/2017/05/01/dll-injection-using-loadlibrary-c/

http://blog.opensecurityresearch.com/2013/01/windows-dll-injection-basics.html

## Interesting proj
https://github.com/fdiskyou/injectAllTheThings

