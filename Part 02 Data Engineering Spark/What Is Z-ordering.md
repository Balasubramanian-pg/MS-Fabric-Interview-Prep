# What Is Z ordering

Canonical documentation for What Is Z ordering. This document defines concepts, terminology, and standard usage.

## Purpose
Z-ordering is the methodology used to manage the visual arrangement of overlapping objects within a two-dimensional space that represents a three-dimensional depth. In graphical user interfaces (GUIs) and computer graphics, Z-ordering resolves spatial ambiguity by determining which elements appear "in front of" or "behind" others when they occupy the same horizontal (X) and vertical (Y) coordinates.

The primary purpose of Z-ordering is to simulate physical depth and maintain visual hierarchy, ensuring that user interactions and rendering logic align with human perception of foreground and background.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The mathematical and logical foundations of depth layering.
* The relationship between the Z-axis and the observer.
* Standard methods for calculating and maintaining visibility.
* Theoretical constraints of overlapping geometry.

**Out of scope:**
* Specific syntax for CSS `z-index`.
* Vendor-specific API calls (e.g., Win32 `SetWindowPos` or Unity-specific sorting layers).
* Hardware-level GPU pipeline optimizations (unless relevant to the conceptual model).

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Z-axis** | The imaginary axis perpendicular to the plane of the screen, representing depth. |
| **Occlusion** | The effect where a foreground object obscures the view of an object behind it. |
| **Stacking Order** | The sequential list of elements from back to front that determines rendering priority. |
| **Z-depth** | The numerical value assigned to an object representing its position along the Z-axis. |
| **Painter’s Algorithm** | A rendering technique where objects are drawn from back to front, allowing newer pixels to overwrite older ones. |
| **Z-buffer** | A data structure used in 3D graphics to store the depth value of each pixel to manage visibility. |
| **Stacking Context** | A localized scope or container within which Z-ordering is calculated independently of the global stack. |

## Core Concepts

### The Z-Axis and the Observer
In a Cartesian coordinate system, X represents the horizontal plane and Y represents the vertical plane. The Z-axis represents the distance between the observer and the object. By convention:
* **Lower Z-values** typically represent objects further from the observer (background).
* **Higher Z-values** typically represent objects closer to the observer (foreground).

### Visibility Determination
Z-ordering is the primary mechanism for visibility determination. Without a defined Z-order, the rendering engine would have no logical basis for deciding which color to display when two objects overlap, leading to visual artifacts or unpredictable flickering.

### Relative vs. Absolute Ordering
* **Absolute Ordering:** Every object in the entire system is assigned a unique, global Z-value.
* **Relative Ordering:** Objects are ordered relative to their siblings within a specific container or parent element.

## Standard Model

The standard model for Z-ordering relies on a **Stacking Stack**. This is a conceptual list where the first item in the list is rendered first (at the bottom) and the last item is rendered last (at the top).

### The Rendering Pipeline
1. **Sort:** The system identifies all objects to be rendered and sorts them based on their Z-depth.
2. **Clip:** The system determines which parts of the background objects are occluded by foreground objects.
3. **Composite:** The system draws the objects onto the frame buffer in the sorted order.

### Hierarchical Stacking
Modern systems use a hierarchical model. An object's effective Z-order is determined by its position within its local **Stacking Context**. If a parent container is moved behind another container, all children of that parent move behind the second container, regardless of their internal Z-values.

## Common Patterns

### Fixed Layering
Assigning specific ranges of Z-values to functional categories. For example:
* **Background Layer:** Z-values 0–100
* **Content Layer:** Z-values 101–200
* **UI/Overlay Layer:** Z-values 201–300
* **Modal/System Layer:** Z-values 301+

### Dynamic Reordering (Bring-to-Front)
A pattern common in windowing systems where the most recently interacted-with object is assigned the highest Z-value in its context, effectively moving it to the top of the stack.

### Implicit Ordering
Ordering based on the sequence of definition (e.g., the order of elements in a file or the order in which objects were created in memory).

## Anti-Patterns

### Z-Index "Arms Race"
The practice of assigning arbitrarily large Z-values (e.g., `999999`) to force an element to the top. This breaks the logical hierarchy and makes future maintenance difficult as other elements must use even larger numbers to compete.

### Global Dependency
Relying on a single global Z-order for complex applications. This prevents modularity, as components cannot be moved or reused without risking Z-order conflicts with unrelated elements.

### Over-reliance on Transparency
Using Z-ordering to stack semi-transparent elements without considering the performance cost of "overdraw" (rendering the same pixel multiple times).

## Edge Cases

### Z-Fighting (Coplanar Surfaces)
When two objects have identical or nearly identical Z-values, the rendering engine may struggle to determine which is in front. This results in "flickering" or "stitching" artifacts as the engine alternates between the two surfaces based on rounding errors.

### Translucency and Alpha Blending
Z-ordering becomes more complex with semi-transparent objects. Unlike opaque objects, where the background can be ignored once occluded, translucent objects require the background to be rendered first so it can be blended with the foreground. This necessitates a strict back-to-front rendering order.

### Non-Integer Z-values
In 3D environments, Z-values are often floating-point numbers. Precision issues can occur at extreme distances (very far or very near), leading to incorrect occlusion logic.

## Related Topics
* **Alpha Compositing:** The process of combining an image with a background to create the appearance of partial transparency.
* **Spatial Indexing:** Data structures (like Quadtrees or Octrees) used to efficiently query objects in a 2D or 3D space.
* **Coordinate Systems:** The foundational mathematical frameworks (Cartesian, Polar) used to define position.
* **Draw Calls:** The command sent to the graphics API to render an object, often optimized by Z-order sorting.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |