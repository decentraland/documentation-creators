---
date: 2022-06-29
title: Debug in Production
description: How you can debug your scene that is running inside Decentraland
categories:
  - development-guide
type: Document
---

When running a scene that's already deployed to land in Decentraland, there are a number of things you can try out to debug it.


## The test server 

You can deploy scenes to a test server as a staging environment before production

no asset bundles

players are not likely to discover it, but it's not private

## Access debug information


Debug information in the console is hidden by default, but there are some simple ways to access it.

### See logs

&DEBUG_SCENE_LOG


 (not available in desktop)

### See debug UI

 (not available in desktop)


&SCENE_DEBUG_PANEL



## 3d models

Missing models

- models outside scene

### See Asset bundles

- no asset bundles

- brach with asset bundles in green

&DISABLE_ASSET_BUNDLES


https://play.decentraland.zone/?renderer-branch=feat%2Fab-view

## Performance

&LOS=1  (setear line of sight)
&MAX_VISIBLE_PEERS=100

### See FPS panel

Type `/showfps` into the chat window, and this will display the FPS panel, to help you assess the performance of the scene more objectively. The FPS will vary depending on the machine running it, but 

- isolate from neighbors, set LOD => 1
hack LOD => 0 via URL

- check with multiple players





