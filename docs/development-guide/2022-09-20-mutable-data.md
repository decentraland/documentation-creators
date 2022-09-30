---
date: 2022-09-20
title: Data mutability
description: Learn how to handle ead-only and mutable data from components
categories:
  - development-guide
type: Document
redirect_from:
slug: /creator/development-guide/mutable-data/
---

When referencing data from a [component](/creator/development-guide/entities-components), you can either fetch the mutable or the read-only (immutable) version. 

When possible, you should always try to deal with the read-only versions of data as much as possible. This can bring a very significant improvement in the performance of your scene, when compared to always dealing with that same data from mutable versions.

The `.get()` function in a component returns an immutable (read-only) version of the component. You can only read its values, but can't change any of the properties on it.

The `.getMutable()` function returns a representation of the component that allows you to change its values. Use mutable versions only when you plan to make changes to a component, otherwise, always use `get()`.

```ts
// fetch a read-only (immutable) version
const immutableTransform = Transform.get(myEntity)

// the following does NOT work:
// 	immutableTransform.position.y = 2

const mutableTransform = Transform.getMutable(myEntity)

// the following line DOES change the entity's position
mutableTransform.position.y = 2
```

A good practice may be to iterate over read-only components, and then only fetch the mutable version when a change is required.


```ts
// hard-coded maximum height
const MAX_HEIGHT = 10

// Define the system
function HeightLimitSystem(dt: number) {
	// iterate over all entities that have a Transform component
  for (const [entity] of engine.getEntitiesWith(Transform)) {

	// get read-only values
	const currentHeight = Transform.get(entity).position.y

	// compare values
	if(currentHeight > MAX_HEIGHT){

		// fetch mutable version to make a change
		const mutableTransform = Transform.getMutable(entity)

		// change transform
		mutableTransform.position.y = MAX_HEIGHT
	}
  }
}

// Add system to engine
engine.addSystem(HeightLimitSystem)
```

In the example above, a system checks the read-only values of an entity's `Transform` component. On every tick it checks to see if the position's _y_ is higher than a hard-coded maximum height. If the height on the transform happens to be above this limit, then and only then we fetch the mutable version of the Transform. This may seem like extra work, but in a scene where we're checking values for many entities on every tick of the game loop, and only making changes occasionally, it results in huge performance gains.

This practice follows the principles of [data oriented programming](/creator/development-guide/data-oriented-programming).


> Note: In older versions of the SDK (6.x or older), components were always treated as mutable. This pattern may be a bit more straight forward to learn, but was a lot less efficient to run.