# Best Practices / Guidelines

## Introduction

### Use the SHARI Principle to define what goes into the store

The NgRx core team has come up with a principle called [**SHARI**](https://ngrx.io/guide/store/why#when-should-i-use-ngrx-store-for-state-management), that can be used as a rule of thumb on data that needs to be added to the store.

* **Shared**: State that is shared between many components and services
* **Hydrated**: State that needs to be persisted and hydrated across page reloads
* **Available**: State that needs to be available when re-entering routes
* **Retrieved**: State that needs to be retrieved with a side effect, e.g. an HTTP request
* **Impacted**: State that is impacted by other components

> Having store in an application doesn't mean that every component should use it. Always evaluate what state should be in the store.

### Prefer using  Component Store or services with a behavior subject for local state

Global store better suited for managing global shared state, where [Component Store](https://ngrx.io/guide/component-store/comparison#benefits-and-trade-offs) shines managing more local, encapsulated state, as well as component UI state.

Instead of franken state

### Prefer using atomic state

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

### Prefer the use of containers and presentational components

The separation of components into presentational and [containers](https://ngrx.io/guide/schematics/container) helps make the application easy to reuse, understand, and refactor. 

It is easier to refactor and and reuse the [presentational components](https://www.youtube.com/watch?v=LUGu1xQinU8) that do not depend on state management or services.

## Actions

### Prefer categorizing actions based on the event source

The actions should be categorized by the event source. Having a file for each source can also help to locate them. 

This makes easier to **debug**, and the specific names makes it **easy to back track.**  
  
It is easier to refactor, because of the Single-Responsibility-Principle if multiple components use the same action, it is harder to abstract.  


![](.gitbook/assets/image%20%2810%29.png)

### Avoid creating actions that modify multiple parts of the state

> This is about creating actions that are actually commands and modify multiple parts of the state

### Avoid creating actions based on commands

Actions should [capture _events_ ](https://ngrx.io/guide/store/actions#writing-actions)**not** _commands_ as you are separating the description of an event and the handling of that event.

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

Do not create actions that just modify part of the state, since that is the equivalent to a command

```typescript
export const setIsLoading = createAction(
      '[Books Page] Set is loading',
      props<{isLoading: boolean}>()
);
```

### Prefer the use of a naming convention 

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

### _**Avoid sharing actions**_

This builds on the previous guidelines about categorizing actions based on events sources and making sure that the actions are events and not commands.

If we part from this, it should be clear that you should not be able to re-use actions since each event source \(component, effect, or service\) are different. 

 The following example shows a wrong action that doesnt have a specifict event source, represents a command and it is reused in multiple components:

```typescript
// Wrong action used in the Home, Client and Product Component
export const loadUser = createAction(
  '[User Data] Load user'
);
```

Instead you should create an action per event source and it should represent the event:

```typescript
export const enterHomePage  = createAction(
  '[Home Page] Enter'
);

export const enterClientPage  = createAction(
  '[Cient Page] Enter'
);

export const enterProductPage  = createAction(
  '[Product Page] Enter'
);
```

Having multiple actions does not mean that you will need to create separate reducers and effects if they happen to do the same thing, but by using good action hygiene  you will gain better traceability and debugabbility

```typescript
// Example of a reducer that is used for three actions that save the user
const userReducer = createReducer(
 initialState,
 on(enterHomePage,
    enterClientPage,
    enterProductPage,
    (state, action) => ({ ...state,user: action.user}))
);

// Example of an effect that listens to multiple actions
export class MovieEffects {

loadMovies$ = createEffect(() =>
   this.actions$.pipe(
      ofType(loadHomeUser,
            loadClientUser,
            loadProductUser
),
```



### Prefer using Event Storming

[Event Storming](https://dev.to/alfredoperez/my-notes-from-ngrx-workshop-from-ngconf-2020-part-2-actions-1ilh) helps to think in terms of events and not in terms of things that you want to happens as a response when an event happens. This also helps the team to get better at using ngrx, because this is done as a group exercise. This can help to have one developer come up with the  UI and selectors while  other builds the effect and reducers.

```text
[User on the Books Page] Create a Book
- BookRequiredProps

[User on the Books Page] Update a Book
-	BookRequiredProps
-	ID of the book being edited

[User on the Books Page] Delete a Book
-	ID of the book being deleted

[User on the Books Page] Cancel Editing a Book
```

### 

### Prefer using standardize actions for API requests 

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

### Avoid implicit state duplication

#### 

![](.gitbook/assets/image%20%2817%29.png)

In the code above the state is duplicated in the selected client, instead of using this, you can save the identifier  


![](.gitbook/assets/image%20%2819%29.png)

#### Implicit State Duplication

![](.gitbook/assets/image%20%2818%29.png)

Instead of saving this, create a selector 

### Avoid storing transformed data

It is wrong to keep flatten view models directly in the store because it hard to shared the same view model in different component that might not have the same view model. 

{% hint style="success" %}
It is preferable to keep original API data to hydrate multiple components. Use @ngrx/entity  for server requests and selectors to manipulated data for a component.
{% endhint %}

{% hint style="danger" %}
If the data in the store is only shared by a single view, it should not be in the store.\
{% endhint %}

### Avoid related nested data

### Prefer dictionaries \(ngrx entity\) versus arrays



## Selectors

### Prefer the use selectors to filter and manipulate data from the store

### Avoid to broad selectors by using composed selectors 

### Avoid saving selectors values into components properties 

### Prefer initializing selectors in the constructor 

### Prefer using custom RxJs operators

## Effects 

### Avoid dispatching multiple actions from an effect \(or component method\) 

### Avoid monolithic effects 

### Prefer the use of stateless effects

## References

{% embed url="https://www.sourceallies.com/2020/11/state-management-anti-patterns/" %}



## 

## WIP

### Be careful of how you handle subscriptions

prefer the use of async instead of trying to unsubscribe

#### Do not save observable values into properties

![](.gitbook/assets/image%20%2821%29.png)

![](.gitbook/assets/image%20%2826%29.png)

## 

## Selectors

### Use selectors to shape data

To keep the store state in a usable format for multiple selectors and components.

![](.gitbook/assets/image%20%2814%29.png)

{% hint style="info" %}
Selectors can be used to create view models
{% endhint %}

### Use high-level selectors

This helps with refactoring when the state changes it is easier to modify the higher selector

### _**Avoid too broad electors**_

everything will be re render when something changes, use composed selectors instead. Another issue is that with this broad selectors the components are aware of the shape of the data. Instead create selectors that return small discrete units of data

![](.gitbook/assets/image%20%2830%29.png)

![](.gitbook/assets/image%20%2822%29.png)

![](.gitbook/assets/image%20%2827%29.png)

### Prefer to initialize selectors in the constructor

This will help if you care about AOT or enable the TypeScript's strict mode 

### Compose selectors to benefit from memoization

![](.gitbook/assets/image%20%2813%29.png)

### Create custom RxJs Operators

![](.gitbook/assets/image%20%2812%29.png)

References:

* [https://www.youtube.com/watch?v=A4vKgnvXz9Y&list=WL&index=6&t=681s](https://www.youtube.com/watch?v=A4vKgnvXz9Y&list=WL&index=6&t=681s)
* [http://github.com/kaitlynekdahl/ngrx-selectors-demo](http://github.com/kaitlynekdahl/ngrx-selectors-demo)

## Effects

### Avoid dispatching multiple actions from an effect

### Use stateless effects

### Avoid Monolithic Effects

this effects are hard to test. Instead dispatch an action that the effect only does a unit of work and from there it keeps going until everything is done.

![](.gitbook/assets/image%20%2824%29.png)

![](.gitbook/assets/image%20%2823%29.png)

#### References

#### [https://www.sourceallies.com/2020/11/state-management-anti-patterns](https://www.sourceallies.com/2020/11/state-management-anti-patterns)



## References

{% embed url="https://labs.thisdot.co/resources/AdvancedNgRx" %}

{% embed url="https://www.sourceallies.com/2020/11/state-management-anti-patterns/" %}







### Twitter accounts from related content

* [https://twitter.com/gdoutriaux](https://twitter.com/gdoutriaux)
* [https://twitter.com/KateSky8](https://twitter.com/KateSky8)
* [https://twitter.com/AlexOkrushko](https://twitter.com/AlexOkrushko)
* [https://twitter.com/brandontroberts](https://twitter.com/brandontroberts)
* [https://twitter.com/kaitlynekdahl](https://twitter.com/kaitlynekdahl)











