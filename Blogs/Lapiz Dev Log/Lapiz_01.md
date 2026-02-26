---
title: "Lapiz Dev Log: Engineering a Modern C Graphics HAL"
date: "Feb 25, 2026"
description: "An deep dive into Lapiz—a modular, cross-platform graphics library built for the modern C developer."
image: "../../assets/pencil.jpg"
part: 1
---

# What is Lapiz?

Lapiz is a lightweight, cross-platform graphics library written in pure C. It is designed to act as a **Hardware Abstraction Layer (HAL)**, bridging the gap between low-level graphics APIs—**Metal, OpenGL, and Vulkan**—and the developer.

### Why build another graphics library?
1. **Mastering Complexity:** Understanding the intricate "glue" code required to synchronize windowing systems with modern graphics pipelines.
2. **Tooling Autonomy:** Building a bespoke, "no-nonsense" tool that I can carry across projects, ensuring that my code runs everywhere from a high-end Linux rig to a MacBook Pro without changing a line of application logic.

---

## The Tech Stack

Lapiz stands on the shoulders of industry standards to ensure stability and performance:

* **Windowing HAL:** GLFW & SDL (Backend-swappable)
* **Math:** [cglm](https://github.com/recp/cglm) (Highly optimized C99 math library)
* **Asset Loading:** Assimp (Comprehensive 3D model processing)
* **OpenGL Loading:** glad (Multi-language GL loader)

### Architecture Matrix
| Component | Supported Backends |
| :--- | :--- |
| **Graphics API** | Metal (macOS), OpenGL 4.6, Vulkan 1.3 |
| **Windowing API** | GLFW, SDL3 |

---

## Roadmap: Features for v1.0.0

The goal for version 1.0.0 is a robust, production-ready core:

* **Build-Time Swappability:** Switch Graphics or Windowing backends via simple CMake flags.
* **Unified Shader Pipeline:** Support for fullscreen post-processing and custom shader effects.
* **Primitive & Mesh Rendering:** Out-of-the-box support for 2D/3D primitives and complex Assimp-loaded models.
* **Text Rendering:** Integrated SDF or bitmap-based text engine.
* **High-DPI Awareness:** Native scaling for Retina and 4K displays.

---

## Usage: The Lapiz API in Action

I want the API to feel familiar to C developers—explicit, clean, and state-driven. Here is the vision for a standard application loop:

```c
#include <Lapiz/Lapiz.h>

int main() 
{
    // Initialize global state
    LapizInit();
    
    // Create the window using the selected backend (GLFW/SDL)
    LapizWindow* window = LapizCreateWindow(WIDTH, HEIGHT, "Lapiz Window", LPZ_WIN_RESIZABLE);

    // Initialize the Hardware Renderer (Metal/Vulkan/GL)
    LapizRenderer* renderer = LapizCreateRenderer(window);

    // Main Application Loop
    while(LapizWindowIsOpen(window)) 
    {
        // Handle OS events & Input
        LapizPollEvents();
        
        if (LapizGetEventAction(window, LPZ_KEY_ESCAPE) == LPZ_EVENT_PRESS)
            LapizCloseWindow(window);

        // Frame Rendering Logic
        LapizBeginFrame();
            
            LapizClearColor(LAPIZ_COLOR_DARK_SLATE_GRAY);
            
            // Draw a 3D Model
            LapizDrawModel(my_mesh, my_shader, position);
            
        LapizEndFrame();
    }

    // Graceful Shutdown
    LapizDestroyRenderer(renderer);
    LapizDestroyWindow(window);
    LapizTerminate();
    
    return 0;
}

```

---

## Project Structure

The project is organized to keep the "Core" logic strictly separated from platform-specific "Backend" implementations.

```text
lapiz/
├── external/           # Vendored dependencies (GLFW, cglm, etc.)
├── include/            # Public API
│   ├── Lapiz/
│   │   ├── backends/   # API-specific glue (Metal, Vulkan, GL)
│   │   ├── core/       # Global defines, types, and error handling
│   │   └── graphics/   # High-level rendering commands
├── src/                # Implementation (.c, .m, .cpp files)
└── tests/              # Unit tests and example demos

```

---

## What's next?

In the next dev log, I'll walk through setting up the project—CMake configuration and vendoring dependencies.

If you're interested in following along, check out the repo: [github.com/UlizesR/Lapiz](https://github.com/UlizesR/Lapiz/tree/main)