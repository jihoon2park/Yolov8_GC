# YOLOv8+GC for Fracture Detection
>[Global Context Modeling in YOLOv8 for Pediatric Wrist Fracture Detection](https://arxiv.org/abs/2407.03163)

[![PWC](https://img.shields.io/endpoint.svg?url=https://paperswithcode.com/badge/global-context-modeling-in-yolov8-for/object-detection-on-grazpedwri-dx)](https://paperswithcode.com/sota/object-detection-on-grazpedwri-dx?p=global-context-modeling-in-yolov8-for)

## Comparison
<p align="left">
  <img src="img/figure_comparison.jpg" width="480" title="details">
</p>

## Performance
| Model | Test Size | Param. | FLOPs | F1 Score | AP<sub>50</sub><sup>val</sup> | Speed |
| :--: | :-: | :-: | :-: | :-: | :-: | :-: |
| YOLOv8 | 1024 | 43.61M | 164.9G | 0.62 | 63.58% | 7.7ms |
| YOLOv8+SA | 1024 | 43.64M | 165.4G | 0.63 | 64.25% | 8.0ms |
| YOLOv8+ECA | 1024 | 43.64M | 165.5G | 0.65 | 64.24% | 7.7ms |
| YOLOv8+GAM | 1024 | 49.29M | 183.5G | 0.65 | 64.26% | 12.7ms |
| YOLOv8+ResGAM | 1024 | 49.29M | 183.5G | 0.64 | 64.98% | 18.1ms |
| YOLOv8+ResCBAM | 1024 | 53.87M | 196.2G | 0.64 | 65.78% | 8.7ms |
| YOLOv9 | 1024 | 69.42M | 244.9G | 0.66 | 65.62% | 16.1ms |
| **YOLOv8+GC** | 1024 | **43.85M** | **165.6G** | **0.66** | **66.32%** | **7.9ms** |

## Citation
If you find our paper useful in your research, please consider citing:
```
  @article{ju2024global,
    title={Global Context Modeling in YOLOv8 for Pediatric Wrist Fracture Detection},
    author={Ju, Rui-Yang and Chien, Chun-Tse and Lin, Chia-Min and Chiang, Jen-Shiun},
    journal={arXiv preprint arXiv:2407.03163},
    year={2024}
  }
```

## Requirements
* Linux (Ubuntu)
* Python = 3.9
* Pytorch = 1.13.1
* NVIDIA GPU + CUDA CuDNN

## Environment
```
  pip install -r requirements.txt
```

## Network Architecture
<p align="center">
  <img src="img/figure_network.jpg" width="640" title="details">
</p>

## Dataset Download
* You can download the GRAZPEDWRI-DX Dataset on this [Link](https://figshare.com/articles/dataset/GRAZPEDWRI-DX/14825193).

## Dataset Split
* To split the dataset into training set, validation set, and test set, you should first put the image and annotatation into `./GRAZPEDWRI-DX/data/images`, and `./GRAZPEDWRI-DX/data/labels`.
* And then you can split the dataset as the following step:
  ```
    python split.py
  ```
* The dataset is divided into training, validation, and testing set (70-20-10 %) according to the key `patient_id` stored in `dataset.csv`. The script then will move the files into the relative folder as it is represented here below.


       GRAZPEDWRI-DX
          └── data   
               ├── meta.yaml
               ├── images
               │    ├── train
               │    │    ├── train_img1.png
               │    │    └── ...
               │    ├── valid
               │    │    ├── valid_img1.png
               │    │    └── ...
               │    └── test
               │         ├── test_img1.png
               │         └── ...
               └── labels
                    ├── train
                    │    ├── train_annotation1.txt
                    │    └── ...
                    ├── valid
                    │    ├── valid_annotation1.txt
                    │    └── ...
                    └── test
                         ├── test_annotation1.txt
                         └── ...


The script will create 3 files: `train_data.csv`, `valid_data.csv`, and `test_data.csv` with the same structure of `dataset.csv`.
                      
## Data Augmentation
* Data augmentation of the training set using the addWeighted function doubles the size of the training set.
```
  python imgaug.py --input_img /path/to/input/train/ --output_img /path/to/output/train/ --input_label /path/to/input/labels/ --output_label /path/to/output/labels/
```
For example:
```
  python imgaug.py --input_img ./GRAZPEDWRI-DX/data/images/train/ --output_img ./GRAZPEDWRI-DX/data/images/train_aug/ --input_label ./GRAZPEDWRI-DX/data/labels/train/ --output_label ./GRAZPEDWRI-DX/data/labels/train_aug/
```
* The path of the processed file is shown below:

       GRAZPEDWRI-DX
          └── data   
               ├── meta.yaml
               ├── images
               │    ├── train
               │    │    ├── train_img1.png
               │    │    └── ...
               │    ├── train_aug
               │    │    ├── train_aug_img1.png
               │    │    └── ...
               │    ├── valid
               │    │    ├── valid_img1.png
               │    │    └── ...
               │    └── test
               │         ├── test_img1.png
               │         └── ...
               └── labels
                    ├── train
                    │    ├── train_annotation1.txt
                    │    └── ...
                    ├── train_aug
                    │    ├── train_aug_annotation1.txt
                    │    └── ...
                    ├── valid
                    │    ├── valid_annotation1.txt
                    │    └── ...
                    └── test
                         ├── test_annotation1.txt
                         └── ...
  
## Experimental setup
* We have provided a training set, test set and validation set containing a single image that you can run directly by following the steps in the example below.
* Before training the model, make sure the path to the data in the `./GRAZPEDWRI-DX/data/meta.yaml` file is correct.
  
```
  # patch: /path/to/GRAZPEDWRI-DX/data
  path: 'E:/GRAZPEDWRI-DX/data'
  train: 'images/train_aug'
  val: 'images/valid'
  test: 'images/test'
```

## Train
### Arguments
You can set the value in the `./ultralytics/cfg/default.yaml`.

| Key | Value | Description |
| :---: | :---: | :---: |
| model | None | path to model file, i.e. yolov8m.yaml, yolov8m_GC_M1.yaml |
| data | None | path to data file, i.e. coco128.yaml, meta.yaml |
| epochs | 100 | number of epochs to train for, i.e. 100, 150 |
| patience | 50 | epochs to wait for no observable improvement for early stopping of training |
| batch | 16 | number of images per batch (-1 for AutoBatch), i.e. 16, 32, 64 |
| imgsz | 640 | size of input images as integer, i.e. 640, 1024 |
| save | True | save train checkpoints and predict results |
| device | 0 | device to run on, i.e. cuda device=0 or device=0,1,2,3 or device=cpu |
| workers | 8 | number of worker threads for data loading (per RANK if DDP) |
| pretrained | True | (bool or str) whether to use a pretrained model (bool) or a model to load weights from (str) |
| optimizer | 'auto' | optimizer to use, choices=SGD, Adam, Adamax, AdamW, NAdam, RAdam, RMSProp, auto |
| resume | False | resume training from last checkpoint |
| lr0 | 0.01 | initial learning rate (i.e. SGD=1E-2, Adam=1E-3) |
| momentum | 0.937 | 	SGD momentum/Adam beta1 |
| weight_decay | 0.0005 | optimizer weight decay 5e-4 |
| val | True | validate/test during training |

* Training Steps:
```
  python start_train.py -model path to model file --data_dir  path to data file
```

* Example (YOLOv8+GC-M):
```
  python start_train.py --model ./ultralytics/cfg/models/v8/yolov8m_GC.yaml --data_dir ./GRAZPEDWRI-DX/data/meta.yaml
```
## Related Works

<details><summary> <b>Expand</b> </summary>

* [https://github.com/RuiyangJu/Bone_Fracture_Detection_YOLOv8](https://github.com/RuiyangJu/Bone_Fracture_Detection_YOLOv8)
* [https://github.com/RuiyangJu/Fracture_Detection_Improved_YOLOv8](https://github.com/RuiyangJu/Fracture_Detection_Improved_YOLOv8)
* [https://github.com/RuiyangJu/YOLOv9-Fracture-Detection](https://github.com/RuiyangJu/YOLOv9-Fracture-Detection)

</details>
