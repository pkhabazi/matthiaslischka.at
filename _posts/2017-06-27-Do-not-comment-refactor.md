---
title: Do not comment. Refactor.
layout: post
tags: [code, refactor]
---
Whenever I'm tempted to write a comment or I see a colleague writing one I try to find a way to refactor the code to a point where its intend becomes so clear that any additional comment would be redundant.

So over time comments became just a code smell for me. An indicator that the code is not readable to the point it should be. (* except for external API and XML documentation)

Something like this
``` csharp
//'home' is the default tab
this.activeTab = 'home';
```
...could be refactored to something like...
``` csharp
activeTab = constants.defaultTab;
```
The intend is very clear and the comment is not needed anymore. You get the point.

We also tend to use comments for additional informations. Like notes for future stories, placeholders, hints for bugs and so on.
Those things do not belong within the code. They need to be tracked as a story in the backlog, an issue in your tracking system or a clarification task for the product owner...

```csharp
//clarify how this should be done when customer xzy ....
public void AddCustomer(...
```
That is not the right place for a question like that. Your product owner is not gonna read the code. And even if, would you like a new commit that only deletes this comment once this question could be clarified? Me neither. Make this something trackable in your backlog tool.

The most annoying comments are those that are just leftovers from something that is not needed anymore but one was too close to to delete it. When code becomes obsolete just delete it. Do never comment code out because you might need it again later. That's why we use a VCS.

![useless comment]({{"/assets/2017-06-27-Do-not-comment-refactor_wet_floor.jpg"}})