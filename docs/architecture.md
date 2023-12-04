# Architecture

## Core Concepts

In Blueprint you build apps using events, state, and hooks. You do not need to worry about webserver routing or networking between frontend and server. Just build events, state, and business logic. Blueprint handles the rest!

**App -** Logical grouping of features. Apps contain state, events, and hooks that are synchronized between your server and frontend.

**Events -** Signals that can kick off hooks. Use events to signal that a button has been clicked or a table has been updated in the database.

**State -** System to keep track of variables. State changes can trigger hooks. State values can be leveraged in hooks. Use states to track inputs from a user (e.g. text inputs, switch button state).

**Hooks -** Business logic that may be triggered by events or state changes. Use hooks to query your database, insert into your database, run calculations, etc. A hook may also kick off events and make state changes.

#### App

An app is the organizational structure for building an app. Typically, you will build apps that correspond with a page or re-usable widget in the browser. On the server you will create an app that contains state, events, and hooks. On the frontend you will create a corresponding React component that wraps the ui elements for your app.

```typescript
//server

//myApp will be served through the blueprint serve function
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
export const MyApp = app("myApp"); //MyApp will be used as a React component.

```

#### Events

Events are signals that can kick off hooks. On the server you can create events via the `event` function. On the frontend you can create corresponding events. You can trigger events from either the frontend or backend. See [hooks](#hooks) for details about triggering and subscribing to events.

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

#### State

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
      <button onClick={triggerMyEvent}>Trigger My Event</button>
    </MyApp>
  );
};

```

#### Hooks

TODO

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