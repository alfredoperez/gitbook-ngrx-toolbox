# Best Practices / Guidelines

## Foundation

### Use containers and presentational components

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

Prefer the use of atomic state

### 

## Actions

### Use Event Storming as a tool to figure it out the events and actions

This helps to think in terms of events and not in terms of things that you want to happens as a response when an event happens. This also helps the team to get better at using ngrx, because this is done as a group exercise. This can help to have one developer buld UI and selectors while  other builds the effect and reducers.

### Create actions based on events no commands

### Categorize actions based on the event source

### Do not create actions that just modify part of the state \(this is a command\)

### Use the three action approach\(request, success, failure\) for the server requests 

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

{% embed url="https://twitter.com/gdoutriaux/status/1279317072686743554" %}



Twitter

{% embed url="https://twitter.com/gdoutriaux" %}







