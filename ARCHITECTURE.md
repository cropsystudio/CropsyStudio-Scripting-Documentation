# Architecture Plan

## High-Level Goals

Cropsy Studio is split into three native modules:

- `Engine`: reusable runtime systems with no editor UI dependency
- `Runtime`: play-mode bridge and simulation ownership
- `Editor`: desktop tooling, docked UI, and scene authoring

## Core Design

### Engine

The engine is a static library containing:

- application/window bootstrap helpers
- logging and assertions
- input state
- scene/entities/components
- rendering resources and viewport rendering
- asset and path services
- scene serialization
- optional physics/audio integrations
- project browser service contracts

### Runtime

The runtime owns the difference between edit mode and play mode:

- clones the editable scene into a runtime scene
- initializes simulation state from the cloned scene
- updates runtime-only systems
- safely tears down and returns control to the editor

This prevents scene corruption by ensuring the editor never simulates directly on the authoring scene.

### Editor

The editor executable owns:

- GLFW window and OpenGL context
- Dear ImGui docking shell
- panels: hierarchy, inspector, content browser, viewport, console, project browser
- menu bar and runner toolbar
- selection state and editor camera
- scene creation/save/load commands

## Data Flow

1. The editor loads a scene JSON file into an edit scene.
2. The viewport renders the edit scene with editor camera controls.
3. Pressing `Play` clones the scene into a runtime scene owned by `Runtime::PlaySession`.
4. Runtime systems update physics, controller behavior, audio listener state, and rendering.
5. Pressing `Stop` destroys the runtime scene and restores the editor to the untouched edit scene.

## Scene Model

Aurora uses a light ECS-style scene:

- entities are stable ids plus names
- components are stored in typed maps keyed by entity id
- transforms support parent/child hierarchy
- components are plain structs for clarity

The first implementation favors simplicity over maximum cache efficiency.

## Rendering Model

The renderer is intentionally conservative:

- forward rendering
- basic materials and texture slots
- built-in primitive meshes for cube and sphere
- optional model import via Assimp
- one editor framebuffer for the viewport
- simple light uniforms for directional, point, and spot lights

## Service Layer

The project browser is designed behind interfaces:

- `IProjectCatalogService`
- local file-backed implementation today
- backend-ready swap later without rewriting editor panels

## Portability Strategy

- discover project root by walking relative to the executable
- keep asset references as relative paths
- keep generated artifacts in the build folder
- isolate local sample data in `ExampleGame/`
