+++
date = "2025-08-31T00:00:00+02:00"
draft = false
title = "Wgpu Triangle test"
toc = false
tags = ["rust", "wgpu", "winit"]
categories = ["rust"]
description = "A triangle. That it. Made in rust with winit and wgpu"
image = "thumbnail.png"
layout = "wasm"
+++

## About

A simple triangle rendered using [WGPU](https://crates.io/crates/wgpu) and [Winit](https://crates.io/crates/winit) in WebAssembly.

Thanks to these examples for learning wgpu!

- https://github.com/w4ngzhen/wgpu_winit_example

- https://github.com/Foxicution/wgpu-template

## Observe the triangle

<div style="text-align: center; margin: 40px 0;">
    <button id="play-button" style="padding: 15px 30px; font-size: 18px; background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; border: none; border-radius: 8px; cursor: pointer; box-shadow: 0 4px 15px rgba(0,0,0,0.2); transition: all 0.3s ease;">
        Run in Fullscreen
    </button>
</div>

<script type="module">
    import init from './wgpu_test2.js';

    let gameInitialized = false;

    document.getElementById('play-button').addEventListener('click', async () => {
        if (!gameInitialized) {
            // Initialize the game
            await init();
            gameInitialized = true;
        }

        // Wait for canvas to be created
        const checkForCanvas = setInterval(() => {
            const canvas = document.getElementById('wgpu-canvas');
            if (canvas) {
                clearInterval(checkForCanvas);

                // Request fullscreen
                if (canvas.requestFullscreen) {
                    canvas.requestFullscreen();
                } else if (canvas.webkitRequestFullscreen) {
                    canvas.webkitRequestFullscreen();
                } else if (canvas.msRequestFullscreen) {
                    canvas.msRequestFullscreen();
                }

                // Style for fullscreen
                canvas.style.width = '100vw';
                canvas.style.height = '100vh';
                canvas.style.display = 'block';
            }
        }, 100);
    });
</script>

<style>
    #play-button:hover {
        transform: translateY(-2px);
        box-shadow: 0 6px 20px rgba(0,0,0,0.3);
    }

    #play-button:active {
        transform: translateY(0);
    }

    #wgpu-canvas {
        background: #000;
    }
</style>


