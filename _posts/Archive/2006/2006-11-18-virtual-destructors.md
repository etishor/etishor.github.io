---
title: Virtual Destructors
layout: post
comments: true
dsq_thread_id: '26 http://www.erata.net/weblog/programming/2006/11/18/virtual-destructors/'
category: Programming
---
## When do you need to declare a destructor virtual?

Recently I’ve been involved in a thread on qt-interest mailing list about virtual destructors. A few people there suggested that you should always make the destructor of your class virtual. I strongly disagree. 

The argument was that if you use the virtual keyword on your destructor you will always be on the safe side. If someone inherits from your class and then deletes one of his instances of the class by a pointer to your base class you are sure his destructor gets called. So you are designing a class with value semantics (can be copied, can be assigned to) and you fear that someone will eventually inherit from your class so you make the destructor virtual. Well then why not make all the methods virtual so that if someone will inherit your class will have the possibility to customize its behavior. Is this starting to sound wrong? Well it should. 

Just to be clear, I’m not trying to say that declaring a destructor virtual on a class WILL cause trouble. I’ll try to explain why it’s BAD CODING STYLE to always declare you destructor virtual. A good coding style will most probably keep you away from dangers like inheriting a class that has not been meant to be used as a base class.

As Herb Sutter and Andrei Alexandrescu state in “C++ Coding Standards: 101 Rules, Guidelines, and Best Practices” item 32:

**Be clear what kind of class you're writing**

Be clear to yourself and to the users of your class. The most used types of classes are value classes and polymorphic classes. As a rule of thumb:

**Never try to write a class that is of both types.**

These types of classes are complementary. Value types are concrete types that mimic the built-in types, which are usually instantiated on the stack and are passed around by reference or by value. Polymorphic classes are meant to be used as base classes, are usually instantiated on the heap and used via (smart) pointer. In C++ the keyword virtual means “this will be a polymorphic class” and a copy constructor and assignment operator mean “this will be a value class”.

## Now when the destructor should be declared virtual?

You **MUST** declare the destructor virtual **ONLY** if you are creating a polymorphic class witch has a **PUBLIC** destructor. That’s it. That’s the only case when you must declare the destructor virtual. If you don’t declare it virtual BAD THINGS will happen (destructors of child classes will not be called when deleting by base class).

{% highlight c++ linenos %}
struct A { 
  ~A(){
    cout << " calling ~A()"; 
  }
};

struct B: public A { 
  ~B(){
    cout << "~B()";
  }
};

int main(){
  A* a = new B;
  delete a; ///In this case ~B() will not be called.
} 
{% endhighlight %}

You **SHULD** (read **MUST**) **NOT** declare your destructor virtual in any other case. If you declare it virtual the biggest problem is someone using your class might think that your class is a polymorphic class that can be inherited from. If this happens the code will probably work but will be awkward and difficult to write and maintain. There is also a performance hit that comes with virtual destructors but unless you are writing something where every nanosecond counts you will not care about this hit.

Now what do you do if you find a nice class that is a value class (even if the author made the destructor virtual) but you want to add some custom functionality to it?

Use **FREE FUNCTIONS**. That’s it. Having free functions will not make your coding less Object Oriented. Free function will give you the same access level as inheriting from a value class as value classes don’t usually have protected methods. To avoid problems place the free functions in the same namespace as the class. As an example if you have a string class and want to have a method that will return the word count from the string: 

{% highlight c++ linenos %}
int wordCount( const string& str ){
  ///count the words
  return count;
}

void doSomething(const string& str){
  /// instead of str.wordCount() you will now have:
  int count = wordCount(str);
}
{% endhighlight %}

There are lots of things to be said about class design. You should search for books written on the subject and learn about good ways of creating classes. The sad part is that until you are bitten by a bad design that you have to live with you most probably will not understand the importance of good design and coding style in general.

In the end if you are an employer and want to quickly see if a candidate knows about virtual functions ask him what “static virtual functions” and “virtual constructors” have in common. (if you don’t know the answer contact me).

## Updates

A very good article about [virtuality][1].

And another great article about [virtual functions][2].

> "Virtual functions should be treated very much like data members — make them private, until design needs indicate a less restricted approach is indicated. It is much easier to promote them to a more accessible level, than it is to demote them to a more private level."

[1]: http://www.gotw.ca/publications/mill18.htm
[2]: http://www.ddj.com/dept/cpp/184403760