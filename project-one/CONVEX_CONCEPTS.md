## Introduction

Convex is not just a database, it's a total backend service. There are other alternatives as well like Firebase and Supabase.

Creating a convex application can actually be done within the convex site. But this is also possible into the terminal.<br>
The first thing you gotta do is install convex in that project, just like this: `npm i convex` <br>
Then create the application with this command: `npx convex dev`<br>
There will be many things installed inside a `convex` folder. But we don't have to worry about what it contains.

Once the convex application is created, add a schema file in it:

```ts
// convex/schema.ts

import { defineSchema, defineTable } from "convex/server"

import { v } from "convex/values"

export default defineSchema({
  todos: defineTable({
    text: v.string(),
    isComplete: v.boolean(),
  }),
})
```

There you also include a file for queries and mutations. The file name can be arbitrarily chosen. Here in this case it's `todos.ts`. In this file you would write a query function this way:

```ts
import { query } from "./_generated/server"

export const getTodos = query({
  handler: async (ctx) => {
    const todos = await ctx.db.query("todos").order("desc").collect()
    return todos
  },
})
```
