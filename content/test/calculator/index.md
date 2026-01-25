+++
date = "2026-01-25T00:00:00+02:00"
draft = false
title = "Rust Calculator with Iced UI"
toc = false
tags = ["rust", "ui"]
categories = ["rust"]
description = "Just a simple calculator made with Rust + Iced"
image = "thumbnail.png"
+++

<div id="calculator-frame" style="width: 400px; height: 500px; border: 2px solid #ddd; border-radius: 8px; margin: 20px auto; overflow: hidden; box-shadow: 0 4px 6px rgba(0,0,0,0.1); background: white;"></div>

<script type="module">
    import init, * as bindings from './iced_test-7afbea151ae2bbad.js';

    // Initialize calculator immediately
    try {
        const wasm = await init({ module_or_path: './iced_test-7afbea151ae2bbad_bg.wasm' });
        window.wasmBindings = bindings;
        dispatchEvent(new CustomEvent("TrunkApplicationStarted", {detail: {wasm}}));

        // Move the calculator canvas into our frame
        setTimeout(() => {
            const canvas = document.querySelector('canvas');
            const frame = document.getElementById('calculator-frame');
            if (canvas && frame) {
                frame.appendChild(canvas);
            }
        }, 100);
    } catch (error) {
        console.error('Failed to load calculator:', error);
        document.getElementById('calculator-frame').innerHTML = '<p style="color: red; text-align: center; padding: 20px;">Failed to load calculator. Please check console for details.</p>';
    }
</script>

This is a Rust calculator built with Iced UI running in WebAssembly.