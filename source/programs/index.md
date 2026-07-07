title: Programs - JS applet
date: 2019-03-30 20:30:29
layout: page
---
<div class="markdown-body">

## [Chem4D (4D Chemistry)](https://wxyhly.github.io/Chem4D/)
A conceptual viewer for common substance structures in four-dimensional chemistry, supporting interaction with the Tesserxel demo library to generate 4D molecular models. See [“4D World (XI): The Periodic Table”](/archives/elem4d/) for the element system.
- [Link to the Periodic Table Page](https://wxyhly.github.io/Chem4D/periodic/)

![4D molecules from the Tesserxel demo library](/img/chemie006.jpg?size=500x)
## [Deductrium](https://wxyhly.github.io/deductrium/)
A browser-based game combining mathematical formal systems and hyperbolic space using TypeScript. Includes propositional logic, first-order logic, Peano axioms, ZFC set theory, and some ordinal and type theory content. See: 1. [Game Introduction](https://github.com/wxyhly/deductrium/blob/main/readme_en.md); 2. [Intro to Set Theory](/archives/1stlogic/); 3. [Intro to Type Theory](/archives/hottese/).

If you find the game too difficult, a “Creative Mode” is available (link provided in related blog post), where all axiom systems are unlocked and many theorems are preloaded—but hyperbolic space and ordinals are disabled in this mode.  
Here’s a survival mode save file for quick access to Type Theory: copy the following code.

<textarea id="progress-hott" style="width:100%" disabled>00Uhl1"..2e.,)`o.1,o7U,aa[.i{([9`][eU80"`]",{`f2e5"UU3U(dU`Ur0j,gra`U.`tp2[g#9,1t6=p`.U,.1p0)i0`:r=Udg,`:`0`8]c"-,.cU]`1`fljyll,"`,0.zm="]4-9,imlt4oj-5"[]5[`1"`ma},tb,e.".o[3,7,}89`p,130]6ee.`"-(-w.j[[7`-lr]2"99.L,`,U2]jg,..#3)3a`q</textarea>
<script>
    const textarea = document.getElementById("progress-hott");
    textarea.onclick = window.onload = function() {
        textarea.select(); // Select all content
    };
</script>

## [OrdMap (Ordinal Map)](https://wxyhly.github.io/ordmap/)
[[Source Code](https://github.com/wxyhly/ordmap)]  
Ordinals are larger than all natural numbers but still smaller than absolute infinity. See [“Introduction to Googology”](/archives/ggg-ord/) for more. The map goes up to EBO, supports BOCF/MOCF and Veblen notation.  
![Ordinal map near the origin](/img/ord006.png)  
On mobile, drag the empty space to move the map, tap +/− to zoom. Hold +/− and swipe right to zoom faster. On PC, drag with the mouse, scroll to zoom, press T/G to change zoom speed, and W/S to adjust iteration depth.

## [Tesserxel](https://wxyhly.github.io/tesserxel/examples/#)
[[Source Code](https://github.com/wxyhly/tesserxel)]  
A new WebGPU-based 4D engine for algebra, modeling, rendering, and physics. [Intro series here](/categories/Tesserxel-Series/), detailed tutorials and guides coming soon.

## [4D Viewer](https://wxyhly.github.io/4dViewer/)  
[[Source Code](https://github.com/wxyhly/4dViewer)]  
The precursor to Tesserxel, this JS library (like Three.js) renders 4D spaces with stereoscopic vision to help humans imagine 4D perception. Includes demos like hypercubes, polytopes, and puzzle levels.

[Usage guide here](/archives/eye3d/)

### 4D Rigid Body Physics Engine  
A simple 4D rigid body engine inside 4D Viewer with interactive scenes: chains, gears, cars, blocks, tops, etc. Now ported to Tesserxel.  
[Details and tutorial here](/archives/newton4/)

### [Minecraft4D](https://wxyhly.github.io/4dViewer/minecraft4d/)  
- Minecraft-style game using 4D ray tracing and 3D textures on hypercubes  
- Infinite procedural world with 4D terrain, biomes, villages  
- Supports a Minecraft-like command system including teleport and fill commands  
- Save/load for global and local structures  

[Tutorial here](/archives/mc4tutorial/)

## [Computer Piano](https://wxyhly.github.io/Eop/)  
[[Source Code](https://github.com/wxyhly/Eop)]  
![Screenshot: editing “Frère Jacques”](img/eopplt002.png)  
A personalized keyboard piano app. No keybinding options, adapted for improvised playing. Supports MIDI import/export, note editing, multitrack, and basic MIDI controls (7, 64), with features like quantization, velocity adjustment, and speed recording. Also includes non-piano sounds.

[More details here](/archives/Eop-Analogue/)

## JS Applet Collection in Blog Posts
- [[Original](/archives/2dtime/)] 2D Time World
    + [Ball Collision Simulator](/three/2d2t.html)
    + [2D Entropy Simulator](/three/en2t.html)
    + [Electromagnetic Wave Simulator](/three/em2t.html)
    + [Game of Life Simulator](/three/ca2t.html)
- [[Original](/archives/subspace-angle-2/)] [Angle ranges of Plane and Cell (1)](/three/angle_range.html?type=pc) and [Angle ranges of Plane and Cell (2)](/three/angle_range.html?type=cp)
- [[Original](/archives/subspace-angle/)] [Angle ranges of Planes](/three/angle_range.html?type=pp)
- [[Original](/archives/eye2d/)] [Experience 2D perspective in 3D world](/three/3dviewer42der.html)
- [[Original](/archives/orbit4d/)] [Solar altitude on 4D planets](/three/4dOrbit.html)
- [[Original](/archives/projearth/)] Mercator projection:
    + [Mercator projections in different Earth orientations](/three/shaderEarth.html)
    + [Mercator projection of regular polyhedral](/three/ployhedralEarth.html)
- [[Original](/archives/escher1/)] [Hyperbolic tiling model](/three/HyperbolicSpace.html)
- [[Original](/archives/josleys4ds/)] [Lattice space from Dimension Film's trailer](/three/LatticeViewer.html)
- [[Original](/archives/fibration4ds/)] Hopf fibration:
    + [Three orthogonal Hopf fibrations](/three/Hopf%20fibre1.html)
    + [Part of a Hopf fibration](/three/Hopf%20fibre2.html)
    + [Orthogonal projection of Hopf fibration](/three/Hopf%20fibre3.html)

## Other WebGL Mini Demos
- [Latitude and longitude on a 4D planet in Hopf coordinates](/three/mercator.html)

</div>
