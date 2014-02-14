---
title: Test Data Generator
author: Iulian Margarintescu
layout: post
permalink: /projects/test-data-generator/
dsq_thread_id:
  - 474955486
categories:
  - Projects
tags:
  - fill objects
  - generator
  - persistance
  - random data
  - Serialization
  - test data
---
I've pushed to github a new project, [TestDataGenerator][1] that should help with filling random objects with data. I felt the need for a tool like this when testing various serialization techniques and persistence strategies.

Basically this utility should construct the instance of an object using a public constructor, and fill all it's public, writable properties with random data.

Sample usage:

<pre class="brush:csharp">class Sample
{
	private readonly int intValue;
	private readonly string stringValue;

	private Sample()
	{
		this.intValue = -1;
		this.stringValue = null;
		this.StringProp = null;
		this.DateProp = DateTime.MinValue;
	}

	public Sample(int intVal, string stringVal)
		:this()
	{
		this.intValue = intVal;
		this.stringValue = stringVal;
	}

	public int PrivateInt { get { return intValue; } }
	public string PrivateString { get { return stringValue; } }

	public string StringProp { get; set; }
	public DateTime DateProp { get; set; }
}

[Test]
public void Catalog_Can_Create_Using_Consutrctor()
{
	Catalog catalog = new Catalog();
	object instance = catalog.CreateInstance(typeof(Sample));

	Assert.IsInstanceOfType(typeof(Sample), instance);
	Sample sample = instance as Sample;

	Assert.AreNotEqual(-1, sample.PrivateInt);
	Assert.IsNotNull(sample.PrivateString);
	Assert.IsNotNull(sample.StringProp);
	Assert.AreNotEqual(DateTime.MinValue, sample.DateProp);
}</pre>

For more samples see [unit tests][2].  
I'll try to add more cases ( abstract class, interface implementation discovery ) in the future. You are welcome to submit sample classes, preferably as unit tests.

 [1]: https://github.com/etishor/TestDataGenerator "Test Data Generator"
 [2]: https://github.com/etishor/TestDataGenerator/tree/master/TestDataGenerator.Tests "Unit Tests"