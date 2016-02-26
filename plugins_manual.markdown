---
layout: default
title: Plugins Manual
---

## Table of Content

* [SetPointerPlugin](#setpointerplugin)
* [MockSupportPlugin](#mocksupportplugin)
* [IEEE754ExceptionsPlugin](#ieee754exceptionsplugin)

<a id="setpointerplugin"> </a>

## SetPointerPlugin

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
      //overwrite the production function pointer witha an output method that captures
      //output in a buffer.
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

//Hello.h
#ifndef HELLO_H_
#define HELLO_H_

extern void printHelloWorld();

struct helloWorldApi {
   int (*printHelloWorld_output) (const char*, ...);
};

#endif /*HELLO_H_*/

//Hello.c

#include <stdio.h>
#include "hello.h"

//in production, print with printf.
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

Tba

<a id="ieee754exceptionsplugin"> </a>

## IEEE754ExceptionsPlugin

Tba