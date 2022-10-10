---
date: 2018-02-14
title: Button events
description: Learn how to handle user clicks in your scene.
categories:
  - development-guide
redirect_from:
  - /sdk-reference/event-handling/
  - /development-guide/event-handling/
  - /development-guide/click-events/
type: Document
slug: /creator/development-guide/click-events/
---


A Decentraland scene can detect button events from all of the buttons that are used to control the player's avatar. These include pointer clicks, several action buttons, and the keys that are used to move the avatar around. Button events can come from a mouse, a touch screen, a VR controller or some other device, these are all interpreted the same by the SDK.


## Pointer events

To detect button events performed while pointing at an entity, you must give the entity a `PointerEvents` component.

To trigger an action based on a pointer event, you must implement a system that checks for these events and carries out the desired action.

```ts
// create entity
const myEntity = engine.addEntity()

// give entity a PointerEvents component
PointerEvents.create(myEntity, {
    pointerEvents: [
      {
        eventType: PointerEventType.DOWN,
        eventInfo: {
          button: ActionButton.POINTER,
        }
      }
    ]
})

// create a system to react to pointer events on this entity
engine.addSystem(() => {
    if (wasEntityClicked(myEntity, ActionButton.POINTER)){
      log("Entity was clicked")
    }
})
```

The `PointerEvents` component requires that you define at least one pointer event. You can potentially add multiple ones as an array. Each pointer event must specify at least what kind of event to listen for (eg: button down, button up), and which button to listen for.



> Note: The entity must also have a [collider](/creator/development-guide/colliders) to respond to button events. If an entity has no collider, give it a `MeshCollider` component to make it clickable.



### Types of pointer events

There are various types of pointer event that can be detected. Configure the `PointerEvents` component to include the events you're interested in detecting. Refer to these events through the `PointerEventType` enum.

The following events are available:

- `DOWN`: Player pushes down a specific button while having the cursor pointing at the entity's collider.
- `UP`: Player releases a specific button while having the cursor pointing at the entity's collider.
- `HOVER_ENTER`: Player's cursor starts pointing at the entity's collider.
- `HOVER_LEAVE`: Player's cursor stops pointing at the entity's collider.

A single `PointerEvents` component can detect as many of these events as needed, by including an object for each.

```ts
PointerEvents.create(myEntity, {
    pointerEvents: [
      {
        eventType: PointerEventType.DOWN,
        eventInfo: {
          button: ActionButton.POINTER,
        }
      }, {
        eventType: PointerEventType.UP,
        eventInfo: {
          button: ActionButton.POINTER,
        }
      }, {
        eventType: PointerEventType.HOVER_ENTER,
        eventInfo: {}
      }, {
        eventType: PointerEventType.HOVER_LEAVE,
        eventInfo: {}
      }
    ]
  })
```



### Pointer buttons

The following buttons can be registered as pointer events on an entity:

- `ActionButton.POINTER`: This maps to the left-mouse button on a computer.
- `ActionButton.PRIMARY`: This maps to the **E** key on a computer.
- `ActionButton.SECONDARY`: This maps to the **F** key on a computer.

```ts
PointerEvents.create(myEntity, {
    pointerEvents: [
      {
        eventType: PointerEventType.DOWN,
        eventInfo: {
          button: ActionButton.PRIMARY,
        }
      }, {
        eventType: PointerEventType.DOWN,
        eventInfo: {
          button: ActionButton.SECONDARY,
        }
      }
    ]
})
```

A single `PointerEvents` component can detect button events from different buttons.


Other buttons, such actions 3 to 6 and the movement keys, are not usable as pointer buttons. They can only be detected as global events. For example, you can detect that the player pushed the JUMP key, but the event does not belong to the entity that the cursor was pointing at that time.

### Detect pointer events

Check if an entity was triggered by a pointer event in the last frame by using the `wasEntityClicked()` within a [system](/creator/development-guide/system).

```ts
engine.addSystem(() => {
    if (wasEntityClicked(myEntity, ActionButton.POINTER)){
      log("Entity was just clicked")
    }
})
```

`wasEntityClicked()` returns _true_ only if the indicated button was pressed down in the current tick of the game loop. If the button is not pushed down, or it was already down from the previous tick, it returns _false_.

`wasEntityClicked()` helps disambiguate several corner cases that might occur, especially if the player clicks fast. For example, if the button was pushed down and up since the last tick, this should be considered a new click event, even if the button is currently no longer down. Also, if the button was down on the last tick, but was raised and then pushed down again since the last tick, this should be considered a new click event. 


### Detect active pointer events

Check if a button is currently being held down while aiming at an entity by using `isPointerEventActive()` within a system. 

```ts
engine.addSystem(() => {
    if (isPointerEventActive(myEntity, ActionButton.POINTER)){
      log("Entity is currently being clicked")
    }
})
```

`isPointerEventActive()` returns _true_ if the button is currently being held down, no matter when the button was pushed down.

### Handle multiple entities

If your scene has multiple entities that might be affected by pointer events, it makes sense to write a system that iterates over all of them.

```ts
function killSystem() {
  const clickedCubes = engine.getEntitiesWith(PointerEvents)
  for (const [entity] of clickedCubes) {
    if (wasEntityClicked(entity, ActionButton.PRIMARY)) {
		engine.removeEntity(entity)
    }
  }
}
engine.addSystem(killSystem)
```

This example uses a [component query](/creator/development-guide/querrying-components) to iterate over all the entities with a `PointerEvents` component. It then checks each with `wasEntityClicked()`, and if so removes the entity from the engine.

Instead of iterating over _all_ the entities with a `PointerEvents` component in a single system, you might want to instead write different systems to handle entities that should behave in different ways.

```ts
function openDoorSystem() {
  const clickedDoors = engine.getEntitiesWith(PointerEvents, IsDoor)
  for (const [entity] of clickedDoors) {
    if (wasEntityClicked(entity, ActionButton.PRIMARY)) {
		openDoor(entity)
    }
  }
}
engine.addSystem(openDoorSystem)
```

In the example above, the system iterates over the entities that have both a `PointerEvents` component and a custom `IsDoor` flag component, that is added to all doors in your scene. The system allows you to run the same code for every door that has been clicked.



### Hint messages

When a player hovers the cursor over an item with an `PointerEvents` component, the cursor changes shape to hint to the player that the entity is interactive.

You can also display a toast message in the UI that lets the player know what happens if they interact with the entity.

```ts
// create entity
const chest = engine.addEntity()

// give entity a PointerEvents component
PointerEvents.create(chest, {
    pointerEvents: [
      {
        eventType: PointerEventType.DOWN,
        eventInfo: {
          button: ActionButton.POINTER,
		  showFeedback: true
		  hoverText: "Open",
        }
      }
    ]
})
```


In the example above, the pointer event includes some additional arguments:

- `showFeedback`: Boolean to turn the hover hints on or off. It's _true_ by default.
- `hoverText`: String to display in the UI while the player points at the entity. By default, this string spells _Interact_, unless `showFeedback` is _false_.

> TIP: The `hoverText` string should describe the action that happens when interacting. For example `Open`, `Activate`, `Grab`, `Select`. These strings should be as short as possible, to avoid stealing too much focus from the player.

If an entity has multiple pointer events on it, the hover hints for each of these are displayed radially around the cursor.

The `hoverText` of an `.UP` pointer event is only displayed while the player is already holding down the corresponding key and pointing at the entity.

TODO: Confirm

If an entity has both a `DOWN` pointer event and an `UP` pointer event, the hint for the `DOWN` action is shown while the button is not being pressed. The hint switches to the one from the `UP` event only when the button is pressed and remains pressed.

```ts
// create entity
const entity = engine.addEntity()

// give entity a PointerEvents component
PointerEvents.create(entity, {
    pointerEvents: [
      {
        eventType: PointerEventType.DOWN,
        eventInfo: {
          button: ActionButton.POINTER,
		  hoverText: "Drag",
        }
      }, {
        eventType: PointerEventType.UP,
        eventInfo: {
          button: ActionButton.POINTER,
		  hoverText: "Drop",
        }
      }
    ]
})
```

#### Custom hints

You can otherwise use `HOVER_ENTER` and `HOVER_LEAVE` pointer events to hint that something is interactable in some custom way. For example, you could play a subtle sound when `HOVER_ENTER` event is activated. You could also show a glowing highlight around the entity when a `HOVER_ENTER` event is activated, and hide it when `HOVER_LEAVE` is activated. It could also be used for specific gameplay mechanics.


> TIP: Note that all entities with a `PointerEvents` component by default show a UI hint when hovered over. If this conflicts with the feedback you want to show, you can disable this UI hint by setting the `showFeeback` property on the pointer event to false.

```ts
// create entity
const entity = engine.addEntity()

// give entity a PointerEvents component
PointerEvents.create(entity, {
    pointerEvents: [
      {
        eventType: PointerEventType.DOWN,
        eventInfo: {
          button: ActionButton.POINTER,
		  showFeedback: false
        }
      },
	   {
        eventType: PointerEventType.HOVER_ENTER,
        eventInfo: {
        }
      },
	   {
        eventType: PointerEventType.HOVER_LEAVE,
        eventInfo: {
        }
      }
    ]
})


TODO: trigger actions based on hover events
```


### Max distance

Pointer events have a max range of interaction. If a player is too far away from an entity, the pointer event won't be activated and the hover hint won't be displayed next to the cursor.

By default, entities are only clickable when the player is within a close range of the entity, at a maximum distance of _10 meters_. You can change the maximum distance by setting the `maxDistance` property of a pointer event.

```ts
// create entity
const myEntity = engine.addEntity()

// give entity a PointerEvents component
PointerEvents.create(myEntity, {
    pointerEvents: [
      {
        eventType: PointerEventType.DOWN,
        eventInfo: {
          button: ActionButton.POINTER,
		  maxDistance: 6
        }
      }
    ]
})
```

The example above sets the maximum distance to _6 meters_.


> Note: The `maxDistance` is measured in meters from meters from the player's camera. Keep in mind that in 3rd person the camera is a bit further away, so make sure the distance you set works well in both modes.


### Ray Obstacles

Button events cast rays that only interact with the first entity on their path, as long as the entity is closer than its distance limit.

For an entity to be intercepted by the ray of a button event, the entity's 3d model must either have a collider mesh, or the entity must have a `CollierMesh` component.

If another entity's collider is standing on the way of the entity that the player wants to click, the player won't be able to click the entity that's behind, unless the entity's `MeshCollider` component is configured to allow clicking through it.


TODO

### Multiple buttons on an entity

TODO

### Differentiate meshes inside a model

TODO

## Global button events

TODO

### Tracking player movements


In real-time multiplayer games where the timing of player movements is critical, you may want to keep track of each player's position using a 3rd party server as the source of truth. You can improve response time by listening to the button in advance and predict their effects in your server before the avatar has shifted position.

This approach helps compensate for network delays, but is sure to result in discrepancies, so you should also regularly poll the player's current position to make corrections. Balancing these predictions and corrections may require plenty of fine-tuning.

## Button event arguments

### Handle pointer events - advanced

























### Event arguments

The _pointer down event_ and the _pointer up event_ objects are implicitly passed as parameters of the functions in the `OnPointerDown` and `OnPointerUp` components, respectively. This event object contains various properties that might be useful for the function. See [Properties of button events](#properties-of-button-events) for more details.

```ts
const myEntity = new Entity()
myEntity.addComponent(new BoxShape())

myEntity.addComponent(
  new OnPointerDown(
    (e) => {
      log("Click distance: " + e.length)
    },
    { button: ActionButton.PRIMARY }
  )
)
```

### Multiple buttons on an entity

You may want to make an entity respond to different buttons in different ways. Each entity can only have _one_ `OnPointerDown` component, and _one_ `OnPointerUp` component, but you can use `ActionButton.ANY` and then tell them apart within the function.

Check the `buttonId` field from the event data. The value of this field returns a number, which maps to the values in the `ActionButton` array, for example by `POINTER` maps to _0_, `PRIMARY` to _1_, `SECONDARY` to _2_, etc.

```ts
const myEntity = new Entity()
myEntity.addComponent(new BoxShape())

myEntity.addComponent(
  new OnPointerDown(
    (e) => {
      if (e.buttonId == 0) {
        log("Clicked pointer")
      } else if (e.buttonId == 1) {
        log("Pressed primary button")
      } else if (e.buttonId == 2) {
        log("Pressed secondary button")
      }
    },
    { button: ActionButton.ANY }
  )
)
```

Players will see a single label when hovering over the entity, so make sure it's clear that there are multiple ways to interact with it.

## Properties of button events

The events from `OnPointerDown` and `OnPointerUp` components, as well as all the global button event objects, contain the following parameters:

- `origin`: Origin point of the ray, as a _Vector3_
- `direction`: Direction vector of the ray, as a normalized _Vector3_ that points in the same direction.
- `buttonId`: ID of the button that triggered the event (_POINTER_, _PRIMARY_ or _SECONDARY_)
- `hit`: _(Optional)_ Object describing the entity that was clicked on. If the click didn't hit any specific entity, this field isn't present. The `hit` object contains the following parameters:

  - `length`: Length of the ray in meters, as a _number_
  - `hitPoint`: The intersection point between the ray and the entity's mesh, as a _Vector3_
  - `meshName`: The name of the mesh, if applicable, as a _string_
  - `worldNormal`: The normal of the hit, in world space, as a _Vector3_
  - `entityId`: The ID of the entity, if applicable, as a _string_

### Differentiate meshes inside a model

Often, _.glTF_ 3D models are made up of multiple meshes, that each have an individual internal name. All button events events include the information of what specific mesh was clicked, so you can use this information to trigger different click behaviors in each case.

To see how the meshes inside the model are named, you must open the 3D model with an editing tool, like [Blender](https://www.blender.org/) for example.

<img src="/images/media/mesh-names.png" alt="Mesh internal names in an editor" width="250"/>

> Tip: You can also learn the name of the clicked mesh by logging it and reading it off console.

You access the `meshName` property as part of the `hit` object, that's returned by the click event.

In the example below we have a house model that includes a mesh named `firePlace`. We want to turn on the fireplace only when its corresponding mesh is clicked.

```ts
houseEntity.addComponent(
  new OnPointerDown(
    (e) => {
      log("button A Down", e.hit.meshName)
      if (e.hit.meshName === "firePlace") {
        // light fire
        fireAnimation.play()
      }
    },
    { button: ActionButton.POINTER, showFeeback: false }
  )
)
```

> Note: Since the `OnPointerDown` component belongs to the whole entity, the on-hover feedback would be seen when hovering over any part of the entity. In this case, any part of the house, not just the fireplace. For that reason, we set the `showFeedback` argument of the `OnPointerDown` component to _false_, so that no on-hover feedback is shown. For a better player experience, it's recommended to instead have the fireplace as a separate entity and maintain the on-hover feedback.

## Global button events

The _BUTTON_DOWN_ and _BUTTON_UP_ events are fired whenever the player presses or releases an input controller button.

These events are triggered every time that the buttons are pressed or released, regardless of where the player's pointer is pointing at, as long as the player is standing inside the scene's boundaries.

It also tracks keys used for basic avatar movement whilst in the scene.

Instance an `Input` object and use its `subscribe()` method to initiate a listener that's subscribed to one of the button events. Whenever the event is caught, it executes a provided function.

The `subscribe()` method takes four arguments:

- `eventName`: The type of action, this can be either `"BUTTON_DOWN"` or `"BUTTON_UP"`
- `buttonId`: Which button to listen for.
  The following buttons can be tracked for both BUTTON_DOWN and BUTTON_UP events:

      - `POINTER` (left mouse click on PC)
      - `PRIMARY` (_E_ on PC)
      - `SECONDARY`(_F_ on PC)
      - `JUMP`(_space bar_ on PC)
      - `FORWARD`(_W_ on PC)
      - `LEFT`(_A_ on PC)
      - `RIGHT`(_D_ on PC)
      - `BACK`(_S_ on PC)
      - `WALK`(_Shift_ on PC)
      - `ACTION_3`(_1_ on PC)
      - `ACTION_4`(_2_ on PC)
      - `ACTION_5`(_3_ on PC)
      - `ACTION_6`(_4_ on PC)

- `useRaycast`: Boolean to define if raycasting will be used. If `false`, the button event will not contain information about any `hit` objects that align with the pointer at the time of the event. Avoid setting this field to `true` when information about hit objects is not required, as it involves extra calculations.
- `fn`: The function to execute each time the event occurs.

> Note: Other keys on the PC keyboard aren't tracked for future cross-platform compatibility, as this limited set of keys can be mapped to a joystick. For detecting key-strokes when writing text, check the [UIInputBox](/creator/development-guide/onscreen-ui).

```ts
// Instance the input object
const input = Input.instance

// button down event
input.subscribe("BUTTON_DOWN", ActionButton.POINTER, false, (e) => {
  log("pointer Down", e)
})

// button up event
input.subscribe("BUTTON_UP", ActionButton.POINTER, false, (e) => {
  log("pointer Up", e)
})
```

The example above logs messages and the contents of the event object every time the pointer button is pushed down or released.

The event objects of both the `BUTTON_DOWN` and the `BUTTON_UP` contain various useful properties. See [Properties of button events](#properties-of-button-events) for more details.

> Note: The code for subscribing an input event only needs to be executed once, the `subscribe()` method keeps polling for the event. Don't add this into a system's `update()` function, as that would register a new listener on every frame.

### Detect hit entities

If the third argument of the `subscribe()` function (`useRaycast`)is true, and the player is pointing at an entity that has a collider, the event object includes a nested `hit` object. The `hit` object includes information about the collision and the entity that was hit.

Raycasting is not available when detecting basic movement keys. It's only available when tracking the following buttons:

- `POINTER`
- `PRIMARY`
- `SECONDARY`
- `ACTION_3`
- `ACTION_4`
- `ACTION_5`
- `ACTION_6`

The ray of a global button event only detects entities that have a collider mesh. Primitive shapes have a collider mesh on by default, 3D models need to have one built into them.

> Tip: See [Colliders](/creator/3d-modeling/colliders) for details on how to add collider meshes to a 3D model.

```ts
input.subscribe("BUTTON_DOWN", ActionButton.POINTER, true, (e) => {
  if (e.hit) {
    let hitEntity = engine.entities[e.hit.entityId]
    hitEntity.addComponent(greenMaterial)
  }
})
```

The example above checks if any entities were hit, and if so it fetches the entity and applies a material component to it.

The event data returns a string for the `entityId`. If you want to reference the actual entity by that ID to affect it in some way, use `engine.entities[e.hit.entityId]`.

