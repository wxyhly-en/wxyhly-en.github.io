---
title: Minecraft4D Tutorial
tags:
  - 4D
  - Minecraft
  - 4dViewer
date: 2020-03-14 11:23:05
excerpt: Minecraft4D is a true 4D (hypercube-based) 4-voxel sandbox game. (If you include time, it’s actually in 5D spacetime!) Currently, the game only supports single-player creative mode.
index_img: /img/minecraft02.jpg
---

(This article is only a tutorial, so it’s not included in the “4D World” series.)
Minecraft4D is a true four-dimensional (hypercube-based) voxel sandbox game. (If you include time, it’s actually in 5D spacetime!) Currently, the game only supports single-player creative mode. Minecraft4D is written in Javascript and WebGL, so you can play just by clicking the link below. (Best used with Google Chrome; compatibility with other browsers is not guaranteed, and it requires a physical keyboard.)
### [Minecraft4D](/4dViewer/minecraft4d/) 

After entering, wait for the textures and terrain to fully load, and then you’ll see the view. You’ll notice that the screen is split into two nearly identical halves — these are the images for your left and right eyes, meant to create **stereoscopic vision**. Since I don’t know how to make VR, you’ll need to use cross-eyed viewing to perceive the 3D image. If you can’t do that, then rotate the view using the arrow keys to get a sense of depth. [This article](/archives/eye3d/) has a detailed explanation of how to view stereoscopic images. The three small corner windows are stereo vision cross-sections, separated for clearer observation.
<!--more-->

<a href="javascript:void(0)" target="_self" onclick="$('#3dview').toggle()">Q: What is stereoscopic vision? How do I view it? (Click to expand/collapse)</a>

<span id="3dview" style="display:none">Our eyes are positioned slightly apart, and the brain uses this visual disparity to calculate depth. If you can't cross your eyes, place your finger in the center of the screen and stare at your fingertip while slowly moving it toward your eyes. Keep your line of sight horizontal, and use peripheral vision to observe the two images on screen start to misalign and blur. When the two images overlap perfectly, you’ll be amazed to find the image pops out in 3D! That floating point will roughly match your fingertip’s position. That’s when you’ve achieved stereoscopic viewing.</span>

4D beings can directly perceive solid 3D structures, so the solid cube view here is made transparent to allow internal observation. If you’re still confused, check out the following articles:
- [4D World (IV): Vision of 2D Beings](/archives/eye2d/)
- [4D World (V): 4D Vision and Orientation](/archives/eye3d/)

## Direction Controls

Let’s learn about directions in four-dimensional space. The image below shows the name of each direction. You can try moving with the keys `W` `S` `A` `D` `Q` `E`. Note that forward and backward directions are not shown in the image — they are perpendicular to the 3D view and can’t be drawn. Moving along the ana and kata directions may feel odd at first, since our 3D brains haven’t developed an intuitive sense of the fourth dimension, but you’ll get used to it soon.
![Note that the forward/backward direction is perpendicular to the 3D view and cannot be shown.](/img/minecraft01.jpg)

Because the transparent cube view overlaps many colors, it can be hard to see clearly. So, as shown below, some cross-sections are extracted and color-coded:
($x$: left-right; $y$: up-down; $z$: ana-kata; $w$: forward-backward)
![](/img/eye3d005.jpg)
These stereo vision cross-sections provide depth along the $w$ axis (perpendicular to the 3D view) as a compromise to simulate 4D perception.

After clicking the screen, the mouse will be hidden and mouse movement will rotate the view; press `Esc` to exit this mode — similar to vanilla Minecraft. Mouse movement left/right rotates the player's head left/right; unlike vanilla Minecraft, up/down mouse movement rotates in the ana-kata direction, so the player always faces forward. To look up/down, scroll the mouse wheel or press `I` / `K`.

## World Controls

There are two types of worlds: normal and superflat. The default is normal mode. Each time you enter, a random seed is used. To load a specific seed, append `?seedNumber` to the URL (the seed must be a positive integer). Use `/seed` to view the current seed.

To enter superflat mode, append `?flat` to the URL. This mode generates no additional structures.

Example seeds:
- Superflat: <a href="/4dViewer/minecraft4d/?flat" target="minecraft4d_flat">?flat</a>
- At x=-510 z=-230 w=-440: pyramid, stone beach, river, village (TP manually): <a href="/4dViewer/minecraft4d/?873556" target="minecraft4d_873556">?873556</a>
- At x=34 z=72 w=64: riverside village: <a href="/4dViewer/minecraft4d/?962259" target="minecraft4d_962259">?962259</a>
- At x=160 z=-61 w=284: observatory in village: <a href="/4dViewer/minecraft4d/?661280" target="minecraft4d_661280">?661280</a>

To save a world after editing, press `/` to open the command bar and enter `/save` (saves as a `.mc4a` file locally).  
To load a save, enter any world (seed/type doesn’t matter) and run `/open` to load a local `.mc4a` save.

Minecraft4D has a time system. If it's too dark at night (lighting isn’t supported yet), use `/skipnight` or `/skip` to skip the night. See the [Command List](#sudo) for more.

## Block Controls

Like vanilla Minecraft, Minecraft4D uses right-click to place, left-click to destroy, and middle-click to select blocks. Note: the center crosshair is in 3D space, so you must align it correctly to select a block.  
Use `N` and `M` to switch held blocks. Full block list is [here](#touhh).

Since 4D space is vastly larger than 3D, building by hand requires far more blocks. So Minecraft4D includes WorldEdit-like features. Use `/w` to toggle WorldEdit mode. Only hyperrectangular selections are currently supported. Use left/right click to select two diagonal corners to define a hyperrectangular region. You can also use `/pos1` and `/pos2` for precise coordinates. Use `/sel` to view selection info. Use `/set <block>` to fill the selection. (Block names: [see here](#touhh)). More commands: [see here](#sudo).

## Shortcut Keys

### Shared with other 4DViewer apps

#### Rendering & Display

|Key|Action|
|--|--|
|`=`|Increase 3D view layers|
|`-`|Decrease 3D view layers|
|`]`|Increase 3D pixel opacity|
|`[`|Decrease 3D pixel opacity|
|`;`|Shrink cross-section|
|`'`|Enlarge cross-section|
|`,`|Darken background|
|`.`|Brighten background|
|`9`|Reduce FOV|
|`0`|Increase FOV|
|`C`|Wireframe mode|
|`Alt+[`|Wireframe mode|
|`Alt+,`|Lower resolution|
|`Alt+.`|Increase resolution|
|`Alt+1`|Default display config|
|`Alt+2`|Optimized for 3D view (hide cross-section, increase layers, lower res)|
|`Alt+3`|Optimized for cross-section (no 3D overlay, zoom in)|
|Arrow keys|Rotate 3D view|

#### Player / Camera

|Key|Action|
|--|--|
|`W`|Forward|
|`S`|Backward|
|`A`|Left|
|`D`|Right|
|`Q`|Kata|
|`E`|Ana|
|`Shift`|Descend (fly mode)|
|`Space`|Jump / Ascend|
|`I`|Look up|
|`K`|Look down|
|`J`|Look left|
|`L`|Look right|
|`U`|Look kata|
|`O`|Look ana|
|`Z`|Rotate (left/ana)|
|`X`|Rotate (right/kata)|

### Minecraft4D-specific

|Key|Action|
|--|--|
|`M`|Previous block|
|`N`|Next block|
|`/`|Open command bar|
|`P`|Pause / Resume|

<a name="sudo"></a>

## Command List
|Command|Format|Description|
|----|----|---|
|/tp|tp <x> <y> <z> <w>|Teleport the player to the specified coordinates (prefix with ~ for relative position)|
|/seed|seed|Display the current world seed|
|/fly|fly|Toggle flying mode|
|/speed|speed <speed>|Set the player’s movement speed|
|/dayspeed|dayspeed <speed>|Set the day-night cycle speed|
|/skipnight or /skip|skipnight \| skip|Quickly skip the night (can only be used at night)|
|/save|save [clipboard \| clip \| sel \| selection]|Without arguments: save the world to local file; with argument (e.g., `save clipboard`) saves clipboard or current selection as `.schematic4d` file|
|/open|open|Open a previously saved local world file|
|/load|load [-c \| clip \| clipboard]|Load a local `.schematic4d` file. By default, it’s placed at player’s location. Use `-clipboard` to load into clipboard without placing|
|/loadmacro or /macro|loadmacro \| macro [prev]|Load a local [macro command](#macro) file. Use `macro prev` to repeat the last loaded macro|
|/regen|regen <me \| all>|`regen me`: regenerate current chunk using the same seed; `regen all`: reset the entire world using the current seed|
|/chunks|chunks|Display number of loaded and modified chunks (modified chunks won’t be unloaded)|
|/wand or /w|wand \| w|Toggle WorldEdit selection tool mode (use left/right click to select two diagonal corners of a hyperrectangle)|
|/pos1|pos1 <x> <y> <z> <w>|Set the first corner of the WorldEdit selection (prefix with ~ for relative coordinates)|
|/pos2|pos2 <x> <y> <z> <w>|Set the second corner of the WorldEdit selection (prefix with ~ for relative coordinates)|
|/sel|sel|View information about the current selection|
|/set|set <id>|Fill the selected region with the specified block ID|
|/hset|hset <id>|Fill the outer shell of the selection with the specified block|
|/wall|wall <id>|Fill the vertical sides (excluding top/bottom) with the specified block|
|/hwall|hwall <id>|Fill the vertical edges (2D ridges) of the selection with the specified block|
|/copy|copy|Copy the current selection to the clipboard (relative position preserved)|
|/paste|paste|Paste clipboard content into the world (placement depends on current player position)|
|/flip|flip [dir]|Flip the clipboard content in the specified direction (`x`, `y`, `z`, or `w`). Defaults to player-facing direction|
|/stack|stack <num> [dir]|Repeat the selection `num` times in the specified direction (e.g., `x+`, `z-`, `f` for forward, `u` for up, `d` for down). Defaults to player-facing|
|/move|move <num> [dir]|Move the selection content by `num` units in the specified direction|
|/shift|shift <num> [dir]|Move the selection box (not the content) by `num` units in the specified direction|
|/expand|expand <num> [dir] or expand <num> <num> [dir]|Expand the selection in the specified direction(s); if two numbers are given, expand in both opposite directions|
|/glome|glome <id> <radius> [<x> <y> <z> <w>]|Generate a solid 4D sphere (glome) with the specified block ID and radius. Defaults to player’s position if coordinates are omitted|
|/hglome|hglome <id> <radius> [<x> <y> <z> <w>]|Generate a hollow 4D sphere (glome) with the specified block ID and radius. Defaults to player’s position if omitted|
|/spherinder|spherinder <id> <radius> <length> [<direction> [<x> <y> <z> <w>]]|Generate a spherinder (3D sphere extruded along 4th axis) with specified radius and height. `direction` sets axis; default is player-facing|
|/hspherinder|hspherinder <id> <radius> <length> [<direction> [<x> <y> <z> <w>]]|Generate a hollow spherinder with given specs|
|/duocylinder|duocylinder <id> <radius1> <radius2> [<direction> [<x> <y> <z> <w>]]|Generate a duocylinder (product of two circles) with specified radii and block type. `direction` defines which 2D plane to use (e.g., `xy`, `yz`). Defaults to left-right & up-down plane at player’s location|
|/tiger|tiger <id> <radius1> <radius2> <radius3> [<direction> [<x> <y> <z> <w>]]|Generate a tiger (3-circle toroidal figure) with given radii and block type. `direction` defines one of the circle planes. Defaults to left-right & up-down at player’s location|

<a name="macro"></a>

## Macros

Because block count grows with the fourth power of size in 4D, manual construction is very hard. It's recommended to use WorldEdit-style commands. You can write macros (a list of commands in a file) and load them with `/macro`. Use `def <name> value` to define constants, and define reusable code blocks with `fn <name>:` and `endfn`. Macros support almost all [commands](#sudo), except those involving file I/O: `\save`, `\open`, `\load`, `\macro`.

Example: A small village of 26 houses.
<iframe src="https://wxyhly.github.io/4dViewer/minecraft4d/macro.txt" style="background-color: rgb(255,255,255,0.7)"></iframe>

Result:
![](/img/minecraft02.jpg)

<a name="touhh"></a>

## Block List

|Numeral ID|Name|Block ID|
|--|--|--|
|0|Air|air|
|1|Stone|stone|
|2|Grass Block|grass|
|3|Dirt|dirt|
|4|Oak Log|oak_log|
|5|Leaves|leaves|
|6|Brick|brick|
|7|Sand|sand|
|8|Water|water|
|9|Smooth Stone|smooth_stone|
|10|Double Slab|stone_slabs|
|11|Stone Brick|stone_brick|
|12|Planks|planks|
|13|White Concrete|white_concrete|
|14|Red Concrete|red_concrete|
|15|Yellow Concrete|yellow_concrete|
|16|Green Concrete|green_concrete|
|17|Cyan Concrete|cyan_concrete|
|18|Blue Concrete|blue_concrete|
|19|Purple Concrete|purple_concrete|
|20|Gray Concrete|gray_concrete|
|21|Black Concrete|black_concrete|
|22|Cactus|cactus|
|23|Creeper Head|creeper_head|
|24|Enderman Head|enderman_head|
|25|Player Head|steve_head|
|30|Glass|glass|

## Scenery List

(Images to be added)
- Forest
![](/img/minecraft06.jpg)
- Desert
![](/img/minecraft04.jpg)
- Desert Pyramid
- River
![](/img/minecraft03.jpg)
- Village
- Desert Village
![](/img/minecraft05.jpg)
- Observatory
- Desert Well
- Swamp
- Road
![](/img/minecraft07.jpg)

<!-- A purely geometric method is to draw perpendiculars from the centers of adjacent cells to the center of their shared face; these form a triangle. Then calculate side lengths and use the law of cosines to find the dihedral angle. Use the fact that each face of an octahedron is an equilateral triangle, and the 24-cell has a hollow cube structure made of octahedra faces — no algebra required. -->

## How It Works

A brief explanation of Minecraft4D’s internals. Like Minecraft, it stores the world in chunks. The 4D world generator is similar to 3D. Rendering uses ray tracing to compute intersections, implementing only basic ambient occlusion and solar shadows. Texturing is interesting: 4D blocks require 3D textures. Inspired by Minecraft’s 2D texture files, I manually drew 3D textures layer by layer — 8 layers total at 8×8×8 resolution. The textures are stored in regular 2D `.png` format, where each cube texture is drawn in 8 slices.
![Some 3D textures in-game](/img/minecraft08.jpg)
