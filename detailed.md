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
- Absorbtion coefficient [`Spectrum`]

For different models additional parameters can be provided.

Implement: `PhaseFunction` abstract class and all related classes such as `VolumeSegment` (Instead of `ShadingPoint`), `PhaseFunctionSample`.

`PhaseFunction` functionality:

- `PhaseFunction::evaluate_pdf(...)` - almost same as `BSDF::evaluate_pdf(...)`
- `PhaseFunction::sample(...)` - takes `VolumeSegment` and computes incoming direction (if not absorbed) and the probability of this pair of directions [`PhaseFunctionSample`]
- `PhaseFunction::transmission(...)` - takes `VolumeSegment` and computes the ratio of light that is not absorbed or out-scattered while traversing the segment 
- `PhaseFunction::evaluate(...)' - evaluates the amount of light [`Spectrum`] in-scattered from the given direction


