---
date: 2022-09-01
title: Creating and exporting emotes
description: Steps for creating and exporting an avatar emote.
categories:
  - emotes
type: Document
---


This documentation will cover the file specifications, the basics of animation in Blender, the proper way to export an Emote, and how to import one into the Builder.

### Animation Specs Chart

| Frame Rate | 30 fps |
| --- | --- |
| Max Length | 10 seconds (300 frames) |
| Animations per File | 1 |
| Export Format | .glb |
| Sampling Rate | 2 or 3 (if needed) |
| Max File Size | 1 MB |

You can find a more detailed explanation of the animation specifications [**below**](https://www.notion.so/Creating-and-Exporting-Emotes-5e962e5d54a24bcb9b906748007eb4cc).

### Before Starting

**Frame Rate**

Before getting started, it’s important to check the frame rate. Decentraland’s animations must have a frame rate of **30 fps**. The rig file provided probably has that set up, but since Blender’s default value is 24 fps, it is best to double check before starting (a wrong frame rate will affect the speed of the animation). That option can be found in *Output Properties* (the printer icon) under *Format*, as shown below:

![Make sure the framerate is set to 30 fps before starting.](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e27608ec-9c89-4d67-ac7f-0cc1da5b61f9/framerate.png)

Make sure the framerate is set to 30 fps before starting.

**Pose Mode**

In Blender, a rig can be viewed in three different modes: *Object Mode*, *Edit Mode,* and *Pose Mode*. Animations can only be done in ***Pose Mode*** (in that mode, controls have colors). With the rig selected, you’ll find that option in a dropdown menu, at the top right.

![Changing to Pose Mode.](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3c57d24b-4f75-4945-bf0b-a5acd2f8305b/pode_mode.gif)

Changing to Pose Mode.

**Workspace Windows**

In the rig file, other than the two windows for the viewport (front and side view), there are three more at the bottom: a ***Graph Editor***, ***a Dope Sheet***, and a ***Timeline***. 

- ***Graph Editor***: In this editor, it is possible to edit the animation curves of each transform property of the selected controls. Those curves show how the interpolation is being calculated and they can be edited to achieve the wanted effect in the animation. Both in here and in the dope sheet the ***Only Show Selected*** tool is toggled, which means it’ll only include channels related to the selected control. This can be turned on and off by simply clicking on the arrow icon.
- ***Dope Sheet***: Here you can edit the keyframes. This is also where you can create new animations or go through the multiple ones created. Keep in mind that in order to have access to the animation, the ***Action Editor*** must be selected. This option is right next to the *Dope Sheet* icon, in a dropdown menu.
- ***Timeline***: This is where the timeline and playback controls are found. In here, the ***Auto Keying*** is on, which means that every time a control is manipulated it automatically creates a keyframe. You can always disable that function by clicking on the dot next to the playback controls.

With this workspace, you have everything needed to start animating!

 

![These are the bottom windows. The top one is in the ***Graph Editor,*** the middle one in the ***Dope Sheet,*** and the bottom one is the ***Timeline.*** The top red arrow shows the ***Only Show Selected*** tool and the bottom one shows the ***Auto Keying***.](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2227d4f3-0a4d-4cef-bb63-c9329c5b8bdb/workspace.png)

These are the bottom windows. The top one is in the ***Graph Editor,*** the middle one in the ***Dope Sheet,*** and the bottom one is the ***Timeline.*** The top red arrow shows the ***Only Show Selected*** tool and the bottom one shows the ***Auto Keying***.

### **Hint!**

<aside>
💡 **Since Blender is highly customizable, this is also a good time to set up the layout that best suits you, adding, adjusting, or removing windows. Each animator has their own preferences, so feel free to edit the layout however you want!**

</aside>

### Getting Started

**Starting Pose**

In the rig file provided, there’s already an action, the ***Starting_Pose***. Considering that all avatar actions start from the idle pose, **we really encourage starting your animation from that pose and also using it again in the last frame**. This will make for a better transition from Idle to Emote and a more fluid animation. 

**Ground Reference and Animation Area** 

In order to avoid ground penetration during animation, a plane has been added to the file as a ground reference. Along with the animation area reference, it also helps identify the area that can be used for the animation.

![Ground and animation area reference.](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9e9b5f2d-8686-4adf-92a6-67575a4b976d/ground_reference.png)

Ground and animation area reference.

**Creating an Animation**

To create a new animation, simply click on ***Create A New Action*** button (this will duplicate the current animation with all the keyframes) or press the X next to it, the ***Unlink Action,*** and press the ***New*** button. This way you’ll start with no keyframes at all. Make sure to always toggle ***Fake User*** (the shield icon) so your animation is saved!

![Create a new animation by duplicating the existing one or by clicking on ***Unlink Action*** and then ***New***.](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e5ad7960-ec6b-4ae3-83b1-b42f493cf30e/new_anim.gif)

Create a new animation by duplicating the existing one or by clicking on ***Unlink Action*** and then ***New***.

**Browsing and Deleting Animations**

In Blender, you can have multiple animation tracks in the same file. It is possible to browse them by clicking on the ***Browse Action*** dropdown menu. All animation with and F (***Fake User***) will be saved. To delete an animation, press ***Shift*** on the keyboard and click on the ***X***. After doing that, the animation will show a 0 next to it, which means that it will be deleted the next time you close Blender or reopen the file.

![Browsing animations: The ones with an F will be saved, and the ones with 0 will be deleted.](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0f768fd8-6e6d-409b-ae6e-04230ba938bc/anim_list.gif)

Browsing animations: The ones with an F will be saved, and the ones with 0 will be deleted.

### **Hint!**

<aside>
💡 **Do not always edit the same animation track. Before making major changes, just duplicate the animation. That way you have a back up version in case you regret deleting or changing something. This is also a nice way to keep track of the progress made so far!**

</aside>

**Naming**

**An animation’s name should start with a capital letter and if the name is more than one word long, the words should be separated by _.** Do not use spaces or special characters. Here are some examples of naming: 

- Snowfall
- Rainbow_Dance
- Throw_Money
- Talk_To_Hand

### The Animation Specifications

**Length**

The max length of an animation is **10 seconds** or **300  frames**. Remember to keyframe every control’s properties on the first and last frames.

### Attention!

<aside>
⚠️ **Channels with visibility turned off in the Graph Editor won’t be keyframed, deleted, or even shown in the Action Editor. Unless it was intentionally done that way, pay extra attention to the visibility.**

</aside>

![Make channels visible before keyframing!](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/482e7cb3-14d0-46e4-b1ec-c3cd1c673681/visibility_graph_editor.gif)

Make channels visible before keyframing!

**Number of Animations**

The exported file can only have **one animation**. If animations were duplicated during the process, make sure you delete all of them before exporting. Keep only the final version. Sequence emotes that need many animations to work (action start, action loop, and action end) are not supported right now.

**Format**

Animations should be exported as .**GLB**. The file can only contain the deforming skeleton and the animation. **Mesh, controls, and any other object should not be exported**. More details on how to export can be found [**below**](https://www.notion.so/Creating-and-Exporting-Emotes-5e962e5d54a24bcb9b906748007eb4cc).

**Sampling**

Since constraints can’t be exported, the only way to export the animation clip is by baking it, which means that all the deforming bones’ positions, rotation, and scale will be keyframed in every single frame of the animation. If the clip is too long, like up to 300 frames, it’ll have 300 keyframes after exporting and the more keyframes it has, the heavier the file gets. 

Sampling is a good way to optimize the animation. The sampling rate will define how often a keyframe will be baked in the animation. For example, if the sampling rate is set to 2, that means a keyframe will be created at every two frames. A sampling rate of 3 will bake a keyframe every three frames and so on. The higher the sampling rate, the lighter the file. 

The drawback, however, is that the animation will start getting less and less fluid since it loses some important keyframes (they are distributed through the animation in an uneven way). It’s also important to notice that **sampling is NOT dividing the number of the animation’s frames by the sampling rate**. 

Usually, a **sampling rate of 2 or 3** will do the trick. Those numbers can optimize the animation without compromising the quality. 

### Hint!

<aside>
💡 **If the number of frames of the animation can be divided by the sampling rate, that’s a good thing! It means that the final frame will be baked, preserving the transition from end to start of the animation.**

</aside>

**File Size**

The max file size is **1 MB**. If the file is over that after exporting, try checking if the mesh wasn’t exported by accident or if the animation isn’t over 10 seconds. If it is still over 1 MB, try experimenting with the Sampling Rate, as higher values will improve the optimization.

### Exporting

Since we only want the armature and the animation to be exported, turn off the mesh visibility and any object other than the armature before exporting, as shown below:

![Turn off the mesh visibility before exporting!](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4e7cd82e-ce5d-497a-ab82-aa46d0011e03/mesh_visibility.gif)

Turn off the mesh visibility before exporting!

To export, go to *File* > *Export* > *glTF2.0 (.glb, .gltf)*

![export.gif](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/26b81cf5-3678-4de6-867f-79b94eccf8a0/export.gif)

For the export settings, expand ***Include*** and in ***Limit to*** toggle ***Visible Objects***. For *Transform* and *Geometry*, leave it as it is.

![export1.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2222b50d-13d4-4e2f-a672-557055e46d57/export1.png)

![visible_objects2.gif](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/eae83302-0cdf-42b0-928b-4ce79e101452/visible_objects2.gif)

Next, expand the ***Animation*** tab, expand the second ***Animation*** tab and toggle ***Export Deformation Bones Only.*** This is also where the ***Sampling Rate*** is defined. 

![export2.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3aca8f9b-eb55-4021-8580-3418e9f0417d/export2.png)

![animation_export.gif](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c3701113-4f03-49e5-b5e4-2fc3680a156e/animation_export.gif)

That’s it for exporting the animation!

### Import into the Builder

When all the files are ready to go you can import them to the builder to test the animation! Go to [https://builder.decentraland.org/](https://builder.decentraland.org/) and import the item you want to test. 

![Screen Shot 2022-08-04 at 12.39.58.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4b711bee-a3f9-4984-b37f-9366d462e076/Screen_Shot_2022-08-04_at_12.39.58.png)

Once it is imported, click on this eye icon to visualize it! 

![visulize.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/800809df-dfb5-4584-aedd-75be377d15c9/visulize.png)

You can also add a description and tags to the emote. **The overrides and hides section must be empty.**