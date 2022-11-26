---
title: 基础概念
date: 2022-11-14 11:13:20
categories:
  - webgl
tags:
  - webgl base
---

## 概念

**顶点**
存储一系列基本绘图所需属性的基本元素。包含位置、法向量、颜色、第二色彩、纹理坐标等属性。

**光栅化**

把物体的数学描述以及与物体相关的颜色信息转换为屏幕上用于对应位置的像素及用于填充像素的颜色，这个过程（将矢量顶点组成的图形进行像素化的过程）称为光栅化。

每次绘制对象都需要先设置一系列状态值再调用`gl.drawArrays` 或 `gl.drawElements` 运行着色方法，保证能在 GPU 上运行

**着色器获取数据的 4 种方法**

1. 属性（Attributes）和缓冲

   > 缓冲是发生至 GPU 的二进制数据序列，包含位置、法向量、纹理坐标，顶点颜色等等。
   > 属性用来指明怎么从缓冲获取所需数据并提供给顶点着色器。
   > WebGL 中的颜色值范围从 `0 -> 1`

2. 全局变量（Uniforms）

   > 在着色程序运行前赋值

3. 纹理(Textures)

   > 纹理是一个数据序列，多数存储图像数据，可在着色程序运行中随意读取其中的数据。

4. 可变量（Varyings）
   > 一种顶点着色器给片段着色器传值的方式，渲染图元

## Hello World

WebGL 只关心两件事：裁剪空间中的坐标值和色值。无论画布多大，裁剪的空间坐标永远是 `-1 -> 1`

```js
const canvasRef = ref<HTMLCanvasElement | null>(null);

const gl = unref(canvasRef)!.getContext("webgl");

/**
 * 通过 `viewport` 来传递画布当前尺寸告诉 WebGL 裁剪空间分别对应 x y 轴
 *
 *0 -> gl.canvas.width 和 y轴的 0 -> gl.canvas.height。
 *
 * */

gl.viewport(0, 0, gl.canvas.width, gl.canvas.height);

// 使用 (0, 0, 0, 0) 清空画布 分别对应  r, g, b, alpha （红，绿，蓝，阿尔法）值

gl.clearColor(0, 0, 0, 0);
gl.clear(gl.COLOR_BUFFER_BIT);

// 使用哪个着色器程序
gl.useProgram(program)

// 顶点着色器
const vertex_shader = `
  // 一个属性变量，将会从缓冲中获取数据
  attribute vec4 a_position;

  // 所有着色器都有一个main方法
  void main() {

    // gl_Position 是一个顶点着色器主要设置的变量
    gl_Position = a_position;
  }
`
// 片段着色器
const fragment_shader = `
   // 片段着色器没有默认精度，所以我们需要设置一个精度
  // mediump是一个不错的默认值，代表“medium precision”（中等精度）
  precision mediump float;

  void main() {
    // gl_FragColor是一个片段着色器主要设置的变量
    gl_FragColor = vec4(1, 0, 0.5, 1); // 返回“瑞迪施紫色”
  }
`

```
