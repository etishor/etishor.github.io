---
title: Life with g++
excerpt: The intention of this article is to show a few useful ( i hope ) tips about using g++ in the every day development cycle. Also this article describes three build configurations that should help the developer catch bugs in the early stages of development.
layout: post
permalink: /programming/life-with-g/
views:
  - 8641
dsq_thread_id:
  - 306768952
category: Programming
---
The intention of this article is to show a few useful ( i hope ) tips about using g++ in the every day development cycle. Also this article describes three build configurations that should help the developer catch bugs in the early stages of development.

## Debug Build

Most often you will use this configuration. With this you will have a bigger and slower application but it will be easier to debug. The application will contain no optimizations made by the compiler and will also contain debug symbols witch are needed to run you application inside gdb ( or other debugger ) or if you want to analyze a core dump. For the debug build i suggest you use the following flags to g++:

g++ -W -Wall -Werror -g -fno-inline

The -W and -Wall parameters will enable all the warnings in g++ and the -Werror will make g++ treat the warnings as errors. If g++ is warning you about something it's either a compiler bug ( witch is not very probable if you are using a recent version of g++ ) either something you need to fix. You might thing that the compiler is wrong but in 99.99% of the cases the bastard is right and the warning needs to be fixed. And by fixed i don't mean adding something to quiet down the compiler but really fixed as in correcting the code. The -g option will make g++ add debugging symbols to the binary and the -fno-inline will prevent g++ from inlining any method or function. In some rare cases no-inline will make it easy to debug functions that otherwise would be inline.

## Developer Release Build

The purpose of this build is to catch more warnings and errors that might not occur in the debug build. You should run all the test cases ( if you don't have test cases stop reading and go write them ) against this version. Your application will be compiled with optimizations and also with debug code disabled but with debug symbols witch will only make the binary bigger. My recommendation for the flags is:

g++ -W -Wall -Werror -g -O4 -DNDEBUG

As with the debug build the -W -Wall and -Werror will add warnings and the -g will add the debug symbols. The -O4 will enable the optimizer inside g++. The use of the optimizer in this build is very important because it will enable g++ to find even more warnings than the debug build. Because of this you should do this build at least once a few hours of development or at least before an important commit to the repository. The -DNEBUG will disable all the debug code ( like asserts and debug messages ). You may need to add other defines if your code does not relay on NDEBUG for debug only code.

## Customer Release Build

This is the build that will produce what is given to the customer. This should be an optimized build without debug symbols. This build should contain a build number ( a static string or something similar ) and in the repository you should add a tag or create a branch for the build. If the product is in an alfa/beta stage you might let the debug symbols in. The flags for the build are:

g++ -W -Wall -Werror -O2 -DNDEBUG

The only difference from above is the absence of -g because we don't need debug symbols and the use of -O2 instead of -O4. In the developer build we use O4 to try to catch as many warnings high optimization might reveal but in practice the speed difference between -O4 and O2 is negligible. You should have a good reason for using more than -O2 in this kind of build. Usually computation intensive software ( science , math ) might benefit from using -O3 but for a desktop application -O2 should be more than enough.

## Other Tips

After changing something that affects ABI ( application binary interface ), like adding a function/member, if you receive strange errors you should always make clean and rebuild your application. To speed up the step of make clean and make you may use [ccache][1] witch caches preprocessed c++ files and saves a lot of time parsing included files. Also the build typed described above can be used with other compilers ( C or C++ compilers ), by using the equivalent options.

In conclusion you should listen to your compiler and remember that most of the time you are wrong and he is right.

[1]: http://ccache.samba.org/ "ccache"