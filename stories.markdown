---
layout: default
title: Useful CppUTest Application Stories
---

# Useful CppUTest Application Stories

## Table of Content

* [Unit Testing With IAR Embedded Workbench](#iar)
* [Guide to setup CppUTest for Eclipse in Windows 7](#eclipsewindows7)

<a id="iar"> </a>

## Unit Testing With IAR Embedded Workbench

By Heath Raftery

[The original post in CppUTest google group](https://groups.google.com/forum/#!topic/cpputest/WxCfnVZYGHw)

I've just completed an assessment of CppUTest for adoption as our company's standard Unit Testing framework. It was a bit of a battle at times but I've come out victorious so would like to share my experience for three reasons: see if there's anything I could be doing better; provide a crumb trail for other pioneers; and also contribute to CppUTest itself.

One of our strict criteria is compatibility with IAR Embedded Workbench 6.4 projects. We don't want to have to maintain a separate build environment just to run tests. Here's what I found:

Pulling from git source was best way to download, since that simultaneously allows us to make local changes to enable a build and store it all in our own repositories, while still allowing synchronisation with changes to the CppUTest source. As a bonus, for our users that don't want to know about git, as far as they're concerned they're pulling the software from our (SVN) repository.

To build the CppUTest library in IAR, I created a new empty IAR project in the root folder of cpputest and made the following changes:

<pre>
Project -> Options -> General Options -> Target -> Core = Cortex-M3.
Project -> Options -> General Options -> Output -> Output file = Library
Project -> Options -> General Options -> Output -> Executables/libraries = Debug (removed exe subdirectory)
Project -> Options -> C/C++ Compiler -> Language 1 -> Language = C++
Project -> Options -> C/C++ Compiler -> Language 1 -> Language conformance =  Standard
Project -> Options -> C/C++ Compiler -> Language 1 -> C++ Dialect = C++ (leave exceptions checked)
Project -> Options -> C/C++ Compiler -> Preprocessor -> Additional include directories = $PROJ_DIR$\include
Project -> Options -> C/C++ Compiler -> Diagnostics -> Suppress these diagnostics = Pa050 (turn off warning about non-standard line endings)
Added all .cpp files in src\CppUTest\
Added src\Platforms\Iar\UtestPlatform.cpp
</pre>
*Changed line 89 of UtestPlatform.cpp from return 1; to return t; which enables timing.
CppUTest then builds successfully in both Debug and Release configurations, producing a CppUTest.a file*

To build the CppUTest tests, I created a new empty IAR project in the root folder of cpputest, called it CppUTestTest, and made the following changes:

<pre>
Project -> Options -> General Options -> Target -> Core = Cortex-M3.
Project -> Options -> General Options -> Library Configuration -> Library low-level interface implementation = Semihosted
Project -> Options -> C/C++ Compiler -> Language 1 -> Language = Auto
Project -> Options -> C/C++ Compiler -> Language 1 -> Language conformance =  Standard
Project -> Options -> C/C++ Compiler -> Language 1 -> C++ Dialect = C++ (leave exceptions checked)
Project -> Options -> C/C++ Compiler -> Preprocessor -> Additional include directories = $PROJ_DIR$\include
Project -> Options -> C/C++ Compiler -> Diagnostics -> Suppress these diagnostics = Pa050 (turn off warning about non-standard line endings)
Project -> Options -> Linker -> Config -> Override default -> Edit -> Stack/Heap Sizes -> CSTACK = 0x600
Project -> Options -> Linker -> Config -> Override default -> Edit -> Stack/Heap Sizes -> HEAP =  0x8000
Added all .cpp and .c files in tests\
Added Debug\CppUTest.a
Changed line 16 of tests\AllocLetTestFree.c to explicit cast to (AllocLetTestFree) to satisfy compiler
Changed line 22 of tests\AllocLetTestFree.c to type AllocLetTestFree instead of void* to satisfy compiler
Changed tests\AllTests.cpp to declare a const char*[] with "-v" as the second element, so it can be passed to RunAllTests to turn on verbose mode in IAR
Built and ran in simulator.
Turned on Debug->C++ Exceptions->Break on uncaught exception to intercept mysterious jumps to abort.
With that out of the way I did the same for CppUTestExt and CppUTestExtTester, with no further dramas.
</pre>
The stack/heap allocations were probably the largest source of drama, firstly because it wasn't clear that an out of memory situation had occurred. If the heap was exhausted a malloc/new would return zero, throw an exception and the debugger would jump to abort with no sign of the offensive statement. Turning on "Break on uncaught exception" helped. If the stack was exhausted, program behaviour was very hard to predict, and often it was in the course of trying to print out a useful error message that the stack would get corrupted! I found values of 0x600 and 0x8000 were narrowly enough to allow completion execution of the tests. It was a very tight fit though, since the Cortex-M3 architecture in IAR has 64kB of RAM on chip. With those allocations the map file showed these totals for the RAM region:

<pre>
Static: 21056 bytes
Heap: 32768 bytes
iar.dynexit (atexit statics): 8760
Stack:  1536 bytes
Total:  64120 bytes out of 65535 bytes.
</pre>
Turning off "Destroy static objects" might have given us another 8760 bytes to play with, but it's still pretty tight.

As already noted, I also needed to make two changes to the source to build and run correctly. As far as I can see, these could be applied to the master:

<pre>
src\Platforms\Iar\UtestPlatform.cpp:89 (return t;)
tests\AllocLetTestFree.c:16 and  tests\AllocLetTestFree.c:22 (explicit types)
</pre>
The third change to AllTests.cpp will probably only be important for IAR users, because there's no option to set command line arguments of the target executable in IAR.

With the libraries and their tests built and run successfully, the next step was to create a test for a real project. I found the following to be a suitable procedure:
Add a folder called tests to the target project's source hierarchy.
Create AllTests.cpp with this content:

{% highlight c++ %}
#include "CppUTest/CommandLineTestRunner.h"
int main(int ac, char** av)
{
  const char * av_override[] = { "exe", "-v" }; //turn on verbose mode

  //return CommandLineTestRunner::RunAllTests(ac, av);
  return CommandLineTestRunner::RunAllTests(2, av_override);
}

And create MyCodeTest.cpp with this content:
extern "C"
{
#include "..\MyCode.h"
}

#include "CppUTest/TestHarness.h"

TEST_GROUP(FirstTestGroup)
{
  void setup() {}
  void teardown() {}
};

TEST(FirstTestGroup, FirstTest)
{
  FAIL("Fail me!");
}

TEST(FirstTestGroup, SecondTest)
{
  STRCMP_EQUAL("hello", "world");
}
{% endhighlight %}

Then in the same directory as your target project's project file, create a new C++ main project called MyProjectTest. Make the following changes to Project -> Options:

<pre>
General Options -> change Device to your target device
General Options -> Library Configuration -> check "Use CMSIS" if it used in your target project
C/C++ Compiler -> Language 1 -> Language = Auto
C/C++ Compiler -> Language 1 -> C++ Dialect = C++
C/C++ Compiler -> Preprocessor -> Additional include directories = path\to\cpputest\include
C/C++ Compiler -> Preprocessor -> add any necessary #defines from the target project to Defined symbols
C/C++ Compiler -> Diagnostics -> Suppress these diagnostics = Pa050
Linker -> Config -> Override default with icf file used by target project
</pre>
Ensure CSTACK is at least 0x600 and HEAP is at least 0x5000. If your target project already uses those regions, expand accordingly, otherwise ensure they're placed somewhere where they'll fit.

Remove main.cpp and add AllTests.cpp, MyCodeTest.cpp and Debug\CppUTest.a (use Debug version so breakpoints can be used in code and there's an inconsequential performance/size hit).
Create a new Group called src and add source files from the target project as necessary.
Use the simulator to debug the executable and run the tests.

Show the Terminal I/O window to see the output. In non-verbose mode a dot is written for a successful test and an exclamation mark for an ignored test.

There's lots of other details to be considered such as Workspaces, build configurations, and what to do when things go wrong, but hopefully that's enough to get started.

So that's about it. At the end of the day, CppUTest looks like it will meet our needs nicely. Once the initial setup is done, it integrates well into IAR and the output is readable within the IDE. The simulator provides a suitable alternative to execution on the PC (which means having to maintain a build with a different compiler) and execution on the hardware (which requires working hardware, Flash writes, functioning peripherals and is difficult to inject test vectors into).

<a id="eclipsewindows7"> </a>

## Guide to setup CppUTest for Eclipse in Windows 7

By Miguel Mora Perea

Can be found [at the github page](https://github.com/miguelmoraperea/guide_setup_cpputest_eclipse_win_7)

