+++
date = "2025-09-14T00:00:00+02:00"
draft = false
title = "Wgpu Pentagone test"
toc = false
tags = ["rust", "wgpu", "winit"]
categories = ["rust"]
description = "A pentagone. You can rotate it. Made in rust with winit and wgpu"
image = "thumbnail.png"
+++

## About

A simple pentagone rendered using [WGPU](https://crates.io/crates/wgpu) and [Winit](https://crates.io/crates/winit) in WebAssembly.
You can rotate/zoom it using the arrow keys or the WASM.

The code is based on [the Chapter 6 of Learn WGPU by Sotrh : Uniforms](https://github.com/sotrh/learn-wgpu/tree/master/code/beginner/tutorial6-uniforms).

## Run

{{< first_wasm "wgpu_test.js" >}}
