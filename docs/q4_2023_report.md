# PIPE-3956: Q4 2023 Progress Report

## Context: Modelling thermal comfort indices for urban areas

Heat waves are harming cities, increasing water demand and health risks. 
Sydney needs data to address urban heating and implement cooling strategies. 
This project aims to calculate real-time human thermal comfort using spatial imagery and existing datasets, paving the way for future solutions to support urban cooling and water demand management without additional infrastructure.

To do this, we will develop computer vision models and software utilities to automatically process geospatial imagery and generate polygon segmentations of building outlines and tree coverage. This data will then be fed into downstream processing to classify different types of local climate zones and better understand urban morphology.

**Table of Contents**
- [2023 Key Outcomes](#2023-key-outcomes)
- [Development Plan](#development-plan)
  * [Development Team](#development-team)
  * [Project Plan](#project-plan)
- [Architecture](#architecture)
  * [Annotation](#annotation)
  * [Model Fine Tuning](#model-fine-tuning)
  * [Prediction](#prediction)
  * [Dev Kit Performance Benchmarking](#dev-kit-performance-benchmarking)
- [Next Steps](#next-steps)
- [Additional Details](#additional-details)
  * [Development Tools](#development-tools)
  * [Data Sources](#data-sources)

## 2023 Key Outcomes

**1. Urban Morphology Datasets** - There were no existing datasets in computer vision ready formats as of September 2023 suitable for the task, so we developed software and tools to make use of available open source geospatial data sources. For buildings, we were able to find high quality open source annotations from Open Street Map. However for trees, we needed to create our own dataset - for this we made use of a state-of-the-art multi modal model (Grounded LangSAMGeo) to generate tree annotation data. Our two datasets include over 55,000 tree patches and 75,000 building outlines across 3,900 aerial images from locations across the Greater Sydney Area.


**2. Model Fine Tuning Pipeline** - We developed scalable model fine tuning utilities, usable on local computers, Google Colab, and on cloud computing environments. We used Google Colab fine tuning worflows to rapidly iterate and develop proof of concept prototypes. Once our fine tuning workflow was optimised, we then ran full scale model fine tuning on AWS using the University of Sydney's Ronin platform. This process is iterative and will continue running to improve model performance and optimise efficiency.


**3. Model Prediction Tools** - Fine tuned model weights can be implemented into prediction utilities and applications to fit several different needs. Prediction can be run on personal computers using Python scripts, or for bulk prediction over large geospatial extents prediction can be run on cloud computing deployments. To demonstrate potential for deployment on UAVs, fixed wing aircraft, or spacecraft, we implemented a python benchmarking script and deployed it on a Jetson Nano Edge compute dev kit. 

## Development Plan

### Development Team

- Henry Lydecker, Data Science Group Lead
- Thomas Mauch, Research Compute Data Scientist
- Sahand Vahidnia, Machine Learning Engineer
- Xinwei Luo, Software Engineer

### Project Plan

This project is split into three parallel development streams:

**1. Annotation** - collecting and generating the data needed for model fine tuning and for prediction at scale

**2. Segmentation** - computer vision model fine tuning and prediction

**3. Conversion** - utilities to support data interchange between computer vision models and GIS data formats

Each of these streams has been developed in a seperate GitHub repository, however over time the production ready features from each stream will be brought together into a final single GitHub repository.

## Architecture

The project is built from a series of modular utilities designed to:
- support data aquisition from geospatial data source APIs
- dataset creation using existing open data
- dataset creation using AI assisted annotation
- data interchange between geospatial and computer vision formats
- model fine tuning
- model evaluation
- model prediction
- postprocessing and downstream analysis

![Architecture](https://github.com/Sydney-Informatics-Hub/PIPE-3956-aerial-segmentation/blob/0c437dca213e562cc1f1bb187bed1b98ca92aee1/docs/aerial_seg_architecture.png)

### Annotation

For building outlines, we identified ABS SA1 regions of Sydney where Open Street Maps Buildings annotations covered all of the buildings in the SA1. These regions were then cut into 220 meter by  220 meter tiles, based on the optimal image size using the maximum resolution of SixMaps aerial imagery.

For tree coverage, we used a multi-modal model to automaticall generate annotations. This model, grounded LangSAMGeo, combines a large language model to understand text prompts with a general purpose image segmentation utility (Segment Anything Model) that has been optimised for geospatial use. Using this method, we fed in the text prompt of "trees" and ran the model on AWS Ronin instances to generate tree annotations for the same tiles used for the building annotations.

![Annotation](https://github.com/Sydney-Informatics-Hub/PIPE-3956-aerial-segmentation/blob/fdc030840f370e3bfe9e73910d92406e869175ed/docs/aerial-seg-annotation.png)

Our two current datasets include over 55,000 tree patches and 75,000 building outlines across 3,900 aerial images from locations across the Greater Sydney Area. 

The annotation utilities we developed could be used anywhere in the world, producing ever larger datasets that could be used generally across the globe or optimised datasets to train models to perform well on different types of urban areas.
  
### Model Fine Tuning

Rather than training models from scratch, we will fine tune our models on the excellent performance in existing segmentation model. 

![Segmentation](https://github.com/Sydney-Informatics-Hub/PIPE-3956-aerial-segmentation/blob/fdc030840f370e3bfe9e73910d92406e869175ed/docs/aerial-seg-fine-tuning.png)

### Prediction 

Fine tuned models will be deployed in prediction utilities that allow users to specify the geospatial extent they would like predictions for. The model will then run, retrieving imagery for the selected area and running prediction. Predictions will then be returned as GeoJSON vector data that can be downloaded for future use.

![Prediction](https://github.com/Sydney-Informatics-Hub/PIPE-3956-aerial-segmentation/blob/678047bd8e161a15ae875a31d67d0c12ae982e9f/docs/aerial-seg-Inference-Application.png)

### Dev Kit Performance Benchmarking

Benchmarking scripts have been developed to run on Jetson Nano dev kits and simulate a real world edge deployment on an aircraft or spacecraft capturing imagery and running prediction in real time.

The scripts run on a stream of images and return predicted building outlines rendered on output images, counts of buildings detected, and metrics of the amount of time required to process each image.

Current benchmarking scripts run in Python, using CPU or CUDA. Future builds will run in more lightweight deployments like ONNX and C++.

![Dev Kit](https://github.com/Sydney-Informatics-Hub/PIPE-3956-aerial-segmentation/blob/fdc030840f370e3bfe9e73910d92406e869175ed/docs/aerial_seg_figure.png)

## Next Steps

1. Create full scale segementation model training dataset for the Sydney area.
2. Fine tune segmentation model at scale on AWS, assessing performance and iterating as needed.
3. Run prediction using fine tuned model on the entire Sydney area, and convert predictions into shapefiles.
4. Discuss post processing steps, including calculating LCZs from polyon preditions.

## Additional Details

### Development Tools

Python, Pytorch, Detectron2

Segmentation Model Backbone: mask RCNN trained on COCO

### Data Sources

- Imagery: NSW Government SixMaps
- Building Polygons: Open Street Map Buildings
- Trees Polygons: AI generated using grounded Segment Anything Geospatial (grounded SAMGeo).
- ABS SA1 Shapefiles: used to help identify regions of Sydney where all of the buildings present are annotated by Open Street Map Buildings.
