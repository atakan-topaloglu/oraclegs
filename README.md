# OracleGS [WACV 2026 Oral] Code Release

This repository is based on the original paper from [3D Gaussian Splatting as Markov Chain Monte Carlo](https://github.com/ubc-vision/3dgs-mcmc), which is built on top of the [Original 3DGS code base](https://github.com/graphdeco-inria/gaussian-splatting)

## How to Install

### Installation Steps

1. **Clone the Repository:**
   ```sh
   git clone --recursive https://github.com/atakan-topaloglu/diff-gaussian-rasterization.git
   cd 3dgs-mcmc
   ```
2. **Set Up the Conda Environment:**
    ```sh
    conda create -y -n 3dgs-mcmc python=3.8
    conda activate 3dgs-mcmc
    ```
3. **Install Dependencies:**
    ```sh
    pip install plyfile tqdm lpips torch==1.13.1+cu117 torchvision==0.14.1+cu117 torchaudio==0.13.1 --extra-index-url https://download.pytorch.org/whl/cu117
    conda install cudatoolkit-dev=11.7 -c conda-forge
    ```
4. **Install Submodules:**
    ```sh
    pip install submodules/diff-gaussian-rasterization
    pip install submodules/simple-knn
    ```

5. **Generate Synthetic Images:**
- Use [Stable Virtual Camera](https://github.com/Stability-AI/stable-virtual-camera) to generate images. Throughout the experiments, we used `CFG=3.0`, `T=80`, `L_short=80`. An example training command is: 
```python
python demo.py --data_path /home/mipnerf_360 --task img2img  --data_items bicycle,bonsai,garden,stump,room,counter,kitchen
 --num_inputs 12 --cfg 3.0 --L_short 576 --use_traj_prior True --traj_prior orbit --chunking_strategy nearest-gt --output_dir {output_dir} --T 80
 ```

## How to run
Running code is similar to the [Original 3DGS code base](https://github.com/graphdeco-inria/gaussian-splatting) with the following differences. You may specify:
- Maximum number of Gaussians that will be used. This is performed using --cap_max argument. The results in the paper uses the final number of Gaussians reached by the original 3DGS run for each shape.
- Scale regularizer coefficient. This is performed using --scale_reg argument. For all the experiments in the paper, we use 0.01.
- Opacity regularizer coefficient. This is performed using --opacity_reg argument. For Deep Blending dataset, we use 0.001. For all other experiments in the paper, we use 0.01.
- Noise learning rate. This is performed using --noise_lr argument. For all the experiments in the paper, we use 5e5.
- Input image downsampling rate, we follow the common guidelines for evaluation (-r 4 for Mip-NeRF360 and -r 2 for Synthetic NeRF)
- Initialization type. This is performed using --init_type argument. Options are random (to initialize randomly) or sfm (to initialize using a pointcloud).
- If Progressive Augmentation Strategy is going to be used with --gt_synth_schedule and --gt_synth_ratio arguments.
- LPIPS loss coefficient with the --lambda_lpips argument
- Path to depth maps for the GT and synthetic images, with the --depths argument. More details about depth map generation can be accessed on [Depth Regularization](https://github.com/graphdeco-inria/gaussian-splatting?tab=readme-ov-file#depth-regularization) section of original 3DGS Repository.
- Number of GT training views used in --num_train_views argument.

## Example Training Run to Reproduce Results in Paper
```python
python train.py -s {path_to_shape} -m {output_dir} --eval --cap_max 1800000  --scale_reg 0.01 --opacity_reg 0.01 -r 4 --init_type sfm --gt_synth_schedule --gt_synth_ratio 1 --lambda_lpips 0.3 --synth_attention_dir --depths depths --num_train_views 12 
```




