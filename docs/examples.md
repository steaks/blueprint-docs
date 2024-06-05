# Examples 

## Live Examples

| Example                      | Website                                                       | Diagram                                                                    | Code                                                                          |
|------------------------------|---------------------------------------------------------------|----------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| Simple Rectangle Application | [Website](https://rectangle-ui-7y67ff2sba-uc.a.run.app/myApp) | [Diagram](https://rectangle-ui-7y67ff2sba-uc.a.run.app/__blueprint__)      | [Code](https://github.com/steaks/blueprint/tree/main/examples/rectangle)      |
| User Management Application  | [Website](https://usermanagement-ui-7y67ff2sba-uc.a.run.app)  | [Diagram](https://usermanagement-ui-7y67ff2sba-uc.a.run.app/__blueprint__) | [Code](https://github.com/steaks/blueprint/tree/main/examples/userManagement) |

## More Examples

### Hello World

This example is a simple application that demonstrates the basics of Blueprint. Below are code snippets that show the core code of the application. You can also browse the full code [here](https://github.com/steaks/blueprint/tree/main/templates/helloWorld) or run the application locally [here](#run-the-application-locally).

#### Code Snippets

```typescript
//server
import {app, create, useState, useQuery} from "blueprint-server";

const wordCount = (input: string): number =>
  input.split(/\s/).filter(x => x).length;

const helloWorld = app(() => {
  const myInput$ = useState("myInput", "Hello World!");
  const wordCount$ = useQuery(wordCount, [myInput$]);

  return {
    name: "helloWorld",
    state: [myInput$],
    events: [],
    queries: [wordCount$]
  };
});
```

```typescript
//frontend
import React from 'react';
import {Blueprint, app, state, query} from "blueprint-react";

const HelloWorldApp = app("helloWorld");
const useMyInput = state<string>("helloWorld", "myInput");
const useWordCount = query<number>("helloWorld", "wordCount");

const HelloWorld = () => {
  const [myInput, setMyInput] = useMyInput();
  const [wordCount] = useWordCount();

  return (
    <HelloWorldApp>
      <input defaultValue={myInput} onChange={e => setMyInput(e.target.value)}/>
      <div>Word count: {wordCount}</div>
    </HelloWorldApp>
  );
};
```

#### Run the application locally

```shell
npx create-blueprint-app -t helloWorld MyHelloWorldApplication
cd MyHelloWorldApplication
make run-server
make run-ui # Run in separate terminal. Open browser to http://localhost:3000
```

### User Profile

This example is an application with that allows a user to view and edit their email, first name, and last name on a user profile page. Below are code snippets that show the core code of the application. You can also browse the full code [here](https://github.com/steaks/blueprint/tree/main/templates/helloWorld) or run the application locally [here](#run-the-application-locally_1).

#### Code Snippets

```typescript
//server
import {app, useState, useQuery, useEffect} from "blueprint-server";
import {queryUser, updateUser} from "../db/user";

const userProfile$$ = app(() => {
  const email$ = useState<string>("email");
  const firstName$ = useState<string>("firstName");
  const lastName$ = useState<string>("lastName");

  const save$ = useEvent("save");
  const user$ = useQuery(queryUser, [], {name: "user"});
  const onSave$ = useEffect(updateUser, [email$, firstName$, lastName$], {name: "onSave", triggers: [save$], onSuccess: [user$]})

  return {
    name: "userProfile",
    state: [email$, firstName$, lastName$],
    events: [save$],
    queries: [user$, onSave$],
    effects: [onSave$]
  };
});
```

```typescript
//frontend
import React from "react";
import {app, state, event, query} from "blueprint-react"
import {User} from "../../../shared/src/apps/userProfile";

export const useEmail = state<string>("userProfile", "email");
export const useFirstName = state<string>("userProfile", "firstName");
export const useLastName = state<string>("userProfile", "lastName");
export const useSave = event("userProfile", "save");
export const useUser = query<User>("userProfile", "user");
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

#### Run the application locally

```shell
npx create-blueprint-app -t userProfile MyUserProfileApplication
cd MyUserProfileApplication
make run-server
make run-ui # Run in separate terminal. Open browser to http://localhost:3000
```

### Rectangle

This example is a simple application that calculates the area of a rectangle provided width and height. Below are code snippets that show the core code of the application. You can also browse the full code [here](https://github.com/steaks/blueprint/tree/main/templates/rectangle) or run the application locally [here](#run-the-application-locally_2).

#### Code Snippets

```typescript
//server
import {app, useState, useQuery} from "blueprint-server";

const area = (width: number, height: number) =>
  width * height;

const myApp = app(() => {
  const width$ = useState("width", 10);
  const height$ = useState("height", 15);
  const area$ = useQuery(area, [width$, height$]);

  return {
    name: "myApp",
    state: [width$, height$],
    queries: [area$]
  };
});

export default myApp;
```

```typescript
//frontend
import {app, state, query} from "blueprint-react";

const MyApp = app("myApp");
const useWidth = state<number>("myApp", "width");
const useHeight = state<number>("myApp", "height");
const useArea = query<number>("myApp", "area");

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

#### Run the application locally

```shell
npx create-blueprint-app -t rectangle MyRectangleApplication
cd MyUserProfileApplication
make run-server
make run-ui # Run in separate terminal. Open browser to http://localhost:3000
```

### User Management

This example is a more complex application that manages users. Browse the code on [github](https://github.com/steaks/blueprint/tree/main/examples/userManagement) or run the application locally.

```shell
npx create-blueprint-app -t userManagement UserManagement
cd UserManagement 
make run-server
make run-ui # Run in separate terminal. Open browser to http://localhost:3000
```
