---
title: Never use AutoMapper Dynamic Map
layout: post
tags: [Code, .Net]
---

I recently noticed some odd behavior of the [AutoMapper Dynamic Map](http://docs.automapper.org/en/stable/Dynamic-and-ExpandoObject-Mapping.html) feature and came to the conclusion that this should actually never be used. Here is why.

![AutoMapper]({{ "/assets/posts/2019-04-10-Never-Use-AutoMapper-Dynamic-Map_automapper_logo.png"}}){:style="background-color: black"}

## How to Dynamic Map

A dynamic map is a map from one class to another where you do not specify any mappings. Initialize the Mapper with `CreateMissingTypeMaps = true` and `ValidateInlineMaps = false`.

```csharp
AutoMapper.Mapper.Initialize(cfg => { 
	cfg.CreateMissingTypeMaps = true; 
	cfg.ValidateInlineMaps = false; }); 
```
Map an object of `Model` to class `A`. This will create a new instance of `A` and try to map all properties.
```csharp
var model = new Model { Message = "Hello World" };
var a = AutoMapper.Mapper.Map<A>(model); // a.Message == "Hello World"
```

You can also map to an existing object and overwrite all properties that [AutoMapper](http://automapper.org/) can map.

```csharp
var model = new Model { Message = "Hello World" };
var a = AutoMapper.Mapper.Map(model, new A { Message = "Something" }); // a.Message == "Hello World"
```

## Why to Never Dynmic Map

When performing a dynamic map [AutoMapper](http://automapper.org/) stores the dynamically created mapping configuration in the background. This behavior adds some unexpected state to the mapping. Depending on what mappings have already been done before the same dynamic map might now behave differently!

**For [some reason](https://github.com/AutoMapper/AutoMapper/issues/3025) an already saved base-class mapping configuration will also be used for any derived classes.**

```csharp
class BaseClass {}
class A : BaseClass {
	public string Message {get;set;}
}
```
Usually AutoMapper dynamically creates a map for class `A` and maps all properties.

```csharp
var model = new Model { Message = "Hello World" };
var a = AutoMapper.Mapper.Map(model, new A()); // a.Message == "Hello World"
```

However, when AutoMapper finds an already existing map for BaseClass it will use this. Since BaseClass does not have a property `Message` it will now not be mapped.

```csharp
var model = new Model { Message = "Hello World" };
AutoMapper.Mapper.Map(model, new BaseClass()); // dynamically creates and stores the BaseClass map
var a = AutoMapper.Mapper.Map(model, new A()); // a.Message == NULL!
```

This is really dangerous and unexpected and therefore [Dynamic Mapping](http://docs.automapper.org/en/stable/Dynamic-and-ExpandoObject-Mapping.html) should never be used!

## How to use AutoMapper then?

Feelings are always running high when it comes to [AutoMapper](http://automapper.org/) and my colleagues all know that I'm not a huge fan. However, it's not that I think it is a bad tool or [even hate it](https://jimmybogard.com/automappers-design-philosophy/), I just never saw a project where it was used the way it is [intended to be used](https://jimmybogard.com/automapper-usage-guidelines/). Personally I do like to use it in tests sometimes but try to avoid it in production code.

So, first of all, challenge [if AutoMapper is really needed](https://jimmybogard.com/automapper-usage-guidelines/). If the answer is yes make extra sure that you use it correctly - it's a powerful tool and misuse will be punished later on.


**Make sure to follow Jimmy Bogard's [AutoMapper Usage Guidelines](https://jimmybogard.com/automapper-usage-guidelines/).**

Did you stumble across any unexpected problems with [AutoMapper](http://automapper.org/) yourself? Let me know in the comments.