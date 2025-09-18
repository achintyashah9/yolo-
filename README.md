# YOLO_Object_Detection
A comparative study of state-of-the-art object detection algorithm YOLO on the Canadian Road Dataset. This repository contains all code, configuration files, and evaluation scripts needed to reproduce training and benchmark results.

## Background
Detecting road users and infrastructure reliably is critical for autonomous driving systems. This project implements and compares three model detectors-YOLOv8, YOLOv9, and YOLOv10 -on the Canadian Road Dataset, which features diverse weather and lighting conditions across urban and rural road scenes.

# YOLO: A Comparative Study of YOLOv8, YOLOv9, and YOLOv10 Algorithms

*Achintya Shah*

---

### *Abstract*

This study presents a comprehensive comparative analysis of recent advancements in the YOLO (You Only Look Once) family, focusing on YOLOv8, YOLOv9, and YOLOv10. YOLO models are known for their real-time inference capabilities and architectural efficiency. This report details the significant improvements from version 8 to version 10. YOLOv9 introduced novel structural changes like an enhanced backbone, and YOLOv10 further refined the architecture for a fully end-to-end pipeline, removing the need for post-processing steps like non-maximum suppression. Our experiments evaluate these models on a custom traffic dataset, considering key metrics such as mean Average Precision (mAP), F1 score, and precision/recall. The results show a marked improvement with each successive version, with YOLOv10 demonstrating superior performance in both mAP and detection speed, affirming its suitability for real-time applications.

---

### *I. INTRODUCTION*

Accurate and efficient object detection is fundamental to autonomous driving and advanced driver-assistance systems (ADAS), where timely recognition of vehicles, pedestrians, and road signs underpins safe navigation in diverse environments. You Only Look Once (YOLO) ushered in the era of high-speed, single-stage detection by framing object detection as a unified regression problem, achieving upwards of 45 FPS with competitive mean Average Precision (mAP) on datasets such as PASCAL VOC and COCO. Subsequent YOLO iterations have continuously advanced the speed-accuracy frontier, making YOLO models a staple for applications demanding real-time performance.

This report focuses on the evolution within the YOLO family. Despite the breadth of benchmarks on generic datasets such as COCO, these collections often underrepresent severe weather and region-specific road characteristics. To address this gap, we introduce a custom Canadian Road dataset and conduct a comparative study analyzing the performance progression of YOLOv8, YOLOv9, and YOLOv10.

---

### *II. THE YOLO ALGORITHM*

You Only Look Once (YOLO) formulates object detection as a single, unified regression task that directly maps an input image to bounding-box coordinates and class probabilities in one network evaluation.

Given an image $I \in \mathbb{R}^{3 \times H \times W}$, YOLO first resizes it to a fixed resolution (e.g., 448x448) and passes it through a deep convolutional backbone. The final output is a tensor $Y = f_{\theta}(I) \in \mathbb{R}^{S \times S \times (B \cdot 5 + C)}$, where $S \times S$ is the grid size, B is the number of boxes per cell, and C is the number of classes.

*Loss Function:*
During training, each ground-truth box is assigned to the grid cell containing its center and to the box predictor with the maximal IoU. The total loss is a sum of localization error (coordinates and dimensions), confidence error, and classification error.
$$
L = \lambda_{coord}\sum_{i,j}\sum_{b}1_{ij}^{obj}[(x_{ijb}-\hat{x}_{ijb})^{2}+(y_{ijb}-\hat{y}_{ijb})^{2}] \\
+ \lambda_{coord}\sum_{i,j}\sum_{b}1_{ij}^{obj}[(\sqrt{w_{ijb}}-\sqrt{\hat{w}_{ijb}})^{2}+(\sqrt{h_{ijb}}-\sqrt{\hat{h}_{ijb}})^{2}] \\
+ \sum_{i,j}\sum_{b}1_{ij}^{obj}(C_{ijb}-\hat{C}_{ijb})^{2} + \lambda_{noobj}\sum_{i,j}\sum_{b}1_{ij}^{noobj}(C_{ijb}-\hat{C}_{ijb})^{2} \\
+ \sum_{i,j}1_{ij}^{obj}\sum_{c}(p_{ijc}-\hat{p}_{ijc})^{2}

---

### *III. EXPERIMENTS*

#### *A. Dataset Used & Experimental Settings*

The dataset for this study was derived from a traffic video by extracting individual frames, yielding roughly 30,000 images. These frames capture a wide array of urban traffic scenarios. All images were annotated manually in YOLO format. The dataset encompasses twelve object categories: biker, car, pedestrian, trafficLight, trafficLight-Green, trafficLight-GreenLeft, trafficLight-Red, trafficLight-RedLeft, trafficLight-Yellow, trafficLight-YellowLeft, truck, and Arret (Stop sign).

All experiments were performed on an NVIDIA P100 GPU, with the YOLO models being trained for 100 epochs.

#### *B. Comparison Between YOLOv8, v9 & v10*

*YOLOv8* streamlines real-time detection by adopting an anchor-free backbone. *YOLOv9* builds on this by improving gradient flow and feature fusion. *YOLOv10* furthers the series toward a fully end-to-end pipeline by removing the need for post-processing steps like non-maximum suppression (NMS) and integrating efficiency-focused modules that reduce latency.

To evaluate performance, we used precision, recall, F1-score, and mean average precision (mAP). We achieved mAP@0.5 of *0.595, 0.628, and 0.698* for YOLOv8s, YOLOv9c, and YOLOv10s, respectively.

<br>


Fig. 1: Comparison of Precision, Recall and F1 for YOLOv8, v9 and v10.

<br>

The following table summarizes the performance of each model across different object classes.

Table 1: Comparison of Precision, Recall, and F1 Across YOLO versions
| Object Class | YOLOv8s F1 | YOLOv9c F1 | YOLOv10s F1 |
| :--- | :---: | :---: | :---: |
| *Bike* | 0.620 | 0.679 | 0.729 |
| *Car* | 0.663 | 0.656 | 0.753 |
| *Pedestrian* | 0.801 | 0.834 | 0.888 |
| *Truck* | 0.591 | 0.634 | 0.692 |
| *Arret* | 0.795 | 0.880 | 0.917 |

---

### *IV. CONCLUSION*

Our comparative evaluation demonstrates a clear progression across YOLO variants. We observed incremental gains in F1-score from YOLOv8s to YOLOv10s (e.g., for the "Bike" class, the score improved from 0.620 to 0.729). These improvements reflect YOLOv10's efficient end-to-end design, including its NMS-free architecture and holistic efficiency-accuracy optimization.

The YOLO series illustrates marked real-time detection improvements, with YOLOv10s offering a compelling speed-accuracy balance. Per-class analysis revealed that small, textured objects remain challenging, while large rigid objects like "Truck" and "Arret" attain high F1 scores across all models.

For real-time edge deployment, *YOLOv10s is recommended* based on our findings. Future research could explore self-supervised pre-training on domain-specific traffic datasets to further bridge performance gaps.


