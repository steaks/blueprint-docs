# Examples 

## User Profile

This example is a simple application with that allows a user to view and edit their email, first name, and last name on a user profile page.

For the full example see [userprofile](https://github.com/steaks/blueprint/tree/main/examples/userprofile).

#### App

The userProfile tracks a user's information and displays the ui.

```typescript
//server
const userProfile$$ = app(() => {
  return {
    name: "userProfile",
    state: [], //TODO state 
    events: [], //TODO events
    hooks: [] //TODO hooks 
  };
});
```


```typescript
//ui
export const UserProfile = app("userProfile");

const UserProfileUI = () => {
  return (
    <UserProfile>
      <>TODO UI</>
    </UserProfile>
  );
};
```

#### Shared Types

The server and ui will both use the User type, so the User type will be in the `shared` directory.

```typescript
//shared
export interface User {
    readonly email: string;
    readonly firstName: string;
    readonly lastName: string;
}
```

#### Mock Database

For this app we'll use a mock database that track's a users email, first name, and last name. This database is not part of the blueprint framework, but we need to simulate a db to build a realistic app.

```typescript
//database
const db = {
  email: "",
  firstName: "",
  lastName: ""
};

const queryUser = (): Promise<User> =>
  Promise.resolve({email: db.email, firstName: db.firstName, lastName: db.lastName})

const updateUser = (email: string, firstName: string, lastName: string): Promise<string> => {
  db.email = email;
  db.firstName = firstName;
  db.lastName = lastName;
  return Promise.resolve("Saved to db!");
};
```

#### State
The app will keeps track of three state variables: email, firstName, lastName.
```typescript
//server
const email$ = state<string>("email");
const firstName$ = state<string>("firstName");
const lastName$ = state<string>("lastName");
```

```typescript
//ui
export const useEmail = state<string>("userProfile", "email");
export const useFirstName = state<string>("userProfile", "firstName");
export const useLastName = state<string>("userProfile", "lastName");

const UserProfileUI = () => {
  const [user] = useUser();
  const [email, setEmail] = useEmail();
  const [firstName, setFirstName] = useFirstName();
  const [lastName, setLastName] = useLastName();  
  return (
    <UserProfile>
      <>TODO UI</>
    </UserProfile>
  );
};
```

#### Events

The app tracks one event: save.

```typescript
const save$ = event("save");
```

```typescript
export const useSave = event("userProfile", "save");
```

#### Hooks

The app will have two hooks - user and onSave. User fetches a user's information to display on the user profile. And onSave updates the user's information in the database. The user hook is executed when the page initializes and after the onSave updates the database. The onSave hook fires when the save event fires.

```typescript
//server
const user$ = hook(
  "user",
  {}, //using default options
  operator(queryUser)
);

const onSave$ = hook(
  "onSave",
  {triggers: [save$]},
  operator(updateUser, email$, firstName$, lastName$),
  trigger(user$)
);
```

```typescript
//ui
export const useUser = hook<User>("userProfile", "user");
```

#### Full Application

Putting it all together the application looks like:

```typescript
//database
const db = {
  email: "",
  firstName: "",
  lastName: ""
};

const queryUser = (): Promise<User> =>
  Promise.resolve({email: db.email, firstName: db.firstName, lastName: db.lastName})

const updateUser = (email: string, firstName: string, lastName: string): Promise<string> => {
  db.email = email;
  db.firstName = firstName;
  db.lastName = lastName;
  return Promise.resolve("Saved to db!");
};
```

```typescript
//server
import {app, state, event, hook, operator, trigger} from "@blueprint/server";
import {User} from "../../../shared/src/apps/userProfile";
import {queryUser, updateUser} from "../db/user";

const userProfile$$ = app(() => {
  const email$ = state<string>("email");
  const firstName$ = state<string>("firstName");
  const lastName$ = state<string>("lastName");

  const save$ = event("save");

  const user$ = hook(
    "user",
    {}, //use default options
    operator(queryUser)
  );

  const onSave$ = hook(
    "onSave",
    {triggers: [save$]},
    operator(updateUser, email$, firstName$, lastName$),
    trigger(user$)
  );

  return {
    name: "userProfile",
    state: [email$, firstName$, lastName$],
    events: [save$],
    hooks: [user$, onSave$]
  };
});

export default userProfile$$;
```

```typescript
//ui app
import {app, state, event, hook} from "../rxreact"
import {User} from "../../../shared/src/apps/userProfile";

export const useEmail = state<string>("userProfile", "email");
export const useFirstName = state<string>("userProfile", "firstName");
export const useLastName = state<string>("userProfile", "lastName");

export const useSave = event("userProfile", "save");

export const useUser = hook<User>("userProfile", "user");

export const UserProfile = app("userProfile");
```

```typescript
//ui
import React from "react";
import {useEmail, useFirstName, useLastName, useSave, UserProfile, useUser} from "../apps/userProfile";

const UserProfileUI = () => {
  const [user] = useUser();
  const [email, setEmail] = useEmail();
  const [firstName, setFirstName] = useFirstName();
  const [lastName, setLastName] = useLastName();

  const [save] = useSave();

  return (
    <UserProfile>
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

export default UserProfileUI;
```

### Connect the App to React

Use rx-react to connect your app's state, events, and hooks, and create a component that wraps the app.

```typescript
import {app, state, event, hook} from "../rxreact"
import {User} from "../../../shared/src/apps/userProfile";

export const useEmail = state<string>("userProfile", "email");
export const useFirstName = state<string>("userProfile", "firstName");
export const useLastName = state<string>("userProfile", "lastName");

export const useSave = event("userProfile", "save");

export const useUser = hook<User>("userProfile", "user");

export const UserProfile = app("userProfile");
```

### Build the React UI

Build a React UI using the `UserProfile` component and hooks you created when connecting the Rx Blueprint app to React.

```typescript
import React from "react";
import {useEmail, useFirstName, useLastName, useSave, UserProfile, useUser} from "../apps/userProfile";

const UserProfileUI = () => {
  const [user] = useUser();
  const [email, setEmail] = useEmail();
  const [firstName, setFirstName] = useFirstName();
  const [lastName, setLastName] = useLastName();

  const [save] = useSave();

  return (
    <UserProfile>
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

export default UserProfileUI;
```