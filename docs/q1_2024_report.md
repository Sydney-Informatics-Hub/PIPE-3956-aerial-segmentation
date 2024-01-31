# PIPE-3956: Q1 2024 Progress Report

## Context: Modelling thermal comfort indices for urban areas

Heat waves are harming cities, increasing water demand and health risks. 
Sydney needs data to address urban heating and implement cooling strategies. 
This project aims to calculate real-time human thermal comfort using spatial imagery and existing datasets, paving the way for future solutions to support urban cooling and water demand management without additional infrastructure.

To do this, computer vision models and software utilities have been developed to automatically process geospatial imagery and generate polygon segmentations of building outlines and tree coverage. This data will then be fed into downstream processing to classify different types of local climate zones and better understand urban morphology.

## Key Outcomes

### 2023

**1. Urban Morphology Datasets** - There were no existing datasets in computer vision ready formats as of September 2023 suitable for the task, so we developed software and tools to make use of available open source geospatial data sources. For buildings, we were able to find high quality open source annotations from Open Street Map. However for trees, we needed to create our own dataset - for this we made use of a state-of-the-art multi modal model (Grounded LangSAMGeo) to generate tree annotation data. Our two datasets include over 55,000 tree patches and 75,000 building outlines across 3,900 aerial images from locations across the Greater Sydney Area.

**2. Model Fine Tuning Pipeline** - We developed scalable model fine tuning utilities, usable on local computers, Google Colab, and on cloud computing environments. We used Google Colab fine tuning worflows to rapidly iterate and develop proof of concept prototypes. Once our fine tuning workflow was optimised, we then ran full scale model fine tuning on AWS using the University of Sydney's Ronin platform. This process is iterative and will continue running to improve model performance and optimise efficiency.

**3. Model Prediction Tools** - Fine tuned model weights can be implemented into prediction utilities and applications to fit several different needs. Prediction can be run on personal computers using Python scripts, or for bulk prediction over large geospatial extents prediction can be run on cloud computing deployments. To demonstrate potential for deployment on UAVs, fixed wing aircraft, or spacecraft, we implemented a python benchmarking script and deployed it on a Jetson Nano Edge compute dev kit. 

### 2024
**1. Urban Tree Dataset** - A large scae tree dataset has been created, showing tree coverage in the Greater Sydney Area. This dataset can be used to train models to detect trees in aerial imagery, and can be used to understand the distribution of trees across the city. (@mauch please fix/complete)

**2. Urban Building Dataset** - A large scale building dataset has been created, showing building outlines in the Greater Sydney Area. This dataset can be used to train models to detect buildings in aerial imagery, and can be used to understand the distribution of buildings across the city. 

**3. End to End Prediction Pipeline** - After merging all 3 software streams, a fully featured prediction pipeline has been developed, allowing users to prepare dataset for fine tuning, fine tune models, and run predictions on large geospatial extents. This pipeline can be used to generate predictions for any location in the world.



**Table of Contents**

- [Overview](#overview)
  * [System Architecture](#system-architecture)
  * [Annotation](#annotation)
  * [Conversion](#conversion)
  * [Model Fine Tuning](#model-fine-tuning)
  * [Prediction](#prediction)
  * [Dev Kit Performance Benchmarking](#dev-kit-performance-benchmarking)
- [Development Plan](#development-plan)
  * [Development Team](#development-team)
  * [Project Plan](#project-plan)
  * [Development Tools](#development-tools)
  * [Data Sources](#data-sources)

## Overview

### System Architecture

Development was split intro three different software stream that could be developed independently, but together combined as submodules to create a comprehensive geospatial computer vision platform entitled [AIGIS](https://github.com/Sydney-Informatics-Hub/aigis) that supports data acquisition, conversion, pre-processing, model training, prediction, postprocessing, and production deployment. The submodules are <b>aigis_annotate</b>, <b>aigis_segment</b>, and <b>aigis_convert</b>, containing the annotation, segmentation, and conversion utilities respectively.

Overall, the utilities and scripts within the submodules are developed for the following purposes:
- support data aquisition from geospatial data source APIs
- dataset creation using existing open data
- dataset creation using AI assisted annotation
- data interchange between geospatial and computer vision formats
- model fine tuning
- model evaluation
- model prediction
- postprocessing and downstream analysis (e.g. climate zone and density mapping)

![Architecture](https://github.com/Sydney-Informatics-Hub/PIPE-3956-aerial-segmentation/blob/0c437dca213e562cc1f1bb187bed1b98ca92aee1/docs/aerial_seg_architecture.png)

### Annotation

For building outlines, we identified ABS SA1 regions of Sydney where Open Street Maps Buildings annotations covered all of the buildings in the SA1. These regions were then cut into 220 meter by  220 meter tiles, based on the optimal image size using the maximum resolution of SixMaps aerial imagery.

For tree coverage, we used a multi-modal model to automaticall generate annotations. This model, grounded LangSAMGeo, combines a large language model to understand text prompts with a general purpose image segmentation utility (Segment Anything Model) that has been optimised for geospatial use. Using this method, we fed in the text prompt of "trees" and ran the model on AWS Ronin instances to generate tree annotations for the same tiles used for the building annotations.

![Annotation](https://github.com/Sydney-Informatics-Hub/PIPE-3956-aerial-segmentation/blob/fdc030840f370e3bfe9e73910d92406e869175ed/docs/aerial-seg-annotation.png)

Our two current datasets include over 55,000 tree patches and 75,000 building outlines across 3,900 aerial images from locations across the Greater Sydney Area. (Please update these numbers @mauch and @hlydecker) 


In the example below, the input image on the left and the annotations are overlaid on the right. Buildings are in grey, and are very simple rectangles in this case encompasing the rough extent of each building. Tree patch outlines are in green, and can include anywhere from individual small trees to forests.

![Annotation Example](https://github.com/Sydney-Informatics-Hub/PIPE-3956-aerial-segmentation/blob/d19ad098d13dd068930df6434e58851ae46782bd/docs/annotation-comparison.png)

The annotation utilities we developed could be used anywhere in the world, producing ever larger datasets that could be used generally across the globe or optimised datasets to train models to perform well on different types of urban areas.

### Conversion

The annotation and conversion utilisies can be used together to generate tile-based datasets for model fine tuning. 

Conversion submodule contains utilities to convert between geospatial and computer vision formats. This allows for geospatial datasets to be turned into forms that can be used to fine tune models, and for computer vision predictions to be converted into GIS formats for future analysis. 

This submodule contains the following main functionalities:
- Convert from geospatial raster imagery to computer vision PNG images through `geojson2coco` script.
- Convert from geospatial vector shapefiles to computer vision COCO JSON annotations through `geojson2coco` script.
- Convert a batch of geospatial raster imagery and vector shapefiles to computer vision PNG images and COCO JSON annotations through `batch_geojson2coco` script.
- Convert jpeg images to geospatial raster imagery through `jpeg2tiff` script.
- Convert from computer vision COCO JSON annotations to geospatial vector shapefiles through `coco2geojson` script.
- Dataset splitting through `coco_split` script for train, validation, and test sets.
- Balancing datasets through `coco_balance` script to achieve a more balanced dataset.
- Isolating and selecting categories from a dataset through `coco_balance` script.



### Model Fine Tuning

Rather than training models from scratch, we have fine tuned our models on the excellent performance in existing segmentation model. 

Fine tuning scripts have been developed enabling the use of pre-trained weights from the Meta AI Research Detectron2 model zoo. These weights are then fine tuned on our datasets of building and tree annotations.

At this stage multiple fine tuned models are available, including tree segmentation models and building segmentation models. These models can be used for prediction, or can be used as a starting point for further fine tuning.

The buildings model have been trained on Sydney tiles and are able to detect buildings with high accuracy and precision. Similarly, the trees model have been trained on Sydney tiles and are able to detect trees with high accuracy and precision.

For multi-class segmentation of buildings, multipe models have been trained on Sydney tiles and are able to detect high rise buildings separately, distinguishing them from the rest of the buildings.


![Segmentation](https://github.com/Sydney-Informatics-Hub/PIPE-3956-aerial-segmentation/blob/fdc030840f370e3bfe9e73910d92406e869175ed/docs/aerial-seg-fine-tuning.png)

### Prediction 

#### Buildings

In order to make the redictions more building-like, in terms of their geometries, post-procesings steps have been added to the detection pipeline. These steps include:
- reducing the number of polygons per building
- merging polygons that have overlaps accross multiple detection tiles
- generating either a minimum rotated rectangle per building, or an orthogonalised geometry for each building. In most cases the orthoganalised geometry is more accurate, but in some cases the minimum rotated rectangle can be preferred.

#### Trees

Tree predictions have been acquired in the Sydney area using the tree segmentation model. This shows the scalability of the prediction model. (@mauch please update this section)

![Prediction](https://github.com/Sydney-Informatics-Hub/PIPE-3956-aerial-segmentation/blob/678047bd8e161a15ae875a31d67d0c12ae982e9f/docs/aerial-seg-Inference-Application.png)

### Dev Kit Performance Benchmarking 

Benchmarking scripts have been developed to run on Jetson Nano dev kits and simulate a real-world edge deployment on an aircraft or spacecraft capturing imagery and running prediction in real time.

The model currently being used for these benchmarking scripts is a Mask RCNN R 101 FPN backbone pre-trained on MS COCO for instance segmentation and fine-tuned using a dataset of building polygons for the suburb of Chatswood, New South Wales. This model was fine-tuned on Google Colab GPU virtual machine for 3000 epochs. 

The scripts run on a stream of images and return predicted building outlines rendered on output images, counts of buildings detected, and metrics of the amount of time required to process each image.

Current benchmarking scripts run in python, using CPU or CUDA. Future builds will run in more lightweight deployments like ONNX and C++.

Performance is assessed by recording the amount of time and resources required to make each prediction, and by comparing the ground truth building annotations with the predicted building polygons using the metric of mean average precision (mAP) of Intersection over Union (IoU). Results are also visually inspected for sanity checking.

![Dev Kit](https://github.com/Sydney-Informatics-Hub/PIPE-3956-aerial-segmentation/blob/fdc030840f370e3bfe9e73910d92406e869175ed/docs/aerial_seg_figure.png)

## Development Plan

### Development Team

- Henry Lydecker, Data Science Group Lead
- Thomas Mauch, Research Compute Data Scientist
- Sahand Vahidnia, Machine Learning Engineer
- Xinwei Luo, Software Engineer

### Project Plan

This project is split into three parallel development streams of annotation, segmentation, and conversion. These streams are developed independently, but are combined together to form a comprehensive geospatial computer vision platform entitled [AIGIS](https://github.com/Sydney-Informatics-Hub/aigis) that supports data acquisition, conversion, pre-processing, model training, prediction, postprocessing, and production deployment all in a single package.


### Development Tools

- Language: Python
- Deep Learning Framework: PyTorch
- Computer Vision Library: Detectron2
- Spatial Processing: Rasterio, Geopandas, Shapely

Core development work has been done in the general-purpose programming language Python, using the current industry leading Pytorch neural net framework. GitHub is used for version control, with pre commit checks to ensure code quality and reliability.
  
For the annotation stream, we developed Python scripts to collect and pre-process raster imagery and building polygon shapefiles. There are no open-source tree polygon shapefiles available for the Sydney area, so we developed a script to automatically generate tree polygons using a Multi-Modal Model (MMM). Our MMM involved two modalities: a large language model to receive text inputs and “ground” a computer vision model (Segment Anything Geospatial) to understand imagery and segment out polygons with visual embeddings sufficiently aligned with the LLM prompt textual embeddings. This overall approach can be referred to as grounded SAMGeo.

For the segmentation stream, we used the open-source Detectron2 computer vision framework developed by Meta AI Research. This framework was chosen due to its relative ease of use and maintainability, its support for efficient data interchange using the COCO JSON format, and its linkages with the broader Pytorch ecosystem of neural net tooling. Pre-trained weights (Mask RCNN R 101 FPN, trained on the MS COCO Instance Segmentation dataset) were used to fine tune a multiclass instance segmentation model to detect buildings and trees in aerial imagery.

The conversion stream involved the creation of a fully featured conversion Python library called gis2coco to allow seamless data interchange between geospatial data formats (raster imagery and vector polygon shapefiles) and computer vision data formats (PNG images and COCO JSON annotations). This stream built on existing work done by Henry Lydecker to develop a conversion utility to convert GIS data into computer vision formats, and now has the functionality to convert from computer vision formats back into GIS formats. This utility allows for geospatial datasets to be turned into forms that can be used to fine tune models, and for computer vision predictions to be converted into GIS formats for future analysis.

### Data Sources

- Imagery: NSW Government SixMaps
- Building Polygons: Open Street Map Buildings
- Trees Polygons: AI generated using grounded Segment Anything Geospatial (grounded LangSAMGeo).
- ABS SA1 Shapefiles: used to help intelligently collect and process data from the Sydney area.
- Road Polygons: NSW Government Land Use 
- Model Weights: Meta AI Research
