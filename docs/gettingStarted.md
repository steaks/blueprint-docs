# Getting Started

This tutorial walks you through building the [hello world](./examples.md#hello-world) application. It takes ~10 minutes to complete the tutorial.

## Prerequisites

Install [Node](https://nodejs.org/en/) with node >= 16 and npm >= 8. Install [GNU make](https://www.gnu.org/s/make/manual/make.html) version >= 4.

## Initialize your project 

Download and execute the `createBlueprint.sh` to initialize a new project.

```shell
wget https://raw.githubusercontent.com/steaks/blueprint-templates/main/createBlueprint.sh
chmod +x createBlueprint.sh
./createBlueprint.sh {your-app-name}
```

## Install Node dependencies, build, and serve

Blueprint recommends using a Makefile to manage scripts for installing, building and serving. The skeleton project comes with a Makefile populated with useful functions. Use these commands to install node dependencies, build, and serve your application.

```shell
cd {your-app-name}
make install
make build
make run-server # Run in separate terminal.
make run-ui # Run in separate terminal. Open browser to http://localhost:3000
make run-blueprint # Run in separate terminal. Open browser to http://localhost:3001
```

## Build your app

Create an app called "helloWorld." An app encapsulates specific functionality - typically a page or re-usable widget on a website.

### Build the server-side.

```typescript
/* /server/src/apps/helloWorld.ts */
import {app} from "blueprint-server";

const helloWorld = app(() => {
  return {
    name: "helloWorld",
    state: [],
    events: [],
    tasks: []
  };
});

export default helloWorld;
```

### Serve your app.

```typescript
/* /server/src/index.ts */
import "dotenv/config";
import {serve} from "blueprint-server";
import helloWorld from "./apps/helloWorld";
import session from "./session";

const options = {
  cors: {origin: process.env.CORS_ORIGIN}
};

serve({helloWorld}, session, options);
```

### Hook up the frontend.

```typescript
/* /ui/src/apps/helloWorld.tsx */
import {app} from "blueprint-react";

export const HelloWorld = app("helloWorld");

const UI = () => {
  return (
    <HelloWorld>
      <div>Hello World!!</div>
    </HelloWorld>
  );
};

export default UI;
```

### Route to your app.

```typescript
/* /ui/src/index.tsx */
import React from 'react';
import ReactDOM from 'react-dom/client';
import {Blueprint} from "blueprint-react";
import {BrowserRouter, Route, Routes} from "react-router-dom";
import Home from "./home";
import HelloWorld from "./apps/helloWorld";

const root = ReactDOM.createRoot(
  document.getElementById("root") as HTMLElement
);

root.render(
  <React.StrictMode>
    <Blueprint uri={process.env.REACT_APP_URI}>
      <BrowserRouter>
        <Routes>
          <Route path="/" element={<Home />}/>
          <Route path="/helloWorld" element={<HelloWorld />} />
        </Routes>
      </BrowserRouter>
    </Blueprint>
  </React.StrictMode>
);
```

### Link to your app on the home page.

```typescript
/* /ui/src/apps/home.tsx */
const UI = () => {
  return (
    <>
      <h1>Welcome to Blueprint!</h1>
      <div>This is the blueprint hello world template. In addition to this page a user profile Blueprint App has been created for you.</div>
      <br />
      <a href="/helloWorld">Hello World</a>
    </>
  );
};

export default UI;
```

## Build state

### Create state in the server-side of your app.

```typescript
/* /server/src/apps/helloWorld.ts */
import {app, state} from "blueprint-server";

const helloWorld = app(() => {
  const myState$ = state("myState", "Hello State!");

  return {
    name: "helloWorld",
    state: [myState$],
    events: [],
    tasks: []
  };
});

export default helloWorld;
```

### Hook it up to your frontend.

```typescript
/* /ui/src/apps/helloWorld.tsx */
import {app, state} from "blueprint-react";

const HelloWorld = app("helloWorld");
const useMyState = state<string>("helloWorld", "myState");

const UI = () => {
  const [myState, setMyState] = useMyState();

  return (
    <HelloWorld>
      <div>Hello World!!</div>
      <input defaultValue={myState} onChange={e => setMyState(e.target.value)} />
    </HelloWorld>
  );
};

export default UI;
```

## Build an event

### Create an event in the server-side of your app.

```typescript
/* /server/src/apps/helloWorld.ts */
import {app, event, state} from "blueprint-server";

const helloWorld = app(() => {
  const myState$ = state("myState", "Hello State!");
  const myEvent$ = event("myEvent");

  return {
    name: "helloWorld",
    state: [myState$],
    events: [myEvent$],
    tasks: []
  };
});

export default helloWorld;
```

### Hook it up to your frontend.

```typescript
/* /ui/src/apps/helloWorld.tsx */
import {app, state, event} from "blueprint-react";

const HelloWorld = app("helloWorld");
const useMyState = state<string>("helloWorld", "myState");
const useMyEvent = event("helloWorld", "myEvent");

const UI = () => {
  const [myState, setMyState] = useMyState();
  const [triggerMyEvent] = useMyEvent();

  return (
    <HelloWorld>
      <div>Hello World!!</div>
      <input defaultValue={myState} onChange={e => setMyState(e.target.value)} />
      <button onClick={triggerMyEvent}>Trigger My Event!</button>
    </HelloWorld>
  );
};

export default UI;
```

## Build your first task

Build a task that counts the number of works in your myState$ variable. 

### Create a task in the server-side of your app.

Write a javascript function that counts the number of words provided a string. Then invoke that function with a task that is triggered when myState$ changes.

```typescript
/* /server/src/apps/helloWorld.ts */
import {app, event, state, task, from} from "blueprint-server";

const wordCount = (words: string): number => {
  const trimmedWords = words.trim();
  return trimmedWords.length === 0 ? 0 : trimmedWords.trim().split(/\s/).length;
};

const helloWorld = app(() => {
  const myState$ = state("myState", "Hello State!");
  const myEvent$ = event("myEvent");
  const wordCount$ = task(
    from(wordCount, myState$)
  );

  return {
    name: "helloWorld",
    state: [myState$],
    events: [myEvent$],
    tasks: [wordCount$]
  };
});

export default helloWorld;
```


### Hook it up to your frontend.

```typescript
/* /ui/src/apps/helloWorld.tsx */
import {app, state, event, task} from "blueprint-react";

const HelloWorld = app("helloWorld");
const useMyState = state<string>("helloWorld", "myState");
const useMyEvent = event("helloWorld", "myEvent");
const useWordCount = task<number>("helloWorld", "wordCount")

const UI = () => {
  const [myState, setMyState] = useMyState();
  const [triggerMyEvent] = useMyEvent();
  const [wordCount] = useWordCount();

  return (
    <HelloWorld>
      <div>Hello World!!</div>
      <input defaultValue={myState} onChange={e => setMyState(e.target.value)} />
      <button onClick={triggerMyEvent}>Trigger My Event!</button>
      <div>Word Count: {wordCount}</div>
    </HelloWorld>
  );
};

export default UI;
```

## Build your second task 

Build a task that tracks the number of clicks tied to myEvent$.

### Create a task in the server-side of your app.

Track the number of clicks with a variable _clickCount. And use a task to increment _clickCount everytime myEvent$ is triggered.

```typescript
/* /server/src/apps/helloWorld.ts */
import {app, event, state, task, from} from "blueprint-server";

const wordCount = (words: string): number => {
  const trimmedWords = words.trim();
  return trimmedWords.length === 0 ? 0 : trimmedWords.trim().split(/\s/).length;
};

let _clickCount = 0;
const clickCount = () => {
  _clickCount = _clickCount + 1;
  return _clickCount;
};

const helloWorld = app(() => {
  const myState$ = state("myState", "Hello State!");
  const myEvent$ = event("myEvent");
  const wordCount$ = task(
    from(wordCount, myState$)
  );

  const clickCount$ = task(
    "clickCount",
    {triggers: [myEvent$]},
    from(clickCount)
  );

  return {
    name: "helloWorld",
    state: [myState$],
    events: [myEvent$],
    tasks: [wordCount$, clickCount$]
  };
});

export default helloWorld;
```

### Hook it up to the frontend.

```typescript
/* /ui/src/apps/helloWorld.tsx */
import {app, state, event, task} from "blueprint-react";

const HelloWorld = app("helloWorld");
const useMyState = state<string>("helloWorld", "myState");
const useMyEvent = event("helloWorld", "myEvent");
const useWordCount = task<number>("helloWorld", "wordCount")
const useClickCount = task<number>("helloWorld", "clickCount");

const UI = () => {
  const [myState, setMyState] = useMyState();
  const [triggerMyEvent] = useMyEvent();
  const [wordCount] = useWordCount();
  const [clickCount] = useClickCount();


  return (
    <HelloWorld>
      <div>Hello World!!</div>
      <input defaultValue={myState} onChange={e => setMyState(e.target.value)} />
      <button onClick={triggerMyEvent}>Trigger My Event!</button>
      <div>Word Count: {wordCount}</div>
      <div>Click Count: {clickCount || 0}</div>
    </HelloWorld>
  );
};

export default UI;
```

## View the Control Flow Diagram

Go to http://localhost:3001. Since Blueprint tracks your control-flow it is able to display an interactive control-flow diagram.