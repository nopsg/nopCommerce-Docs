---
title: Creating a new Unit test and running the tests.
author: SeaSudip
uid: developer/tutorials/creating-and-running-unit-test
---
## Creating a new Unit test

Since nopCommerce is using **.NET Core** from version 4.0, this tutorial mainly focus on creating a new *Unit test* in **.NET Core** and running it. For this purpose, create a new `NUnit Test Project (.NET Core)` project.

**NUnit** is a unit-testing framework for .NET applications in which the entire application is isolated into diverse modules. Each module is tested independently to ensure that the objective is met. **The NUnit Framework** caters a range of attributes that are used during unit tests. They are used to define *Test fixtures*, *Test methods* and *Ignore methods*.

## Unit test attributes

### TestFixture Attribute

The *TestFixture* attribute is an indication that a class contains test methods. When you mention this attribute to a class in your project, the **Test Runner** application will scan it for test methods. The following code illustrates the usage of this attribute:

```cs
using System;  
using NUnit.Framework;  
using System.Text;  
namespace MyFirstUnitTest {  
    [TestFixture]  
    public class MyUnitTests {}  
}
```

### SetUp Attribute

This attribute is used inside a *TestFixture* to provide a common set of functions that are performed just before each test method is called.

**SetUp** methods may be either static or instance methods and you may define more than one of them in a fixture. Normally, multiple *SetUp* methods are only defined at different levels of an inheritance hierarchy, as explained below.

The *SetUp* attribute is inherited from any base class. Therefore, if a base class has defined a *SetUp* method, that method will be called before each test method in the derived class.

You may define a *SetUp* method in the base class and another in the derived class. *NUnit* will call base class *SetUp* methods before those in the derived classes.

> [!NOTE]
> If a *SetUp* method fails or throws an exception, the test is not executed and a failure or error is reported.

```cs
[TestFixture]  
public class MyUnitTests {  
    [SetUp]  
    public new void SetUp()
    {
        ...  
    }  
}  
```

### Assert Class

There is a **TestExtensions** class inside **Nop.Tests** project which extends *Assert* class to create methods like *ShouldEqual()* or *ShouldBeNull()*. The **Assert** class is used to confirm whether the test cases is producing the expected result or not using its auxiliary methods such as *AreEqual()* or *AreNotEqual()*.

### Ignore Attribute

The **Ignore** attribute is required to indicate that a test should not be run on a particular method. Use the *Ignore* attribute as following:

```cs
[TestFixture]  
public class MyUnitTests  
{  
    [Test]  
    [Ignore("This method is skipped")]  
    public void SkipTest()  
    {  
        ...  
    }  
}  
```

### Test Attribute

The **Test** attribute indicates that a method within a test fixture should be run by the *Test Runner* application. The method must be public, return void, and will not be run when the *TestFixture* is run. The following code depicts the use of this attribute:

```cs
[TestFixture]  
public class MyUnitTests {  
    [Test]  
    public void MyFirstTestMethodName()
    {
        ...  
    }  
}  
```

> [!NOTE]
> Inside this method, various conditional statements will be written and is asserted whether it should be true or false or equal to. An example to find out if customer activities contains certain activity or not could be found [here](https://github.com/nopSolutions/nopCommerce/blob/master/src/Tests/Nop.Services.Tests/Logging/CustomerActivityServiceTests.cs).

## Running the Unit test

In order to run your test project, **NUnit 3 TestAdapter** is required. Select the test project in Visual Studio *right click* where **Run Tests** option can be found. On execution, a new window will show up which contain information what has passed and what has failed as shown in figure below.

![unit-test-result](_static/creating-and-running-unit-test/unit-tests-result.png)
