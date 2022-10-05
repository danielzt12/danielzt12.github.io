---
layout: post
title:  "AI-enabled smart scanning"
date:   2022-10-05 17:23:00
categories: latest_news
---
## Background
Recent rapid developments in deep learning methods has meant that artificial intelligence (AI) 
techniques are now finding applications in all domains of science and technology. An interesting
such application is ***autonomous experiments***, where an algorithm adaptively, through
neural networks (NN) or other AI techniques,  makes data-driven
decisions on where and/or how to conduct the experiment next --- decisions that would typically
require human intervention. This AI-based experimental control has the potential to remove the 
need for constant human supervision in experiments, and even make optimal choices in 
parameter spaces that are too large for humans to easily contextualize. 
They have the potential to revolutionize experimental design in imaging, materials characterization, 
and many other scientific fields.


In this work, we implemented an autonomous (or smart) scanning microscopy method that adaptively 
decides which regions of a sample are of most interest, and directs the scanning towards 
these regions. While our specific workflow is designed for a synchrotron-based scanning Bragg x-ray 
microscopy experiment, it is straightforward to apply in other scanning microscopy experiments. 
The following section contains a video, recorded during the experiment itself, that
shows our method in action. A detailed description of the technique is being prepared as a manuscript --- 
we will upload it here once the manuscript is ready for submisison.

## Workflow and Demonstration

Our smart scanning algorithm consists of roughly three steps: i) initialization, ii) NN-based prediction,
and iii) scan route planning. For the initialization, we randomly 
select $1\%$ of the scanning area and conduct the measurement at these points. We then feed these measurements
to the [SLADS-Net](https://arxiv.org/abs/1803.02972) [1] NN-based method 
that identifies the most uncertain unmeasured points given the measurement state. Next, we take a batch of 
50 of these uncertain points and use a route planning method to 
calculate the scan path that minimizes the travel time required between these measurements. 
After measuring these points, we repeat the NN-based prediction step and continue the algorithm until 
the image reconstructed from these measurements stops improving.


For this scanning Bragg x-ray microscopy experiment, the measurement is acquired by recording a diffraction pattern,
and counting the total number of photons in this diffraction pattern.

# Video illustration

The following video contains an illustrated example of the scanning procedure:
<video src="/videos/smart_new_2_edited.mp4" controls="controls" style="max-width: 730px;"></video>

And a slightly slower version without the illustration:

<video src="/videos/smart7_edited.mp4" controls="controls" style="max-width: 730px;"></video>


## References
[1] Zhang et al. Electronic Imaging, 2018, 15, 131. URL: [https://arxiv.org/abs/1803.02972](https://arxiv.org/abs/1803.02972)
