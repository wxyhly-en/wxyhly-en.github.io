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
Minecraft4D is a true four-dimensional (hypercube-based) voxel sandbox game. (If you include time, it’s actually in 5D spacetime!) Currently, the game only supports single-player creative mode. Minecraft4D is written in Javascript and WebGL, so you can play just by clicking the link below. (Best used with Google Chrome; compatibility with other browsers is not guaranteed, and it requires a keyboard.)
### [Minecraft4D](https://wxyhly.github.io/4dViewer/minecraft4d/) 

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

There are two types of worlds: normal (default) and superflat. Each time you enter, a random seed is used. Use `/seed` to view the current seed.

To enter superflat mode, append `?flat` to the URL. This mode generates no additional structures.

Example seeds:
- Superflat: <a href="https://wxyhly.github.io/4dViewer/minecraft4d/?flat" target="minecraft4d_flat">?flat</a>
- At x=-510 z=-230 w=-440: pyramid, stone beach, river, village (TP manually): <a href="https://wxyhly.github.io/4dViewer/minecraft4d/?873556" target="minecraft4d_873556">?873556</a>
- At x=34 z=72 w=64: riverside village: <a href="https://wxyhly.github.io/4dViewer/minecraft4d/?962259" target="minecraft4d_962259">?962259</a>
- At x=160 z=-61 w=284: observatory in village: <a href="https://wxyhly.github.io/4dViewer/minecraft4d/?661280" target="minecraft4d_661280">?661280</a>

To save a world after editing, press `/` to open the command bar and enter `/save` (saves as a `.mc4a` file locally).  
To load a save, enter any world (seed/type doesn’t matter) and run `/open` to load a local `.mc4a` save.

Minecraft4D has a time system. If it's too dark at night (lighting isn’t supported yet), use `/skipnight` or `/skip` to skip the night. See the [Command List](#sudo) for more.

## Block Controls

Like vanilla Minecraft, Minecraft4D uses right-click to place, left-click to destroy, and middle-click to select blocks. Note: the center crosshair is in 3D space, so you must align it correctly to select a block.  
Use `N` and `M` to switch held blocks. Full block list is [here](#touhh).

Since 4D space is vastly larger than 3D, building by hand requires far more blocks. So Minecraft4D includes WorldEdit-like features. Use `/w` to toggle wand mode. Only tetracuboid (tesseractoid) selections are currently supported. Use left/right click to select two diagonal corners to define a tetracuboid region. You can also use `/pos1` and `/pos2` for precise coordinates. Use `/sel` to view selection info. Use `/set <block>` to fill the selection. (Block names: [see here](#touhh)). More commands: [see here](#sudo).

## Shortcut Keys

### Shared with other 4DViewer apps

#### Rendering & Display

|Key|Action|
|--|--|
|`=`|Increase 3D view layers|
|`-`|Decrease 3D view layers|
|`]`|Increase 3D voxel opacity|
|`[`|Decrease 3D voxel opacity|
|`;`|Shrink cross-section|
|`'`|Enlarge cross-section|
|`,`|Darken background|
|`.`|Brighten background|
|`9`|Reduce FOV|
|`0`|Increase FOV|
|`C`|Wireframe mode|
|`Alt+[`|Wireframe mode|
|`Alt+,`|Decrease resolution|
|`Alt+.`|Increase resolution|
|`Alt+1`|Use default rendering preset|
|`Alt+2`|Use preset for 3D retina (No cross-section, more layers and low resolution)|
|`Alt+3`|Use preset for cross-sections (No 3D voxel retina, big cross-section)|
|Arrow keys|Rotate 3D view|

#### Player / Camera

|Key|Action|
|--|--|
|`W`|Move Forward|
|`S`|Move Backward|
|`A`|Move Left|
|`D`|Move Right|
|`Q`|Move Kata|
|`E`|Move Ana|
|`Shift`|Descend (fly mode)|
|`Space`|Jump / Ascend|
|`I`|Look up|
|`K`|Look down|
|`J`|Look left|
|`L`|Look right|
|`U`|Look kata|
|`O`|Look ana|
|`Z`|Rotate (CW Spin)|
|`X`|Rotate (CCW Spin)|

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
|/tp|tp &lt;x&gt; &lt;y&gt; &lt;z&gt; &lt;w&gt;|Teleport player (use prefix ~ to indicate relative position)|
|/seed|seed|Show the seed of the current world|
|/fly|fly|Toggle fly mode|
|/speed|speed &lt;speed&gt;|Set speed of player|
|/dayspeed|dayspeed &lt;speed&gt;|Set day-night cycle speed|
|/skipnight or /skip|skipnight&vert; skip|Skip the night (can only be used at night)|
|/save|save [clipboard&vert; clip&vert; sel&vert; selection]|Save the world to local file without parameter, and use parameter to save the specified contents (e.g. `save clipboard`) into a schematic4d file|
|/open|open|Open local world file|
|/load|load [-c&vert; clip&vert; clipboard]|Load local schematic4d file. By default the structure will be loaded immediately relative to player. Use option `-clipboard` to load into clipboard rather than in the world|
|/loadmacro or /macro|loadmacro&vert; \macro [prev]|Load local [Macro](#macro) file, use `macro prev` to execute the last macro|
|/regen|regen &lt;me&vert;all&gt;|`regen me`: Regenerate the current chunk according to the seed; `regen all`: Regenerate the world completely|
|/chunks|chunks|Show total loaded chunks and modified chunks (modified chunks cannot be unloaded)|
|/wand or /w|wand&vert; w|Toggle WorldEdit wand mode|
|/pos1|pos1 &lt;x&gt; &lt;y&gt; &lt;z&gt; &lt;w&gt;|Set first vertex of tetracuboid selection area (use prefix ~ to indicate relative position)|
|/pos2|pos2 &lt;x&gt; &lt;y&gt; &lt;z&gt; &lt;w&gt;|Set second diagonal vertex of tetracuboid selection area (use prefix ~ to indicate relative position)|
|/sel|sel|Check current selection infomation|
|/set|set &lt;id&gt;|fill the selection with a specified block type by block id|
|/hset|hset &lt;id&gt;|fill the surface of the selection with a specified block type by block id|
|/wall|wall &lt;id&gt;|fill the surface of the selection except ceil and floor with a specified block type by block id|
|/hwall|hwall &lt;id&gt;|fill the vertical 2D frames of the selection with a specified block type by block id|
|/copy|copy|Copy the current selection into clipboard. Your relative position will be stored like in original WordEdit|
|/paste|paste|Paste the clipboard to the world, the position is relative to the player|
|/flip|flip [dir]|Flip the clipboard toward direction `dir` (e.g. `x`, `y`, `z` or `w`). The default direction is front|
|/stack|stack &lt;num&gt; [dir]|Stack the current selection `num` times toward direction `dir`(e.g. `x+`, `z-`, `f` for front, `u` for up, `d` for down). The default direction is front|
|/move|move &lt;num&gt; [dir]|Move the current selection `num` blocks toward direction `dir`(e.g. `x+`, `z-`, `f` for front, `u` for up, `d` for down). The default direction is front|
|/shift|shift &lt;num&gt; [dir]|Move only the current selection (without blocks) `num` blocks toward direction `dir`(e.g. `x+`, `z-`, `f` for front, `u` for up, `d` for down). The default direction is front|
|/expand|expand &lt;num&gt; [dir] or expand &lt;num&gt; &lt;num&gt; [dir]|Expand the current selection  `num` blocks toward direction `dir`(e.g. `x+`, `z-`, `f` for front, `u` for up, `d` for down). If two `num`s are given, selection will be expand toward and also opposide the player|
|/glome|glome &lt;id&gt; &lt;radius&gt; [&lt;x&gt; &lt;y&gt; &lt;z&gt; &lt;w&gt;]|Generate a glome of radius `radius` with material `id`. The center is at the position of the player if coordinates are not given.|
|/hglome|hglome &lt;id&gt; &lt;radius&gt; [&lt;x&gt; &lt;y&gt; &lt;z&gt; &lt;w&gt;]|Generate a hollow glome of radius `radius` with material `id`. The center is at the position of the player if coordinates are not given.|
|/spherinder|spherinder &lt;id&gt; &lt;radius&gt; &lt;length&gt; [&lt;direction&gt; [&lt;x&gt; &lt;y&gt; &lt;z&gt; &lt;w&gt;]]|Generate a spherinder of radius `radius` and height `length` with material `id`. The orientation of the height is given by `dir`. The center is at the position of the player if coordinates are not given. |
|/hspherinder|hspherinder &lt;id&gt; &lt;radius&gt; &lt;length&gt; [&lt;direction&gt; [&lt;x&gt; &lt;y&gt; &lt;z&gt; &lt;w&gt;]]|Generate a hollow spherinder of radius `radius` and height `length` with material `id`. The orientation of the height is given by `dir`. The center is at the position of the player if coordinates are not given. |
|/duocylinder|duocylinder &lt;id&gt; <radius1> <radius2> [&lt;direction&gt; [&lt;x&gt; &lt;y&gt; &lt;z&gt; &lt;w&gt;]]|Generate a duocylinder of radii `radius1` and `radius2` with material `id`. The orientation of two perpendicular circles is given by `dir`(e.g. `xy` or `xz`). The center is at the position of the player if coordinates are not given.|
|/tiger|tiger &lt;id&gt; <radius1> <radius2> <radius3> [&lt;direction&gt; [&lt;x&gt; &lt;y&gt; &lt;z&gt; &lt;w&gt;]]|Generate a tiger of radii `radius1`, `radius2` and `radius3` with material `id`. The orientation of two perpendicular circles is given by `dir`(e.g. `xy` or `xz`). The center is at the position of the player if coordinates are not given.|

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

A brief explanation of Minecraft4D’s internals. Like Minecraft, it stores the world in chunks. The 4D world generator is similar to 3D. Rendering uses ray tracing to compute intersections, implementing only basic ambient occlusion and solar shadows. Texturing is interesting: 4D blocks require 3D textures. Inspired by Minecraft’s 2D texture files, I manually drew 3D textures layer by layer — 8 layers total at 8×8×8 resolution. The textures are stored in normal 2D `.png` format, where each cubic texture is drawn in 8 slices.
![Some 3D textures in-game](/img/minecraft08.jpg)
