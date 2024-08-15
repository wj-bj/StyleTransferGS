# StyleTransferGS
The project is to construct to Style transfer 3DGS
## Video Demonstration
* The video demonstration is available to view [here](https://youtu.be/5JS3QZU3JSY).
## Introduction
<p align="center">
    <img src="pics/Result.gif" height="250" width="450">
</p>
<p align="center">
    <b>Fig:  The result in Unity</b>
</p>

This project was built on top of [gaussian splatting for nerfstudio](https://github.com/jonstephens85/nerfstudio_guassians)
, which incorperates [gsplat](https://github.com/nerfstudio-project/gsplat) to train gaussian splats instead of the offical version of "3D Gaussian Splatting for Real-Time Radiance Field Rendering". The new method of gsplat reduces GPU memory usage and speeds up the training process.


* The aim of the project is to support the function of Style transfer.
* A new expasion for the command ns-train, splatfactoStyle, is added for Stylizing 3DGS


## Technique
Features in the project are list as follows:

**3D Gaussian Splatting**: a technology of 3D reconstruction with point cloud data trained by 3D Gaussian distribution.

**Neural Style Transfer** : Use Vgg19 to extract feature maps from output and reference image, then decrease the discrepency of corresponding Gram matrice.

## Installation
Please refer to the following link and video for the installation environment(there are some pitfalls)：

Github: 
[nerfstudio_guassians from jonstephens85](https://github.com/jonstephens85/nerfstudio_guassians)

Video:[How to install Nerfstudio 2024](https://www.youtube.com/watch?v=3JIpZd5XNAc)

nerfstudio installation [quickstart](https://github.com/nerfstudio-project/nerfstudio/blob/main/docs/quickstart/installation.md#dependencies)

if you encounter some erros during installation, pay attention on the following checklist(not sure if some of them has been fixed):


* [install tiny cuda issue](https://github.com/NVlabs/tiny-cuda-nn/issues/280)
* Don’t install the 17.10+ version visual studio, instead use the 17.9 version!
* SET DISTUTILS_USE_SDK=1 # Windows only
* don’t update setuptools to the latest version, instead use following: pip install setuptools==69.5.1


## Project hierarchy
In oder to expand nerfstudio,  code files are put in relevant folders in nerfstudio project.
```commandline
📦project
 ┣ 📂nerfstudio                 
 ┃ ┣ 📂models                      
 ┃ ┃ ┣ 📜splatfactoStyle.py     //Implementation of training progress  
 ┃ ┣ 📂utils                       
 ┃ ┃ ┣ 📜loss_utils.py          // various loss functions
 ┃ ┣ 📂configs                  // configuration for parameters 
 ┃ ┃ ┣ 📜method_configs.py      // "splatfactoStyle" configuration 
 ┣ 📂data                       // The fold for input data
 ┣ 📂outputs                    // The fold for ouput result 
 ┣ 📂...                       
 ```

##  Operation Instruction
The video demonstration is available to view [here](https://youtu.be/33Z2bZXNBrI).

You can find the explainaion of some commands: [here](https://docs.nerf.studio/reference/cli/index.html)


1.Preprocess video or images,converting video into images, use COLMAP to generate camera data  and point cloud
<br> 
```bash
ns-process-data images --data data/mydata/bicycle/input --output-dir data/mydata/bicycle
 ```
<p align="center">
    <img src="pics/preprocess.png">
</p>
<p align="center">
    <b>Fig.1: output folders</b>
</p>

2.Gaussian Splatting trainning with orignial content<br>
```bash
ns-train splatfacto --max-num-iterations 3000 --data data/mydata/bicycle
```
 <p align="center">
    <img src="pics/train1.png">
</p>
<p align="center">
    <b>Fig.2: training process</b>
</p>

3.Stylized Gaussian splatting training, based on the model of previous result<br>
 ```bash
 ns-train splatfactoStyle --max-num-iterations 400 --data data/mydata/bicycle --load-dir outputs/bicycle/splatfacto/2024-08-07_152903/nerfstudio_models --pipeline.model.style-path data/style/starrynight.jpg
 ```
#### Explanation of Command Parameters:
**`max-num-iterations`**: Specifies the maximum number of iterations for this training session<br>
**`data`**: Points to the folder containing the content images that will be used in the training process.<br>
**`load-dir`**: Specifies the directory from which to load the checkpoint of the previously trained model. This ensures that the current training starts from where the previous session ended<br>
**`pipeline.model.style-path`**: Provides the file path to the reference image that will be used to apply the desired style during the stylized training process

<p align="center">
   <img src="pics/train2.png">
</p>
<p align="center">
    <b>Fig.3: training process</b>
</p>

4.View the result in a web browser
```bash
ns-viewer --load-config "G:\Project\Nerf\nerfstudio\outputs\bicycle\splatfactoStyle\2024-07-30_133908\config.yml"
```
<p align="center">
   <img src="pics/view.png">
</p>
<p align="center">
    <b>Fig.4: View the result in browser</b>
</p>

5.Export Trained Gaussian Splats into PLY File<br>
```bash
ns-export gaussian-splat --load-config "G:\Project\Nerf\nerfstudio\outputs\bicyclesplatfactoStyle\2024-08-07_160813\config.yml" --output-dir data/mydata/bicycle
```

To import the exported ply file into Unity, A plug-in needs to be installed. It can be acquired from [here](https://github.com/aras-p/UnityGaussianSplatting)
<p align="center">
   <img src="pics/export2.png">
</p>
<p align="center">
    <b>Fig.4: View the result in Unity</b>
</p>


