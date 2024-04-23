# Welcome to Blueprint
*Simple state and data management for web applications*

Blueprint is a full-stack web application framework that simplifies state and data management. Blueprint handles data synchronization, data flow dependencies and race-conditions so devs can focus on business logic. See [why blueprint](./whyBlueprint.md) for a more comprehensive explanation of why Blueprint outperforms traditional web application frameworks.

#### A simple example

The example below creates a web page that calculates the area of a rectangle provided width and height. Blueprint recognizes area should automatically re-calculated when width or height change.

**Code:**

```
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
    tasks: [area$]
  };
});
```

```typescript
//frontend
import {app, state, task} from "blueprint-react";

const MyApp = app("myApp");
const useWidth = state<number>("myApp", "width");
const useHeight = state<number>("myApp", "height");
const useArea = task<number>("myApp", "area");

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

See more examples [here](./examples.md).

#### How to Get Started?

Get started by following this [tutorial](./gettingStarted.md) to build your first Blueprint web application.