# zig-html-canvas
Pure Zig HTML Canvas implementation. Is WASM compatible and will support hardware acceleration.  

If you want to render canvas graphics in Node JS you previously had three options, however each has its own drawbacks:
1) puppeteer :: Nothing about opening an entire web browser to draw a square is efficiency
2) node canvas :: Requires native dependencies meaning installing it will likely be agony; that is if it even supports your particular architecture and operating system in the first place. You may likely need admin privileges to even install the dependencies so good luck if you're just a regular user. 
3) node-pureimage :: It's written in JavaScript. V8 is impressive, but it's still nowhere close to native speed.

I want a canvas library that is: lightweight, no native dependencies, fast

Solution: Web Assembly! WASM is very compact and lightweight. It's not native so that it's extremely portable and you don't need to undergo special steps to install it. Lastly, even though it's not quite native speed, it's only ~1.4x slower which is a massive improvement compared to JavaScript which is at least 8x slower than native.

|  | Puppeteer  | Node canvas | node-pureimage | zig-html-canvas |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| Dependency Size  | 310 MB | 192 MB | 2 MB | 0.01 MB |
| Portable  | ✗ | ✗ | ✓ | ✓ |
| Can Run Natively  | ✓ | ✓ | ✗ | ✓ |
| Speed compared to native  | start time + 1x | 1x | 8x - 30x | 1x - 1.5x |
| Supports WebGL  | ✓ | ✗ | ✗ | to be implemented |
| Hardware Accelerated  | ✓ | ✗ | ✗ | to be implemented |
| Secure  | ✓ | ? | ✓ | ✓ |

## Language Support
Because the library is written in Zig it can easily be used by Zig, C, and C++. When compiled to WASM it can be used by JavaScript and any other language capable of running WASM. Bindings can be made for other languages but I don't feel like maintaining bindings so you'll have to do that yourself.

### Use from Zig
Copy `html-canvas.zig` and its dependency `vexlib.zig` into your project and then in your main zig file use `@import("html-canvas.zig")`

### Compile to WASM
Set `wasmFreestanding` to true in `vexlib.zig` then run the following command `zig build-lib -O ReleaseSmall -target wasm32-freestanding -dynamic -rdynamic src/wasm-bind.zig`. It will then produce a `wasm-bind.wasm` file which you can then directly use from JavaScript or use the official wrapper library which does automatic type conversions for you to make your life easier.

### Use official JS wrapper library
Import `html-canvas.js`. Then call `faux = HTML_Canvas.createCanvas(width, height)` and treat `faux` as a regular HTML canvas. To render the faux HTML canvas to a real canvas use `faux.drawToNativeCanvas(realHtmlCanvas`.

## API
The official JS wrapper has the exact same API as a real html canvas context. The Zig version of the library due to being a staticly typed language has a few differences for example instead of `ctx.fillStyle = "#ffffff"` you must use `ctx.fillStyle = [_]u8{255, 255, 255, 255}` instead.
