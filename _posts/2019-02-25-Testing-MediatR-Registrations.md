---
title: Testing the MediatR Registrations
layout: post
tags: [CI, Testing, Code, .Net]
---
Reflection is a powerful tool. IOC and mediator frameworks make strong use of that. However, the power of reflection comes with a big drawback. The code evaluation is moved from the compiletime to the runtime. When the framework tries to resolve a dependency and realizes that some link in the dependency chain is faulty it has to throw a runtime exception.

With IOC frameworks the whole container is usually evaluated at startup what makes the problem more manageable. At least everything stops working once an error occurs in the IOC container setup. It also makes writing a unittest that checks the IOC container pretty easy. With every tool that does not verify itself at startup testing gets harder.

One of those tools is [MediatR](https://github.com/jbogard/MediatR), a very popular implementation of the mediator pattern for .Net. I use this for CQRS setups where I loose couple the queries and commands to the handlers.

A query might look like this
```csharp
public class AllCustomersQuery: IRequest<AllCustomersQueryResponse> { }
```

All queries and commands in my setup implement `IRequest<SomeResponseType>`. This is important for the testing further down.

Upfront the mediator needs to know the assemblies of the handlers. Then it can be used like this:
```csharp
_mediator.Send(new AllCustomersQuery()));
```
The framework knows all the handlers from reflection and tries to find the correct handler for the given query. Therein lies the problem. The mediator does not know all queries and commands that one might try to send upfront, so it can not validate if everything could be resolved. Only when `send` is called it will evaluate and might throw
`InvalidOperationException: Handler was not found for request of type` 

Unittest to the rescue.

A unittest can look for all existing queries and commands and try to find a matching handler for each and every one. With that we can move the exception from late runtime to at least unittest execution time. Now the CI build will break as soon as we miss a handler or forget to delete a query that should no longer be used.

```csharp
public class HandlerRegistrationTests
{
	[Fact]
	public void AllRequests_ShouldHaveMatchingHandler()
	{
		var requestTypes = typeof(AllCustomersQuery).Assembly.GetTypes()
			.Where(IsRequest)
			.ToList();

		var handlerTypes = typeof(AllCustomersQueryHandler).Assembly.GetTypes()
			.Where(IsIRequestHandler)
			.ToList();

		foreach (var requestType in requestTypes) ShouldContainHandlerForRequest(handlerTypes, requestType);
	}

	private static void ShouldContainHandlerForRequest(IEnumerable<Type> handlerTypes, Type requestType)
	{
		handlerTypes.Should().ContainSingle(handlerType => IsHandlerForRequest(handlerType, requestType), $"Handler for type {requestType} expected");
	}

	private static bool IsRequest(Type type)
	{
		return typeof(IBaseRequest).IsAssignableFrom(type);
	}

	private static bool IsIRequestHandler(Type type)
	{
		return type.GetInterfaces().Any(interfaceType => interfaceType.IsGenericType && interfaceType.GetGenericTypeDefinition() == typeof(IRequestHandler<,>));
	}

	private static bool IsHandlerForRequest(Type handlerType, Type requestType)
	{
		return handlerType.GetInterfaces().Any(i => i.GenericTypeArguments.Any(ta => ta == requestType));
	}
}
```

This test gets all query and command types and tries to find a matching handler. If it does not find one the test fails. The assertion is done with [FluentAssertions](https://matthiaslischka.at/2018/11/15/fluentAssertions/):
```
Expected handlerTypes to contain a single item matching IsHandlerForRequest(handlerType, value(YourApp.Api.Tests.HandlerRegistrationTests+<>c__DisplayClass1_0).requestType) because Handler for type YourApp.Api.Contract.Queries.Customer.NoMatchingHandlerQuery expected, but no such item was found.
```
The developer now knows that something has to happen with `NoMatchingHandlerQuery`. Either the correct handler is missing or this query should be deleted.