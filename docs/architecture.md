# Architecture

## Core Concepts

**App -** Logical grouping of features - typically a website page or re-usable widget.

**Events -** Signals that can trigger queries ro effects.

**State -** Variables that track state. State changes can trigger queries or effects.

**Queries -** Declarative functions that fetch data and may be triggered by events or state changes.

**Effects -** Declarative functions that cause side effects and may be triggered by events or state changes.

### App

An app is the organizational structure for building. Typically, you will build apps that correspond with a page or re-usable widget in the browser. On the server you will create an app that contains state, events, queries, or effects. On the frontend you will create a corresponding React component that wraps the ui elements for your app.

```typescript
//server
const myApp = app(() => {
  // Add state, events, queiries, and effects 
  
  return {
    name: "myApp",
    state: [], //TODO state 
    events: [], //TODO events
    queries: [], //TODO queries
    effects: [] //TODO effects
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

Events are signals that can trigger queries or effects. They are useful for responding to button clicks, link clicks, toggle switches, etc. You can trigger events from either the frontend or backend. See [queries](#queries) for details about triggering and subscribing to events.

```typescript
//server
const myApp = app(() => {
  const myEvent$ = useEvent("myEvent"); 
  
  return {
    name: "myApp",
    state: [], //TODO state 
    events: [myEvent$], //TODO events
    queries: [], //TODO queries
    effects: [] //TODO effects
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
  const myState$ = useState<string>("myState");

  return {
    name: "myApp",
    state: [myState$], //TODO state 
    events: [], //TODO events
    queries: [], //TODO queries
    effects: [] //TODO effects
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

### Queries

Queries wrap typescript functions. Queries can be triggered by events or state changes. And they can inject state variables as parameters into the function being invoked.

#### Simple query

This simple query wraps the function `myFunc` and will be invoked when the app is initialized.

```typescript
//server
const myFunc = () => 
  "Hello World";

const myApp = app(() => {
  const myFunc$ = useQuery(myFunc);

  return {
    name: "myApp",
    state: [], //TODO state 
    events: [], //TODO events
    queries: [myFunc$], //TODO queries
    effects: [] //TODO effects
  };
});
```

#### Queries with asyncronous function

Queries work with asyncronous functions natively.

```typescript
//server
const myFunc = (): Promise<string> => 
  Promise.resolve("Hello World");

const myApp = app(() => {
  const myFunc$ = useQuery(myFunc);

  return {
    name: "myApp",
    state: [], //TODO state 
    events: [], //TODO events
    queries: [myFunc$], //TODO queries
    effects: [] //TODO effects
  };
});
```

#### Queries injected with state

This query wraps the myFunc function and passes the variable tracked by the a$ into the function when invoked. Queries trigger when state it depends on changes or on initialization if it doesn't depend on any state. This query will be triggered when a$ changes.

```typescript
const myFunc = (a: string) =>
  console.log(`a: ${a}`);

const myApp = app(() => {
  const a$ = useState<string>();
  const myFunc$ = useQuery(myFunc, [a$]);

  return {
    name: "myApp",
    state: [a$], //TODO state 
    events: [], //TODO events
    queries: [myFunc$], //TODO queries
    effects: [] //TODO effects
  };
});
```

#### Queries triggered by events

Queries can be triggered by events specified in the `triggers` option. This query will be triggered by myEvent$.

```typescript
//server
const myFunc = () => 
  console.log("Hello World");

const myApp = app(() => {
  const myEvent$ = useEvent();
  const myFunc$ = useQuery(myFunc, [], {triggers: [myEvent$]});

  return {
    name: "myApp",
    state: [], //TODO state 
    events: [myEvent$], //TODO events
    queries: [myFunc$], //TODO queries
    effects: [] //TODO effects
  };
});
```

#### Queries can trigger events

After a query has completed it's function it can trigger downstream events. This query invokes myFunc. Then it triggers myEvent$.

```typescript
//server
const myFunc = () => 
  console.log("Hello World");

const myApp = app(() => {
  const myEvent$ = useEvent();
  const myFunc$ = useQuery(myFunc, [], {onSuccess: [myEvent$]});

  return {
    name: "myApp",
    state: [], //TODO state 
    events: [myEvent$], //TODO events
    queries: [myFunc$], //TODO queries
    effects: [myFunc$], //TODO effects
  };
});
```

#### Queries are also events and state

#### File Structure

Blueprint has four top-level directories: scripts, server, shared, and ui.

**scripts** - Build scripts used by the makefile for queries like installing, compiling, and cleaning<br/>
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