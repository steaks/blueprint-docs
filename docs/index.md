# Welcome to Blueprint
*The ergonomic web application framework*

Blueprint re-imagines web application architecture. It unifies server and frontend code so developers can build in a cohesive, declarative, and ergonomic code-base. Additionally, since all control-flow is encapsulated by Blueprint it creates a visual, interactive control-flow diagram to display the architecture of your application.

- **Cohesive - ** Build frontend and server code using the same style and system.
- **Declarative - ** Build declarative functions with complex control flows logic.
- **Ergonomic - ** Build code that matches your application flow. 

#### A simple example

The example below creates a web page that calculates the area of a rectangle provided width and height. The width and height inputs are automatically synchronized between frontend and backend. And area is automatically calculated when width or height changes.

```
//server
import {app, state, hook, operator} from "blueprint-server";
const area = (width: number, height: number) =>
  width * height;
  
const myApp = app(() => {
  const width$ = state("width", 10);
  const height$ = state("height", 15);
  const area$ = hook(operator(area, width$, height$));

  return {
    name: "myApp",
    state: [width$, height$],
    events: [],
    hooks: [area$]
  };
});
```

```typescript
//frontend
import {app, state, hook} from "blueprint-react";

const MyApp = app("myApp");
const useWidth = state<number>("myApp", "width");
const useHeight = state<number>("myApp", "height");
const useArea = hook<number>("myApp", "area")

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

#### How to Get Started?

Get started by following this [tutorial](./gettingStarted.md) to build your first Blueprint web application.