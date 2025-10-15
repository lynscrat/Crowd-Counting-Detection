# Crowd-Counting-Detection
This project focuses on estimating crowd counts from images using density map estimation integrated with deep learning models.

### Crowd Counting using Density Map Estimation
This project focuses on estimating crowd counts from images using density map estimation integrated with deep learning models. Instead of detecting individuals directly, the models learn to predict density maps that represent how people are distributed spatially within an image. By summing over the density map, the total number of people can be accurately estimated even in highly congested scenes.
Three deep learning models were implemented and compared:
- MCNN (Multi-Column CNN) – built from scratch, designed to capture multi-scale crowd features.
- CSRNet with Decoder – combines a pretrained CSRNet encoder with a custom decoder built from Conv2DTranspose layers to refine upsampling.
- ResNet101 with Decoder – uses a deeper encoder for complex feature extraction, followed by the same decoder architecture as CSRNet.
  
The models were evaluated using Mean Squared Error (MSE) and GAME (Grid Average Mean Absolute Error) metrics. Among them, MCNN achieved the best overall performance, producing realistic density maps and the most consistent crowd count estimations.

### Team Members
1. Alexander Rafael (2702256542)
2. Jonathan William Gunawan (2702251794)
3. Jonathan Sebastian Moiras (2702269633)
4. Hansel Sebastian (2702272842)
5. Derrick Vericho (2702291305)

### Objective
The main goal of this project is to estimate the number of people in an image (crowd counting) using density map estimation integrated with deep learning models.
Instead of detecting individuals directly, the model predicts a density map — a spatial representation indicating how densely people are distributed in the image.
The total count is then obtained by summing over the density map’s pixel values.

### Methodology
1. Density Map Generation

A Gaussian kernel was applied to each annotated head position in the dataset to generate the ground-truth density maps. The Gaussian kernel smooths each point, spreading the influence over neighboring pixels. This ensures that instead of binary head annotations (which are sparse), the model learns a continuous density distribution.

2. Model Architectures
Three models were implemented and compared:
  - MCNN (Multi-Column Convolutional Neural Network) – Built from Scratch
    1. Consists of three parallel convolutional branches, each with different kernel sizes (9×9, 7×7, and 5×5).
    2. Each branch captures features at different spatial scales:
    3. Large kernels → capture large-scale features (useful for distant or sparse crowds).
    4. Small kernels → capture finer details (for dense crowds).
    5. The outputs from each branch are concatenated and passed through convolutional layers to produce the final density map.
    6. This model performed the most stable and realistic predictions during testing.
  - CSRNet (Pretrained VGG16 Encoder) + Decoder
    1. Uses a pretrained CSRNet architecture, originally designed for crowd counting.
    2. The encoder (based on VGG16) extracts hierarchical spatial features.
    3. A decoder was added — consisting of several Conv2DTranspose layers — to upsample the feature maps and reconstruct a detailed density map.
    4. Each Conv2DTranspose layer helps the model learn to upsample smoothly, reducing the risk of producing coarse or blocky outputs.
    5. The decoder also helps the model “relearn” spatial structure from high-level features extracted by the encoder.
  - ResNet101 + Same Decoder as CSRNet
    1. The ResNet101 encoder was used for deeper and more complex feature extraction.
    2. It was followed by the same decoder structure as CSRNet (stacked Conv2DTranspose layers).
    3. Despite its depth, the model sometimes overfit and produced less stable density predictions.
   
### Metrics
1. Mean Squared Error (MSE)
Measures the average squared difference between predicted and ground-truth counts. Lower values indicate better accuracy in estimating the total crowd count.

2. GAME (Grid Average Mean Absolute Error)
GAME divides an image into 4^L grids (where L is a level parameter) and computes the mean absolute error in each subregion. This metric not only measures total count accuracy but also assesses spatial distribution quality — ensuring the model doesn’t predict all density in the wrong areas.

### Results 

| Model                   | MSE   | GAME   | Observation                                                                                  |
| ----------------------- | ----- | ------ | -------------------------------------------------------------------------------------------- |
| **MCNN (Scratch)**      | 68.4  | 42.1   | Most stable and realistic predictions; captures density variations well.                     |
| **CSRNet + Decoder**    | 91.7  | 57.3   | Predicted density maps often too smooth; decoder helped but not enough fine detail recovery. |
| **ResNet101 + Decoder** | 107.2 | 63.8   | Overfit training data; poor generalization and slower convergence.                           |

The MCNN model achieved the best balance between prediction stability, training efficiency, and visual realism of the density maps. Its multi-scale feature extraction allowed it to adapt well to varying crowd densities, making it the most consistent across test samples.

This project demonstrates how density map estimation integrated with deep learning architectures can effectively predict crowd count, with MCNN showing the most robust results among tested models.




