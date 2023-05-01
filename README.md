# 3D-model-prediction-Photogrammetry
This project is a report involving photogrammetry by predicting 3D models from images

# Photogrammetry for 3D Model Prediction
In this project, the generation of 3D models is done using only photographs or images. The process is known as photogrammetry and requires multiple overlapping images of the scene, structure or object whose digital 3D model is to be generated.

This project is based on the Pytorch3D framework and this notebook contains the code for an example provided in the official Pytorch3D documentation.

## Description
An artificial dataset of images was generated from multiple viewpoints of a single 3D object mesh. These images will later be used to train the model and generate, or rather transform a primitive mesh into the corresponding 3D model of the object portrayed in the images.

The following is a brief step by step explanation of the project:

3D Mesh and textures of an object were loaded from its .obj file (only for artificially synthesizing the images for dataset.)
Create a synthetic dataset by rendering a textured mesh from multiple viewpoints.
Using differential silhouette rendering to fit a primitive mesh (generate the 3D model) to the truth in the images.
Using differential textured rendering to fit this primitive mesh as well as the texture as per the images.

A mesh is a datastructure in pytorch3d. Graphically can be thought of as the set of vertices forming an object.
A texture gives RGB information about meshes.

Also the target mesh, here the Cow, is normalized for performance reasons to have center at (0, 0, 0) and be fitable within a sphere of radius 1.
Our primitive mesh is infact a sphere of radius 1.

## Dataset Creation
We sample different camera positions that encode multiple viewpoints of the cow. We create a renderer with a shader that performs texture map interpolation. We render a synthetic dataset of images of the textured cow mesh from multiple viewpoints.

The steps being done here are:

Initialization of perspective cameras over various elevations and azimuthal angles at a fixed distance from the object mesh.
Initialization of rasterization and shading settings which are required for graphically transforming to an output graphic comprised of pixels. Rasterization is a relationship between the pixels and here, the vertices of the mesh.
Building a renderer with the rasterizer and shader called the Phong renderer. The textured Phong shader will interpolate the texture coordinates for each vertex, sample from a texture image to the mesh and apply the Phong lighting model.
Repeating the rendering process for multiple no. of viewpoints for a dataset, each with a different elevation and azimuthal angle of the viewpoint.

# Generating the 3D model from images
Predicting the mesh (model):
As described above, there are two steps in this prediction process. Firstly the silhouette rendering for the mesh itself and then the pong shader rendering for the textures alongside with mesh prediction. We use a different shader in each.

## 1. Mesh prediction using silhouette rendering
In this section, a mesh is predicted by observing the target images without any knowledge of the ground truth cow mesh. It is assumed we know the position of the cameras and lighting from the steps above. This will help us in generating silhouettes.

### Process Description
The prediction process for the silhouette rendering involves reducing the loss of a number of loss functions, namely silhouette image loss, mesh edge loss, mesh normal consistency, and mesh laplacian smoothing.

Most importantly the silhouette image loss. This loss is the offsets of each vertex of the predicted mesh silhouette from the target silhouette image. We learn these offsets and reduce them such that the predicted mesh silhouette is more similar to the target silhouette image at each optimization step.

The initial predicted mess silhouette, deform_verts at Niter = 0 or src_mesh is a sphere in our case as we are generating or transforming a primitive mesh into the prediction of the target silhouette. A silhouette here rendered will be a circle.


## 2. Mesh prediction with texture using RGB rendering with texture
In this section, both the mesh and its texture can be predicted if we an additional loss based on comparing a predicted rendered RGB image to the target image is added.

Note that in the previous method we used only a silhouette to fit.

As before, we start with a primitive sphere mesh. We learn both translational offsets and RGB texture colors for each vertex in the sphere mesh. Since our loss is based on rendered RGB pixel values instead of just the silhouette, we use a SoftPhongShader instead of a SoftSilhouetteShader.

### Fitting predicted mesh to target RGB mesh:
In this step, the vertices of the primitive mesh are also learning the RGB colours along with the shape as part of the texture from the RGB images rendered in the previous step.

#Conclusion
This project notebook shows the end-to-end process for generating 3D models from only images of said model.
An important section was generating a dataset of RGB images overlapping in viewpoints in order to have enough data to accurately create the 3D model. This was done using rasterization with various viewpoint parameters and lighting effects.
This involved learning how to load a textured mesh from an obj file and creating the synthetic dataset by rendering the mesh from those multiple viewpoints.
The initial method to generate the 3D model was learning the mesh vertices to reduce the loss between the 'silhouette' difference between the primitive mesh here and the target mesh or groun truth.
An optimization loop was setup to fit to the rendered truth silhouette.
In the second method, the optimization step involved reducing another loss function based on the rendered RGB images itself, which allowed us to predict both a mesh and its texture.




The code originally belongs to:
Copyright (c) Meta Platforms, Inc. and affiliates. All rights reserved.





