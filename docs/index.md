# Welcome to Blueprint
*Hooks for the Backend*

Server-side hooks function similarly to React hooks but operate on your backend. These hooks have access to frontend state, react to changes in that state, trigger re-renders, and are type-safe. Blueprint, an express middleware, syncs React state with your backend, allowing you to write server-side hooks without affecting your existing endpoint.

#### How to Get Started?

Get started with the [quick start guide (2 mins)](./quickStart.md) or by following the [hello world tutorial (10 mins)](./tutorial.md).

#### A simple example

**Code:**

```
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
```
<br />
**Result:**
<iframe src="https://rectangle-ui-7y67ff2sba-uc.a.run.app/myApp" frameBorder=0 style="background-color:#f8f8f8;border:1px solid #e1e4e5;width:100%;"></iframe>
<br />
**Diagram:**

Additionally, Blueprint generates a diagram of data dependencies for your web application allowing devs to visualize their architecture.
<iframe src="https://rectangle-ui-7y67ff2sba-uc.a.run.app/__blueprint__?sheet=myApp" frameBorder=0 width="100%" height="300px" style="background-color:#f8f8f8;border:1px solid #e1e4e5;width:100%;"></iframe>
<br />

#### More examples

Find more examples [here](./examples.md). Or go straight to the flagship example: [user management app](https://usermanagement-ui-7y67ff2sba-uc.a.run.app).