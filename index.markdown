---
layout: default
title: CppUTest
---

## What is CppUTest.

CppUTest is a C /C++ based unit xUnit test framework for unit testing and for test-driving your code. It is written in C++ but is used in C and C++ projects and frequently used in embedded systems but it works for any C/C++ project.

CppUTest's core design principles are:

* Simple in design and simple in use.
* Portable to old and new platforms.
* Build with Test-driven Development for Test-driven Developers.

## Setting up CppUTest

There are several ways to setup CppUTest.  One is to install via package management and the other is from source. The big difference is that from source you can use **MakefileWorker.mk**. MakefileWorker is not supported pre-packaged.  MakefileWorker does not require you to know a lot about **make** and makefiles to get started.

An easy way to get your first test case running is to use James Grenning's [cpputest-starter-project for gcc](https://github.com/jwgrenning/cpputest-starter-project) or [cpputest-starter-project for Visual Studio](https://github.com/jwgrenning/cpputest-starter-project-vs).  James is the author of [Test-Driven Development for Embedded C](https://wingman-sw.com/tddec).  You'll find instructions, your first test case, and some other example code.  James' training resources use MakefileWorker, so you need to install from source.

Adding tests to untested C and C++ can be a big challenge.  You might find [Get your Legacy C into a Test Harness](https://wingman-sw.com/articles/tdd-legacy-c) a useful recipe and resource. The page includes links to numerous articles of real legacy C challenges.

### Pre-packaged install

*Linux*

There is a Debian and an Ubuntu package available for CppUTest. This is by far the easiest way to install it, via:

{% highlight bash %}
$ apt-get install cpputest
{% endhighlight %}

*MacOSX*

For Mac, a Homebrew package is available too. You can install via:

{% highlight bash %}
$ brew install cpputest
{% endhighlight %}

### From source install

You can download the latest 'automatically released' version:

* [Latest version passing the build](https://github.com/cpputest/cpputest.github.io/blob/master/releases/cpputest-3.8dev.tar.gz?raw=true)
This version is automatically packages after a build has passed.

Alternatively, you can clone the github repository, read-only:

{% highlight bash %}
$ git clone git://github.com/cpputest/cpputest.git
{% endhighlight %}

Or clone it via ssh (which requires a github account)

{% highlight bash %}
$ git clone git@github.com:cpputest/cpputest.git
{% endhighlight %}

CppUTest can also be added to your git repo as a git submodule.

{% highlight bash %}
$ git submodule add https://github.com/cpputest/cpputest.git
{% endhighlight %}

Now that you have CppUTest sources, you can build it with your favorite build tool (CMake or autoconf).

Building with autoconf requires you to (this requires you to have installed GNU autotools, apt-get/brew install automake autoconf libtool):

{% highlight bash %}
$ cd cpputest_build
$ autoreconf .. -i
$ ../configure
$ make
{% endhighlight %}

**NOTE**: Building from **cpputest_build** means you will not be able to use **MakefileWorker.mk**.  To use MakefileWorker you need to build from the cpputest home directory.

### Using CppUTest with MakefileWorker.mk and gcc

If you want to use CppUTest's MakefileWorker, you cannot currently get CppUTest using the "Pre-packaged" options described above. Instead you can get CppUTest from source using the options already described.

Change to the top level directory of CppUTest (the directory containing **include/** and **src/** among other files)

{% highlight bash %}
$ cd cpputest \
$ autoreconf . -i
$ ./configure
$ make tdd
$ export CPPUTEST_HOME=$(pwd).
{% endhighlight %}

You will want to add **export CPPUTEST_HOME=<path>** somewhere like **.bashrc** or in your build script as a relative path.

### Using CppUTest with Visual Studio

You can build CppUTest using cmake or in the Visual Studio IDE.

*from Visual Studio IDE*

Depending on your VS version double click either

* **CppUTest_VS201x.sln** - for VS 2010 and later
* **CppUTest.sln** - for pre VS 2010

Say yes to suggested conversions.  Select the menu item corresponding to run without debugging.  CppUTest should build (probably with warnings).  When the build completes the test runner runs. You should see over 1000 tests passing and no test failures. The build also produced a static library (cpputest/lib) holding CppUTest you can link your tests to.

To use CppUTest, define an environment variable **CPPUTEST_HOME** that points to the home directory of CppUTest.  You will find a working example and some more help in [cpputest-starter-project for Visual Studio](https://github.com/jwgrenning/cpputest-starter-project-vs).

## How to create a coverage report

You can use autoconf to create a coverage report for CppUTests own tests. If you have lcov installed, a browsable html report will be generated as well. After the steps outlined in the previous paragraph, do the following:

{% highlight bash %}
$ make check_coverage
{% endhighlight %}

This will generate a file called gcov_report.txt with the coverage report in plain text format. It will also generate an HTML file called gcov_report.txt.html. If you have lcov installed, you will be able to browse the lcov report by opening ./cpputest_build/test_coverage/index.html . The lcov report is by far the easiest way to inspect CppUTest's own test coverage.

Alternatively, you can use CMake if that is the build tool you fancy (this requires you have install CMake, apt-get install cmake):

{% highlight bash %}
$ cd cpputest_build
$ cmake ..
$ make
{% endhighlight %}

For Windows users, the above will work with cygwin. There are also several MS VC++ projects available.

## Where to find more information

* If you have any questions, check out the [Google Groups](https://groups.google.com/forum/?fromgroups#!forum/cpputest)
* The source is at the [main github page](https://github.com/cpputest/cpputest)
* You can report bugs or features at [the issues page](https://github.com/cpputest/cpputest/issues)
* You can follow [CppUTest on twitter](https://twitter.com/CppUTest)

## Quick introduction (some code!)

To write your first test, all you need is a new cpp file with a TEST_GROUP and a TEST, like:

{% highlight c++ %}
TEST_GROUP(FirstTestGroup)
{
};

TEST(FirstTestGroup, FirstTest)
{
   FAIL("Fail me!");
}
{% endhighlight %}

This test will fail.

You can add new tests to the test group by just writing more tests in the file, like this:

{% highlight c++ %}
TEST(FirstTestGroup, SecondTest)
{
   STRCMP_EQUAL("hello", "world");
   LONGS_EQUAL(1, 2);
   CHECK(false);
}
{% endhighlight %}

You do need to create a main where you run all the unit tests. Such a main will look like this:

{% highlight c++ %}
int main(int ac, char** av)
{
   return CommandLineTestRunner::RunAllTests(ac, av);
}
{% endhighlight %}

For more information, We'd recommend [reading the manual](manual.html) or, even better, check some [existing tests](https://github.com/cpputest/cpputest/tree/master/tests) such as [SimpleStringTest](https://github.com/cpputest/cpputest/blob/master/tests/CppUTest/SimpleStringTest.cpp) or (a bit more complicated) [MemoryLeakDetectorTest](https://github.com/cpputest/cpputest/blob/master/tests/CppUTest/MemoryLeakDetectorTest.cpp) or the [mocking tests](https://github.com/cpputest/cpputest/blob/master/tests/CppUTestExt/MockSupportTest.cpp) or just check out the [Cheat Sheet](https://github.com/cpputest/cpputest/blob/master/tests/CppUTest/CheatSheetTest.cpp)

## Related projects

For Eclipse users, also check:
* [CppUTest Eclipse Test Runner](https://github.com/tcmak/CppUTestEclipseJunoTestRunner) This will allow you to run your tests JUnit style with red and green bars, and rerun arbitrary selections of tests.
Prerequisites:
  - CppUTest off master
  - Eclipse Juno, Kepler, or later
  - Eclipse C/C++ Unit Plugin (if not already present in your version of Eclipse; install directly from Eclipse Help -> Install New Software...).
* [CppUTest Eclipse Plugin Project](https://github.com/cpputest/CppUTestEclipsePlugin)

## Authors and Contributors

CppUTest has had many contributions from its users. We can't remember all, but we appreciate it a lot! Much of the original code was written by Michael Feathers (based on CppUnit Lite). The current main maintainers are [@jwgrenning](https://github.com/jwgrenning) and [@basvodde](https://github.com/basvodde)
