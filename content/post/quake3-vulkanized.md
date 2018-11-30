---
title: "Quake 3 Vulkanized"
date: 2017-06-06
draft: true
---

# Quake 3 Vulkanized

Last week I made a new release of the [Quake-III-Arena-Kenny-Edition](https://github.com/kennyalive/Quake-III-Arena-Kenny-Edition) project. The major feature of this release is Vulkan rendering support. Here I present some summary information.

### Goals

* Get practical experience of Vulkan API usage.

* Implement all Quake 3 rendering features including customization options and debug features, even those that are rarely used.

![Vulkan backend in action](https://cdn-images-1.medium.com/max/2000/1*Zy43RO5id8OPvnYnKWbxRA.jpeg)
<p style="text-align: center;">*Vulkan backend in action*</p>

### Time

* It took 2.5 months to finish the project working ~40 hours per week or ~450 hours in total.

* 2 weeks to implement something that can render maps/UI in recognizable quality.

* the next month to get picture indistinguishable from the original for ordinary usage scenarios.

* the last month to implement debug and rarely used features, defects elimination, testing.

### **Testing**

* Renderer was tested in the followed mods: baseq3, cpma, spiterbot, missionpack.

* GPUs: GeForce GTX 770, Radeon RX 580.

* There are no Vulkan drivers for integrated graphics of Intel 4th gen processors thus no luck with my 4790K.

### Statistics

* ~3200 lines of code including empty lines and comments: `vk.h`/`vk.cpp` comprise 90% of the new backend implementation, the rest 10% are smeared throughout the renderer module and corresponding places are marked with `//VULKAN` comment.

* 6.5 Mb of video memory is allocated for geometry buffers (vertices and indices).

* Image memory is allocated in chunks of 32 Mb. Usually one or two chunks are used.

* The number of pipelines varies depending on the map but typical numbers are 50–80 pipelines. In addition there are 36 standard pipelines that are created upon renderer start.

* Pipeline creation takes 10–100 milliseconds depending on the particular map and machine specs.

### Rendering

*General setup.* Single command buffer that records all the commands. Single render pass which specifies color and depth-stencil attachment. Stencil buffer is used to render Q3’s stencil shadows (cg_shadows=2).

*Geometry.* Quake 3 renderer stores geometry data for each draw call in `tess.xyz` and `tess.indexes` arrays. OpenGL backend calls `qglDrawElements` to feed this geometry to GPU. Vulkan backend appends this data to geometry buffers that are bound to host visible memory chunk. At the end of the frame when command buffer is submitted to the queue the geometry buffers contain all the geometry data to render the frame. Typically up to 500Kb of vertex data is copied to the vertex buffer and up to 100Kb of index data is copied to the index buffer (per frame).

*Descriptor sets.* For each image used by the renderer separate descriptor set is created. Each descriptor set contains single descriptor (combined image sampler). For each draw call either one or two (if lightmap is available) descriptor sets are bound. Descriptor sets initialized only one when they are created. There are no descriptor set updates during frame.

*Per-primitive uniform data.* Vulkan guarantees that minimum size of push constants range is at least 128 bytes. To render ordinary view we use 64 bytes to specify mvp transform. For portaled/mirrored views additional 64 byte are used to specify eye transform and clipping plane.

*Pipeline layout.* 2 sets + 128 bytes push constant range.

*Pipelines.* Standard pipelines are created when renderer starts. They are used for skybox rendering, fog/dynamic light effects, shadow volumes and various debug features. Map specific pipelines are created as part of parsing Q3 shaders and are created during map load. For each Q3 shader we create three pipelines: one pipeline to render regular view and two additional pipelines for portal and mirror views.

*Shaders.* Emulate corresponding fixed-function functionality. Vertex shaders are boring with the only thing to mention that for portaled/mirrored views we additionally compute distance to the clipping plane. Fragment shaders do one or two texture lookups and modulate the results by the color.

*Draw calls.* `vkCmdDrawIndexed` is used to draw geometry in most cases. Additionally there are few debug features that use `vkCmdDraw` to convey unindexed vertexes.

### Code

vk.h* *provides interface that brings Vulkan support to Q3 renderer. The interface is quite concise and consists of a dozen of functions that can be divided into 3 category: initialization functions, resource management functions and rendering setup functions.

*Initialization:*

* `vk_initialize`/`vk_shutdown` — initialize/shutdown Vulkan backend

*Resource management:*

* images: `vk_create_image`/`vk_upload_image_data`

* descriptor sets: `vk_update_descriptor_set`

* samplers: `vk_find_sampler`

* pipelines: `vk_find_pipeline`

*Rendering setup:*

* `vk_clear_attachments` — clears framebuffer’s attachments

* `vk_bind_geometry` — called when we start drawing new geometry

* `vk_shade_geometry` — called to shade geometry specified with vk_bind_geometry. Can be called multiple times for Q3’s multi-stage shaders.

* `vk_begin_frame`/`vk_end_frame` — frame setup

* `vk_read_pixels` — takes a screenshot.

We have 13 functions in total to provide foundation necessary to fuel full-featured Q3 Vulkan backend.

### Twin mode

One of the goals of the entire project is to preserve original look and feel and gameplay of the original Quake III Arena. On the graphics side this means that Vulkan backend should provide the same output as native OpenGL-based.

The tool that I used to compare output from both backends is a *twin mode* that is enabled by the `r_twinMode` cvar. In this mode the renderer creates two windows that display both OpenGL and Vulkan outputs side-by-side.

![Early stages of development. The difference is obvious.](https://cdn-images-1.medium.com/max/2000/1*ER6j9uL7yDbOo1zlNF8Z2Q.jpeg)
<p style="text-align: center;">*Early stages of development. The difference is obvious.*</p>

![Twin mode allowed to detect the difference when stencil shadows are active (blood in front of the doom guy)](https://cdn-images-1.medium.com/max/2000/1*UiSxDH2yzKg4kC3TAyQrQg.jpeg)
<p style="text-align: center;">*Twin mode allowed to detect the difference when stencil shadows are active (blood in front of the doom guy)*</p>

![Debug surfaces visualization (r_debugSurface 1)](https://cdn-images-1.medium.com/max/2000/1*fZUxu8mRZdYEKv8Sb4cYAQ.jpeg)
<p style="text-align: center;">*Debug surfaces visualization (r_debugSurface 1)*</p>

![Screenshot from the release build. More or less the same pixels!](https://cdn-images-1.medium.com/max/2000/1*MWqnS9_c4V6sH-2TGEokZQ.jpeg)
<p style="text-align: center;">*Screenshot from the release build. More or less the same pixels!*</p>

### Conclusion

Quake 3 is one of my favorite games and I had a very positive experience working with its code. Q3 codebase is of high quality. Its complexity corresponds to the provided functionality. Modern C++ codebases, overwhelmed with abstractions and with poor performance characteristics, are just terrible.

Vulkan is good. I like its explicit nature — if you have to do something it’s hard to forget to do this. Validation layers are really cool — good example of how to write good warning/error messages. Also I don’t remember if I had a black screen at some point (remember OpenGL?). On the other side I had to restart my PC several times (AMD drivers) when validation layers did not catch invalid call. Yes, thin drivers.

`/r_renderAPI 1`, `/vid_restart`, frag!
