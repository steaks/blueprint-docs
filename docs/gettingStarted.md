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

Run the commands below to spin up a Blueprint web application with a helloWorld app.

```shell
cd {your-app-name}
make install
make build
make run-server
make run-ui # Run in separate terminal. 
# Open browser to http://localhost:3000 to see your app. Follow instructions to navigate to the helloWorld app.
# Open browser to http://localhost:3000/__blueprint__. This will contain diagrams of your architecture that update as you build.
```

## Examine the File Structure

At this point you have a working Blueprint application with a helloWorld app. Examine the helloWorld files listed below as they will be where you code in this tutorial.

|File Path|Description|
|----|----|
|/server/src/apps/helloWorld.ts|Server logic for your app|
|/ui/src/apps/helloWorld.tsx|UI components for your app|
|/shared/src/apps/helloWorld.ts|Shared types between the server and ui of your app|

Additionally, notice that helloWorld has been hooked into the infrastructure of your application in `/server/src/index.ts` and `/ui/src/index.tsx`.

## Build state

State are variables that track information in your app. They are typically inputs from your user. State is automatically synced between your server and ui. Create your first state variable.

```typescript
/* /server/src/apps/helloWorld.ts */
import {app, state} from "blueprint-server";

const helloWorld = app(() => {
  const myInput$ = state("myInput", "Hello Input!");

  return {
    name: "helloWorld",
    state: [myInput$],
    events: [],
    tasks: []
  };
});

export default helloWorld;
```

```typescript
/* /ui/src/apps/helloWorld.tsx */
import {app, state} from "blueprint-react";

const HelloWorld = app("helloWorld");
const useMyState = state<string>("helloWorld", "myInput");

const UI = () => {
  const [myInput, setMyState] = useMyState();

  return (
    <HelloWorld>
      <div>Hello World!!</div>
      <input defaultValue={myInput} onChange={e => setMyState(e.target.value)} />
    </HelloWorld>
  );
};

export default UI;
```

!!! Note 
    Server updates are not automatically reflected. `make run-server` compiles and serves your Blueprint server. So CTRL+C and re-run `make run-server` to see changes.

## Build a task

Tasks are functions (can be async) that can be triggered by events or state changes. Tasks are often used to fetch data from your database, insert data into your db, or run an intense calculation. For your first task you will build a word counter.

```typescript
/* /server/src/apps/helloWorld.ts */
import {app, state, task, from} from "blueprint-server";

const wordCount = (input: string): number => {
  const trimmedInput = input.trim();
  return trimmedInput.length === 0 ? 0 : trimmedInput.trim().split(/\s/).length;
};

const helloWorld = app(() => {
  const myInput$ = state("myInput", "Hello Input!");
  const wordCount$ = task(
    from(wordCount, myInput$)
  );

  return {
    name: "helloWorld",
    state: [myInput$],
    events: [],
    tasks: [wordCount$]
  };
});

export default helloWorld;
```

```typescript
/* /ui/src/apps/helloWorld.tsx */
import {app, state} from "blueprint-react";

const HelloWorld = app("helloWorld");
const useMyState = state<string>("helloWorld", "myInput");
const useWordCount = state<number>("helloWorld", "wordCount");

const UI = () => {
  const [myInput, setMyState] = useMyState();
  const [wordCount] = useWordCount();

  return (
    <HelloWorld>
      <div>Hello World!!</div>
      <input defaultValue={myInput} onChange={e => setMyState(e.target.value)} />
      <div>Word count: {wordCount}</div>
    </HelloWorld>
  );
};

export default UI;
```

!!! Note
    Server updates are not automatically reflected. `make run-server` compiles and serves your Blueprint server. So CTRL+C and re-run `make run-server` to see changes.

## Build an event

Events are signals that can be used to trigger tasks. Often you will fire events when a user clicks a button. Build a button that triggers a task which counts the number of letters in your input.

```typescript
/* /server/src/apps/helloWorld.ts */
import {app, state, task, event, from} from "blueprint-server";

const wordCount = (input: string): number => {
  const trimmedInput = input.trim();
  return trimmedInput.length === 0 ? 0 : trimmedInput.trim().split(/\s/).length;
};

const letters = (input: string): number =>
  input.trim().length;

const helloWorld = app(() => {
  const myInput$ = state("myInput", "Hello Input!");
  const countLetters$ = event("countLetters");
  const wordCount$ = task(
    from(wordCount, myInput$)
  );
  const letters$ = task(
    {name: "letters", triggers: [countLetters$]},
    from(letters, myInput$)
  );

  return {
    name: "helloWorld",
    state: [myInput$],
    events: [countLetters$],
    tasks: [wordCount$, letters$]
  };
});

export default helloWorld;
```


```typescript
/* /ui/src/apps/helloWorld.tsx */
import {app, state, event, task} from "blueprint-react";

const HelloWorld = app("helloWorld");
const useMyState = state<string>("helloWorld", "myInput");
const useWordCount = task<number>("helloWorld", "wordCount");
const useCountLetters = event("helloWorld", "countLetters");
const useLetters = task<number>("helloWorld", "letters");

const UI = () => {
  const [myInput, setMyState] = useMyState();
  const [wordCount] = useWordCount();
  const [countLetters] = useCountLetters();
  const [letters] = useLetters();

  return (
    <HelloWorld>
      <div>Hello World!!</div>
      <input defaultValue={myInput} onChange={e => setMyState(e.target.value)}/>
      <button onClick={countLetters}>Count Letters</button>
      <div>Word count: {wordCount}</div>
      <div>Letter count: {letters}</div>
    </HelloWorld>
  );
};

export default UI;
```

!!! Note
    Server updates are not automatically reflected. `make run-server` compiles and serves your Blueprint server. So CTRL+C and re-run `make run-server` to see changes.

## View the Control Flow Diagram

Navigate to [http://localhost:3000/\__blueprint__](http://localhost:3000/__blueprint__) to see the interactive architecture diagram of the app you built.