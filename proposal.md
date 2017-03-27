# Volume Rendering for appleseed

## About the project
Classical method of path tracing looks at physical media as a set of 3D surfaces that can interact with light by absorbing, 
reflecting it, or transmitting it inwards. This interaction makes light rays to repeatedly bounce off objects, until they 
eventually reach the camera and produce the image that people can see.

In real life, though, light does not only interact with surfaces of objects, but also can be scattered or absorbed while traversing the space between these surfaces. The broad branch of techniques that take these effects into account is generally called _Volume Rendering_ or _Volumetric Rendering_. This term implies the classical path tracing to be extended by allowing light rays to interact with so-called participating media, be it fog, dust, milk or any other substance that can scatter the light out of its linear path.

[Appleseed](http://appleseedhq.net/) is a performant physically-based rendering engine. It has already gained the level of maturity when volume rendering becomes an essential milestone, which opens up the competition with the most powerful and respected production engines. My goal is to make the first step on the way of reaching this milestone by integrating the feature of rendering _homogeneous_ volumes to appleseed engine, and thus making it capable to handle simple volumetric effects, such as light shafts in a foggy environment. During my work I will investigate different approaches of visualizing volumes, select the techniques that are modern, efficient and fit the best to the existing path tracing code of appleseed, and then implement the chosen methods. Additionally, I will introduce how users will interact with the newly added features by extending the user interface of appleseed.studio.

After my modification, appleseed will be able to render the scenes like this one: 

<p><img height="385" width="578" src="http://lucas-zimmermann.com/images/_o6a52952.jpg" alt="Photograph by Lucas Zimmermann" data-canonical-src="http://lucas-zimmermann.com/images/_o6a52952.jpg">
<br>(Photograph by Lucas Zimmermann, <br>from the "Traffic Light 2.0" series — <a href="http://lucas-zimmermann.com/traffic-lights-2.0.html">Source</a>)</p>

## About me
I am **Artem Bishev**, M.Sc. student of Technical University of Munich, Germany. My current program is called "Informatics: Games Engineering". In my studies I focus primarily on graphical aspects of computer games and scientific visualization. Previously I attained a B.Sc. degree in Mathematics and Computer Science in Lomonosov Moscow State University.

Since school I was interested in computer graphics, both real-time and photorealistic, and have experience in the field, working for EligoVision company in Russia. I have a professional knowledge of modern C++, and I am closely familiar with Python.

My contacts:
*   E-mail: biart95@gmail.com
*   Phone: +49 176 268 90 351
*   GitHub: https://github.com/Biart95/

## Goals
The following features must be delivered at the end of the summer project:
-   **Raymarching engine** that extends appleseed path tracer and is capable of rendering homogeneous participating media inside closed surfaces
-   Support for both single and multiple scattering
-   Support for adaptive steps during raymarching
-   Full support for OSL volume shaders
-   Database consisting of reference images that are used to compare and test rendering results

Raymarching engine must be fully integrated with appleseed, i.e.:
-   Python bindings for all aforementioned functions and related entities must be provided
-   Simple and convenient UI extention for volume rendering must be provided for appleseed.studio
-   Volume rendering must work with all three currently supported rendering modes

Optional deliverables:
-   Support for multiple importance sampling for volume rendering

## Project plan
### Community bonding period
#### Now -- 29 May
-   Investigate path tracing code of appleseed by actively contributing to it: add new features and fix bugs
-   Review scientific literature about volume rendering, select modern and efficient methods suitable for my task and discuss these methods with my mentors
-   Find out more about phase functions that are used in production rendering
-   Get closer to Qt and investigate how the interface for volume rendering should look like, discuss this topic with appleseed community
### Phase 1
#### 30 May -- 2 June
-   Implement and test absorbtion and scattering formulas
-   Implement abstract Phase Function class (analogical to BRDF) and classes related to it. Implement formulas of some specific phase functions.
-   Implement other basic entities necessary for volume rendering, such as Volumetric Material
-   Expose some of these entities to users, providing UI for appleseed.studio
#### 5 June -- 9 June, 12 June -- 16 June
-   Develop simple ray marching engine with constant steps that can compute attenuation
-   Incorporate this ray marcher to the existing path tracing engine
-   Include some settings of ray marching engine in the UI of appleseed.studio
#### 19 June -- 23 June
-   Add single scattering to the ray marcher
-   Add support for adaptive steps
#### 26 June -- 30 June
-   Prepare a reference database with images of existing scenes, real or rendered with existing volume rendering engines
-   Compare results of our raymarcher with this reference, thoroughly test functionality, fix bugs
-   Prepare the project for Phase 1 submition, finalize the changes
#### By the end of phase 1 there must be:
-   The basic volume rendering, available for all existing rendering modes except photon mapping
-   Support for both adaptive and constant stepping
-   Support for single scattering
### Phase 2
#### 3 July -- 7 July, 10 July -- 14 July
-   Add multiple scattering
#### 17 July -- 21 July, 24 July -- 28 July
-   Add photon maps support to volume rendering
-   Integrate the raymarcher with the "Stochastic Progressive Photon Mapping" rendering mode
-   Prepare the project for Phase 2 submition
#### By the end of phase 2 there must be:
-   Volume rendering, available for all existing rendering modes
-   Support for both adaptive and constant stepping, for single and mutiple scattering
### Phase 3
#### 31 July - 4 August
-   Include support for OSL volume shaders
-   Write python bindings for volume rendering functions and entities
#### 7 August - 11 August
-   Profile volume rendering solution, make optimizations that increase performance
-   Derive formulas for multiple importance sampling (OPTIONAL)
-   Implement and test multiple importance sampling for volume rendering (OPTIONAL)
#### 14 August - 18 August
-   Thoroughly test all implemented elements. Ensure that they do not break old functionality and are well integrated with appleseed infrastructure, such as appleseed.python
-   Write documentation
#### 21 August - 25 August
-   Final product evaluation
#### By the end of phase 3 all goals must be reached
## Further development
These goals are beyond the scope of GSoC'17, and can be implemented afterwards:
-   Volume rendering for non-homogeneous volumes (such as clouds): Voxel-based, Particle-based, Analytical
-   Procedural generation of non-homogeneous volumes
-   Support for internal formats, such as OpenVDB
