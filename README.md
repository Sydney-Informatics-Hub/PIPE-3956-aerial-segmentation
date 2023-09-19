# PIPE-3956-aerial-segmentation
Open source aerial segmentation models and toolkits.

This is a meta repository that will contain key information about the project, and provide links to code repositories and useful resources.

## Context

Most of the world's human population is now clustered into cities. It is easy to think of cities as monolithic uniform things, but their morphology can be highly variable and heterogenous. 

Like any animal, human's have evolved to exist in a particular thermal niche. The environments we create, such as cities, have different thermal conditions that influence the temperatures that we experience. 

To better understand the thermal ecology of cities from the human perspective, we will use computer vision to create segmentation layer masks that quantify the morpholoy of cities.

## Project Overview

Three connected parallel products:

1. [aerial-annotation](https://github.com/Sydney-Informatics-Hub/aerial-annotation) - Tools for creating datasets of aerial imagery and segmentation annotations.

2. [aerial-segmentation](https://github.com/Sydney-Informatics-Hub/aerial-segmentation) - Aerial imagery segmentation model fine tuninf, evaluation, and prediction tools.

3. [aerial-conversion](https://github.com/Sydney-Informatics-Hub/aerial-conversion) - Computer vision prediction to GIS layer conversion tool. Prediction outputs from the segmenetation model pipeline will be aerial imagery tiles and the associated segmentation pipelines in COCO JSON. We will need to merge the polygons intelligently to unify the results into one shapefile that can be delivered as a data product.

Together this will produce two data products:

1. Aerial Segmentation Dataset

2. AI prediction shapefiles

## Setup

### Local (or interactive VM)

```bash
conda create -n aerial-seg-dev python==3.9

conda activate aerial-seg-dev

pip install -r requirements.txt
```

## Dataset

A toy dataset has been uploaded to Roboflow. It is a small subset, containing Chatswood region, available [here](https://universe.roboflow.com/sih-vpfnf/gis-hd-200x200).

There are multiple versions of this dataset. Please ignore the first version. Version 2 and later versions are the ones that are being used. The main difference of version 2 and 3 is that [version 2](https://universe.roboflow.com/sih-vpfnf/gis-hd-200x200/2) contains 90 degree augmentaions, while [version 3](https://universe.roboflow.com/sih-vpfnf/gis-hd-200x200/3) does not.

For implementing this in your code, you can use the following code snippet:

```python
from roboflow import Roboflow
 
rf = Roboflow(api_key= 'your_roboflow_api_key' )
workspace_name = "sih-vpfnf" 
dataset_version = 3 
project_name = "gis-hd-200x200" 
dataset_download_name = "coco-segmentation" 

project = rf.workspace(workspace_name).project(project_name)
dataset = project.version(dataset_version).download(dataset_download_name)
```
<!-- 
# Register the dataset
from detectron2.data.datasets import register_coco_instances
dataset_name = "chatswood-dataset" #@param {type:"string"}
dataset_folder = "gis-hd-200x200" #@param {type:"string"}
register_coco_instances(f"{dataset_name}_train", {}, f"{dataset_folder}/train/_annotations.coco.json", f"/content/{dataset_folder}/train/")
register_coco_instances(f"{dataset_name}_val", {}, f"{dataset_folder}/valid/_annotations.coco.json", f"/content/{dataset_folder}/valid/")
register_coco_instances(f"{dataset_name}_test", {}, f"{dataset_folder}/test/_annotations.coco.json", f"/content/{dataset_folder}/test/")

# Use the dataset
from detectron2.config import get_cfg

cfg = get_cfg()
cfg.DATASETS.TRAIN = (f"{dataset_name}_train",)
cfg.DATASETS.TEST = (f"{dataset_name}_test",)
# then do the other configs

``` -->

## Resources

### Project Context
- [Local Climate Zone classification scheme](https://journals.ametsoc.org/view/journals/bams/93/12/bams-d-11-00019.1.xml)
- [Geoscape](https://geoscape.com.au/) - Lots of address/building/parcel data for Australia.

### Segmentation Details

- Aerial Segmentation with Detectron2 [colab tutorial](https://colab.research.google.com/drive/1xijyRU_t5SbWGlkZoTF94kaBKIWgX_Ul?usp=sharing)
- gis2coco: GIS data format conversion into COCO [GitHub](https://github.com/hlydecker/gis2coco)
- [samgeo](https://samgeo.gishub.org/): segment-geospatial - basically geospatial segment anything
- [Computer vision fine tuning](https://github.com/Sydney-Informatics-Hub/computer-vision-fine-tuning): some general tips and tricks and explanations of core concepts.
- [YOLO face mask detector demo](https://github.com/Sydney-Informatics-Hub/cv-demo)
- [SAHI](https://github.com/obss/sahi): a very useful way to deal with image size limitations with CV models.
