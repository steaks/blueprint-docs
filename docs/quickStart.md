# Quick Start

You can try out Blueprint by spinning up a new app or integrating it into your existing app.

#### New App

Spin up a React frontend and Express backend with Blueprint. This is a great way to try out Blueprint.
```
npx create-blueprint-app
```

#### Integrate into your App

Inject Blueprint into an existing app.

**Backend**

1) Install the npm package
```
npm i -s blueprint-react
```

2) Inject middleware 

```typescript
// index.ts

import {create} from "blueprint-server";

const bp = create({});
yourApp.use(bp.express.path, bp.express.app);
```

**Frontend**

1) Install the npm package 

```
npm i -s blueprint-react
```
2) Wrap components in Blueprint

```typescript
// index.tsx

<Blueprint>
  {/* Your Components Here */}
</Blueprint>
```