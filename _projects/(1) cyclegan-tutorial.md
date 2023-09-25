---
name: CycleGAN - A Tutorial in PyTorch
tools: [python, PyTorch]
image: "/assets/img/tutorial_cyclegan/tutorial_cyclegan.png" 
description: How to use CycleGAN withouth diving into the code
---



<figure>
  <img src="{{site.baseurl}}/assets/img/tutorial_cyclegan/tutorial_cyclegan.png" class="figure-img img-fluid rounded" alt="" style="width:50%">
</figure>


In this tutorial, I will go through how to use CycleGAN for your project. For more information on CycleGAN, please have a look at my post on [CycleGAN](http://127.0.0.1:4001/blog/cyclegan-model) or the [infographic](http://127.0.0.1:4001/infographics/cyclegan). 

{% include elements/highlight.html text="change links" %}


{% capture list_items %}
1. Ingredients
2. Cloning the Repo and Installing the Requirements
3. Preparing the Datasets
4. Train CycleGAN
5. Training Outputs
6. Testing the Model
7. Results
8. My Research
Final Words
{% endcapture %}
{% include elements/list.html title="Table of Contents" type="toc" %}



#### 1. Ingredients

You will need the following:
- 2 datasets
- a virtual environment
- PyTorch 1.10.2 + CUDA toolkit 11.3
- CPU or GPU
- the official [CycleGAN repo](https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix)


#### 2. Cloning the Repo and Installing the Requirements

Follow these steps:
1. Activate your virtual environment
```
conda activate cyclegan_env
```
2. Clone the repo in your selected directory
```
git clone https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix
```
3. Access the folder
```
cd pytorch-CycleGAN-and-pix2pix
```
4. Install the requirements
```
pip install -r requirements.txt
```



#### 3. Preparing the Datasets

Under `./datasets` create the following folders:

```
datasets
├─ swan2duck
  ├─ testA #images of swans that you want to transform to ducks (swan2duck)
  ├─ testB #images of ducks that you want to transform to swans (duck2swan)
  ├─ trainA #images of swans
  └─ trainB #images of birds

```
You can either use your own data, or use the data provided by the authors. Please refer to the official [CycleGAN repo](https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix) for more information. A selection of datasets: `apple2orange, summer2winter_yosemite, horse2zebra, monet2photo`, etc. is provided.


In this tutorial I will be working with ukiyoe2photo dataset. Photos are images of photographs. Ukiyo-e refers to a traditional Japanese art that originated in the Edo period. The term "ukiyo-e" translates to "pictures of the floating world." It primarily consists of woodblock prints and paintings depicting scenes from everyday life, landscapes, historical events, kabuki theater actors, erotic scenes, and legendary heroes.

An iconic examle of ukiyo-e art is the "Under the Wave off Kanagawa" *(Kanagawa oki nami ura)*, also known as "The Great Wave", one of Hokusai's most famous print.

<figure>
  <img src="{{site.baseurl}}/assets/img/tutorial_cyclegan/ukiyoe_greatwave.jpeg" class="figure-img img-fluid rounded" alt="" style="width:50%">
  <figcaption class="figure-caption text-center"> Katsushika Hokusa, Under the Wave off Kanagawa (Kanagawa oki nami ura)
  <br>~The Metropolitan Museum of Art, New York~</figcaption>
</figure>

An example of each class is illustrated below:

<figure>
  <img src="{{site.baseurl}}/assets/img/tutorial_cyclegan/samples_A_B.png" class="figure-img img-fluid rounded" alt="" style="width:50%">
  <figcaption class="figure-caption text-center"> Images of class ukiyo-e and class photo</figcaption>
</figure>


#### 4. Train CycleGAN

To train the model, enter the following line in your terminal:

```
python3 train.py --dataroot ./datasets/ukiyoe2photo/ --name ukiyoe2photo --use_wandb  
--model cycle_gan --batch_size 64 --gpu_ids  0,1
```

- `--dataroot` the path to the images (with subfolders `trainA`, `trainB`) is under `./datasets/ukiyoe2photo/`
- ` --name` the name of my experiment is called `ukiyoe2photo`. Try to be  specific, don't call it `experiment1`
- `--use_wandb` use [WandB](https://wandb.ai/site) (Weights & Biases), which is a dashboard that can be used to track the model's performance, visualise the model's learning process, as well as the power usage and temperature of the GPUs. You will need to create an account.
- `--model` the CycleGAN model is used for training
- `--batch_size` input batch size is 64
- `--gpu_ids` GPUs 0 and 1 are used for training. In case you are training on CPU, you need to write `--gpu_ids -1` (it'll just be very slow).

Models are saved every 5 epochs in `./checkpoints`, unless changed using the parameter `--checkpoints_dir`. All training options are detailed in `./options/base_options.py` and `./options/train_options.py`. 




#### 5. Training Outputs

Once the training is finished, you will find the outputs in  `./checkpoints` with the following directory structure:

```
checkpoints
├─ ukiyoe2photo
  ├─ 5_net_D_A.pth            # Discriminator D_A: G_A(A) vs. B
  ├─ 5_net_D_B.pth            # Discriminator D_B: G_B(B) vs. A
  ├─ 5_net_G_A.pth            # Generator G_A: A -> B
  ├─ 5_net_G_B.pth            # Generator G_B: B -> A
  ├─ ...
  ├─ web                      # intermediate results
    ├─ images
      ├─ epoch001_real_A.png  # source image of domain A (ukiyo-e)
      ├─ epoch001_real_B.png  # source image of domain B (photo)
      ├─ epoch001_fake_A.png  # G_B(B): mapping G_B converts image of domain B to image of domain A
      ├─ epoch001_fake_B.png  # G_A(A): mapping G_A converts image of domain A to image of domain B
      ├─ epoch001_idt_A.png   # G_A(B): mapping G_A converts image of domain B to image of domain B
      ├─ epoch001_idt_B.png   # G_B(A): mapping G_B converts image of domain A to image of domain A
      ├─ epoch001_rec_A.png   # G_B(G_A(A))
      ├─ epoch001_rec_B.png   # G_A(G_B(B))
      ├─ ... 
    └─ index.html             
  ├─ loss_log.txt             # log of losses per epoch and iteration
  └─ train_opt.txt            # training parameters
```


#### 6. Testing the Model

There are two options to test the model:

1.  two-way: this will transform the Ukiyo-e images to photos AND vice versa

    Simply use the following line (with your dataset and experiment name):

    ```
    python3 test.py --dataroot ./datasets/ukiyoe2photo/ --name ukiyoe2photo --model cycle_gan 
    ```
    By default, 50 test images are run. If you want to change that, you can simply add `--num_test 353`, with 353 the number of images you want to transform in both folders.


2.  one-way: this will transform the Ukiyo-e images to photos OR photos to Ukiyo-e style

    - if you want to transform A->B (Ukiyo-e to photos): 

      `cp ./checkpoints/ukiyoe2photo/latest_net_G_A.pth ./checkpoints/ukiyoe2photo/latest_net_G.pth`
    - if you want to transform B->A (photos to Ukiyo-e): 

      `cp ./checkpoints/ukiyoe2photo/latest_net_G_B.pth ./checkpoints/ukiyoe2photo/latest_net_G.pth`

    If I want to transform photos to the style of Ukiyo-e:
    ```
    python3 test.py --dataroot ./datasets/ukiyoe2photo/testB --name ukiyoe2photo 
    --model test --no_dropout
    ```



#### 7. Results

The test results are saved to a html file:  `./results/ukiyoe2photo/latest_test/`. Here are some examples of photos in `testB` folder transformer into ukiyoe:

<figure>
  <img src="{{site.baseurl}}/assets/img/tutorial_cyclegan/samples_result.png" class="figure-img img-fluid rounded" alt="" style="width:60%">
  <figcaption class="figure-caption text-center"> Some samples generated using the trained CycleGAN</figcaption>
</figure>

Dee was also wondering what photos of us would look like transformed:

<figure>
  <img src="{{site.baseurl}}/assets/img/tutorial_cyclegan/yan_dee.png" class="figure-img img-fluid rounded" alt="" style="width:70%">
  <figcaption class="figure-caption text-center"> Photos of us: Hasliberg, Switzerland (2022); Great Otway National Park, Australia.</figcaption>
</figure>



Here you go Dee... what a masterpiece.


<figure>
  <img src="{{site.baseurl}}/assets/img/tutorial_cyclegan/yan_dee_uki.png" class="figure-img img-fluid rounded" alt="" style="width:60%">
  <figcaption class="figure-caption text-center"> Photos of us in Ukiyo-e style, CycleGAN.</figcaption>
</figure>



#### 8. My Research

The focus of my research is in vehicle re-identification, which is  is a technology used in the field of computer vision. It involves recognizing and tracking vehicles across different cameras and locations. 

One of the datasets contains faces, which is not privacy compliant, and noises, showing timestamps with location and time related information.  I use CycleGAN to transform the images to the style of another dataset that does not have these issues. Here are some results:



<figure>
  <img src="{{site.baseurl}}/assets/img/tutorial_cyclegan/cyclegan_vreid.png" class="figure-img img-fluid rounded" alt="" style="width:60%">
  <figcaption class="figure-caption text-center"> Some transformed examples using CycleGAN on the vehicle dataset:
  <br>the trained model was able to blur faces, remove the background, and remove the noise (timestamps).</figcaption>
</figure>



#### Final Words
 
Have fun creating!

Thank you for reading,

Cheers,

Yan

----
#### Resource(s)

- [Official CycleGAN repo](https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix)
- [Official PyTorch CycleGAN tutorial](https://colab.research.google.com/github/junyanz/pytorch-CycleGAN-and-pix2pix/blob/master/CycleGAN.ipynb)


