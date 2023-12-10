# Architecture

## Core Concepts

**App -** Logical grouping of features - typically a website page or re-usable widget.

**Events -** Signals that can trigger tasks.

**State -** Variables that track state. State changes can trigger tasks.

**Tasks -** Declarative functions that may be triggered by events or state changes

### App

An app is the organizational structure for building. Typically, you will build apps that correspond with a page or re-usable widget in the browser. On the server you will create an app that contains state, events, and tasks. On the frontend you will create a corresponding React component that wraps the ui elements for your app.

```typescript
//server
const myApp = app(() => {
  // Add state, events, and tasks
  
  return {
    name: "myApp",
    state: [], //TODO state 
    events: [], //TODO events
    tasks: [] //TODO tasks 
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

Events are signals that can trigger tasks. They are useful for responding to button clicks, link clicks, toggle switches, etc. You can trigger events from either the frontend or backend. See [tasks](#tasks) for details about triggering and subscribing to events.

```typescript
//server
const myApp = app(() => {
  const myEvent$ = event("myEvent"); 
  
  return {
    name: "myApp",
    state: [], //TODO state 
    events: [myEvent$], //TODO events
    tasks: [] //TODO tasks 
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
    tasks: [] //TODO tasks 
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

### Tasks

Tasks wrap typescript functions. Tasks can be triggered by events or state changes. And they can inject state variables as parameters into the function being invoked.

#### Simple task

This simple task wraps the function `myFunc` and will be invoked when the app is initialized.

```typescript
//server
const myFunc = () => 
  console.log("Hello World");

const myApp = app(() => {
  const myFunc$ = task(from(myFunc));

  return {
    name: "myApp",
    state: [], //TODO state 
    events: [], //TODO events
    tasks: [myFunc$] //TODO tasks 
  };
});
```

#### Task with asyncronous function

Tasks work with asyncronous functions natively.

```typescript
//server
const myFunc = (): Promise<string> => 
  Promise.resolve("Hello World");

const myApp = app(() => {
  const myFunc$ = task(from(myFunc));

  return {
    name: "myApp",
    state: [], //TODO state 
    events: [], //TODO events
    tasks: [myFunc$] //TODO tasks 
  };
});
```

#### Task injected with state

This task wraps the myFunc function and passes the variable tracked by the a$ into the function when invoked. Tasks trigger when state it depends on changes or on initialization if it doesn't depend on any state. This task will be triggered when a$ changes.

```typescript
const myFunc = (a: string) =>
  console.log(`a: ${a}`);

const myApp = app(() => {
  const a$ = state<string>();
  const myFunc$ = task(from(myFunc, a$));

  return {
    name: "myApp",
    state: [a$], //TODO state 
    events: [], //TODO events
    tasks: [myFunc$] //TODO tasks 
  };
});
```

#### Tasks triggered by events

Tasks can be triggered by events specified in the `triggers` option. This task will be triggered by myEvent$.

```typescript
//server
const myFunc = () => 
  console.log("Hello World");

const myApp = app(() => {
  const myEvent$ = event();
  const myFunc$ = task(
    "myFunc",
    {triggers: [myEvent$]},
    from(myFunc)
  );

  return {
    name: "myApp",
    state: [], //TODO state 
    events: [myEvent$], //TODO events
    tasks: [myFunc$] //TODO tasks 
  };
});
```

#### Tasks can trigger events

After a task has completed it's function it can trigger downstream events. This task invokes myFunc. Then it triggers myEvent$.

```typescript
//server
const myFunc = () => 
  console.log("Hello World");

const myApp = app(() => {
  const myEvent$ = event();
  const myFunc$ = task(
    "myFunc",
    {},
    from(myFunc),
    trigger(myEvent$)
  );

  return {
    name: "myApp",
    state: [], //TODO state 
    events: [myEvent$], //TODO events
    tasks: [myFunc$] //TODO tasks 
  };
});
```

#### Tasks are also events and state

A task is considered to be both an event and state. So you can trigger a task just like you can trigger an event. And you can inject the state of the result of a task just like you'd inject state.

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