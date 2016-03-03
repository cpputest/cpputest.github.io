---
layout: default
title: Plugin Manual
---

CppUTest plugins can be installed in the main and 'extend' the unit test framework. A plugin is a place where you can put work that needs to be done in all unit tests.

## Table of Content

* [SetPointerPlugin](#setpointerplugin)
* [MockSupportPlugin](#mocksupportplugin)
* [IEEE754ExceptionsPlugin](#ieee754exceptionsplugin)

<a id="setpointerplugin"> </a>

## SetPointerPlugin

### Description

The SetPointerPlugin provides a Pointer restore mechanism - helpful when tests overwrite a pointer that must be restored to its original value after the test.  This is especially helpful when a pointer to a function is modified for test purposes.

### Example

{% highlight c++ %}
int main(int ac, char** av)
{
    TestRegistry* r = TestRegistry::getCurrentRegistry();
    SetPointerPlugin ps("PointerStore");
    r->installPlugin(&ps);
    return CommandLineTestRunner::RunAllTests(ac, av);
}

TEST_GROUP(HelloWorld)
{
   static int output_method(const char* output, ...)
   {
      va_list arguments;
      va_start(arguments, output);
      cpputest_snprintf(buffer, BUFFER_SIZE, output, arguments);
      va_end(arguments);
      return 1;
   }
   void setup()
   {
      // overwrite the production function pointer witha an output method that captures
      // output in a buffer.
      UT_PTR_SET(helloWorldApiInstance.printHelloWorld_output, &output_method);
   }
   void teardown()
   {
   }
};

TEST(HelloWorld, PrintOk)
{
   printHelloWorld();
   STRCMP_EQUAL("Hello World!\n", buffer)
}

// Hello.h
#ifndef HELLO_H_
#define HELLO_H_

extern void printHelloWorld();

struct helloWorldApi {
   int (*printHelloWorld_output) (const char*, ...);
};

#endif

// Hello.c

#include <stdio.h>
#include "hello.h"

// in production, print with printf.
struct helloWorldApi helloWorldApiInstance = {
   &printf
};

void printHelloWorld()
{
   helloWorldApiInstance.printHelloWorld_output("Hello World!\n");
}
{% endhighlight %}

<a id="mocksupportplugin"> </a>

## MockSupportPlugin

MockSupportPlugin makes the work with mocks easier. It does the following work for you automatically: 

* checkExpectations at the end of every test (on global scope, which goes recursive over all scopes)
* clear all expectations at the end of every test
* install all comparators that were configured in the plugin at the beginning of every test
* remove all comparators at the end of every test

Installing the MockPlugin means you'll have to add to main something like:

{% highlight c++ %}
#include "CppUTest/TestRegistry.h"
#include "CppUTestExt/MockSupportPlugin.h"

MyDummyComparator dummyComparator;
MockSupportPlugin mockPlugin;

mockPlugin.installComparator("MyDummyType", dummyComparator);
TestRegistry::getCurrentRegistry()->installPlugin(&mockPlugin);
{% endhighlight %}

This code creates a comparator for MyDummy and installs it at the plugin. This means the comparator is available for all test cases. It creates the plugin and installs it at the current test registry. After installing the plugin, you don't have to worry too much anymore about calling checkExpectations or cleaning your MockSupport.

<a id="ieee754exceptionsplugin"> </a>

## IEEE754ExceptionsPlugin

### Description

This plugin detects floating point error conditions and fails the test, if any were found. According to the IEEE754 Floating Point Standard, floating point errors do not by default cause abnormal program termination. Rather, flags are set to indicate a problem, and the operation returns a defined value such as Infinity or Not-a-Number (NaN).

This is a list of floating point error conditions, and how they are supported by the plugin:

{% highlight c++ %}
FE_DIVBYZERO   /* supported (v3.8) */
FE_OVERFLOW    /* supported (v3.8) */
FE_UNDERFLOW   /* supported (v3.8) */
FE_INVALID     /* supported (v3.8) */
FE_INEXACT     /* supported; disabled by default (v3.8) */
FE_DENORMAL    /* NOT supported (v3.8) */
{% endhighlight %}

You can turn on FE_INEXACT checking manually, although this probably won't be very useful most of the time, since almost every floating-point operation is likely to set this flag:

{% highlight c++ %}
IEEE754ExceptionsPlugin::enableInexact();
IEEE754ExceptionsPlugin::disableInexact();
{% endhighlight %}

# Example

{% highlight c++ %}
#include "CppUTest/CommandLineTestRunner.h"
#include "CppUTest/TestRegistry.h"
#include "CppUTestExt/IEEE754ExceptionsPlugin.h"

int main(int ac, char** av)
{
    IEEE754ExceptionsPlugin ieee754Plugin;
    TestRegistry::getCurrentRegistry()->installPlugin(&ieee754Plugin);
    return CommandLineTestRunner::RunAllTests(ac, av);
}

static volatile float f;

TEST_GROUP(CatchFloatingPointErrors)
{
    void setup()
    {
        IEEE754ExceptionsPlugin::disableInexact();
    }
};

TEST(CatchFloatingPointErrors, underflow)
{
    f = 0.01f;
    while (f > 0.0f) f *= f;
    CHECK(f == 0.0f);
}

TEST(CatchFloatingPointErrors, inexact) {
    IEEE754ExceptionsPlugin::enableInexact();
    f = 10.0f;
    DOUBLES_EQUAL(f / 3.0f, 3.333f, 0.001f);
}
{% endhighlight %}

The output of these tests will be:
{% highlight bash %}
$ ./example.exe
example.cpp:29: error: Failure in TEST(CatchFloatingPointErrors, inexact)
src/CppUTestExt/IEEE754ExceptionsPlugin.cpp:164: error:
        IEEE754_CHECK_CLEAR(FE_INEXACT) failed
.
example.cpp:22: error: Failure in TEST(CatchFloatingPointErrors, underflow)
src/CppUTestExt/IEEE754ExceptionsPlugin.cpp:164: error:
        IEEE754_CHECK_CLEAR(FE_UNDERFLOW) failed
.
Errors (2 failures, 2 tests, 2 ran, 12 checks, 0 ignored, 0 filtered out, 39 ms)
$  
{% endhighlight %}
