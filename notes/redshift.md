# Redshift Notes

Notes on rendering using Redshift with Houdini.

## Notes

- `RS Material` is the main PBR material to use.
- Material Assignments at SOP Level
- Redshift `OBJParam` and `CamParams` in Redshift Shelf:
    - Adds Redshift Parameter Tabs to each object with redshift settings.
    - Tessalation/Displacement: Subdivided for Redshift.
    - CameraParams: DOF, Exposure Settings
    - Lights: can adjust affect specular/diffuse under RSLight->Ray Contribution->Affect Diffuse/Specular
        -also adjust specular/diffuse scale for contribution.
## Depth of Field
- Custom Bokeh (hexagons, triangles, custom size circles, etc.):
    -Camera->Redshift Camera->DOF->Use Bokeh Image
    [Creating Custom Bokeh for Redshift](https://vimeo.com/289093842)

## Lights
- Area Lights
    - Bi Directional, adjust size of the light to have it affects.
    - Normalize Light Intensity: fixed intensity for the light as you adjust it.
    - Cylinder: for tube lights if you want them.

- Objects->Light-Mesh Associations:
    -Light Linking
- Dome Light:
    - Enable Background: will render skymap of HDRI
    - Can use rotate/Transforms, etc.
    - Can adjust dome light affection scales just like regular lights.
- SunLight:
    -Procedural HDRI with a sun that is fully adjustable.

## Volumetrics
- Volume Shader


## Materials
- Redshift Network: vop network for shader building
    -Use RS Nodes in Houdini for correct shader building.
    -Uses PBR Workflow: by default standard is Roughness/Metalness workflow
- Useful Nodes:
    - RSNoise
    - RSFresnel
    - RSColorRange: Remap Range
    - RS Material
    - RS Texture Sample
    - RS COlor Correction
    - RS Color Mix/Composite
    - RS Bump Map
    - RS Normal Map

### Specular Models
    - [Refractive Indices](https://refractiveindex.info/?shelf=3d&book=liquids&page=water)
    - Fresnel Type: different models for reflections (ig GGX)
    - Sub-Surface Scattering:
        - good for more complex plastics -> color transmittance for objects.
    -Metals:
        -IOR(Advanced): IOR for each channel
        -Absorption:
    -Metalness Workflow:
        -Diffuse: color for diffuse, absorption/reflection of just color
        -Reflection: metalness and reflection map values
            -Reflectivity: how much, Metalness: specular/reflection

## Particles
- Cannot Render Particles Directly
    - Use Instance Object to instance geometry onto redshift particles 
    - Pull in attribute via RS Point Attributes and can build into the shader (pscale to drive color/reflectivity/shader attributes etc.)


## Render Quality
- Unified Sampling:
    -Max Samples: min samples doesnt matter as much as max sample, which will affect it a lot more.
    -Max Samples: better for shadow areas
- Sampling OVerrides:
    -sampling for different things depending on scene,m like refractions, caustics, etc.

## GI Settings
- Redshift ROP -> Brute Force -> Irradiance Point Cloud: best quality for GI, everything else is more optimization.
    -sharper lines for edge areas.

## Render Optimizations
- Bucket Size: for GPU renderers, as large as you can.
    - Redshift ROP->Redshift->System->Bucket Size
    - Faster at 256 by a few seconds on currently benchmarked machine.

1) Go to Render Settings > Redshift > Memory > Automatic Memory Management and check this on.  It takes more memory, but speeds up renders.

2) Render Settings > Redshift > System > Bucket Rendering and switch this to 256

3) When using Irradiance Point Cloud as the seconday GI Bounces, which seems to be the best option, change the Samples per Pixel under Irradiance Point Cloud settings to 1.  One one think this would lower the quality, but it doesn't.  It's really strange.  I have not tried it in a lot of scenes right now so if someone could re-verify this it would be great.

4) Under Unified Sampling setting it to 512 max and 256 min seems to be enough to get flicker free for even the hardest scenes.  Setting it higher does not seem to get any better quality for the extra time.  Setting it lower like 256 max 128 min can sometimes add to render time which I find strange.  Setting it very low can cause flickering in some scenes, so for previewing maybe ipr or using 16 max 4 min is good, but final rendering 512 max 256 min seems to be the best between no flickering and a fast render.  If flickering is still seen this should be upped at the cost of more render time.

## Sampling Overrides
1) If using Brute Force for GI, take the ray count up to at least 2x your max unified samples.  So if you're doing 256/512, use at least 1024 for GI. 

2) Do the same for Individual overrides.  So, for the above, it's also recommended to try at least 1024 for lights, refraction, reflection, etc. as needed.  

## Look Dev

If you're working with Multiple SSS, caustics, etc. and you can't really see what's happening, make sure you go to Redshift's render settings, and in the Basic tab, turn off "Force (progressive) Enabled for IPR."  This will take your IPR into bucket mode and show you everything exactly how your final render will come out.