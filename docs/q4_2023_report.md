# PIPE-3956: Q4 2023 Progress Report

## Context: Modelling thermal comfort indices for urban areas

Heat waves are harming cities, increasing water demand and health risks. 
Sydney needs data to address urban heating and implement cooling strategies. 
This project aims to calculate real-time human thermal comfort using spatial imagery and existing datasets, paving the way for future solutions to support urban cooling and water demand management without additional infrastructure.

In other words, we will use computer vision to generate data that can be used to understand urban morphology and heat related health concerns.

## Sydney Informatics Hub Development Team

- Henry Lydecker, Data Science Group Lead
- Thomas Mauch, Research Compute Data Scientist
- Sahand Vahidnia, Machine Learning Engineer
- Xinwei Luo, Software Engineer

## Project Plan

To accomplish our objectives within client timeframes, we have broken up development into three software development streams:

1. Annotation - collecting and generating the data needed for model fine tuning and for prediction at scale
2. Segmentation - computer vision model fine tuning and prediction
3. Conversion - utilities to support data interchange between computer vision models and GIS data formats

## Architecture

![Architecture](https://github.com/Sydney-Informatics-Hub/PIPE-3956-aerial-segmentation/blob/0c437dca213e562cc1f1bb187bed1b98ca92aee1/docs/aerial_seg_architecture.png)

## Development Tools

Pytorch, Detectron2

Segmentation Model Backbone: mask RCNN trained on COCo

## Data Sources

- Imagery: NSW Government SixMaps
- Building Polygons: Open Street Map Buildings
- Trees Polygons: AI generated using grounded Segment Anything Geospatial (grounded SAMGeo).
- ABS SA1 Shapefiles: used to help intelligently collect and process data from the Sydney area.

## Dev Kit Performance Benchmarking

Benchmarking scripts have been developed to run on Jetson Nano dev kits and simulate a real world edge deployment on an aircraft or spacecraft capturing imagery and running prediction in real time.

The scripts run on a stream of images and return predicted building outlines rendered on output images, counts of buildings detected, and metrics of the amount of time required to process each image.

Current benchmarking scripts run in python, using CPU. Future builds will run using CUDA and in more lightweight deployments like ONNX and C++.

## Next Steps

1. Create full scale segementation model training dataset for the Sydney area.
2. Fine tune segmentation model at scale on AWS, assessing performance and iterating as needed.
3. Run prediction using fine tuned model on the entire Sydney area, and convert predictions into shapefiles.
4. Discuss post processing steps, including calculating LCZs from polyon preditions.
