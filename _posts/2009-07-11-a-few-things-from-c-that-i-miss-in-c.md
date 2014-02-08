---
title: A few things from C++ that i miss in C#
layout: post
comments : true
views:
  - 1327
dsq_thread_id:
  - 306769001
category : net
tags:
  - c sharp
  - c++
  - class
  - const
  - const correctness
  - control
  - cpp
  - design bugs
  - destruction
  - deterministic destruction
  - development
  - generics
  - immutable objects
  - lambda expressions
  - Microsoft Windows
  - object
  - paradigms
  - partial specialization
  - reference type
  - relay
  - static initialization
  - templates
  - value type
---
The last 6-7 months i&#8217;ve been working mostly with C# and to be honest I&#8217;ve grown found to it. It has really nice features like lambda expressions,   auto properties, extension methods, LINQ witch can really speed up things in terms development time.

Still &#8230; there are some things from C++ that i now miss: Templates ( as in true generics ) , const correctness, deterministic destruction, static initialization and most of all the control over what is a value and what is a reference.

### Generics ( Templates )

Yes, C# has generics witch give you a level of freedom and ability to write generic code but they are nothing compared to the power of c++ templates. In C++ templates are compiled when instantiated and you don&#8217;t need to specify any constraints on the  generic type and as long as the instantiated type is syntactically correct your code will compile. There are ways to define concepts witch can impose requirements on the types but the concepts are also verified when the template is instantiated. Also C# does not allow specialization or partial specialization of generic types.

### Const Correctness

There are a lot of articles on the web about why C# does not implement const correctness. In the end you learn to leave without it but its a nice thing to have and as long as you use it consistently it can help prevent a lot of bugs mostly design bugs. I guess that there are ways in C# to prevent the bugs that const correctness prevents  ( immutable objects, ReadOnly interfaces ) but they are harder to impose on a team of developers.

### Deterministic Destruction

I&#8217;m quite sure that I only miss this feature because i come with a C++ background. You can easily have deterministic destruction in C# by using IDisposable and using(&#8230;) features, still after years of C++ i kinda like to know when an object gets destructed.

### Static Initialization

To be honest I don&#8217;t miss this feature too much. The only thing i find it useful for is implementing the factory pattern where objects register themselves in the factory using static initialization. The downside of this is that it can relay get messy ( hint Static Initialization Fiasco )

### Control over what is a value and what is a reference

Now this i miss the most. I have to admit it I like pointers. Well i actually like smart pointers and i like being in control of what and how i pass as arguments to methods. In C# it&#8217;s written in stone: structs are values and classes are references (ok, with boxing you can have references to structs but that&#8217;s not the point ). One of the problems is that there is no way of declaring a method that accepts a CONST reference to an instance. When you pass an instance of a class to a method (you actually pass the reference to that instance) , the method has full control over the instance.  This might not be a problem when you write all the code, but in a team with developers of different skill bad things can happen. Add some multi-threading to this and things can get relay messy. Another problem is when you have a value object implemented as a struct that is holding a lot of data and you need to pass this object to a Method that only needs to read something from this object. If you pass it as a value object it will probably get copied ( i&#8217;m not sure if the compiler does not implement some copy-on-write here but i would not relay on that ) and if you pass it as a ref than the method might change the data.

In conclusion I like working with C# but there are some things that i hope will get better in time. Other important &#8221;bonuses&#8221; you get with C# are the .NET Framework and Visual Studio IDE that speed up substantially the development. The major downsite witch in some cases is a showstopper: Platform Dependent. I don&#8217;t know where MONO is right now but if you develop with C# and .NET you better make sure your main target platform is Windows.

I would be glad to hear what others think about this.
