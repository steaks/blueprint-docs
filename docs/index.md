# Welcome to Blueprint
*The full-stack push-based web application framework*

Blueprint re-imagines web application development by adopting a push-based architecture, as opposed to the traditional pull-based architecture. This approach streamlines state management, synchronizing data, and ui reactivity. 

#### Principals

**Declarative - **Build declarative functions with Blueprint's push-based architecture.<br/>
**Ergonomic - ** Build ergonomic code with Blueprint's that reads intuitively.<br/>
**Cohesive - ** Blueprint is full-stack. Build server and ui code with the same style.<br/>
**Visual - ** Blueprint generates an interactive visual diagram of your code architecture to provide clarity and quick understanding of what you've built.

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
<iframe src="https://rectangle-ui-7y67ff2sba-uc.a.run.app/__blueprint__?sheet=myApp" frameBorder=0 width="100%" height="300px" style="background-color:#f8f8f8;border:1px solid #e1e4e5;width:100%;"></iframe>
<br />
#### How to Get Started?

Get started by following this [tutorial](./gettingStarted.md) to build your first Blueprint web application.