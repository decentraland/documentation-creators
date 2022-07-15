---
date: 2022-07-15
title: Data Oriented Programming
description: The Data Oriented Programming is a powerful approach to programming gets the most out of performance.
categories:
  - development-guide
type: Document
---


Data Oriented Programming is a powerful approach to programming that focuses on improving performance by treating data as the cental element. It's also very multiplayer friendly, as it optimizes the data that needs to be synchronized between players to a bare minimum, and makes it easy to access.

Data Oriented Programming encourages you to think about everything in your scene as data that needs to be copied and mutated throughtout the various systems in your scene. The main benefit of this approach is on minimizing cache misses and optimizing the speed at which data can be read from memory, which is often the main bottleneck while running modern applications and games. This very often results in improvements that are very noticeable.

Because of this remarkable improvement in performance, much of the game making industry is adopting it.

## What it looks like

Data Oriented Programming is different from Object Oriented Programming, that is the paradigm many developers are most familiar with. In Object Oriented Programming, the focus is on abstractions that try to replicate real-world constructs that hold both data and functionality. This approach is often easier to plan out, but also more inefficient to execute.


In Data oriented Programming, the flow of data throughout your scene is separate from how that data translates to into the 3d world you render. 

For example, your scene might have a dozen doors that can be open or closed, you can represent the state of each of these doors as a simple array of boolean values. These values might map to each door being open if true, and false if closed. While you're processing any changes in the state of these doors and syncing them with other players, you don't really care about what these booleans represent. They're just an array of booleans that need to be synchronized. A separate system on your scene might then take care of regularly updating the rotation of each door entity to ensure it matches its state.

The Entity, Component System (ECS) model that the Decentraland SDK is built upon is all about Data Oriented Programming. Each component belongs is part of its own collection of data, it belongs to an entity by reference, but the data is not structured around the entity, the data is structured as a collection of similar components. The Transform components in a scene are all equal, one might belong to your main building, the other to a tiny glass. While processing the transforms, that distinction is irrelevant.

In the example above, you might have a `doorState` component that keeps track 

## Why it works



Data Oriented Programming is not necessarily harder, but it's something different that needs to be learnt and familiarized with. Developers that are not used to this approach might take some time to adjust, we encourage that you explore and play with examples to get a better feel.


When the processor needs to fetch data from memory , it fetches it in chunks. These chunks are often larger than what is n


## From a lax DOP to a more purist approach

- no lambdas in components
- no functions in comopnents
- no state or functions in entityes
- no state in systems


## Code samples