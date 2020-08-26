# Best Practices / Guidelines

## Foundation

### Use containers and presentational components

### Use  SHARI Principle to define what goes into the store

### Do not put related nested data

### Use atomic state for UI state

### Do not put view models into the store

### Use service with a subject or component-store for component state

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





