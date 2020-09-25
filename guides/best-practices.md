# Best Practices / Guidelines

## TL;DR;



## Bedrock

### Prefer the separation of components into containers and presentational components

The separation of components into presentational and containers helps make the application easy to reuse, understand, and refactor. It is hard to reuse the presentational component that has dependencies on state management or services.  
  
[https://ngrx.io/guide/schematics/container](https://ngrx.io/guide/schematics/container)  
[https://www.youtube.com/watch?v=LUGu1xQinU8&feature=emb\_title](https://www.youtube.com/watch?v=LUGu1xQinU8&feature=emb_title)

### Use  SHARI Principle to define what goes into the store

The NgRx core team has come up with a principle called **SHARI**, that can be used as a rule of thumb on data that needs to be added to the store.

* **Shared**: State that is shared between many components and services
* **Hydrated**: State that needs to be persisted and hydrated across page reloads
* **Available**: State that needs to be available when re-entering routes
* **Retrieved**: State that needs to be retrieved with a side effect, e.g. an HTTP request
* **Impacted**: State that is impacted by other components

{% hint style="info" %}
Having store in an application doesnt mean that every component should use it. Always evaluate what state should be in the store.
{% endhint %}

[https://ngrx.io/guide/store/why\#when-should-i-use-ngrx-store-for-state-management](https://ngrx.io/guide/store/why#when-should-i-use-ngrx-store-for-state-management)

### Use  ComponentStore or services for local state

Global store better suited for managing global shared state, where ComponentStore shines managing more local, encapsulated state, as well as component UI state.  
  
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

### Prefer the use of atomic state to represent the component or request state

The idea is that sometimes **multiple state variables are related**, but no one of them is derivable from any other one. People expend a lot of effort trying to keep these variables in sync with each other, and often have bugs when they don't succeed.

A classic example is when working with forms, people might track whether the form is: `dirty`, `valid` `submitting`, `submitted`,`canceled`, `rejected`, etc.

These states are not necessarily derivable from each other, but often change in concert with each other = they are not atomic.

In such circumstances, this might point to the presence of another variable that IS atomic, from which our data can be derived, and reduces the complexity of our store  


```typescript
export const enum LoadingState {
    INIT = 'INIT',
    LOADING = 'LOADING',
    LOADED = 'LOADED',
    // Any other states, e.g Dirty, daving, saved, etc.
}
export interface ErrorState {
    errorMsg: string;
}

export type CallState = LoadingState | ErrorState;

// Helper function to extract error, if there is one.
export function getError(callState: CallState): string | null { 
    if ((callState as ErrorState).errorMsg !== undefined) { 
        return (callState as ErrorState).errorMsg;
    } 
    return null;
}
```

In this case,  store the state of our form state machine in our `state` and update that in our reducers.

Then, we **use selectors to get back all the variables** we needed before based on the single state machine state variable.

This can also be applied and reused for `ngrx/entity` by creating an interface that overloads the `EntityState` and adds the `state` prop.  


```typescript
import { EntityState } from '@ngrx/entity';

export interface EntityStateModel<T, U = void> extends EntityState<T> {
   state: CallState;
   // Any other props common in your entities
}
```

_**References**_

* [https://medium.com/angular-in-depth/ngrx-how-and-where-to-handle-loading-and-error-states-of-ajax-calls-6613a14f902d](https://medium.com/angular-in-depth/ngrx-how-and-where-to-handle-loading-and-error-states-of-ajax-calls-6613a14f902d)
* [https://css-tricks.com/robust-react-user-interfaces-with-finite-state-machines/](https://css-tricks.com/robust-react-user-interfaces-with-finite-state-machines/)
* [https://twitter.com/VojtechMasek/status/1278665431910408201](https://twitter.com/VojtechMasek/status/1278665431910408201)

## Actions

### Use Event Storming as a tool to figure it out the events and actions

This helps to think in terms of events and not in terms of things that you want to happens as a response when an event happens. This also helps the team to get better at using ngrx, because this is done as a group exercise. This can help to have one developer buld UI and selectors while  other builds the effect and reducers.

![](../.gitbook/assets/image%20%289%29.png)

_**References**_:  
 [https://dev.to/alfredoperez/my-notes-from-ngrx-workshop-from-ngconf-2020-part-2-actions-1ilh](https://dev.to/alfredoperez/my-notes-from-ngrx-workshop-from-ngconf-2020-part-2-actions-1ilh)

### Create actions based on events no commands

 Capture _events_ **not** _commands_ as you are separating the description of an event and the handling of that event.

```typescript
function submitFormCommands({todo}){
    this.store.dispatch(postTodo());
    this.store.dispatch(openToast('Form Submitted');
    this.store.dispatch(navigateTo('Dashboard');
}

function submitFormEvents({todo}){
    this.store.dispatch(todoSubmitted({todo}));
}
```

{% hint style="danger" %}
Do not create actions that just modify part of the state, since that is the equivalent to a command
{% endhint %}

{% hint style="danger" %}
Do not reuse actions
{% endhint %}

References:  
[https://ngrx.io/guide/store/actions\#writing-actions](https://ngrx.io/guide/store/actions#writing-actions)  
[https://labs.thisdot.co/resources/AdvancedNgRx](https://labs.thisdot.co/resources/AdvancedNgRx)

### Categorize actions based on the event source

The actions should be categorized by the event source. Having a file for each source can also help to locate them. 

This makes easier to **debug**, and the specific names makes it **easy to back track.**  
  
It is easier to refactor, because of the Single-Responsibility-Principle if multiple components use the same action, it is harder to abstract.  


![](../.gitbook/assets/image%20%2810%29.png)

_**References:**_

* [https://ngrx.io/guide/store/actions\#writing-actions](https://ngrx.io/guide/store/actions#writing-actions)

### _**Agree on a naming convention**_

Decide on a naming convention and stick with it. 

Here is an example of a naming convention:

* The **category** of the action is captured within the square brackets `[]`
* Use present or past tense to **describe the event occurred**   


  * When related to components you can use present tense because they are related to events. It is like in HTML the events do not use past tense. Eg. `OnClick` or `click` is not `OnClicked` or `clicked`

  ```text
    export const createBook = createAction(
        '[Books Page] Create a book',
        props<{book: BookRequiredProps}>()
    );

    export const selectBook = createAction(
        '[Books Page] Select a book',
        props<{bookId: string}>()
    );
  ```



  * When the actions are related to API you can use past tense because they are used to describe an action that happened

  ```text
  export const bookUpdated = createAction(
      '[Books API] Book Updated Success',
      props<{book: BookModel}>()
  );

  export const bookDeleted = createAction(
     '[Books API] Book Deleted Success',
     props<{bookId: string}>()
  );
  ```

### Use success and failure actions for the server requests 

```typescript
export const searchSuccess = createAction(
  '[Books/API] Search Success',
  props<{ books: Book[] }>()
);

export const searchSuccess = createAction(
  '[Books/API] Search Success',
  props<{ books: Book[] }>()
);

export const searchFailure = createAction(
  '[Books/API] Search Failure',
  props<{ errorMsg: string }>()
);
```

## Reducers

Reducers are boring keep them that way

  
**References**:

* [https://twitter.com/brandontroberts/status/1253144569606295552](https://twitter.com/brandontroberts/status/1253144569606295552)

## Selectors

### Use selectors to shape data

To keep the store state in a usable format for multiple selectors and components.

![](../.gitbook/assets/image%20%2814%29.png)

{% hint style="info" %}
Selectors can be used to create view models
{% endhint %}

### Use high-level selectors

This helps with refactoring when the state changes it is easier to modify the higher selector

### Prefer to initialize selectors in the constructor

This will help if you care about AOT or enable the TypeScript's strict mode 

### Compose selectors to benefit from memoization

![](../.gitbook/assets/image%20%2813%29.png)

### Create custom RxJs Operators

![](../.gitbook/assets/image%20%2812%29.png)

References:

* [https://www.youtube.com/watch?v=A4vKgnvXz9Y&list=WL&index=6&t=681s](https://www.youtube.com/watch?v=A4vKgnvXz9Y&list=WL&index=6&t=681s)
* [http://github.com/kaitlynekdahl/ngrx-selectors-demo](http://github.com/kaitlynekdahl/ngrx-selectors-demo)

## Effects

### Avoid dispatching multiple actions from an effect

### Use stateless effects

## Entity

## Component Store

### Use it to handle local component state

### Use it with @ngrx/entity

## References





### Twitter accounts from related content

* [https://twitter.com/gdoutriaux](https://twitter.com/gdoutriaux)
* [https://twitter.com/KateSky8](https://twitter.com/KateSky8)
* [https://twitter.com/AlexOkrushko](https://twitter.com/AlexOkrushko)
* [https://twitter.com/brandontroberts](https://twitter.com/brandontroberts)
* [https://twitter.com/kaitlynekdahl](https://twitter.com/kaitlynekdahl)











