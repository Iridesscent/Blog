---
title: "How to build Boost using Visual\_Studio"
id: 304
categories:
  - C++
date: 2016-10-29 22:05:55
tags:
  - Boost
---

转自：[How to build Boost using Visual Studio](https://codeyarns.com/2014/06/06/how-to-build-boost-using-visual-studio/)

真心难找啊，终于找到个说得明白的

If your code compiled with Visual Studio needs Boost, you can get [prebuilt Boost libraries](http://boost.teeks99.com/). However, if your code needs to link with Boost library files, then the Visual Studio version used for your code and used to build Boost has to match. If you cannot find a prebuilt Boost library matching your Visual Studio then you might have to build Boost by yourself.

I had this problem since my code was using C++11 features that required Visual Studio 2015, but the prebuilt Boost libraries were only available for Visual Studio 2012.

To build Boost for Visual Studio, I followed these steps:

*   Download the source of Boost from [here](http://www.boost.org/users/download/). Unzip it anywhere.
*   Open the Developer Command Prompt for Visual Studio. Change to the directory where Boost is unzipped.
*   Build the Boost.Build tool (`b2.exe`) using this command:
<pre class="lang:default decode:true ">&gt; bootstrap.bat</pre>

*   Build Boost using this compile invocation:
<pre class="lang:default decode:true ">&gt; b2 toolset=msvc-14.0 --build-type=complete --abbreviate-paths architecture=x86 address-model=64 install -j4</pre>
`toolset`: Use this to specify the Visual C++ compiler to use. For Visual Studio 2015, this is `msvc-14.0`. For other versions of Visual Studio see [this post](https://codeyarns.com/2014/06/06/how-to-build-boost-using-visual-studio/2014/08/06/visual-studio-versions/).

`architecture`: This is the processor architecture. Keep this `x86` for both 32-bit and 64-bit builds!

`address-model`: Use this to specify whether you want `32` or `64` bit library to be built.

`-j`: Use this to specify how many cores to use for parallel compilation.

*   This compilation took about 30 minutes and after it is done, the Boost header files and built libraries are dumped in `C:\Boost`.
Note: Detailed steps to build Boost for Visual Studio can be found in [this StackOverflow post](http://stackoverflow.com/questions/2629421).

**Tried with:** Boost 1.59, Visual Studio 2015 and Windows 7 x64