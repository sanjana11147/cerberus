# One Model is All You Need: Multi-Task Learning Enables Simultaneous Histology Image Segmentation and Classification 

This repository contains code for using Cerberus, our multi-task model outlined in our [preprint](https://arxiv.org/abs/2203.00077).

Scroll down to the bottom to find instructions on downloading our [pretrained weights](#download-weights) and [WSI-level results](#download-tcga-results).

## Set Up Environment

```
conda env create -f environment.yml
conda activate cerberus
pip install torch==1.6.0 torchvision==0.7.0
```

Above, we install PyTorch version 1.6 with CUDA 10.2. 

## Repository Structure

Below we outline the contents of the directories in the repository.

- `infer`: Inference scripts
- `loader`: Data loading, augmentation and post processing scripts
- `misc`: Miscellaneous scripts and functions
- `models`: Scripts relating to model definition and hyperparameters
- `run_utils`: Model engine and callbacks

The purpose of the main scripts in the repository:

- `run.py`: Script for triggering multi-task training
- `run_train.py`: Main training script - triggered via `run.py` 
- `run_infer_tile.py`: Run inference on image tiles
- `run_infer_wsi.py`: Run inference on whole-slide images
- `extract_patches.py`: Extract patches from image tiles for multi-task learning
- `dataset.yml`: Defines the dataset paths and information

## Training 

Before initialising training, ensure patches are appropriately extracted using `extract_patches.py`. This is only applicable for our segmentation tasks (gland, lumen and nucleus segmentation).

Cerberus uses input patches of size of 448x448 for segmentation. For this, we extract a patches double the size (996x996) [set by `win_size` in the script] and then perform a central crop after augmentation. `step_size` determines the stride used during patch extraction. We use 448 for gland/lumen segmentation tasks and 224 for the nucleus segmentation task. All other information, including the image paths, annotation file extension and fold information (`split_info`) should be populated in `dataset.yml`.

Upon extracting patches, it's time to unleash training. For this, modify the command line arugments in `run.py` enter `python run.py -h` for a full description.

In particular, you can toggle different tasks, set the pretrained weights, determine the batch type and set the paths to the input data. 

For example, if performing single task nuclear instance segmentation on a single GPU with a mixed batch, use:

```
python run.py --gpu="0" --nuclei_inst --nuclei_dir=<path> --mix_target_in_batch --log_dir=<path>
```

If performing multi-task gland, lumen and nuclei instance segmentation, along with patch classification use:

```
python run.py --gpu="0" --gland_inst --gland_dir=<path> --lumen_inst --lumen_dir=<path> --nuclei_inst --nuclei_dir=<path> --pclass --pclass_dir=<path> --mix_target_in_batch --log_dir=<path>
```

Alongside the above, other model details and hyperparameters must be set in `paramset.yml`, including batch size, learning rate, and target output. Also ensure the utilised decoder kwargs, along with the appopriate number of channels are defined - this must align with the tasks specified in the CLIs above!

## Inference
### Tiles
To process large image tiles, run:

```
python run_infer_tile.py --gpu="0" --model=<model_path>
```

### WSIs
To process whole-slide images, run:

```
python run_infer_wsi.py --gpu="0" --model=<model_path>
```

For both tile and WSI inference, the model path should point to a directory containing the settings file and the weights (`.tar` file). 

## Download Weights

In this repository, we enable the download of:

- ResNet weights for transfer learning
- Cerberus model for simultaneous:
    - Gland instance segmentation 
    - Gland semantic segmentation (classification)
    - Nuclear instance segmentation
    - Nuclear semantic semgmentation (classification)
    - Lumen instance segmentation
    - Tisse type patch classification



## Download TCGA Results

Coming soon...







