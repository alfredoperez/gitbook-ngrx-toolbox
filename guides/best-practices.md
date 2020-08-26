# Best Practices / Guidelines

## Foundation

### Prefer the separation of components into containers and presentational components

The separation of components into presentational and containers helps make the application easy to reuse, understand, and refactor. It is hard to reuse the presentational component that has dependencies on state management or services.  
[https://ngrx.io/guide/schematics/container](https://ngrx.io/guide/schematics/container)

### Use  SHARI Principle to define what goes into the store

The NgRx core team has come up with a principle called **SHARI**, that can be used as a rule of thumb on data that needs to be added to the store.

* **Shared**: State that is shared between many components and services
* **Hydrated**: State that needs to be persisted and hydrated across page reloads
* **Available**: State that needs to be available when re-entering routes
* **Retrieved**: State that needs to be retrieved with a side effect, e.g. an HTTP request
* **Impacted**: State that is impacted by other components [https://ngrx.io/guide/store/why\#when-should-i-use-ngrx-store-for-state-management](https://ngrx.io/guide/store/why#when-should-i-use-ngrx-store-for-state-management)

{% hint style="info" %}
Having store in an application doesnt mean that every component should use it. Always evaluate what state should be in the store.
{% endhint %}

### Use  ComponentStore or services for local state

Global Store better suited for managing global shared state, where ComponentStore shines managing more local, encapsulated state, as well as component UI state.  
  
[https://ngrx.io/guide/component-store/comparison\#benefits-and-trade-offs](https://ngrx.io/guide/component-store/comparison#benefits-and-trade-offs)

### Do not put related nested data

### Do not put view models into the store

It is wrong to keep flatten view models directly in the store because it hard to shared the same view model in different component that might not have the same view model. 

{% hint style="success" %}
It is preferable to keep original API data to hydrate multiple components. Use @ngrx/entity  for server requests and selectors to manipulated data for a component.
{% endhint %}

{% hint style="danger" %}
If the data in the store is only shared by a single view, it should not be in the store.
{% endhint %}

### Prefer the use of atomic state

The idea is that sometimes **multiple state variables are related**, but no one of them is derivable from any other one. People expend a lot of effort trying to keep these variables in sync with each other, and often have bugs when they don't succeed.

A classic example is when working with forms, people might track whether the form is: `dirty`, `valid` `submitting`, `submitted`,`canceled`, `rejected`, etc.

These states are not necessarily derivable from each other, but often change in concert with each other = they are not atomic.

In such circumstances, this might point to the presence of another variable that IS atomic, from which our data can be derived, and reduces the complexity of our store.

In this case, we can look to a state machine. More information on state machines here:

[https://css-tricks.com/robust-react-user-interfaces-with-finite-state-machines/](https://css-tricks.com/robust-react-user-interfaces-with-finite-state-machines/)

So in our form example, we could store the state of our form state machine in our `state` and update that in our reducers.

Then, we **use selectors to get back all the variables** we needed before based on the single state machine state variable.

### 

## Actions

### Use Event Storming as a tool to figure it out the events and actions

This helps to think in terms of events and not in terms of things that you want to happens as a response when an event happens. This also helps the team to get better at using ngrx, because this is done as a group exercise. This can help to have one developer buld UI and selectors while  other builds the effect and reducers.

### Create actions based on events no commands

 Capture _events_ **not** _commands_ as you are separating the description of an event and the handling of that event.  
[https://ngrx.io/guide/store/actions\#writing-actions](https://ngrx.io/guide/store/actions#writing-actions)

### Categorize actions based on the event source

The actions should be categorized by event source. Having a file for each source can also help to locate them. 

[https://ngrx.io/guide/store/actions\#writing-actions](https://ngrx.io/guide/store/actions#writing-actions)

### Do not create actions that just modify part of the state \(this is a command\)

Actions should not be used as commands and should be event driven.   
&lt;CODE&gt;

### Use the three action approach\(request, success, failure\) for the server requests 

When having actions for server requests is good to always cover the three types of events that can happen: request, success and failure.

&lt;CODE&gt;

### Use a naming format

## Reducers

## Selectors

### Use selectors to filter and manipulate data from the store

To keep the store state in a usable format for multiple selectors and components.

{% hint style="info" %}
Selectors can be used to create view models
{% endhint %}

### Use high-level selectors

This helps with refactoring when the state changes it is easier to modify the higher selector



### Prefer to initialize selectors in the constructor

## Effects

### Avoid dispatching multiple actions from an effect

### Use stateless effects

## Entity

## Component Store

### Use it to handle local component state

## References

{% embed url="https://www.youtube.com/watch?v=LUGu1xQinU8" %}

{% embed url="https://www.youtube.com/watch?v=JmnsEvoy-gY&feature=youtu.be" %}



{% embed url="https://twitter.com/gdoutriaux/status/1279317072686743554" %}



Twitter  
[https://twitter.com/gdoutriaux](https://twitter.com/gdoutriaux)  
[https://twitter.com/KateSky8](https://twitter.com/KateSky8)  
[https://twitter.com/AlexOkrushko](https://twitter.com/AlexOkrushko)  
[https://twitter.com/brandontroberts](https://twitter.com/brandontroberts)







