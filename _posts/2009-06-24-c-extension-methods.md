---
title: 'C# Extension Methods'
layout: post
permalink: /net/c-extension-methods/
views:
  - 480
dsq_thread_id:
  - 312530825
category: .NET
---
After a few months I've enjoyed the newly ( or not ) added extension methods in 3.0 yesterday i discovered that one of the assumptions I've made about them was false. My assumption was that if the instance on witch you call an extension method is NULL you would get a NullReferenceException. Turns out it's not the case and you can call the extension method on a null reference.

Thinking about it later, it makes sense. In reality extension methods are nothing more than syntax sugar for calling static methods. And since too much sugar can hurt extension methods might do the same thing if not properly used. The code speaks pretty much for itself.

The class used as example:
{% highlight c# linenos %}
public class TestClass
{
    //existing method defined on the class
    public string Hello()
    {
        return "Hello";
    }
}
{% endhighlight %}

Static class defining extension method

{% highlight c# linenos %}
public static class ExtensionContainer
{
    //the extension method defined as static
    public static string NewHello(this TestClass instance)
    {
        return "World";
    }
}
{% endhighlight %}

Test code:

{% highlight c# linenos %}
class Program
{
    static void Main(string[] args)
    {
        // declare and create a test instance
        TestClass first = new TestClass();

        Console.WriteLine(first.Hello()); //outputs: "Hello"
        //call the extension method as a normal method
        Console.WriteLine(first.NewHello()); // outputs: "World"

        // declare a null reference
        TestClass second = null;

        try
        {
            //calling a method on a null reference throws
            Console.WriteLine(second.Hello()); //will throw Null Reference exception
        }
        catch (NullReferenceException)
        {
            Console.WriteLine("NullReference calling Hello");
        }

        try
        {
            //calling an extension method does not throw
            Console.WriteLine(second.NewHello()); //will not throw
        }
        catch (NullReferenceException)
        {
            Console.WriteLine("NullReference calling NewHello");
        }

        //the next two calls are exactly the same
        Console.WriteLine(second.NewHello());
        Console.WriteLine(ExtensionContainer.NewHello(second));

        Console.ReadKey();
    }
}{% endhighlight %}
Even now is still consider extension methods as a very valuable addition to the language, but you just need to understand exactly what they are. The wrong assumption about calling them on a null reference can affect some design choices you may take where in case of a null reference you want an exception to be thrown and no other method called.

Also using them when appropriate the ability to call extension methods on null references might help you write more readable code by moving the null check in the extension method. This can be particularly useful on writing extension methods that are supposed to be used in Views in ASP.NET MVC where moving the null check in the method leaves the the view's html code easier to read an understand.

One conclusion that I've drown from this is that in an extension method you should either check for null and throw NullReferenceException if null is not a valid value or handle null appropriately.

PS: Is it visible that the last 6+ months I've been working with C# and not C++ ? :)
