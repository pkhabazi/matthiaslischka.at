---
title: FluentAssertions
layout: post
tags: [.Net, Testing, Code, Better Know A Framework]
---
[FluentAssertions](https://fluentassertions.com/) is a .Net assertion framework that helps you write better tests. Available via [NuGet](https://www.nuget.org/packages/FluentAssertions/).

The main selling point is human readability. Both for the code and most importantly for the error message when a test fails. Take a look at this classical assertion:
```csharp
Assert.AreEqual(3, calculationResult);
```
This will fail to something like `Assert.AreEqual failed. Expected:<3>. Actual:<4>.`
Compared to [FluentAssertions](https://fluentassertions.com/):
```csharp
calculationResult.Should().Be(3);
```
What will result in `Expected calculationResult to be 3, but found 4.`

Right away you can see that with [FluentAssertions](https://fluentassertions.com/) your code reads like a regular sentence. In addition your failing test output is much more meaningful. And that example was just one the most basic assertions.
One specific assertion scenario I see in every project and I like as a benchmark for readability is the assertion of a specific exception:
```csharp
try
{
    var division = calculator.Divide(2, 0);
}
catch (DivideByZeroException)
{
   return;
}
Assert.Fail();
```
That's an classical approach to assert an exception, seen plenty of times. Not only is this ugly to read and write and error-prone, when the test fails you get a message like `Assert.Fail failed`. Now that's not very helpful. Of course you could add a reason for the fail like `Assert.Fail("DivideByZeroException should have been thrown");` but why would you want to write that yourself? With [FluentAssertions](https://fluentassertions.com/) it just comes out of the box:
```csharp
Action divide = () => calculator.Divide(2, 0);
divide.Should().Throw<DivideByZeroException>();
```
Not only is the code way more readable and error-resistant, whenever the test fails it returns a meaningful message: `Expected a <System.DivideByZeroException> to be thrown, but no exception was thrown.` Now that's something you would like to see in a notification from your CI system.

Just as a brain-teaser, think about how you would write an assertion like that and how readable the result would be compared to [FluentAssertions](https://fluentassertions.com/):
```csharp
var persons = new[] { "Dennis", "Jonas", "Adam" };
persons.Should().Contain(person => person == "Matthias");
```
`Expected persons {"Dennis", "Jonas", "Adam"} to have an item matching (person == "Matthias").`

# Similar frameworks
By now there are quite a few frameworks out there, that attack the same problem. [FluentAssertions](https://fluentassertions.com/) seem to still be the favourite choice by far - measured in GitHub stars and NuGet downloads.
However, I do want to mention [Shouldly](https://github.com/shouldly/shouldly) as an alternative with a similar active community. I invite you to check it out and decide for yourself which one you prefer.

# Final thoughts
[FluentAssertions](https://fluentassertions.com/) has been around for many years now and I use it whenever I write tests - and that's almost all the time. It surprises me how many people still do not know about this or similar frameworks and write tests "the old way". I have yet to find a single dev who is not delighted when I introduce [FluentAssertions](https://fluentassertions.com/) to him. If you have not used any advanced assertion-framework up until now give it a try and you will never write tests the way you used to. You are welcome.


I hope you liked that format. I'm thinking about making this "better know a framework" a series. Inspired by the great [.NET Rocks!](https://dotnetrocks.com/) podcast.