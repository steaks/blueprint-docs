# Architecture

## Core Concepts

**App -** Logical grouping of features - typically a website page or re-usable widget.

**Events -** Signals that can trigger hooks.

**State -** Variables that track state. State changes can trigger hooks.

**Hooks -** Declarative functions that may be triggered by events or state changes

### App

An app is the organizational structure for building. Typically, you will build apps that correspond with a page or re-usable widget in the browser. On the server you will create an app that contains state, events, and hooks. On the frontend you will create a corresponding React component that wraps the ui elements for your app.

```typescript
//server
const myApp = app(() => {
  // Add state, events, and hooks
  
  return {
    name: "myApp",
    state: [], //TODO state 
    events: [], //TODO events
    hooks: [] //TODO hooks 
  };
});
```

```typescript
//ui
const MyApp = app("myApp");

const UI = () => {
  return (
    <MyApp>
      {/* Your Components Here */}
    </MyApp>
  );
};
```

### Events

Events are signals that can trigger hooks. They are useful for responding to button clicks, link clicks, toggle switches, etc. You can trigger events from either the frontend or backend. See [hooks](#hooks) for details about triggering and subscribing to events.

```typescript
//server
const myApp = app(() => {
  const myEvent$ = event("myEvent"); 
  
  return {
    name: "myApp",
    state: [], //TODO state 
    events: [myEvent$], //TODO events
    hooks: [] //TODO hooks 
  };
});
```

```typescript
//ui
const MyApp = app("myApp");
const useMyEvent = event("myApp", "myEvent");

const MyAppUI = () => {
  const [triggerMyEvent] = useMyEvent();
  
  return (
    <MyApp>
      <button onClick={triggerMyEvent}>Trigger My Event</button>
    </MyApp>
  );
};
```

### State

State is synchronized across the server and frontend. On the server you can create state via the `state` function. On the frontend you can create corresponding states.

```typescript
//server
const myApp = app(() => {
  const myState$ = state<string>("myState");

  return {
    name: "myApp",
    state: [myState$], //TODO state 
    events: [], //TODO events
    hooks: [] //TODO hooks 
  };
});
```

```typescript
//ui
const useMyState = state("myApp", "myState");

const MyAppUI = () => {
  const [myState, setMyState] = useMyEvent();

  return (
    <MyApp>
      <input defaultValue={myState} onChange={e => setMyState(e.currentTarget.value)} />
    </MyApp>
  );
};

```

### Hooks

Hooks wrap typescript functions. Hooks can be triggered by events or state changes. And they can inject state variables as parameters into the function being invoked.

#### Simple hook

This simple hook wraps the function `myFunc` and will be invoked when the app is initialized.

```typescript
//server
const myFunc = () => 
  console.log("Hello World");

const myApp = app(() => {
  const myFunc$ = hook(operator(myFunc));

  return {
    name: "myApp",
    state: [], //TODO state 
    events: [], //TODO events
    hooks: [myFunc$] //TODO hooks 
  };
});
```

#### Hook with asyncronous function

Hooks work with asyncronous functions natively.

```typescript
//server
const myFunc = (): Promise<string> => 
  Promise.resolve("Hello World");

const myApp = app(() => {
  const myFunc$ = hook(operator(myFunc));

  return {
    name: "myApp",
    state: [], //TODO state 
    events: [], //TODO events
    hooks: [myFunc$] //TODO hooks 
  };
});
```

#### Hook injected with state

This hook wraps the myFunc function and passes the variable tracked by the a$ into the function when invoked. Hooks trigger when state it depends on changes or on initialization if it doesn't depend on any state. This hook will be triggered when a$ changes.

```typescript
const myFunc = (a: string) =>
  console.log(`a: ${a}`);

const myApp = app(() => {
  const a$ = state<string>();
  const myFunc$ = hook(operator(myFunc, a$));

  return {
    name: "myApp",
    state: [a$], //TODO state 
    events: [], //TODO events
    hooks: [myFunc$] //TODO hooks 
  };
});
```

#### Hooks triggered by events

Hooks can be triggered by events specified in the `triggers` option. This hook will be triggered by myEvent$.

```typescript
//server
const myFunc = () => 
  console.log("Hello World");

const myApp = app(() => {
  const myEvent$ = event();
  const myFunc$ = hook(
    "myFunc",
    {triggers: [myEvent$]},
    operator(myFunc)
  );

  return {
    name: "myApp",
    state: [], //TODO state 
    events: [myEvent$], //TODO events
    hooks: [myFunc$] //TODO hooks 
  };
});
```

#### Hooks can trigger events

After a hook has completed it's function it can trigger downstream events. This hook invokes myFunc. Then it triggers myEvent$.

```typescript
//server
const myFunc = () => 
  console.log("Hello World");

const myApp = app(() => {
  const myEvent$ = event();
  const myFunc$ = hook(
    "myFunc",
    {},
    operator(myFunc),
    trigger(myEvent$)
  );

  return {
    name: "myApp",
    state: [], //TODO state 
    events: [myEvent$], //TODO events
    hooks: [myFunc$] //TODO hooks 
  };
});
```

#### Hooks are also events and state

A hook is considered to be both an event and state. So you can trigger a hook just like you can trigger an event. And you can inject the state of the result of a hook just like you'd inject state.

#### File Structure

Blueprint has four top-level directories: scripts, server, shared, and ui.

**scripts** - Build scripts used by the makefile for tasks like installing, compiling, and cleaning<br/>
**server** - Code for the server-side of your application. This is where you'll write your business logic.<br/>
**shared** - Code shared between your server and ui. Include types shared between your server and ui.<br/>
**ui** - Code for your user interface. Typically, you'll include display logic, preferring to put business logic on your server.

```
/scripts
/server
  /src
    /apps
    index.ts
    diagram.ts
    session.ts
/shared
  /src
    /apps
/ui
  /src
    /apps
  home.tsx  
  index.tsx
```