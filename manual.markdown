---
layout: default
title: CppUTest Manual
---

CppUTest is a C /C++ based unit xUnit test framework for unit testing and for test-driving your code. It is written in C++ but is used in C and C++ projects and frequently used in embedded systems.

CppUTest has a couple design principles
* Simple to use and small
* Portable to old and new platforms

Below is further CppUTest documentation:
* [Getting started](#getting_started)
* Test Macros
* Assertions
* Setup and Teardown
* Command Line Switches
* Memory Leak Detection
* Test Plugins
* Scripts
* Advanced Stuff

<a id="getting_started"></a>
## Getting Started

### Your first test

To write your first test, all you need is a new cpp file with a TEST_GROUP and a TEST, like:

    TEST_GROUP(FirstTestGroup)
    {
    };

    TEST(FirstTestGroup, FirstTest)
    {
       FAIL("Fail me!");
    }

This test will fail. For adding new test_groups, this will be all you need to do (and make sure its compiled). If you want to add another test, all you need to do it:

    TEST(FirstTestGroup, SecondTest)
    {
       STRCMP_EQUAL("hello", "world");
    }

One of the key design goals in CppUTest is to make it *very easy* to add and remove tests as this is something you'll be doing a lot when test-driving your code.

### Writing your main

Of course, in order to get it to run, you'll need to create a main. Most of the mains in CppUTest are very similar. They typically are in an AllTests.cpp file and look like this:

    <a id="getting_started">Your first testint main(int ac, char** av)
    {
       return CommandLineTestRunner::RunAllTests(ac, av);
    }

CppUTest will automatically find your tests (as long as you don't like them in a library).

### Makefile changes

To get the above to work, you'll need a Makefile or change your existing one. The needed changed are:

#### CppUTest path

You'll need to define a CppUTest path either as system variable or in the Makefile, such as:

    CPPUTEST_HOME = /Users/vodde/workspace/cpputest

#### Compiler options

For the compiler you have to add the include path and optional (but recommended) the CppUTest pre-include header which enables debug information for the memory leak detector *and* offers memory leak detection in C. Lets start with the include path, you'll need to add:

    CPPFLAGS += -I(CPPUTEST_HOME)/include

(CPPFLAGS works for both .c and .cpp files!)

Then for the memory leak detection, you'll need to add:

    CXXFLAGS += -include $(CPPUTEST_HOME)/include/CppUTest/MemoryLeakDetectorNewMacros.h
    CFLAGS += -include $(CPPUTEST_HOME)/include/CppUTest/MemoryLeakDetectorMallocMacros.h

These flags need to be added to *both* test code *and* production code. They will replace the malloc and new with a debug variant.

#### Linker options

You need to add CppUTest library to the linker flags, for example, like:

     LD_LIBRARIES = -L$(CPPUTEST_HOME)/lib -lCppUTest -lCppUTestExt

(The last flags is only needed when you want to use extensions such as mocking)
