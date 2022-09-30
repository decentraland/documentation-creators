---
date: 2018-01-15
title: Entities and components
description: Learn the essentials about entities and components in a Decentraland scene
categories:
  - development-guide
redirect_from:
  - /sdk-reference/scene-state/
  - /sdk-reference/scene-content-guide/
  - /docs/entities
  - /sdk-reference/entity-interfaces/
  - /development-guide/scene-state/
  - /development-guide/scene-content/
  - /development-guide/entity-interfaces/
  - /development-guide/entity-components/
  - /development-guide/entities-components/
type: Document
slug: /creator/development-guide/entities-components/
---

Decentraland scenes are built around [_entities_, _components_ and _systems_](https://en.wikipedia.org/wiki/Entity%E2%80%93component%E2%80%93system). This is a common pattern used in the architecture of several game engines, that allows for easy composability and scalability.


TODO: Change image
![](/images/media/ecs-big-picture.png)

## Overview

_Entities_ are the basic unit for building everything in Decentraland scenes. All visible and invisible 3D objects and audio players in your scene will each be an entity. An entity is nothing more than an id, that can be referenced by components. The entity itself has no properties or methods of its own, it simply serves to group several components together.

_Components_ define the traits of an entity. For example, a `transform` component stores the entity's coordinates, rotation and scale. A `BoxShape` component gives the entity a box shape when rendered in the scene, a `Material` component gives the entity a color or texture. You can also create custom components to serve your scene's required data, for example a custom `health` could store an entity's remaining health value, and add it to entities that represent non-player enemies in a game.

If you're familiar with web development, think of entities as the equivalent of _Elements_ in a _DOM_ tree, and of components as _attributes_ of those elements.

> Note: In previous versions of the SDK, Entities were _objects_ that were instanced, and could be extended to add functions. As of version 7.0 of the SDK, entities are only an ID. This structure better fits the principles of [data oriented programming](/creator/development-guide/data-oriented-programming) and can help in the scene's performance.

TODO: change imag
<img src="/images/media/ecs-components.png" alt="Armature" width="400"/>

Components like `Transform`, `Material` or any of the _shape_ components are closely tied in with the rendering of the scene. If the values in these components change, that alone is enough for the engine to change how the scene is rendered in the next frame.

The engine is the part of the scene that sits in the middle and manages all of the other parts. It determines what entities are rendered and how players interact with them. It also coordinates what functions from [systems](/creator/development-guide/systems ) are executed and when.


Components are meant to store data about their referenced entity. They can only store this data, they can't modify this data themselves. All changes to the values in the components are carried out by [Systems](/creator/development-guide/systems). Systems are completely decoupled from the components and entities themselves. Entities and components are agnostic to what _systems_ are acting upon them.


## Syntax for entities and components

The example below shows some basic operations for declaring, and configuring basic entities and components.

```ts
// Create an entity
const door = engine.addEntity()

// Give the entity a position via a transform component
Transform.create(door, {
	position: { x: 5, y: 1, z: 5 }
})

// Give the entity a visible shape via a GLTFShape component
GLTFShape.create(door)
```

> Note: In previous versions of the SDK, it was necessary to manually add an entity to the engine to start rendering it. As of version 7 of the SDK, entities are implicitly added to the engine as soon as they are assigned a component.

When a component is created, it's always assigned to a parent entity. The component's values then affect the entity.

## Remove entities

To remove an entity from your scene, you must remove each of its components. As a short cut, you can call the REMOVE ENTITY FUNCTION

TODO: remve entity


TODO: CONFIRM THIS:
If a removed entity has child entities, all children of that entity are removed too.

You can also manually remove each of the components in the entity one by one

TODO: snippet

NOTE: not the same as making invisible - link to invisible

The entity's id is then free to be reused by a new entity


## Nested entities

An entity can have other entities as children. Thanks to this, we can arrange entities into trees, just like the HTML of a webpage.


TODO: change image
<img src="/images/media/ecs-nested-entities.png" alt="nested entities" width="400"/>

To set an entity as the parent of another, the child entity must have a `Transform` component. You can then set the `parent` field with a reference to the parent entity.

```ts
// Create entities
const parentEntity = engine.addEntity()

const childEntity = engine.addEntity()

// Set parent
Transform.create(childEntity, {
	parent: parentEntity
})
```


Once a parent is assigned, it can be read off the child entity from the `parent` field on its `Transform` component.

```ts
// Get parent from an entity
const parent = Transform.get(childEntity).parent
```


If a parent entity has a `Transform` component that affects its position, scale or rotation, its children entities are also affected. Any position or rotation values are added, any scale values are multiplied.

If either the parent or child entity doesn't have a `Transform` component, the following default values are used.

- For **position**, the parent's center is _0, 0, 0_
- For **rotation** the parent's rotation is the quaternion _0, 0, 0, 1_ (equivalent to the Euler angles _0, 0, 0_)
- For **scale**, the parent is considered to have a size of _1_. Any resizing of the parent affects scale and position in proportion.

Entities with no shape component are invisible in the scene. These can be used as wrappers to handle and position multiple entities as a group.

To remove an entity's parent, you can assign the entity's parent to `null`.

```ts
const mutableChildTransform = Transform.get(childEntity)
mutableChildTransform.parent = null
```

TODO: Confirm this


## Get an entity by ID

Every entity in your scene has a unique number _id_. You can retrieve a component that refers to a specific entity from the engine based on this ID.

```typescript
// fetch a Transform component
Transform.get(1000 as Entity)
```

TODO: get reserved ids

Note: The entity ids lover than 1000 FIND OUT REAL NUMBER are reserved 
see link reserved entities

For example, if a player's click or a raycast hits an entity, this will return the id of the hit entity, and you can use the command above to fetch the transform of the entity that matches that id.


## Add or replace a component

Each entity can only have one component of a given kind. For example, if you attempt to assign a Transform to an entity that already has one, this will cause an error.

To prevent this error, you can use `.createOrReplace` instead of `.create`. This command overwrites any existing components of the same kind if they exists, otherwise it creates a new component just like `.create`.


```ts
Transform.createOrReplace(door, {
	position: { x: 5, y: 1, z: 5 }
})
```

> Note: Since `.createOrReplace` runs an additional check before creating the component, it's always more performant to use `.create`. If you're sure that the entity doesn't already have a component like the one you're adding, use `.create`.


## Access a component from an entity

You can access components of an entity by using the entity's `.get()` or the `getMutable()` functions.

```ts
// Create entity
const box = engine.addEntity()

// Create and add component to that entity
Transform.create(box)

// Get read-only version of component
let transform = Transform.get(box)

// Get mutable version of component
let transform = Transform.getMutable(box)
```

The `get()` function fetches a read-only reference to the component. You cannot change any values from this reference of the component.

If you wish to change the values of the component, use the `getMutable()` function instead. If you change the values in the mutable version of the component, you're directly affecting the entity that component belongs to.

See [mutable data](/creator/development-guide/mutable-data) for more details.

> Note: Only use `getMutable()` if you're actually going to make changes to the component's values. Otherwise, always use `get()`. This practice follows the principles of [data oriented programming](/creator/development-guide/data-oriented-programming), and can significantly help in the scene's performance.

```ts
// Get mutable version of component
let transform = Transform.getMutable(box)

// change a value of the component
transform.scale.x = 5
```

The example above directly modifies the value of the _x_ scale on the Transform component.

If you're not entirely sure if the entity does have the component you're trying to retrieve, use `getOrNull()` or `getMutableOrNull()`.

> Note: Avoid using `getOrNull()` or `getMutableOrNull()` when possible, as these functions involve additional checks that and are therefore less efficient than `.get()` and `getMutable()`.


```ts
//  getComponentOrNull
let scale = box.getComponentOrNull(Transform)

// getComponentOrCreate
let scale = box.getComponentOrCreate(Transform)
```

If the component you're trying to retrieve doesn't exist in the entity:

- `get()` and `getMutable()` returns an error.
- `getOrNull()` and `getMutableOrNull()` returns `Null`.


## Remove a component from an entity

To remove a component from an entity, use the entity's `deleteFrom()` method of the component type.

```ts
Transform.deleteFrom(myEntity)
```

If you attempt to remove a component that doesn't exist in the entity, this action won't raise any errors.

> NOTE: To remove all the components of an entity at once, see TODO link to section up

## Check for a component

You can check if an entity owns an instance of a certain component by using the `has()` function. This function returns _true_ if the component is present, and _false_ if it's not. This can be very handy for using in conditional logic in your scene.

```ts
const hasTransform = Transform.has(myEntity)
```


> Tip: You can also [query components](/creator/development-guide/querrying-components) to fetch a full list of components that hold a specific component, or a specific set of components. Do not iterate over all entities in the scene manually to check each with a `has()`, that approach is a lot less efficient. 



<!-- - `getComponentOrCreate()` instances a new component in its place and retrieves it. -->

<!--
When you're dealing with [Interchangeable component](#interchangeable-components), you can also get a component by _space name_ instead of by type. For example, both `BoxShape` and `SphereShape` occupy the `shape` space of an entity. If you don't know which of these an entity has, you can fetch the `shape` of the entity, and it will return whichever component is occupying the `shape` space.

```ts
let entityShape = myEntity.getComponent(shape)
```

-->

<!--
## Pooling entities and components

If you plan to spawn and despawn similar entities from your scene, it's often a good practice to keep a fixed set of entities in memory. Instead of creating new entities and deleting them, you could add and remove existing entities from the engine. This is an efficient way to deal with the player's memory.

Entities that are not added to the engine aren't rendered as part of the scene, but they are kept in memory, making them quick to load if needed. Their geometry doesn't add up to the maximum triangle count for your scene while they aren't being rendered.

```ts
// Define spawner singleton object
const spawner = {
  MAX_POOL_SIZE: 20,
  pool: [] as Entity[],

  spawnEntity() {
    // Get an entity from the pool
    const ent = spawner.getEntityFromPool()

    if (!ent) return

    // Add a transform component to the entity
    let t = ent.getComponentOrCreate(Transform)
    t.scale.setAll(0.5)
    t.position.set(5, 0, 5)

    //add entity to engine
    engine.addEntity(ent)
  },

  getEntityFromPool(): Entity | null {
    // Check if an existing entity can be used
    for (let i = 0; i < spawner.pool.length; i++) {
      if (!spawner.pool[i].alive) {
        return spawner.pool[i]
      }
    }
    // If none of the existing are available, create a new one, unless the maximum pool size is reached
    if (spawner.pool.length < spawner.MAX_POOL_SIZE) {
      const instance = new Entity()
      spawner.pool.push(instance)
      return instance
    }
    return null
  },
}

spawner.spawnEntity()
```

When adding an entity to the engine, its `alive` field is implicitly set to `true`, when removing it, this field is set to `false`.

Using an object pool has the following benefits:

- If your entity uses a complex 3D model or texture, it might take the scene some time to load it from the content server. If the entity is already in memory when it's needed, then that delay won't happen.
- This is a solution to avoid a common problem, where each entity that's removed could remain lingering in memory after being removed, and these unused entities could add up till they become too many to handle. By recycling the same entities, you ensure this won't happen.

<!--
Similarly, if you plan to set and remove certain components from your entities, it's recommendable to create a pool of fixed components to add and remove rather than create new component instances each time.

```ts
```
-->


