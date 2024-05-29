# Why Blueprint

Managing state and data flow in web applications is difficult, especially as apps become more complex. Blueprint alleviates those headaches for you. Blueprint replaces props drilling, complex state systems, hacky useEffects, etc.

We'll use a user management app to show how Blueprint works. Loading users in user management system is simple at first. But it becomes complex when you add search, update, add, and delete functionality. Blueprint helps you build simple code with complex state and data flow interactions.

Browse the [live example](https://usermanagement-ui-7y67ff2sba-uc.a.run.app/) or [full codebase](https://github.com/steaks/blueprint/tree/main/examples/userManagement) if you'd prefer to skip the explanation.

## Step 1: Load Users
Create a query that loads users from the database and displays them in an html table.

```
//server
const users = async (): Promise<User[]> =>
  await db.map<User>("SELECT id, name FROM users ORDER BY name", {}, row => ({id: row.id, name: row.name}));

const team = app(() => {
  const users$ = useQuery(users);
});
```

```
//ui
const Team = app("team")
const useUsers = query<User[]>("team", "users");

const UI = () => {
  const [users] = useUsers();
  return (
    <Team>
      {/* Table with users */}
    </Team>
  );
};
```

## Step 2: Add Search
Add a search input. Reload users when the search input changes. Implementing this in Blueprint is easy. Just plug in a search state to the query. Queries automatically execute when a state input changes. So users will automatically re-load when the search input changes.

```
//server
const users = async (search: string): Promise<User[]> =>
  await db.map<User>(`SELECT id, name FROM users WHERE name LIKE '%{$search}%' ORDER BY name`, {}, row => ({id: row.id, name: row.name}));

const team = app(() => {
  const search$ = useState("search");
  const users$ = useQuery(users, [search$])
});
```

```
//ui
const Team = app("team")

const useSearch = state<string>("team", "search");
const useUsers = query<User[]>("team", "users");

const UI = () => {
  const [users] = useUsers();
  const [search, setSearch] = useSearch();
  return (
    <Team>
      <input defaultValue={search} onChange={e => setSearch(e.currentTarget.value)} />
      {/* Table with users */}
    </Team>
  );
};
```

## Step 3: Implement Add a User
Implement functionality to add a user. Reload users when a new user is added or when search input changes. Create an add user effect. Trigger the users effect when the add user effect executes or when search state changes.

```
//server
const addUser = async (user: User): Promise<void> => {
  await db.none(`INSERT INTO users(id, name) VALUES('${user.id}', '${user.name}')`);
};

const users = async (search: string): Promise<User[]> =>
  await db.map<User>(`SELECT id, name FROM users WHERE name LIKE '%{$search}%' ORDER BY name`, {}, row => ({id: row.id, name: row.name}));

const team = app(() => {
  const search$ = useState("search");
  const newUser$ = useState<User>("newUser");
  
  const add$ = useEffect(addUser, [newUser$], {name: "add"});
  const users$ = useQuery(users, [search$], {triggers: ["deps", add$]});
});
```

```
//ui
const Team = app("team")

const useSearch = state<string>("team", "search");
const useNewUser = state<User>("team", "newUser");
const useUsers = query<User[]>("team", "users");
const useAdd = effect("team", "add");

const UI = () => {
  const [users] = useUsers();
  const [search, setSearch] = useSearch();
  const [newUser, setNewUser] = useNewUser();
  const [add] = useAdd();
  return (
    <Team>
      <input defaultValue={search} onChange={e => setSearch(e.currentTarget.value)} />
      <input onChange={e => setNewUser({...newUser, name: e.currentTarget.value})} />
      <button onClick={add}>Add</button>
      {/* Table with users */}
    </Team>
  );
};
```

## Step 4: Implement Update and Remove 

Implement update and remove functionality. Reload users when search input changes, a user is added, a user is updated, or a user is removed. Now users are reloaded for four different reasons - search, add, update, and remove. Create update and remove effects. Then fire an event signaling users changed when add, update, or remove effects execute. Trigger the users query when the event fires or when search state changes.

```
//server
const updateUser = async (selected: User): Promise<void> => {
  await db.none(`UPDATE users SET name = '${selected.name}' WHERE id = '${selected.id}'`, []);
};

const removeUser = async (user: User): Promise<void> => {
  await db.none(`DELETE FROM users WHERE id = '${user.id}'`);
};

const addUser = async (user: User): Promise<void> => {
  await db.none(`INSERT INTO users(id, name) VALUES('${user.id}', '${user.name}')`);
};

const users = async (search: string): Promise<User[]> =>
  await db.map<User>(`SELECT id, name FROM users WHERE name LIKE '%{$search}%' ORDER BY name`, {}, row => ({id: row.id, name: row.name}));

const team = app(() => {
  const usersChanged$ = useEvent("usersChanged");
  const search$ = useState("search");
  const newUser$ = useState<User>("newUser");
  const updatedUser$ = useState<User>("updatedUser");
  const removedUser$ = useState<User>("removedUser");
  
  const update$ = useEffect(updateUser, [updatedUser$], {name: "update", onSuccess: [usersChanged$]});
  const remove$ = useEffect(removeUser, [removedUser$], {name: "remove", onSuccess: [usersChanged$]});
  const add$ = useEffect(addUser, [newUser$], {name: "add", onSuccess: [usersChanged$]});
  const users$ = useQuery(users, [search$], {triggers: ["deps", usersChanged$]});
});
```

```
//ui
const Team = app("team")

const useSearch = state<string>("team", "search");
const useNewUser = state<User>("team", "newUser");
const useUpdatedUser = state<User>("team", "updatedUser");
const useRemovedUser = state<User>("team", "removedUser");
const useUsers = query<User[]>("team", "users");
const useAdd = effect("team", "add");
const useUpdate = effect("team", "update");
const useRemove = effect("team", "remove");

const UI = () => {
  const [users] = useUsers();
  const [search, setSearch] = useSearch();
  const [newUser, setNewUser] = useNewUser();
  const [updatedUser, setUpdatedUser] = useNewUser();
  const [removedUser, setRemovedUser] = useRemovedUser();
  const [, add] = useAdd();
  const [, update] = useUpdate();
  const [, remove] = useRemove();
  return (
    <Team>
      <input defaultValue={search} onChange={e => setSearch(e.currentTarget.value)} />
      <input onChange={e => setNewUser({...newUser, name: e.currentTarget.value})} />
      <button onClick={add}>Add</button>
      {/* Table with users */}
      {/* Update user UI */}
      {/* Remove user UI */}
    </Team>
  );
};
```