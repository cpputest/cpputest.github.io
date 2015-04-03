---
layout: default
title: CppUTest
---

## What is CppUTest.

CppUTest is a C /C++ based unit xUnit test framework for unit testing and for test-driving your code. It is written in C++ but is used in C and C++ projects and frequently used in embedded systems but it works for any C/C++ project.

CppUTest's core design principles are:

* Simple in design and simple in use.
* Portable to old and new platforms.
* Build with Test-driven Development in mind

## Where to get CppUTest

### Pre-packaged

*Linux*

There is an Debian and Ubuntu package available for CppUTest. This is by far the easiest way to install it, via:

{% highlight bash %}
$ apt-get install cpputest
{% endhighlight %}

*MacOSX*

For Mac, a Homebrew package is available too. You can install via:

{% highlight bash %}
$ brew install cpputest
{% endhighlight %}

### From source

You can download the latest 'automatically released' version:

* [Latest version passing the build](https://github.com/cpputest/cpputest.github.io/blob/master/releases/cpputest-3.7dev.tar.gz?raw=true)
This version is automatically packages after a build has passed.

Alternatively, you can clone the github repository, read-only:

{% highlight bash %}
$ git clone git://github.com/cpputest/cpputest.git
{% endhighlight %}

Or clone it via ssh (which requires a github account)

{% highlight bash %}
$ git clone git@github.com:cpputest/cpputest.git
{% endhighlight %}

After you cloned CppUTest, you can build it with your favorite build tool (CMake or autoconf).

Building with autoconf requires you to (this requires you to have installed GNU autotools, apt-get/brew install automake autoconf libtool):

{% highlight bash %}
$ cd cpputest_build
$ autoreconf .. -i
$ ../configure
$ make
{% endhighlight %}

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

For Windows users, the above work with cygwin. There are also several MS VC++ projects available.

## Where to find more information

* If you have any question, check out the [Google Groups](https://groups.google.com/forum/?fromgroups#!forum/cpputest)
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

For more information, We'd recommend to [read the manual](http://www.cpputest.org) or, even better, check some [existing tests](https://github.com/cpputest/cpputest/tree/master/tests) such as [SimpleStringTest](https://github.com/cpputest/cpputest/blob/master/tests/SimpleStringTest.cpp) or (a bit more complicated) [MemoryLeakDetectorTest](https://github.com/cpputest/cpputest/blob/master/tests/MemoryLeakDetectorTest.cpp) or the [mocking tests](https://github.com/cpputest/cpputest/blob/master/tests/CppUTestExt/TestMockSupport.cpp) or just check out the [Cheat Sheet](https://github.com/cpputest/cpputest/blob/master/tests/CheatSheetTest.cpp)

## Related projects

For Eclipse users, also check:
* [CppUTest Eclipse Test Runner](https://github.com/tcmak/CppUTestEclipseJunoTestRunner) This will allow you to run your tests JUnit style with red and green bars, and rerun arbitrary selections of tests.
Prerequisites:
  - CppUTest off master
  - Eclipse Juno, Kepler, or later
  - Eclipse C/C++ Unit Plugin (if not already present in your version of Eclipse; install directly from Eclipse Help -> Install New Software...).
* [CppUTest Eclipse Plugin Project](https://github.com/cpputest/CppUTestEclipsePlugin)

## Authors and Contributors

CppUTest has had many contributions from its users. We can't remember all, but we appreciate it a lot!. Much of the original code was written by Michael Feathers (based on CppUnit Lite). The current main maintainers are [@jwgrenning](https://github.com/jwgrenning) and [@basvodde](https://github.com/basvodde)
