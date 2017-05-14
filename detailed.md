# Detailed description of the project

## 1. Add Entities that are necessary for describing volumes

### Volume Material

The _Volume Material_ entity is a volumetric counterpart to _Material_ that describes participating media instead of surfaces. Its interface is more or less the same as the interface of _Material_, and it is also exposed to appleseed.studio and can be assigned to object instances in the same way that _Material_ does. For now, the generic version of _VolumeMaterial_ can contain the following parameters:

- Phase Function
- Volume Shader
- Volume Data (can be added later when implementing non-homogeneous media)

There are also OSL version of _VolumeMaterial_: 

- Volume Shader
- OSL Volume

Therefore, there are at least `VolumeMaterial`, `GenericVolumeMaterial` and `OSLVolumeMaterial` classes implemented with all related factories.

### Phase Function

A volumetric counterpart to _BSDF_, it is actually a way simpler and can be defined by a single _PDF_ and a few coefficients. It can be evaluated and sampled as well, but sampling and evaluating does not directly require outgoing_direction (though it is useful to pass it to these functions), and does not require information about the surface. 

_Phase Function_ can be chosen from different models, such as Isotropic and Henyey-Greenstein. The following parameters are defined for all of them:

- Scattering coefficient [`Spectrum`]
- Absorption coefficient [`Spectrum`]

For different models additional parameters can be provided.

Implement: `PhaseFunction` abstract class and all related classes such as `VolumeSegment` (Instead of `ShadingPoint`), `PhaseFunctionSample`.

`PhaseFunction` functionality:

- `PhaseFunction::evaluate_pdf(...)` - almost same as `BSDF::evaluate_pdf(...)`
- `PhaseFunction::sample(...)` - takes `VolumeSegment` and computes incoming direction (if not absorbed) and the probability of this pair of directions [`PhaseFunctionSample`]
- `PhaseFunction::transmission(...)` - takes `VolumeSegment` and computes the ratio of light that is not absorbed or out-scattered while traversing the segment 
- `PhaseFunction::evaluate(...)` - evaluates the amount of light [`Spectrum`] in-scattered from the given direction

## 2. Implement Heyney-Greenstein and Isotropic phase functions.

- Sampling of Heyney-Greenstein is described, for example, here:
[The use of the Henyey-Greenstein phase function in Monte Carlo simulations in biomedical optics](https://www.researchgate.net/publication/6875978_The_use_of_the_Henyey-Greenstein_phase_function_in_Monte_Carlo_simulations_in_biomedical_optics)
- Isotropic function is trivial
- Test if sample matches evaluation

## 3. Upgrading PathTracer code

### Where to put raymarching code?

`PathTrace::trace` must be extended with the raymarching code. In terms of code design this can be done in two ways:

- Each ray marching step is a path trace step
- At the end of each path trace step, raymarching with several steps will be performed

After examining the code of path tracer, I came to conclusion that the second option is better. The reasons behind this decision are the following:

- Single-scattering raymarcher is very different from path tracer, since we need to trace ray only once, and then sample points on this ray.
- Multiple-scattering ray marching has some similarities with path tracing, but not much of the code would be reused: instead, a lot of conditionals would arise with sampling BSDF, BSSRDF, e.t.c.
- Currently used PathVisitors are not suitable for ray marcher anyway (Need a new visitor or additional functions along with `PathVisitor::visit_vertex(...)`.
- Allows to pass different RayMarchers to the PathVisitor.

So, somestimes instead of the next `shading_context.get_intersector().trace(...)` call we would call something like `shading_context.get_ray_marcher().march_through_volume(...)`

### How to treat objects with both surface material and volume material? With only volume material?

Now the tracing is terminated if there is no surface material. However, in most cases the object with volume material assigned to it does not require surface material. There are two possibilities of handling this:

- Allow users to assign both surface and volume materials; stop tracing if there are no materials assigned to an object, treat surface material as absolutely transparent (trace through) if there is volume material, but no surface material
- Disallow users to assign both materials. If object has a volume material, do raymarching through the volume, if it has a surface material, treat it as usual.

In the first case, there can be two menus: _Assign Materials_ and _Assign Volume Materials_; or just a _Volume Material_ option inside the _Assign Materials_ menu.

In the second case, when picking Front material, there could be two tabs: _Material_ and _Volume Material_. If _Volume Material_ is chosen, then Back material must be always _None_.

### How to stack volume materials together if the ray is inside many of them at once? Should we stack volume material and the absorption from BSDF?

At first, it must be mentioned, that Media structure should be extended with VolumeMaterial.

Now only the topmost media is being considered while computing absorption. Since we sometimes want to stack several absorption BSDF's and several Volumes, sometimes altogether, I propose to change the current algorithm to the following:

> Take all media with the topmost priority from the list of media, and consider all of them in for-loop while computing BSDF absorption.

Then this approach can be extended to volume rendering:

> If there are at least one medium of the topmost priority that contains volume material, call raymarching procedure.
> While raymarching, calculate the contributions from all volume materials and all BSDF's from the media of the topmost priority.

This idea sounds good for me... But some performance issues can arise, maybe?

### What parts of code will be touched by the changes?

At least we are going to change `RayTracer::trace()`, `ShadingContext` and some additional structs, like `Medium`. Most definitely, we will also extend `PathVertex`, at least by adding a new `ScatteringMode` value. Abstract `RayMarcher` will be added, as well as its successor `SingleScatteringRayMarcher`.

## 4. Modify lighting engines

There are a lot of work to do with the lighting engines.

### Upgrade existing lighting engines with the absorption from participating media.

This change is mostly related to `Tracer::trace_between(...)` function that now should be capable of handling transmission of participating media (not only alpha).

### Add a new lighting engine interface, suitable for volume rendering. Add a lighting engine for DRT and PT.

The new lighting engine must take `VolumeSegment` as an input and sample the light and transmission along this segment.

The simplest lighting engine, suitable for single scattering, can do stochastic sampling along the ray and then act as already implemented lighting engines, but with Phase Function instead of BSDF. Multiple Importance Sampling will most probably work "out of the box", so we can test our first scenes using this lighting engine.

Another possible lighting engine is explained here: [Importance Sampling Techniques for Path Tracing in
Participating Media](https://www.solidangle.com/research/egsr2012_volume.pdf)
