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

This is a Rust calculator built with Iced UI running in WebAssembly.

<div style="text-align: center; margin: 40px 0;">
    <button id="calc-button" style="padding: 15px 30px; font-size: 18px; background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; border: none; border-radius: 8px; cursor: pointer; box-shadow: 0 4px 15px rgba(0,0,0,0.2); transition: all 0.3s ease;">
        Launch Calculator
    </button>
</div>

<div id="calculator-container" style="display: none; width: 100%; height: 600px; border: 1px solid #ccc; margin: 20px 0;"></div>

<script type="module">
    import init, * as bindings from './iced_test-7afbea151ae2bbad.js';

    let calcInitialized = false;

    document.getElementById('calc-button').addEventListener('click', async () => {
        if (!calcInitialized) {
            const container = document.getElementById('calculator-container');
            container.style.display = 'block';
            
            try {
                const wasm = await init({ module_or_path: './iced_test-7afbea151ae2bbad_bg.wasm' });
                window.wasmBindings = bindings;
                dispatchEvent(new CustomEvent("TrunkApplicationStarted", {detail: {wasm}}));
                calcInitialized = true;
            } catch (error) {
                console.error('Failed to load calculator:', error);
                container.innerHTML = '<p style="color: red; text-align: center;">Failed to load calculator. Please check console for details.</p>';
            }
        }
    });
</script>

<style>
    #calc-button:hover {
        transform: translateY(-2px);
        box-shadow: 0 6px 20px rgba(0,0,0,0.3);
    }

    #calc-button:active {
        transform: translateY(0);
    }

    #calculator-container {
        background: #f5f5f5;
        border-radius: 8px;
    }
</style>