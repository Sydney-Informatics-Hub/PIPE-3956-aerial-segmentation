# PIPE-3956-aerial-segmentation
Open source aerial segmentation models and toolkits.

This is a meta repository that will contain key information about the project, and provide links to code repositories and useful resources.

## Context

Most of the world's human population is now clustered into cities. It is easy to think of cities as monolithic uniform things, but their morphology can be highly variable and heterogenous. 

Like any animal, human's have evolved to exist in a particular thermal niche. The environments we create, such as cities, have different thermal conditions that influence the temperatures that we experience. 

To better understand the thermal ecology of cities from the human perspective, we will use computer vision to create segmentation layer masks that quantify the morpholoy of cities.

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

### Project Context
- [Local Climate Zone classification scheme](https://journals.ametsoc.org/view/journals/bams/93/12/bams-d-11-00019.1.xml)

### Segmentation Details

- Aerial Segmentation with Detectron2 [colab tutorial](https://colab.research.google.com/drive/1xijyRU_t5SbWGlkZoTF94kaBKIWgX_Ul?usp=sharing)
- gis2coco: GIS data format conversion into COCO [GitHub](https://github.com/hlydecker/gis2coco)
- [samgeo](https://samgeo.gishub.org/): segment-geospatial - basically geospatial segment anything
- [Computer vision fine tuning](https://github.com/Sydney-Informatics-Hub/computer-vision-fine-tuning): some general tips and tricks and explanations of core concepts.
- [YOLO face mask detector demo](https://github.com/Sydney-Informatics-Hub/cv-demo)
- [SAHI](https://github.com/obss/sahi): a very useful way to deal with image size limitations with CV models.
