# Super Resolution

This code is intended for training Super Resolution (SR) algorithms in Pytorch.

![](./sr.jpg)

# Models
Typologies are available for training at this point:

1. Single image super resolution network based on SRResNet architecture
(["Photo-Realistic Single Image Super-Resolution Using a Generative Adversarial
Network"](https://arxiv.org/pdf/1609.04802.pdf)) but with reduced number of channels and depthwise convolution in decoder.
2. Attention-Based single image super resolution network (https://arxiv.org/pdf/1807.06779.pdf) with reduced number of channels and changes in network architecture.
3. [Tiny model to upsampling scanned text images](./README_text.md)

# Results
The PSNR values were calculated with Y channel from YCrCb image.

| Model      | Set5, PSNRx3, dB | Set5, PSNRx4, dB |
| :--------- | :--------------: | :--------------: |
| SmallModel | 33.15            | 31.16            |


## Setup

### Prerequisites

* Ubuntu 16.04 or newer
* OpenVINO 2019 R1 or newer
* Python 3


### Installation

1. Create virtual environment
```bash
virtualenv venv -p python3 --prompt="(sr)"
```

2. Activate virtual environment and setup OpenVINO variables
```bash
. venv/bin/activate
. /opt/intel/openvino/bin/setupvars.sh
```
**NOTE** Good practice is adding `. /opt/intel/openvino/bin/setupvars.sh` to the end of the `venv/bin/activate`.
```
echo ". /opt/intel/openvino/bin/setupvars.sh" >> venv/bin/activate
```

3. Install the module
```bash
pip3 install -e .
```

## Train and evaluation

### Prepare dataset
Create two directories for train and test images. Train images may have any resolution more than `path_size`.
Validation images should have resolution like `path_size`.

```
./data
├── train
│   ├── 000000.png
│   ...
└── val
    ├── 000000.png
    ...
```

### Training

Use `tools/train.py` script to start training process:
```
python3 tools/train.py --config configs/smallmodel_scale4.yaml
```

To start from pretrained checkpoint set `init_checkpoint` in config.
Checkpoints can be downloaded [here](https://download.01.org/opencv/openvino_training_extensions/models/super_resolution/super_resolution.tar.gz).

### Testing

Use `tools/test.py` script to evaluate the trained model.

```
python3 tools/test.py --test_data_path PATH_TO_TEST_DATA \
    --models_path PATH_TO_MODELS_PATH \
    --exp_name EXPERIMENT_NAME
```

## Export to OpenVINO
```
python3 tools/export.py --models_path PATH_TO_MODELS_PATH \
    --exp_name EXPERIMENT_NAME \
    --input_size 200 200 \
    --data_type FP32
```

## Demo

### For the latest checkpoint
```
python3 tools/infer.py --model PATH_TO_CHECKPOINT \
    --scale 4 \
    image_path
```

### For Intermediate Representation (IR)
```
python3 tools/infer_ie.py --model <PATH_TO_IR_XML> \
    image_path
```

[C++ demo](https://github.com/opencv/open_model_zoo/tree/master/demos/super_resolution_demo)


## Know issues

1. Network can't be reshaped after conversation to IR. You should set `input_size` when run `tools/export.py`.
