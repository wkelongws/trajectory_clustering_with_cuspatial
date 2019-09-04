# Trajectory Clustering Example with CUSPATIAL

[CUSPATIAL](https://github.com/rapidsai/cuspatial) is a C++ library with python bindings that is based on [CUDA](https://en.wikipedia.org/wiki/CUDA) and [CUDF](https://github.com/rapidsai/cudf).
CUSPATIAL provides significant GPU acceleration to common spatial and spatio-
temporal operations such as point in polygon, distance between trajectories, and
trajectory clustering. The speed-up ranges from 10x to 10000x for different
operations.

This repo shows an example of accelerating a clustering problem using CUSPATIAL on a real-world vehicle trajectory dataset. On this particualr use case, CUSPATIAL brings the end-2-end computation time from around 11 hours down to less than 15 seconds. 

## Problem Statement - Trajectory Clustering 

The trajectory clustering task is grouping a set of trajectories in such a way that trajectories in the same group are more similar to each other than to those in other groups. It is useful for various problems such as motion pattern extraction, behavior analysis and more.

The clustering task consists of two major components in general:

1. Similarity Metirc
2. Searching Algorithm

Given a specific similarity metric, different clustering algorithm will have different searching mechanism and different complexity. But in some case people may want to precompute all pairwise similarities. One of the many reasons could be people may need to perform multiple interations of hyperparamter search for the clustering algorithm to get a good result and we don't want to redo the time-consuming similarity computation each time. 

In this trajectory clustering example, we work on a particular dataset which is description in the following sectio. We use `Hausdorff distance` as the similarity metric between trajectories. We compute the pairwise similarity matrix and apply agglomerativeClustering (AC) and DBSCAN afterwards.

In this example, CUSPATIAL can significantly accelerate the computation of the `Hausdorff distance` similarity matrix. Comparing to the typical scikit-learning implimentation of `Hausdorff distance` (single CPU thread), CUSPATICAL reduces the computation time from about 11 hours to 7.9 seconds on this paticualr dataset.

## Dataset Used

The real-world trajectory dataset we used here for this example is collected from a traffic intersection located in Dubuque, Iowa. We applied the following steps to extract vehicle trajectories from multiple cameras implemented at this intersection:

* Detect vehicle locations in camera domain using AI based detectors;
* Apply tracking algorithm to assign the same id to the same vehicle;
* Project the vehicle location from camera domain to latitudes and longitudes;
* Create trajectories based on vehicle id. 

The trajectory dataset used in this example has been further processed to suit the visualization purpose and the python pickle file (created using python 3.7) can be downloaded [here](https://drive.google.com/file/d/1GE-_z9HgLp3eV7Lgo_KOl53QuMgiCUMS/view?usp=sharing). 
