---
layout: default
title: CppUTest Mocking Manual
---

CppUTest has support for building mocks. This document described the mocking support. A couple of design goals for the mocking support were:


* Same design goals as CppuTest -- limited C++ set to make it well suitable for embedded soft.
* No code generation
* No or very few magic hiding macros
* Very simple to use
* The developer stays in control

The main idea is to make manual mocking easier, rather than to make automated mocks. If manual mocking is easier, then it could also be automated in the future, but that isn't a goal by itself.

## Table of Content

* [Simple Scenario](#simple_scenario)
* [Using Parameters](#parameters)
* [Using Objects](#objects)
* [Objects as Parameters](#objects_as_parameters)
* [Return Values](#return_values)
* [Passing other data](#other_data)
* [Other MockSupport](#other_mock_support)
* [MockSupport Scope](#mock_scope)
* [MockPlugin](#mock_plugin)
* [C Interface](#c_interface)

TBD
