---
layout: post
title: Be aware of the Garbage Collector when accessing C/C++ from Ruby
date: '2012-07-31'
categories:
- Software
tags:
- libyui
- ruby
- suse
---

When you wrap C/C++ code into a language like Ruby which has a garbage collector, you have to be very careful because the GC knows about the Ruby objects referencing to each other, but not about the underlying C objects. You need to manually hint the GC that two Ruby objects are connected by their underlying pointers so that the GC does not deallocate one, freeing all the pointer chain and leaving another Ruby object with a dangling pointer.

As an example: You have a Ruby r1 object that points to a Widget \*w1, and r2 points to Widget \*w2. The parent of w2 is w1, and if you delete the w1 pointer all children will be deleted.

In Ruby's mark & sweep GC this is achieved by implementing the mark() hook correctly and calling rb\_gc\_mark() from there. For the example above it would be something like:

