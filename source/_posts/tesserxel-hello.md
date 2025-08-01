---
title: "Play Tesserxel (0): A New 4D Engine"
tags:
  - Javascript
  - Tesserxel
  - Graphics
  - 4d
categories: Tesserxel Series
date: 2022-09-08 10:43:30
index_img: /img/tsx001.jpg
excerpt: The previous 4D engine was based on WebGL. Now I have decided to try using the new WebGPU API to re-implement the 4D rendering engine. Thus, the new project - Tesserxel was born. Tesserxel takes the words Tesseract (the Latin root tessera "four" of the 4D cube) and Pixel.
---

Due to the messy code of [4DViewer](https://github.com/wxyhly/4dViewer) and its low performance with cross-section calculations on the CPU side, at the end of July this year I decided to try using the new WebGPU API to re-implement the 4D rendering engine. This is because WebGPU's compute shaders can place cross-section calculations on the GPU, completely solving the performance problem. Thus, the new project—[Tesserxel](https://github.com/wxyhly/tesserxel) was born. Tesserxel takes the words Tesseract (the Latin root tessera "four" of the four-dimensional cube) and Pixel.
![Screenshot of Tesserxel's built-in example browser](/img/tsx001.jpg?size=300x)

Currently, Tesserxel has implemented the following features: 
1. A math library containing 4D vectors, bivectors, quaternions for representing spinors, and matrix operations needed for graphics.
2. A tetrahedron-based rasterization renderer. This renderer is only a low-level encapsulation and requires the user to create their own shader pipelines, GPU buffer resources, etc.
3. The Four sub-module can help users hide the low-level rendering logic, similar to the Three.js library in 3D rendering, by declaring geometries, cameras, materials, and lights to quickly build a 4D scene.
4. A 4D rigid body physics engine.
5. Encapsulation of a user keyboard and mouse interaction system.

Now let's enter the 4D world built by Tesserxel. Here is the link to the example scene library~~(note that WebGPU must be enabled to open it)~~:

[https://wxyhly.github.io/tesserxel/examples/](https://wxyhly.github.io/tesserxel/examples/)

**Please refer to the tutorials in the [follow-up articles of the Tesserxel series](/categories/Tesserxel系列/) to learn more about how to use Tesserxel~**

**Note: Now you can open it directly on a desktop computer by updating to the latest version of Google Chrome (version 113 and above). The following content is now outdated.**

How to enable WebGPU: WebGPU is an experimental API and the future "successor" to WebGL. Its standard is still in the W3C draft stage and has not been officially released. Currently, it is said that only Chrome on Windows provides good support, and it is a bit troublesome to enable this feature. First, you need to download the Canary version of the Chrome browser ([Google's official website](https://www.google.com/intl/zh-CN/chrome/canary/), or find download resources yourself), launch the browser with the --enable-unsafe-webgpu parameter, open chrome://flags/, and turn on WebGPU Developer Features (select Enabled) to enable WebGPU.
![Steps to enable WebGPU](/img/tsx001.png)

The current Tesserxel is just an early version. In the future, an instruction manual for Tesserxel will be added, and more physics calculations, advanced materials, offline rendering, and 4D games based on the Tesserxel engine will be developed. (Hopefully I won't abandon it~)