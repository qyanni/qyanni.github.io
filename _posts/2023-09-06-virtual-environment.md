---
title: Virtual Environment
tags: [python cookbook]
image: 
style: 
color: 
description: How to set up virtual environment for Python using conda.
---


<figure>
  <img src="{{site.baseurl}}/assets/img/post_virtualenv/jan-antonin-kolar-lRoX0shwjUQ-unsplash.jpg" class="figure-img img-fluid rounded" alt="" style="width:50%">
  <figcaption class="figure-caption text-center"> Photo by <a href="https://unsplash.com/@jankolar?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Jan Antonin Kolar</a> on <a href="https://unsplash.com/photos/lRoX0shwjUQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
   </figcaption>
</figure>


Creating virtual environments helps keep things tidy and organized, making it easier for you to manage your projects.

####  1. What is a virtual environment?

A virtual environment is like an isolated box where you can install and use specific Python libraries and packages for a particular project. This way, you can work on multiple projects with different Python libraries and dependencies, without interfering with each other.


####  2. Create your virtual environment


Creating a virtual environment is very straightforward. In this post, I will show you how to create one using conda:

```
conda create --name my_virtual_env
```

Note: I suggest you to choose a name that you can remember. Avoid using names for your environment such as,  `env1`, `env2`, etc.

Follow the instructions as indicated in the terminal. You need to type in `y` and hit `enter`.

####  3. Activate your virtual environment

To activate your virtual environment:
```
conda activate my_virtual_env
```

When activated your virtual environment, you will see the name in the brackets change:

```
(base) yan@yanni directory$ conda activate my_virtual_env
(my_virtual_env) yan@yanni directory$
```

####  4. Install packages in your virtual environment

Once activated, you can install packages into it. 


####  5. Deactivate your virtual environment

Finally, once done, you can deactive your virtual environment:

```
conda deactivate   
```

####  6. Forgetting the name of your virtual environment

In case you forgot the name of your virtual environment, you can just type in the following to show the list of virtual environments:
```
conda env list
```


####  7. Deleting your virtual environment

To delete your virtual environment:
```
conda remove -n my_virtual_env --all
```



#### Final Words
 

Enjoy creating your projects in a tidy and organised way!


___


##### Resource(s)

- [Conda - Managing environments](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#)


