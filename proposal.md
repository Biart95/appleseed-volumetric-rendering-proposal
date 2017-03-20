# Volume Renderer for appleseed

## About the project
Classical method of path tracing looks at physical media as a set of 3D surfaces that can interact with light by absorbing, 
reflecting it, or transmitting it inwards. This interaction makes light rays to repeatedly bounce off objects, until they 
eventually reach the camera and produce the image that people can see.

In real life, though, light does not only interact with surfaces of objects, but also can be scattered or absorbed while traversing 
the space between these objects. The broad branch of techniques that take these effects into consideration is generally called 
_Volume Rendering_ or _Volumetric Rendering_. Volume rendering implies to extend the classical path tracing by allowing light rays 
to interact with so-called participating media, be it fog, dust, milk or any other substance that can scatter the light out of its 
linear path.

Appleseed is a performant physically-based rendering engine. It already gained the level of maturity when volume rendering becomes 
an essential milestone, which opens the door to competition with the most powerful and respected production engines in the whole 
industry of computer graphics and animation. My goal is to make the first step on the way of achieving this milestone by 
integrating the feature of rendering homogenous volumes to appleseed engine, and thus making it capable to handle simple volumetric 
effects, such as light shafts in a foggy environment. During my work I will investigate different approaches of visualizing volumes, 
select the techniques that are modern, efficient, and fit the best to the existing path tracing code of appleseed, and then 
implement the chosen methods. Additionally, I will introduce how users will interact with the newly added features by extending the 
user interface of appleseed.studio.

## About me
I am Artem Bishev, MSc student of Technical University of Munich, Germany. My current program is called "Informatics: Games Engineering". Previously I attained a BSc degree in Mathematics and Computer Science in Lomonosov Moscow State University. Since school I was interested in computer graphics, both real-time and photorealistic, and have experience in the field, working for EligoVision company in Russia. I have a professional knowledge of modern C++, and I am closely familiar with Python.
My contacts:
* email: biart95@gmail.com
* phone: +49 176 268 90 351
* github: https://github.com/Biart95/
