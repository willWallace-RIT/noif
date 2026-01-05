[shadertoy](https://www.shadertoy.com/view/tfcXzr)

noif spatial algorithm

uses the diagonal of a screen to create a box of a position and size using a proportional height and width in relation to the screen.
the code avoid if and uses mults,subtracts and bit shifting to detect overlap of the fairly approximated bounding box centered on the diagonal.

The algorithm recurses the usage of subs and shifting to create a OR gate with math operator only comparisons.

it currently supports two boxes but may support more using some customization.

(this is a 2D prototype. while 3D is possible it was not in my scope of open source support)

NORTHBOT
(will)

ml prompt white paper explanation:
# Noif Algorithm: Branchless Parametric Spatial Optimization

**Author:** Will Wallace  
**GitHub:** https://github.com/willWallace-RIT/noif  
**Date:** January 2026  

---

## Abstract

The **Noif Algorithm** is a branchless, parametric spatial optimization technique for rapid detection of potential intersections in 2D or 3D space. Objects are represented as rectangles (or bounding regions) whose centers move along a line. Overlap is computed without conditional branching, divisions, or per-axis comparisons. 

This makes the algorithm ideal as a **broad-phase collision filter** in physics simulations, graphics rendering, and GPU-compute tasks, providing a low-cost first line of defense against unnecessary geometric computations.

---

## Features

- Branchless computation using bitwise operations
- Parametric rectangle representation along a line
- Avoids divisions and floating-point comparisons
- Suitable for CPU, SIMD, and GPU applications
- Conservative broad-phase filter for collisions

---

## Methodology

### Parametric Rectangle Representation

- Each object is approximated as a rectangle whose **center moves along a line**, parameterized by t ∈ [0, 1].
- Rectangle extents are defined by interpolating **min and max bounds** along this line.

### Branchless Overlap Computation

Traditional per-axis checks:

```c
if (a.maxX < b.minX || a.minX > b.maxX) return false;
if (a.maxY < b.minY || a.minY > b.maxY) return false;
```

Noif replaces these with **bitwise arithmetic**:

```c
mask = ((x1 - x2) | (x2 + w2 - x1 - w1)) >= 0;
```

- **Shifts and OR gates** convert overlap conditions into a binary mask of `0` or `1`.
- Approximate arithmetic avoids divisions and floating-point operations.

### Interval Overlap Along Line

- The rectangle’s position along the line allows computing a **parametric interval [t_min, t_max]** where overlap occurs.
- Reduces multi-axis checks to a **single parametric overlap test**, which is branchless.

---

## Spatial Optimization

### CPU Physics Filtering

- Serves as a **broad-phase collision filter**, removing object pairs that cannot intersect before full AABB or SAT tests.
- Reduces expensive conditional checks in dense simulations.

### GPU / SIMD Applications

- Branchless design ensures **deterministic execution per thread**.
- Avoids pipeline stalls caused by divergent branches.
- Ideal for particle systems, fluid simulations, and real-time shaders.

---

## Performance Considerations

| Feature | Traditional | Noif Algorithm |
|---------|------------|----------------|
| Branches | Many | None |
| Per-axis comparisons | Yes | Single parametric mask |
| Divisions | Possible | Avoided via approximation |
| Parallelization | Limited by branches | Fully SIMD/GPU friendly |
| Broad-phase filtering | Moderate | High efficiency |

---

## Applications

- Physics engines: First-pass collision filter
- Graphics rendering: Shader-friendly overlap detection
- Game engines: Efficient culling of non-colliding entities
- High-density simulations: Particle systems, fluid, crowd simulations

---

## Limitations

- Assumes rectangles moving along a line. Extension to arbitrary motion requires extra interpolation logic.
- Conservative approximation may include false positives but **never eliminates potential collisions**.

---

## Conclusion

The Noif Algorithm provides **branchless, deterministic, and parallelizable** broad-phase collision detection. By using parametric rectangles along a line and bitwise operations, it reduces computational cost for AABB or SAT checks and is suitable for CPU and GPU high-performance applications.

---

## References

1. Slab method for ray-box intersection: https://en.wikipedia.org/wiki/Slab_method  
2. Branchless programming tricks for range checks: https://www.reddit.com/r/C_Programming/comments/gv4q67  
3. GitHub repository: https://github.com/willWallace-RIT/noif
