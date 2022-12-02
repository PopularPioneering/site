---
layout: post
title: Three ways to find network components
date: 2022-12-02
---

## Direct UUID reference

When you connect a component to your network it is assigned a unique identifier, known as a UUID. You can pass this UUID straight into the `component.proxy()` function to get a quick reference to your networked component. You can easily copy the UUID of a component to the clipboard by clicking it in the list of attached components in the terminal.

```lua
local container = component.proxy('XYZ123')
-- container:getInventories()
```

This technique is not advised for a number of reasons. The most obvious is that if you deconstruct a networked component, that instance and the associated UUID is lost forever and you will have to manually enter the new UUID of the replacement. This can be a real hassle - UUIDs are not human-readable and the more you add to your code the less meaning each one has. Scrolling through a long list of UUIDs to find the right one can get annoying very quickly. 

We want our code to be easy to maintain and not rely on lots of manual copy/pasting, that kind of gruntwork is for those other Pioneers! 

## By nickname query

Rather than directly referencing a component, we can search - or query - our network for something more meaningful. You can give your networked components nicknames by interacting with them using the Network monitor. Giving your components nicknames is important, and this is a very powerful tool with a lot of potential that will be covered in much greater detail in further guides.

For now, here's an example of how you find a component with a nickname. We've given a Mk1 container connected to our computer the nickname of `Storage Container 1`.

```lua
local matching_component_uuids = component.findComponent('Storage Container 1')
local container = component.proxy(matching_component_uuids)[1]
-- container:getInventories()
```

This time we are using another method of the component class: `component.findComponent()`. It takes one or more strings (our search terms) and returns an array of component UUIDs with nicknames that match those terms. This means that rather than passing a single UUID string into `component.proxy()` like before, we are passing in an array of UUID strings. 

When `component.proxy()` receives an array instead of a single string, it returns an array of components instead of a single component. Even if there's only a single UUID in the array, `component.proxy()` will return an array containing the single component.

This is why we've added `[1]` to the end our `component.proxy()` call. In this instance we don't want to assign the whole array to our variable `container`, so we're diving right in and assigning the first item instead. We know it's going to be the correct container because there's only one container we named `Storage Container 1`.

You'll probably need to reference a lof of components in your scripts, so these two lines are typically shortened to just a single line:

```lua
local container = component.proxy(component.findComponent('StorageContainer 1'))`
-- container:getInventories()
```

Typically you will only ever pass the result of `component.findComponent()` into a single `component.proxy()` call. There's no need to declare it as a variable we can just pass the result in directly. Tidy!

## By class

Can we avoid having to manually enter nicknames as well? Assigning nicknames is considered good practice for organisation alone, but there are some instances where you might need to 'discover' components on a network without hardcoding a nickname. Enter the `findClass()` function.

Here's how it looks added to our shortened one liner from the previous example:

```lua
local component.proxy(component.findComponent(findClass("Build_StorageContainerMk1_C")))[1]
-- container:getInventories()
```

This time we're chaining three function calls. We pass the result of `findClass()` into `component.findComponent()` and then we pass the result of that into `component.proxy()`.

We're passing a string into `findClass()`, this is the internal name the game uses to identify objects - here's a handy cheat sheet if you're looking for a specific value [TODO]. 

Rather than returning a string or array, `findClass()` returns the Type that matches the name. When `component.findComponent()` recieves a Type instead of string, it returns an array of UUIDs for every component with a matching Type on the network. Just like before, we pass that array into `component.proxy()` and then use the `[1]` index lookup to grab our container.
 
You might have also noticed that `findClass()` isn't a method of the component class - rather, it is an additional function provided by the Lua runtime. Thanks FicsIt-Networks!



