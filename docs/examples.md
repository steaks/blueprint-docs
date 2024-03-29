# Examples 

## Hello World

This example is a simple application that demonstrates the basics of Blueprint. Below are code snippets that show the core code of the application. You can also browse the full code [here](https://github.com/steaks/blueprint/tree/main/templates/helloWorld) or run the application locally [here](#run-the-application-locally).

### Code Snippets

```typescript
//server
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
//frontend
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

### Run the application locally

```shell
wget https://raw.githubusercontent.com/steaks/blueprint-templates/main/createBlueprint.sh
chmod +x createBlueprint.sh
./createBlueprint.sh -t helloWorld MyHelloWorldApplication
cd MyHelloWorldApplication
make install
make build
make run-server # Run in separate terminal.
make run-ui # Run in separate terminal. Open browser to http://localhost:3000
make run-blueprint # Run in separate terminal. Open browser to http://localhost:3001
```

## User Profile

This example is an application with that allows a user to view and edit their email, first name, and last name on a user profile page. Below are code snippets that show the core code of the application. You can also browse the full code [here](https://github.com/steaks/blueprint/tree/main/templates/helloWorld) or run the application locally [here](#run-the-application-locally_1).

### Code Snippets

```typescript
//server
import {app, state, event, task, from, trigger} from "blueprint-server";
import {queryUser, updateUser} from "../db/user";

export default app(() => {
  const email$ = state<string>("email");
  const firstName$ = state<string>("firstName");
  const lastName$ = state<string>("lastName");

  const save$ = event("save");

  const user$ = task(
    {name: "user"},
    from(queryUser)
  );

  const onSave$ = task(
    "onSave",
    {triggers: [save$]},
    from(updateUser, email$, firstName$, lastName$),
    trigger(user$)
  );

  return {
    name: "userProfile",
    state: [email$, firstName$, lastName$],
    events: [save$],
    tasks: [user$, onSave$]
  };
});
```

```typescript
//frontend
import React from "react";
import {app, state, event, task} from "blueprint-react"
import {User} from "../../../shared/src/apps/userProfile";

export const useEmail = state<string>("userProfile", "email");
export const useFirstName = state<string>("userProfile", "firstName");
export const useLastName = state<string>("userProfile", "lastName");
export const useSave = event("userProfile", "save");
export const useUser = task<User>("userProfile", "user");
export const UserProfile = app("userProfile");

const UI = () => {
  const [user] = useUser();
  const [email, setEmail] = useEmail();
  const [firstName, setFirstName] = useFirstName();
  const [lastName, setLastName] = useLastName();

  const [save] = useSave();

  return (
    <UserProfile>
      <a href="/">&lt; Home</a>
      <h1>User Profile!</h1>
      <strong>User:</strong>
      <div>Email: {user?.email}</div>
      <div>First name: {user?.firstName}</div>
      <div>Last name: {user?.lastName}</div>
      <hr/>
      <strong>Edit:</strong>
      <div>
        <input defaultValue={email} onChange={e => setEmail(e.currentTarget.value)} placeholder="Email"/>
      </div>
      <div>
        <input defaultValue={firstName} onChange={e => setFirstName(e.currentTarget.value)} placeholder="First Name"/>
      </div>
      <div>
        <input defaultValue={lastName} onChange={e => setLastName(e.currentTarget.value)} placeholder="Last Name"/>
      </div>
      <button onClick={save}>Save</button>
    </UserProfile>
  );
};

export default UI;
```

### Run the application locally

```shell
wget https://raw.githubusercontent.com/steaks/blueprint-templates/main/createBlueprint.sh
chmod +x createBlueprint.sh
./createBlueprint.sh -t userProfile MyUserProfileApplication
cd MyUserProfileApplication
make install
make build
make run-server # Run in separate terminal.
make run-ui # Run in separate terminal. Open browser to http://localhost:3000
make run-blueprint # Run in separate terminal. Open browser to http://localhost:3001
```

## Rectangle

This example is a simple application that calculates the area of a rectangle provided width and height. Below are code snippets that show the core code of the application. You can also browse the full code [here](https://github.com/steaks/blueprint/tree/main/templates/rectangle) or run the application locally [here](#run-the-application-locally_2).

### Code Snippets

```typescript
//server
import {app, state, task, from} from "blueprint-server";
const area = (width: number, height: number) =>
  width * height;
  
const myApp = app(() => {
  const width$ = state("width", 10);
  const height$ = state("height", 15);
  const area$ = task(from(area, width$, height$));

  return {
    name: "myApp",
    state: [width$, height$],
    events: [],
    tasks: [task$]
  };
});
```

```typescript
//frontend
import {app, state, task} from "blueprint-react";

const MyApp = app("myApp");
const useWidth = state<number>("myApp", "width");
const useHeight = state<number>("myApp", "height");
const useArea = task<number>("myApp", "area")

const UI = () => {
  const [width, setWidth] = useWidth();
  const [height, setHeight] = useHeight();
  const [area] = useArea();


  return (
    <MyApp>
      <input defaultValue={width} onChange={e => setWidth(e.target.value)} />
      <input defaultValue={height} onChange={e => setHeight(e.target.value)} />
      <div>Area of Rectangle: {area}</div>
    </MyApp>
  );
};

export default UI;
```

### Run the application locally

```shell
wget https://raw.githubusercontent.com/steaks/blueprint-templates/main/createBlueprint.sh
chmod +x createBlueprint.sh
./createBlueprint.sh -t rectangle MyRectangleApplication
cd MyRectangleApplication
make install
make build
make run-server # Run in separate terminal.
make run-ui # Run in separate terminal. Open browser to http://localhost:3000
make run-blueprint # Run in separate terminal. Open browser to http://localhost:3001
```
