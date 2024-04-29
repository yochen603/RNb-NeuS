# Our modified model is based on the original RNb-NeuS model as below.
# Here's the modifications we made:
dataset.py:

Added self.density_scale parameter in the __init__ method to control the opacity of the volume during rendering.
Modified the gen_random_rays_at method to return z_vals along with other data.
Added the get_z_vals method to generate z_vals based on the camera distance.


exp_runner.py:

Updated the train_rnb method to use the modified gen_random_rays_at method and pass z_vals to the rendering functions.
Modified the train_rnb method to reshape z_vals to (batch_size, n_samples) before passing it to the rendering functions.
Updated the validate_image method to use the get_z_vals method for generating z_vals.
Modified the validate_image method to reshape z_vals to (batch_size, n_samples) and use the correct number of samples when computing normals.


fields.py:

Added a new parameter density_scale to the __init__ method of the SDFNetwork class to control the opacity of the volume during rendering.
Added the density_scale attribute to the SDFNetwork class.


renderer.py:

Removed the up_sample method and related code in the rendering functions.
Added the density_scale parameter in the __init__ method of the NeuSRenderer class.
Modified the render_core method to use the density_scale parameter when calculating alpha values.
Updated the rendering functions (render, render_rnb_warmup, render_rnb) to accept z_vals as a parameter instead of generating them internally.
Adjusted the render function to handle the case when n_outside > 0 and generate z_vals_outside accordingly.
Modified the render_rnb_warmup and render_rnb methods to handle the case when background_alpha is not defined.
Updated the render_core_mvps method to calculate dists using the distance between adjacent z_vals instead of sample_dist.
Added the missing render_rnb method to the NeuSRenderer class.












# RNb-NeuS
This is the official implementation of **RNb-NeuS: Reflectance and Normal-based Multi-View 3D Reconstruction**.

[Baptiste Brument*](https://bbrument.github.io/),
[Robin Bruneau*](https://robinbruneau.github.io/),
[Yvain Quéau](https://sites.google.com/view/yvainqueau),
[Jean Mélou](https://www.irit.fr/~Jean.Melou/),
[François Lauze](https://loutchoa.github.io/),
[Jean-Denis Durou](https://www.irit.fr/~Jean-Denis.Durou/),
[Lilian Calvet](https://scholar.google.com/citations?user=6JewdrMAAAAJ&hl=en)

### [Project page](https://robinbruneau.github.io/publications/rnb_neus.html) | [Paper](https://arxiv.org/abs/2312.01215)

<img src="assets/pipeline.png">

----------------------------------------


## Usage

#### Data Convention

Our data format is inspired from [IDR](https://github.com/lioryariv/idr/blob/main/DATA_CONVENTION.md) as follows:
```
CASE_NAME
|-- cameras.npz    # camera parameters
|-- normal
    |-- 000.png        # normal map for each view
    |-- 001.png
    ...
|-- albedo
    |-- 000.png        # albedo for each view (optional)
    |-- 001.png
    ...
|-- mask
    |-- 000.png        # mask for each view
    |-- 001.png
    ...
```

One can create folders with different data in it, for instance, a normal folder for each normal estimation method.
The name of the folder must be set in the used `.conf` file.

We provide the [DiLiGenT-MV](https://drive.google.com/file/d/1TEBM6Dd7IwjRqJX0p8JwT9hLmy_vA5nU/view?usp=drive_link) data as described above with normals and reflectance maps estimated with [SDM-UniPS](https://github.com/satoshi-ikehata/SDM-UniPS-CVPR2023/). Note that the reflectance maps were scaled over all views and uncertainty masks were generated from 100 normals estimations (see the article for further details).

### Run RNb-NeuS!

**Train with reflectance**

```shell
python exp_runner.py --mode train_rnb --conf ./confs/CONF_NAME.conf --case CASE_NAME
```

**Train without reflectance**

```shell
python exp_runner.py --mode train_rnb --conf ./confs/CONF_NAME.conf --case CASE_NAME --no_albedo
```

**Extract surface** 

```shell
python exp_runner.py --mode validate_mesh --conf ./confs/CONF_NAME.conf --case CASE_NAME --is_continue
```

Additionaly, we provide the five meshes of the DiLiGenT-MV dataset with our method [here](https://drive.google.com/file/d/1CTQW1YLWOT2sSEWznFmSY_cUUtiTXLdM/view?usp=drive_link).

## Citation
If you find our code useful for your research, please cite
```
@inproceedings{Brument23,
    title={RNb-Neus: Reflectance and normal Based reconstruction with NeuS},
    author={Baptiste Brument and Robin Bruneau and Yvain Quéau and Jean Mélou and François Lauze and Jean-Denis Durou and Lilian Calvet},
    booktitle={IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)},
    year={2024}
}
```
