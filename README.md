# ssbh_wgpu
ssbh_wgpu is a lightweight and portable Smash Ultimate model and texture renderer built using [WGPU](https://github.com/gfx-rs/wgpu). 

> **This fork** is upstream `main` plus two additions [Visionary](https://github.com/Common-Leap/Visionary) needs to draw its effects into the model scene:
>
> - `SsbhRenderer::depth_view` gives the depth the models were drawn with, so other geometry can be hidden behind them.
> - `SsbhRenderer::begin_render_models_with_scene` calls back after the models are drawn and before bloom and color grading, so other geometry (effects) gets the same post processing.
>
> Use it like upstream, from the `visionary` branch:
>
> ```
> ssbh_wgpu = { git = "https://github.com/Common-Leap/ssbh_wgpu", branch = "visionary" }
> ```

## Usage
Add the following lines to the `Cargo.toml`. This library is still highly experimental. Specify the commit tag or commit the `Cargo.lock` file to version control to avoid any versioning issues. For an example of an application using this renderer, see [ssbh_editor](https://github.com/ScanMountGoat/ssbh_editor).

```
ssbh_wgpu = { git = "https://github.com/ScanMountGoat/ssbh_wgpu", rev = "hash" }
nutexb_wgpu = { git = "https://github.com/ScanMountGoat/ssbh_wgpu", rev = "hash" }
```

## Overview
- nutexb_wgpu -- texture renderer and library for converting nutexb files to WGPU textures
- nutexb_wgpu_viewer -- simple winit application for viewing nutexb textures
- ssbh_wgpu -- model and animation rendering library. Converts ssbh_data types to WGPU types.
- ssbh_wgpu_test -- windowless program for testing model loading
- ssbh_wgpu_viewer -- simple winit application for viewing models and animations

## Building
With a recent verison of the Rust toolchain installed, run `cargo build --release`. Don't forget the `--release` since debug builds have very low framerates!

## Development
Implementations are based on data from testing the output of the emulators Yuzu and [Ryujinx](https://ryujinx.org/) using the [RenderDoc](https://renderdoc.org/) debugger. Most testing is done by running a modified build of Ryujinx running OpenGL on the latest version of RenderDoc. The modified build comments out usages of functions not supported by RenderDoc like `GL.AlphaFunc` to fix compatibility issues preventing captures. The OpenGL API can have worse performance than Vulkan but allows for editing shaders in RenderDoc. The shaders use the same format as the decompiled shaders found in [Smush-Material-Research](https://github.com/ScanMountGoat/Smush-Material-Research). Converting the memory layout of Switch textures to a usable layout is handled by [tegra_swizzle](https://github.com/ScanMountGoat/tegra_swizzle), which was also tested using RenderDoc and Ryujinx. Although the WGPU library is more similar to Vulkan or DX12 in terms of design, mapping OpenGL calls in RenderDoc to WGPU is generally straightforward. This approach also avoids the need to reverse engineer any graphis APIs specific to the Switch hardware. Creating a perfect 1:1 recreation of the Smash Ultimate rendering engine is not a goal of this project, so debugging the emulators primarily serves as a way to validate parts of this implementation and debug any relevant differences with in game rendering.

When submitting code, run `cargo fmt` for Rust files and format using [wgsl_analyzer](https://github.com/wgsl-analyzer/wgsl-analyzer) for WGSL files.

## Credits
ssbh_wgpu is a continuation of the rendering work done for [CrossMod](https://github.com/Ploaj/SSBHLib).
File formats are handled by [ssbh_data](https://github.com/ultimate-research/ssbh_lib), [nutexb](https://github.com/jam1garner/nutexb), and [prc-rs](https://github.com/ultimate-research/prc-rs).
