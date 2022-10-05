---
date: 2018-02-6
title: Colliders
description: Learn about the different components that give entities their 3D shape and collision.
categories:
  - development-guide
type: Document
redirect_from:
  - /development-guide/shape-components/
slug: /creator/development-guide/shape-components/
---

Entities that have colliders occupy space and block a player's path, entities without colliders can be walked through by a player`s avatar. 

Colliders are also needed to make an entity clickable. Button events are based on the collider shape of an entity, not on its visible shape. 

> Note: Colliders don't affect how other entities interact with each other, entities can always overlap. Collision settings only affect how the entity interacts with the player's avatar and button events. Decentraland doesn't have a native physics engine, so if you want entities to fall, crash or bounce, you must code this behavior into the scene, or import a library to handle that.

## Colliders on primitive shapes

Entities that have a `MeshRenderer` component to give them a [primitive shape](/creator/development-guide/shape-comopnents#primitive-shapes)(boxes, spheres, planes etc) don't have colliders by default. You must also give the entity a `MeshCollider` component.

This example defines a box entity that can't be walked through.

```ts
// create entity
const meshEntity = engine.addEntity()

// visible shape
MeshRenderer.create(meshEntity, { box: {} })

// collider
MeshCollider.create(meshEntity, { box: {} })
```

The shape used by the `MeshCollider` doesn't need to necessarily match the one used by the `MeshRenderer`.

## Colliders on 3d models

3D models can include their own colliders as part of a _.glTF_ or _.glb_ file. Any mesh in the model who's name ends in `_collider` is interpreted as a collider.

A _collider_ is a set of geometric shapes or planes that define which parts of the model are collided with. This allows for much greater control and is a lot less demanding on the system than using the visible geometry, as the collision object is usually a lot simpler (with less vertices) than the original model.

If a model doesn't have collisions, you can either:

- Give the entity a `MeshCollider` component, to give it a primitive shape collider.
- Overlay an invisible entity that has a `MeshCollider` component.
- Edit the model in an external tool like Blender to include a _collider mesh_. The collider must be named _x_collider_, where _x_ is the name of the model. So for a model named _house_, the collider must be named _house_collider_.


See [3D models](/creator/3d-modeling/3d-models) for more details on how to add colliders to a 3D model.



## Pointer blocking

Only shapes that have colliders block player [button events](/creator/development-guide/click-events), so that for example a player can't click through a wall, or pick something up that is locked inside a chest.


TODO: check if this is all still true!!!

You can however disable this behavior on any shape, no matter if it's a primitive or an imported 3D model.


```ts
let box = new BoxShape()
box.isPointerBlocker = false
myEntity.addComponent(box)
```

By using this property, you could for example have an invisible wall that players can't walk through, but that does allow them to click on items on the other side of the wall.
