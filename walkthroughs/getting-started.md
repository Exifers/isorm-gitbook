# Getting started

## Define your entities

First, define your entities using the `Type` utility. Each entity must have at least two fields:

* a field named **`id`**&#x6F;f type **`Type.id()`**
* a field named **`type`** of type **`Type.literal("name")`** where name is the name of the entity

{% code fullWidth="false" %}
```typescript
import { Type } from "@isorm/type"

const Node = Type.object({
    id: Type.id(),
    type: Type.literal("node"),
    name: Type.string(),
    color: Type.string()
})

const Variable = Type.object({
    id: Type.id(),
    type: Type.literal("variable"),
    name: Type.string()
})
```
{% endcode %}

{% hint style="info" %}
It might be surprising to have to add a field `type` on every entity. This enables to use **discriminated unions** in your code.
{% endhint %}

## Instantiate the datalayer

Then instantiate the datalayer:

```typescript
import { DerivedEntityDataLayer } from "@isorm/orm"

const datalayer = DerivedEntityDataLayer.create([Node, Variable])
```

## Making queries

```typescript

const node = datalayer.create({ id: "1", type: "node", color: "red" })

node.color // red

datalayer.setProperty(node, "color", "blue")

node.color // blue

datalayer.delete(node)
```

The entities are updated synchronously in-memory using mutable updates, and persisted asynchronously. Use `await datalayer.synchronize()` to wait for the asynchronous persistence to be finished.
