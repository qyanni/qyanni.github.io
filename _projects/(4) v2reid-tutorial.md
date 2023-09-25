---
name: V2ReID - Vision-Outlooker-Based Vehicle Re-Identification
tools: [python, PyTorch]
image: "/assets/img/tutorial_v2reid/v2reid_banner.png" 
description: 
---




<figure>
  <img src="{{site.baseurl}}/assets/img/tutorial_v2reid/v2reid_banner.png" class="figure-img img-fluid rounded" alt="" style="width:70%">
</figure>



This post will briefly go over the vehicle re-identification model named V2ReID that I have designed during my Ph.D. research. My code can be found on [GitHub](https://github.com/qyanni/v2reid), and the paper can be accessed [here](https://www.mdpi.com/1424-8220/22/22/8651).


{% capture list_items %}
Ingredients
Vehicle Re-Identification 
Applications of Vehicle Re-ID
Overview of V2ReID
How to Use V2ReID
{% endcapture %}
{% include elements/list.html title="Table of Contents" type="toc" %}


#### Ingredients

You will need the following:
- 1 vehicle reID dataset: I am using [VeRi-776](https://vehiclereid.github.io/VeRi/)
- a virtual environment
- PyTorch
- GPU
- the official [V2ReID repo](https://github.com/qyanni/v2reid)



#### Vehicle Re-Identification 

Vehicle re-identification (Vehicle Re-ID) is a computer vision and image processing technique used to identify and track vehicles across multiple cameras or frames in a video feed or image dataset. This technology is particularly valuable in various applications such as traffic management, surveillance, security, and smart cities. Vehicle Re-ID goes beyond simple object detection, as it focuses on recognizing the same vehicle across different camera views or timestamps, often in non-ideal conditions like varying lighting, weather, and vehicle occlusions.


The steps and key componenents involved in vehicle re-ID include:

1. Feature Extraction: 

    Vehicle Re-ID begins by extracting distinctive features from vehicle images. These features can include color histograms, texture patterns, and more advanced descriptors like deep learning-based representations (e.g., CNNs or Transformers). These features are designed to capture unique characteristics of vehicles that can help distinguish them.

2. Matching and Similarity Calculation: 
    Once features are extracted, the system calculates the similarity or distance between the features of a reference vehicle and those of vehicles in other frames or camera views. Common similarity metrics include Euclidean distance, cosine similarity, or other custom metrics depending on the feature representations used. Vehicles with similar features are considered potential matches.


#### Applications of Vehicle Re-ID

Applications include:

- Traffic management: Vehicle Re-ID can be used to monitor traffic flow, track traffic violations, and detect stolen or suspicious vehicles.

- Surveillance and security: In security systems, it helps track vehicles in parking lots, monitor access points, and identify vehicles of interest in crowded areas.

- Smart cities: Vehicle Re-ID is integral to smart city initiatives, allowing for better management of traffic congestion and parking spaces.

- Law enforcement: It can help in identifying vehicles involved in criminal activities and supports investigations.

- Toll collection: Vehicle Re-ID is used for automatic toll collection, where vehicles are identified without the need for physical toll booths.



#### Overview of V2ReID


<figure>
  <img src="{{site.baseurl}}/assets/img/tutorial_v2reid/v2reid_stepbystep.png" class="figure-img img-fluid rounded" alt="" style="width:80%">
  <figcaption class="figure-caption text-center"> Overview of V2ReID using VOLO as backbone </figcaption>
</figure>



This is an overview of the codes for V2ReID. The codes find their inspiration from [VOLO](https://github.com/sail-sg/volo) and [TransReID](https://github.com/damo-cv/TransReID).
Each _.py_ file contains further descriptions.

[datasets directory](https://github.com/qyanni/v2reid/tree/main/datasets) contains modules related to reading and sampling the input datasets. 
Please refer to the configs under `DATALOADER` and `INPUT` to choose the the desired parameters:

- [bases.py](https://github.com/qyanni/v2reid/blob/main/datasets/bases.py) retrieves the information of the ReID dataset (train, query and gallery), such as number of vehicle IDs, number of images, number of camera IDs (not used in training), and the image paths for data loading.
- [make_dataloader.py](https://github.com/qyanni/v2reid/blob/main/datasets/make_dataloader.py) loads the train, query and gallery data and applies data augmentation such as resizing, padding, flips, etc. 
- [sampler.py](https://github.com/qyanni/v2reid/blob/main/datasets/sampler.py) implements the random identity sampler, that's used when training with the triplet loss. The data needs to be sampled in a specific way, such that we have _k_ instances for each _N_ vehicle identity per batch.
- [veri.py](https://github.com/qyanni/v2reid/blob/main/datasets/veri.py) specifically reads VeRi-776. For other datasets, e.g. VehicleID, you'll need to modify it.


[loss directory](https://github.com/qyanni/v2reid/tree/main/loss) contains modules related to the different losses (ID, triplet and center). Please refer to the configs under `LOSS` to choose the desired parameters. 
- [center_loss.py](https://github.com/qyanni/v2reid/blob/main/loss/center_loss.py) implements the centre loss.
- [make_loss.py](https://github.com/qyanni/v2reid/blob/main/loss/make_loss.py) builds the loss.
- [triplet_loss.py](https://github.com/qyanni/v2reid/blob/main/loss/triplet_loss.py) implements the triplet loss.
 
The total loss is formulated as tot_loss = ID_loss &#10005;  TRI_loss &#10005;  CEN_loss, with  associated weights.

Use the following configs if you want to train:
- using the ID loss only: set  `TIRPLET_LOSS` and `CENTER_LOSS` to `False`
- using the ID and triplet loss: set  `TIRPLET_LOSS` to `True` and `CENTER_LOSS` to `False`
- using the three losses: set  `TIRPLET_LOSS` and `CENTER_LOSS` to `True`
The weights can be adjusted with  `ID_LOSS_WEIGHT`, `TRIPLET_LOSS_WEIGHT` and `CENTER_LOSS_WEIGHT`.


[models directory](https://github.com/qyanni/v2reid/tree/main/models) contains modules related to the backbone architecture (VOLO). Please refer to the configs under `MODEL`.

-	[volo.py](https://github.com/qyanni/v2reid/blob/main/models/volo.py): implementation of VOLO. We added a snippet for overlapping patches, the batch normalization/layer normalization neck and how to create your own model with your own settings.


[solver directory](https://github.com/qyanni/v2reid/tree/main/solver) contains modules related to the optimization and optimization scheduler (i.e. learning rate scheduler). Please refer to the configs under `SOLVER`.

-	[cosine_lr.py](https://github.com/qyanni/v2reid/blob/main/solver/cosine_lr.py) implementats the cosine LR scheduler
-	[make_optimizer.py](https://github.com/qyanni/v2reid/blob/main/solver/make_optimizer.py) creates the optimizer
-	[scheduler_factory.py](https://github.com/qyanni/v2reid/blob/main/solver/scheduler.py) creates the LR scheduler (cosine or step)
-	[scheduler.py](https://github.com/qyanni/v2reid/blob/main/solver/scheduler_factory.py) base class used to schedule optimizer parameter groups
-	[step_lr.py](https://github.com/qyanni/v2reid/blob/main/solver/step_lr.py) implements the step LR scheduler

[tools directory](https://github.com/qyanni/v2reid/tree/main/tools) contains the training options (with and without center loss)
-	[train_with_center.py](https://github.com/qyanni/v2reid/blob/main/tools/train_with_center.py): training with center loss
-	[train_without_center.py](https://github.com/qyanni/v2reid/blob/main/tools/train_without_center.py): training without center loss


[utils directory](https://github.com/qyanni/v2reid/tree/main/utils) contains modules related to the evaluation and logging the process.
-	[logger.py](https://github.com/qyanni/v2reid/blob/main/utils/logger.py): set up a logger
-	[meter.py](https://github.com/qyanni/v2reid/blob/main/utils/meter.py): computes and stores a meter
-	[metrics.py](https://github.com/qyanni/v2reid/blob/main/utils/metrics.py): computes the mAP and CMC
-	[utils.py](https://github.com/qyanni/v2reid/blob/main/utils/utils.py): for loading the pretrained weights



#### How to Use V2ReID

1. create a new virtual environment and activate it:

    `conda create --name v2reid`

    `conda activate  v2reid`


2. clone the repo and access it:

    `git clone https://github.com/qyanni/v2reid`

    `cd v2reid`


3. install the requirements:

    `pip install -r requirements.txt`


4. download the vehicle re-ID dataset:

    The dataset I am working with is called VeRi-776. Details and download information can be found on the [official website](https://vehiclereid.github.io/VeRi/).


5. train V2Reid:

    Training V2ReID with the default settings:

    `python3 train.py`

    Training V2ReID with your customised hyperparameters:

    `python3 train.py --config configs/cfg.yml`

    The training log will appear in the outputs folder named after whatever you put under  `OUTPUT_DIR`.


The final model can then be used to match new vehicles. 

For more information, please have a look at the [official paper](https://www.mdpi.com/1424-8220/22/22/8651).


