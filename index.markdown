---
layout: default
title: CppUTest
---

## What is CppUTest.

CppUTest is a C /C++ based unit xUnit test framework for unit testing and for test-driving your code. It is written in C++ but is used in C and C++ projects and frequently used in embedded systems but it works for any C/C++ project.

CppUTest is based on the following design principles

* Simple in design and simple in use.
* Portable to old and new platforms.

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

The download link for github is at the top of this page. You can either get the latest code or a specific release.

You can find all the downloads [at the download page](https://github.com/cpputest/cpputest/downloads)

Alternatively, you can clone the github repository, read-only:

{% highlight bash %}
$ git clone git://github.com/cpputest/cpputest.git
{% endhighlight %}

Or clone it via ssh (which requires a github account)

{% highlight bash %}
$ git clone git@github.com:cpputest/cpputest.git
{% endhighlight %}

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

* For Eclipse users, check out the [CppUTest Eclipse Plugin Project](https://github.com/cpputest/CppUTestEclipsePlugin)

## Authors and Contributors

CppUTest has had many contributions from its users. We can't remember all, but we appreciate it a lot!. Much of the original code was written by Michael Feathers (based on CppUnit Lite). The current main maintainers are [@jwgrenning](https://github.com/jwgrenning) and [@basvodde](https://github.com/basvodde)
