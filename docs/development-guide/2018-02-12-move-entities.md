---
date: 2018-02-12
title: Move entities
description: How to move, rotate and scale an entity gradually over time, with incremental changes.
categories:
  - development-guide
type: Document
redirect_from:
  - /development-guide/move-entities
slug: /creator/development-guide/move-entities/
---

To move, rotate or resize an entity in your scene over a period of time, change the _position_, _rotation_ and _scale_ values stored in an entity's `Transform` component incrementally, frame by frame. This can be used on primitive shapes (cubes, spheres, planes, etc) as well as on 3D models (glTF).

You can easily perform these incremental changes by moving entities a small amount each time the function of [system](/creator/development-guide/systems) runs.

<!--
> Tip: You can use the helper functions in the [utils library](https://www.npmjs.com/package/decentraland-ecs-utils) to achieve most of the tasks described in this doc. The code shown in these examples is handled in the background by the library, so in most cases it only takes a single line of code to use them.
-->

## Move

The easiest way to move an entity is to gradually modify the _position_ value stored in the `Transform` component.

```ts
function SimpleMove() {
	let transform = Transform.getMutable(myEntity)
	transform.position = Vector3.add(transform.position, Vector3.scale(Vector3.Forward(), 0.1))
}

engine.addSystem(SimpleMove)

const myEntity = engine.addEntity()
Transform.create(myEntity, {
	position: {x: 4, y: 1, z: 4}
})
BoxShape.create(myEntity)
```

In this example we're moving an entity by 0.1 meters per tick of the game loop.

`Vector3.Forward()` returns a vector that faces forward and measures 1 meter in length. In this example we're then scaling this vector down to 1/10 of its length with `Vector3.scale()`. If our scene has 30 frames per second, the entity is moving at 3 meters per second in speed.

 <img src="/images/media/gifs/move.gif" alt="Move entity" width="300"/>

## Rotate

The easiest way to rotate an entity is to gradually change the values in the Transform component incrementally, and run this as part of a system's function of a system.


```ts
function SimpleRotate() {
	let transform = Transform.getMutable(myEntity)
	transform.rotation = Quaternion.add(transform.rotation, Quaternion.create(0.1,0,0,0))
}

engine.addSystem(SimpleRotate)

const myEntity = engine.addEntity()
Transform.create(myEntity, {
	position: {x: 4, y: 1, z: 4}
})
BoxShape.create(myEntity)
```

TODO: Verify this rotate example running it


> Tip: To make an entity always rotate to face the player, you can add a [`Billboard` component](/creator/development-guide/entity-positioning#face-the-user).

 <img src="/images/media/gifs/rotate.gif" alt="Move entity" width="300"/>

## Rotate over a pivot point

When rotating an entity, the rotation is always in reference to the entity's center coordinate. To rotate an entity using another set of coordinates as a pivot point, create a second (invisible) entity with the pivot point as its position and make it a parent of the entity you want to rotate.

When rotating the parent entity, its children will be all rotated using the parent's position as a pivot point. Note that the `position` of the child entity is in reference to that of the parent entity.

```ts
function SimpleRotate() {
	let transform = Transform.getMutable(pivotEntity)
	transform.rotation = Quaternion.add(transform.rotation, Quaternion.create(0.1,0,0,0))
}

engine.addSystem(SimpleRotate)

const pivotEntity = engine.addEntity()
Transform.create(pivotEntity, {
	position: {x: 4, y: 1, z: 4}
})

const childEntity = engine.addEntity()
Transform.create(childEntity, {
	position: {x: 1, y: 0, z: 0},
	parent: pivotEntity
})
BoxShape.create(childEntity)

```

Note that in this example, the system is rotating the `pivotEntity` entity, that's a parent of the `childEntity` entity.

 <img src="/images/media/gifs/pivot-rotate.gif" alt="Move entity" width="300"/>

## Adjust movement to delay time

Suppose that the player visiting your scene is struggling to keep up with the pace of the frame rate. That could result in the movement appearing jumpy, as not all frames are evenly timed but each moves the entity in the same amount.

You can compensate for this uneven timing by using the `dt` parameter to adjust the scale the movement.

```ts
function SimpleMove(dt: number) {
	let transform = Transform.getMutable(myEntity)
	transform.position = Vector3.add(transform.position, Vector3.scale(Vector3.Forward(), dt))
}

engine.addSystem(SimpleMove)

const myEntity = engine.addEntity()
Transform.create(myEntity, {
	position: {x: 4, y: 1, z: 4}
})
BoxShape.create(myEntity)
```

The example above keeps movement at approximately the same speed as the movement example above, even if the frame rate drops. When running at 30 frames per second, the value of `dt` is 1/30.

You can also smoothen rotations in the same way by multiplying the rotation amount by `dt`.

## Move between two points

If you want an entity to move smoothly between two points, use the _lerp_ (linear interpolation) algorithm. This algorithm is very well known in game development, as it's really useful.

The `lerp()` function takes three parameters:

- The vector for the origin position
- The vector for the target position
- The amount, a value from 0 to 1 that represents what fraction of the translation to do.

```ts
const originVector = Vector3.Zero()
const targetVector = Vector3.Forward()

let newPos = Vector3.lerp(originVector, targetVector, 0.6)
```


The linear interpolation algorithm finds an intermediate point in the path between both vectors that matches the provided amount.

For example, if the origin vector is _(0, 0, 0)_ and the target vector is _(10, 0, 10)_:

- Using an amount of 0 would return _(0, 0, 0)_
- Using an amount of 0.3 would return _(3, 0, 3)_
- Using an amount of 1 would return _(10, 0, 10)_

To implement this `lerp()` in your scene, we recommend creating a custom component to store the necessary information. You also need to define a system that implements the gradual movement in each frame.


```ts
// define custom component
const COMPONENT_ID = 2046

const Vector3EcsType = Schemas.Map({
	x: Schemas.Float,
	y: Schemas.Float,
	z: Schemas.Float
  })

const MoveTransportData = {
  start: Vector3EcsType,
  end: Vector3EcsType,
  fraction: Schemas.Float,
  speed: Schemas.Float,
}

export const LerpTransformComponent = engine.defineComponent(MoveTransportData, COMPONENT_ID)


// define system
function LerpMove(dt: number) {
	let transform = Transform.getMutable(myEntity)
	let lerp = LerpTransformComponent.getMutable(myEntity)
	if (lerp.fraction < 1) {
		lerp.fraction += dt * lerp.speed
		transform.position = Vector3.lerp(lerp.origin, lerp.target, lerp.fraction)
	}
}

engine.addSystem(LerpMove)

// create entity
const myEntity = engine.addEntity()

Transform.create(myEntity, {
	position: {x: 4, y: 1, z: 4}
})

BoxShape.create(myEntity)

LerpTransformComponent.create(myEntity, {
  start: {x: 4, y: 1, z: 4},
  end: {x: 8, y: 1, z: 8},
  fraction: 0,
  speed: 1
})
```

TODO: validate new code works

 <img src="/images/media/gifs/lerp-move.gif" alt="Move entity" width="300"/>


## Rotate between two angles

To rotate smoothly between two angles, use the _slerp_ (_spherical_ linear interpolation) algorithm. This algorithm is very similar to a _lerp_, but it handles quaternion rotations.

The `slerp()` function takes three parameters:

- The [quaternion](https://en.wikipedia.org/wiki/Quaternion) angle for the origin rotation
- The [quaternion](https://en.wikipedia.org/wiki/Quaternion) angle for the target rotation
- The amount, a value from 0 to 1 that represents what fraction of the translation to do.

> Tip: You can pass rotation values in [euler](https://en.wikipedia.org/wiki/Euler_angles) degrees (from 0 to 360) by using `Quaternion.Euler()`.


```ts
const originRotation = Quaternion.rotationYawPitchRoll(0, 90, 0)
const targetRotation = Quaternion.rotationYawPitchRoll(0, 0, 0)

let newRotation = Quaternion.slerp(originRotation, targetRotation, 0.6)
```

To implement this in your scene, we recommend storing the data that goes into the `Slerp()` function in a custom component. You also need to define a system that implements the gradual rotation in each frame.


```ts
// define custom component
const COMPONENT_ID = 2046

const QuaternionEcsType = Schemas.Map({
	x: Schemas.Float,
	y: Schemas.Float,
	z: Schemas.Float,
	w:  Schemas.Float
})

const RotateSlerpData = {
  start: QuaternionEcsType,
  end: QuaternionEcsType,
  fraction: Schemas.Float,
  speed: Schemas.Float,
}

export const SlerpData = engine.defineComponent(RotateSlerpData, COMPONENT_ID)


// define system
function SlerpRotate(dt: number) {
	let transform = Transform.getMutable(myEntity)
	let slerpData = SlerpData.getMutable(myEntity)
	if (slerpData.fraction < 1) {
		slerpData.fraction += dt * slerpData.speed
		transform.position = Quaternion.slerp(slerpData.origin, slerpData.target, slerpData.fraction)
	}
}

engine.addSystem(LerpMove)

// create entity
const myEntity = engine.addEntity()

Transform.create(myEntity, {
	position: {x: 4, y: 1, z: 4}
})

BoxShape.create(myEntity)

SlerpData.create(myEntity, {
  start: Quaternion.rotationYawPitchRoll(0, 90, 0),
  end: Quaternion.rotationYawPitchRoll(0, 0, 0),
  fraction: 0,
  speed: 1
})
```


> Note: You could instead represent the rotation with euler angles as `Vector3` values and use a `Lerp()` function, but that would imply a conversion from `Vector3` to `Quaternion` on each frame. Rotation values are internally stored as quaternions in the `Transform` component, so it's more efficient for the scene to work with quaternions.

 <img src="/images/media/gifs/lerp-rotate.gif" alt="Move entity" width="300"/>

<!--

TODO: scalar namespace missing


## Change scale between two sizes

If you want an entity to change size smoothly and without changing its proportions, use the _lerp_ (linear interpolation) algorithm of the `Scalar` object.

Otherwise, if you want to change the axis in different proportions, use `Vector3` to represent the origin scale and the target scale, and then use the _lerp_ function of the `Vector3`.

The `lerp()` function of the `Scalar` object takes three parameters:

- A number for the origin scale
- A number for the target scale
- The amount, a value from 0 to 1 that represents what fraction of the scaling to do.

```ts
const originScale = 1
const targetScale = 10

let newScale = Scalar.Lerp(originScale, targetScale, 0.6)
```

To implement this lerp in your scene, we recommend creating a custom component to store the necessary information. You also need to define a system that implements the gradual scaling in each frame.

```ts
@Component("lerpData")
export class LerpSizeData {
  origin: number = 0.1
  target: number = 2
  fraction: number = 0
}

// a system to carry out the movement
export class LerpSize implements ISystem {
  update(dt: number) {
    let transform = myEntity.getComponent(Transform)
    let lerp = myEntity.getComponent(LerpSizeData)
    if (lerp.fraction < 1) {
      let newScale = Scalar.Lerp(lerp.origin, lerp.target, lerp.fraction)
      transform.scale.setAll(newScale)
      lerp.fraction += dt / 6
    }
  }
}

// Add system to engine
engine.addSystem(new LerpSize())

const myEntity = new Entity()
myEntity.addComponent(new Transform())
myEntity.addComponent(new BoxShape())

myEntity.addComponent(new LerpSizeData())
myEntity.getComponent(LerpSizeData).origin = 0.1
myEntity.getComponent(LerpSizeData).target = 2

engine.addEntity(myEntity)
```

 <img src="/images/media/gifs/lerp-scale.gif" alt="Move entity" width="300"/>

-->

## Move at irregular speeds between two points

While using the lerp method, you can make the movement speed non-linear. In the previous example we increment the lerp amount by a given amount each frame, but we could also use a mathematical function to increase the number exponentially or in other measures that give you a different movement pace.

You could also use a function that gives recurring results, like a sine function, to describe a movement that comes and goes.

```ts
// define custom component
const COMPONENT_ID = 2046

const Vector3EcsType = Schemas.Map({
	x: Schemas.Float,
	y: Schemas.Float,
	z: Schemas.Float
  })

const MoveTransportData = {
  start: Vector3EcsType,
  end: Vector3EcsType,
  fraction: Schemas.Float,
  speed: Schemas.Float,
}

export const LerpTransformComponent = engine.defineComponent(MoveTransportData, COMPONENT_ID)


// define system
function LerpMove(dt: number) {
	let transform = Transform.getMutable(myEntity)
	let lerp = LerpTransformComponent.getMutable(myEntity)
	if (lerp.fraction < 1) {
		lerp.fraction += dt * lerp.speed
		const interpolatedValue = interpolate(lerp.fraction)
		transform.position = Vector3.lerp(lerp.origin, lerp.target, interpolatedValue)
	}
}

// map the lerp fraction to an exponential curve  
function interpolate(t: number){
	return t * t
}

engine.addSystem(LerpMove)

// create entity
const myEntity = engine.addEntity()

Transform.create(myEntity, {
	position: {x: 4, y: 1, z: 4}
})

BoxShape.create(myEntity)

LerpTransformComponent.create(myEntity, {
  start: {x: 4, y: 1, z: 4},
  end: {x: 8, y: 1, z: 8},
  fraction: 0,
  speed: 1
})
```



The example above is just like the linear lerp example we've shown before, but the `fraction` field mapped to a non-linear value on every tick. This non-linear value is used to calculate the `lerp` function, resulting in a movement that follows an exponential curve.

 <img src="/images/media/gifs/lerp-speed-up.gif" alt="Move entity" width="300"/>

## Follow a path

You can have an entity loop over an array of vectors, performing a lerp movement between each to follow a more complex path.

```ts
// define custom component
const COMPONENT_ID = 2046

const Vector3EcsType = Schemas.Map({
	x: Schemas.Float,
	y: Schemas.Float,
	z: Schemas.Float
  })

const PathTransportData = {
  path: Schemas.Array(Vector3EcsSchema),
  start: Vector3EcsType,
  end: Vector3EcsType,
  fraction: Schemas.Float,
  speed: Schemas.Float,
  pathTargetIndex: Schemas.Int
}

export const LerpTransformComponent = engine.defineComponent(PathTransportData, COMPONENT_ID)


// define system
function PathMove(dt: number) {
	let transform = Transform.getMutable(myEntity)
	let lerp = PathTransportData.getMutable(myEntity)
	if (lerp.fraction < 1) {
		lerp.fraction += dt * lerp.speed
		transform.position = Vector3.lerp(lerp.origin, lerp.target, lerp.fraction)
	} else {
      lerp.pathTargetIndex += 1
      if (path.pathTargetIndex >= myPath.path.length) {
        path.pathTargetIndex = 0
      }
      path.origin = path.target
      path.target = myPath.path[path.pathTargetIndex]
      path.fraction = 0
    }
}

engine.addSystem(PathMove)

// create entity
const myEntity = engine.addEntity()

Transform.create(myEntity, {
	position: {x: 4, y: 1, z: 4}
})

BoxShape.create(myEntity)

const point1 = {x: 1, y: 1, z: 1}
const point2 = {x: 8, y: 1, z: 3}
const point3 = {x: 8, y: 4, z: 7}
const point4 = {x: 1, y: 1, z: 7}

const myPath = [point1, point2, point3, point4]


PathTransportData.create(myEntity, {
  path: myPath,
  start: {x: 4, y: 1, z: 4},
  end: {x: 8, y: 1, z: 8},
  fraction: 0,
  speed: 1,
  pathTargetIndex: 1
})
```

The example above defines a 3D path that's made up of four 3D vectors. The `PathTransportData` custom component holds the same data used by the custom component in the _lerp_ example above, but adds a `path` array, with all of the points in our path, and a `pathTargetIndex` field to keep track of what segment of the path is currently in use.

The system is very similar to the system in the _lerp_ example, but when a lerp action is completed, it sets the `target` and `origin` fields to new values. If we reach the end of the path, we return to the first value in the path.

 <img src="/images/media/gifs/lerp-path.gif" alt="Move entity" width="300"/>



TODO: Verify all examples by running them!!