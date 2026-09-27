## Introduction

Convex is not just a database, it's a total backend service. There are other alternatives as well like Firebase and Supabase.

Creating a convex application can actually be done within the convex site. But this is also possible into the terminal.<br>
The first thing you gotta do is install convex in that project, just like this: `npm i convex` <br>
Then create the application with this command: `npx convex dev`<br>
There will be many things installed inside a `convex` folder. But we don't have to worry about what it contains.

## Convex queries and mutations

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

There you also include a file for queries and mutations. The file name can be arbitrarily chosen. Here in this case it's `todos.ts`. In this file you would write a queries and mutations:

```ts
import { ConvexError, v } from "convex/values"
import { mutation, query } from "./_generated/server"

export const getTodos = query({
  args: {},
  handler: async (ctx) => {
    const todos = await ctx.db.query("todos").order("desc").collect()
    return todos
  },
})

export const addTodo = mutation({
  args: {
    text: v.string(),
  },
  handler: async (ctx, args) => {
    const todoId = await ctx.db.insert("todos", {
      text: args.text,
      isComplete: false,
    })

    return todoId
  },
})

export const toggleTodo = mutation({
  args: {
    id: v.id("todos"),
  },
  handler: async (ctx, args) => {
    const todo = await ctx.db.get(args.id)
    if (!todo) throw new ConvexError("Todo not found")

    await ctx.db.patch(args.id, {
      isComplete: !todo.isComplete,
    })
  },
})

export const deleteTodo = mutation({
  args: {
    id: v.id("todos"),
  },
  handler: async (ctx, args) => {
    await ctx.db.delete(args.id)
  },
})

export const updateTodo = mutation({
  args: {
    id: v.id("todos"),
    text: v.string(),
  },
  handler: async (ctx, args) => {
    await ctx.db.patch(args.id, {
      text: args.text,
    })
  },
})

export const clearAllTodos = mutation({
  handler: async (ctx) => {
    const todos = await ctx.db.query("todos").collect()

    // Delete all todos
    for (const todo of todos) {
      await ctx.db.delete(todo._id)
    }

    return { deleteCount: todos.length }
  },
})
```

Now in order to get all these working we need to configure convex in the component tree.<br>
Apply these changes to the root `_layout.tsx` file

```tsx
import { ConvexProvider, ConvexReactClient } from "convex/react"

const convex = new ConvexReactClient(process.env.EXPO_PUBLIC_CONVEX_URL!, {
  unsavedChangesWarning: false,
})

export default function RootLayout() {
  return (
    <ConvexProvider client={convex}>
      ...
    </ConvexProvider>
```

By the way, these configuration rules are already provided in the convex documentation.

## Apply convex in the components

```tsx
// src/app/(tabs)/index.tsx  [as per my codebase]

import { api } from "@/convex/_generated/api"
import { useQuery } from "convex/react"

export default function Index() {
  const todos = useQuery(api.todos.getTodos)
  console.log(todos)

  return (
    ...
  )
}
```

## Table Record Type for TypeScript

```tsx
import { Doc } from "@/convex/_generated/dataModel"

type Todo = Doc<"todos">
```
