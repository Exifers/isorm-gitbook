# Relationships

Relationships can be achieved by adding fields named and typed in a specific way, known as the **namespaced relationships** convention. When a field is recognized as a relation field, Isorm will provide the relationship features on this field.

## Using foreign keys

A field named as `<entity name>Id` and assignable to `id | null` is considered a foreign key field.

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
    name: Type.string(),
    nodeId: Type.union(Type.id(), Type.null()) // foreign key
})
```

Isorm will make sure that any non null value points to an existing entity of the matching type:

```typescript
const node = datalayer.create({ id: "1", type: "node" })
const variable = datalayer.create({ id: "1", type: "variable" })

datalayer.setProperty(variable, "nodeId", "2") // error! node with id "2" nonexistent
datalayer.setProperty(variable, "nodeId", "1") // ok

datalayer.delete(node)
datalayer.getAll("variable").length // 0 (on delete cascade is the default)
```

## Adding relation fields

Relation fields are **special types** of **virtual fields**. When matching the namespaced relationship convention.

```typescript
import { Type } from "@isorm/type"

const Node = Type.object({
    id: Type.id(),
    type: Type.literal("node"),
    name: Type.string(),
    color: Type.string(),
    
    $variables: Type.array(Type.lazy(() => Variable)), // to-many relation field
})

const Variable = Type.object({
    id: Type.id(),
    type: Type.literal("variable"),
    name: Type.string(),
    
    nodeId: Type.union(Type.id(), Type.null()),
    $node: Type.relation(() => Node), // to-one relation field
})
```

Then you can use these fields with the `connect` and `disconnect` utilities:

```typescript
const node = datalayer.create({ id: "1", type: "node" })
const variable = datalayer.create({ id: "1", type: "variable" })
node.$variables // []
variable.$node // null
variable.nodeId // null

datalayer.connect(node, "$variables", variable)
node.$variables // [variable]
variable.$node // node
variable.nodeId // "1"

datalayer.disconnect(node, "$variables", variable)
node.$variables // []
variable.$node // null
variable.nodeId // null
```

`connect` and `disconnect` work on both ways:

```typescript
// This does the same thing as above
datalayer.connect(variable, "$node", node)
datalayer.disconnect(variable, "$node", node)
```

## Distinguish similar relationships

### Between different entities

If you have multiple relationships between the same pair of entities, you can use namespaces to distinguish them:

```typescript
const User = Type.object({
    id: Type.id(),
    type: Type.literal("user"),
    
    $productionMovies: Type.array(Type.lazy(() => Movie)),
    $directionMovies: Type.array(Type.lazy(() => Movie))
})

const Movie = Type.object({
    id: Type.id(),
    type: Type.literal("user"),
    
    productionUserId: Type.union([Type.id(), Type.null()]),
    $productionUser: Type.relation(() => User),
    
    directionUserId: Type.union([Type.id(), Type.null()]),
    $directionUser: Type.relation(() => User),
})
```

### Between the same entities (reflexive relationships)

If the relationship is reflexive, you can add an extra qualifier to mark the distinction between fields other than by plural and singular forms:

```typescript
const User = Type.object({
    id: Type.id(),
    type: Type.literal("user"),
    
    familyParentUserId: Type.union([Type.id(), Type.null()]),
    $familyParentUser: Type.relation(() => User),
    $familyChildrenUsers: Type.array(Type.lazy(() => User)),
})
```
