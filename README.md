# VQASynth

Enhance the reasoning of multimodal models with pipelines to synthesize VQA datasets.

## Background
Inspired by SpatialVLM, this repo uses ZoeDepth to adapt Vision Langauge Models for spatial reasoning.
The demos feature pipelines using LLaVA for object captioning and SAM for segmentation. 
One uses CLIPSeg for region proposal, while the other uses GroundingDINO. 

![VQASynth-diagram.png](https://github.com/remyxai/VQASynth/blob/main/assets/VQASynth-diagram.png?raw=true)

### Environment

Before running the demo scripts, ensure you have the following installed:
- Python 3.9 or later
- [Docker](https://docs.docker.com/engine/install/), [Docker Compose V2](https://docs.docker.com/compose/migrate/)
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)

CLIPSeg-based SpatialVLM data processing (recommended):
```bash
cd tests/data_processing/
docker build -f clipseg_data_processing.dockerfile -t vqasynth:clipseg-dataproc-test .
docker run --gpus all -v /path/to/output/:/path/to/output vqasynth:clipseg-dataproc-test --input_image="warehouse_rgb.jpg" --output_dir "/path/to/output" 
```

GroundingDINO-based SpatialVLM data processing:
```bash
cd tests/data_processing/
docker build -f groundingDino_data_processing.dockerfile -t vqasynth:dino-dataproc-test .
docker run --gpus all -v /path/to/output/:/path/to/output vqasynth:dino-dataproc-test --input_image="warehouse_rgb.jpg" --output_dir "/path/to/output" 
```

The scripts will produce 3D point clouds, segmented images, labels, and prompt examples for a test image.


## Run a Pipeline on Your Images

The main pipeline uses Docker Compose to process a directory of images into a VQA dataset including spatial relations between objects. The dataset follows conventions for training models like [LLaVA](https://llava-vl.github.io/). We recommend using an A10 GPU or larger for processing.

Make sure to update [.env](pipelines/.env) with the full path to your image directory and output directory. Then launch the pipeline with:

```bash
cd /path/to/VQASynth
docker compose -f pipelines/spatialvqa.yaml up --build
```

In your designated output directory, you'll find a json file `processed_dataset.json` containing the formatted dataset.

Once completed, you can follow this resource on [fine-tuning LLaVa](https://github.com/haotian-liu/LLaVA/blob/5d8f1760c08b7dfba3ae97b71cbd4c6f17d12dbd/docs/Finetune_Custom_Data.md#L4).

## Notebooks
We've hosted some notebooks visualizing and experimenting with the techniques included in this repo.

| Notebook | Description | Launch |
|----------|-------------|--------|
| Spatial Reasoning with Point Clouds | Visualize point clouds and evaluate spatial relationships | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1f3rr-y233GvxWVzPE7_mK-DY52pG0fsm?usp=sharing) |

## References
This project was inspired by or utilizes concepts discussed in the following research paper(s):
```
@article{chen2024spatialvlm,
  title = {SpatialVLM: Endowing Vision-Language Models with Spatial Reasoning Capabilities},
  author = {Chen, Boyuan and Xu, Zhuo and Kirmani, Sean and Ichter, Brian and Driess, Danny and Florence, Pete and Sadigh, Dorsa and Guibas, Leonidas and Xia, Fei},
  journal = {arXiv preprint arXiv:2401.12168},
  year = {2024},
  url = {https://arxiv.org/abs/2401.12168},
}
```
