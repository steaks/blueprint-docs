# Welcome to Blueprint
*The ergonomic web application framework*

Blueprint re-imagines web application architecture. It unifies server and frontend code so developers can build in a cohesive, declarative, and ergonomic code-base. Additionally, since all control-flow is encapsulated by Blueprint it creates a visual, interactive control-flow diagram to display the architecture of your application.

- **Cohesive - ** Build frontend and server code using the same style and system.
- **Declarative - ** Build declarative functions with complex control flows logic.
- **Ergonomic - ** Build code that matches your application flow. 

#### A simple example

The example below creates a web page that calculates the area of a rectangle provided width and height. The width and height inputs are automatically synchronized between frontend and backend. And area is automatically calculated when width or height changes.

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
```
<br />
**Result:**
<iframe src="https://rectangle-ui-7y67ff2sba-uc.a.run.app/myApp" frameBorder=0 style="background-color:#f8f8f8;border:1px solid #e1e4e5;width:100%;"></iframe>
<br />
**Diagram:**
<iframe src="https://rectangle-diagram-7y67ff2sba-uc.a.run.app/myApp" frameBorder=0 width="100%" height="300px" style="background-color:#f8f8f8;border:1px solid #e1e4e5;width:100%;"></iframe>
<br />
#### How to Get Started?

Get started by following this [tutorial](./gettingStarted.md) to build your first Blueprint web application.