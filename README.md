# PIPE-3956-aerial-segmentation
Open source aerial segmentation models.

## Context

Phase 1 of PIPE-3956: Urban Heat Island CV/ML.

In this phase of the project, we will be investigating and developing open source aerial segmentation models.

Phase 2 & Phase 3 will be seperate repositories on Github Enterprise

## Project Overview

Three connected parallel products:

1. Open-source assisted annotation tool

2. Segmentation modelling pipeline

3. Computer vision prediction to GIS layer conversion tool: Prediction outputs from the segmenetation model pipeline will be aerial imagery tiles and the associated segmentation pipelines in COCO JSON. We will need to merge the polygons intelligently to unify the results into one shapefile that can be delivered as a data product.

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

## Resources

- Aerial Segmentation with Detectron2 [colab tutorial](https://colab.research.google.com/drive/1xijyRU_t5SbWGlkZoTF94kaBKIWgX_Ul?usp=sharing)
- gis2coco: GIS data format conversion into COCO [GitHub](https://github.com/hlydecker/gis2coco)
- [samgeo](https://samgeo.gishub.org/): segment-geospatial - basically geospatial segment anything
- [Computer vision fine tuning](https://github.com/Sydney-Informatics-Hub/computer-vision-fine-tuning): some general tips and tricks and explanations of core concepts.
- [YOLO face mask detector demo](https://github.com/Sydney-Informatics-Hub/cv-demo)
- [SAHI](https://github.com/obss/sahi): a very useful way to deal with image size limitations with CV models.
