# Welcome to Blueprint
*The full-stack push-based web server and frontend framework.*

Blueprint is a full-stack web application framework. It combines server and frontend state/events into a single push-based architecture. This approach streamlines state management, synchronizing data, and ui reactivity. 

#### Principles 

**Declarative - **Build declarative functions without worrying about how they are called in the system.<br/>
**Ergonomic - ** Build ergonomic code that reads how your application works, not technical details.<br/>
**Cohesive - ** Build server and ui code in the same system with the same style.<br/>
**Visual - ** Blueprint generates an interactive visual diagram of your code architecture to provide clarity and quick understanding of what you've built.

#### Why Blueprint

As web applications become more complex developers disproportionally work on the logistics rather than business logic. If you've built a web application you've likely noticed how much time you spend managing state updates, coordinating data between components, debugging ui responsiveness, etc. And you've probably explored complex state management libraries like Redux, RxJS, and MobX with varying degrees of success.

Blueprint handles data and state management logistics. Blueprint developers can just trust state and coordinate data with a few lines of code.

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