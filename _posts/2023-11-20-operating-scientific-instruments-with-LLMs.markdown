---
layout: post
title:  "operating scientific instruments with LLMs"
date:   2023-11-20 14:40:53 -0500
categories: latest_news
---
[Paper link](https://www.nature.com/articles/s41467-023-41496-z)

# Background
[Ptychography](https://en.wikipedia.org/wiki/Ptychography) is a super-resolution imaging method that has gained widespread application in [synchrontron](https://en.wikipedia.org/wiki/Synchrotron) x-ray and electron microscopy. Typical ptychographic measurement requires the scanning of a [coherent](https://en.wikipedia.org/wiki/Coherence_(physics)) beam across the sample. To ensure sufficient numerical constraint, the beam illuminated area must overlap with one another for each point of the scan. At the end of the scan, the two-dimensional detector images are then fed together into an iterative optimizer for the [phase](https://en.wikipedia.org/wiki/Phase_problem) retrieval process.
{: style="text-align: justify;"}

We [demonstrated](https://aip.scitation.org/doi/10.1063/5.0013065) a while back that if trained, a deep convolutional neural network (NN) can be used to replace the iterative optimizer. This approach has a few advantages. 
{: style="text-align: justify;"}
<ol>
  <li>NN inference is performed on a per image basis. That means we no longer need to wait till the end of the scan to perform phase retrieval. Instead, the phase of the illuminated area can be retrieved in real time as the new detector image comes in.</li>
  <li>Because each detector image can now be analyzed independently, spatial overlap of the illuminated areas is no longer required. This allows us to cover a larger area for the same amount of time, and to reduce the beam damage on the sample.</li>
</ol>
{: style="text-align: justify;"}

Below you can find some recordings of a demonstration we did on the Hard X-ray Nanoprobe (HXN) beamline co-managed by the Center for Nanoscale Materials ([CNM](https://www.anl.gov/cnm)) and the Advanced Photon Source ([APS](https://www.aps.anl.gov/)). To make it more interesting, we moved the phase retrieval task to a palm-size [edge](https://en.wikipedia.org/wiki/Edge_computing) device.
{: style="text-align: justify;"}

# Workflow
<video src="https://user-images.githubusercontent.com/20727490/282402032-4bfbd95b-2da0-46db-bc3c-ab0d51a39713.mp4" controls="controls" style="max-width: 730px;"></video>
Here is an animated version of the workflow. Near the left bottom is a typical ptychographic measurement. A focused x-ray beam is being scanned on the sample with a spiral pattern producing, at each point, a diffraction pattern recorded on the detector. Those detector images are then saved on our data server, awaiting the phase retrieval process which begins on a local GPU at the end of each scan.
{: style="text-align: justify;"}

One of our original [ideas](https://aip.scitation.org/doi/10.1063/5.0013065) was to use this experimentally retrieved phase to train the NN. By not relying on simulated training data, the inference results are more accurate, and the workflow becomes beamline/probe agnostic. So we prepare the training data in pairs of diffraction pattern + retrieved phase, upload them to a high performance computing [HPC](https://www.alcf.anl.gov/support-center/theta/theta-thetagpu-overview) cluster for the training, then download the trained model to a NVIDIA Jetson [AGX](https://developer.nvidia.com/embedded/jetson-agx-xavier-developer-kit) Xavier developer kit, the palm-sized edge device, sitting next to the detector. For the best performance, the training model is optimized using the [TensorRT](https://developer.nvidia.com/tensorrt) SDK.
{: style="text-align: justify;"}

The live inference part is actually more straightforward. The detector images are streamed directly to the edge device via the [pvacess](https://epics-controls.org/resources-and-support/documents/pvaccess/) protocol. Within microseconds of receiving the image, the edge device predicts the real space image (phase), and streams it to the beamline computer. As you can imagine, the inference accuracy gets better as we improve the model by accumulating more training data. The online training loop stops when the inferred phase is almost as good as the iteratively retrieved ones.
{: style="text-align: justify;"}

# Demonstration
<video src="https://user-images.githubusercontent.com/20727490/178196540-206126b5-9ab2-41fc-b5ad-6fd5efa40933.mp4" controls="controls" style="max-width: 730px;"></video>
This video was recorded directly on the beamline computer. You have, on the top left the live detector image (diffraction pattern), and on the top right the live inference result (phase). The diffraction patterns are in the [reciprocal](https://en.wikipedia.org/wiki/Reciprocal_lattice) space so they aren't very useful unless you are trained to read them. The inference results, however, are in real space, which means we can stitch them together to form a complete picture like a jigsaw puzzle. To do that, we simply put the inference results at coordinates indicated by the motor positions. It (bottom left) might look simple, almost reminiscent of pointing a flashlight at the sample in the dark, except that this would not have been possible without the NN. If we trace all the existing results together, we then obtain a sample image (bottom right) being updated in real time as the scan progresses.
{: style="text-align: justify;"}

What is also interesting is that the NN was not trained on regular shapes like these (numbers, vertical and horizontal lines). The training features were randomly etched patterns similar to the ones you see if you skip to the end of the video. Granted they are made of the same material with the same thickness which indicates they would have the same [refractive index](https://en.wikipedia.org/wiki/Refractive_index), it is still a pleasant surprise to us that the inference worked well even on dissimilar patterns.
{: style="text-align: justify;"}

# 2 KHz Inference
For the above experiment, we were only able to reach 100 Hz framerate. The bottleneck at the time was the 1 Gbps ethernet connection from the detector computer. For a detector with 516x516 pixels streaming 16 bit images at 100 Hz, the data rate is 0.5 Gbps, and we had to stream it both to the edge device (for live inference) and to the beamline computer (for live display).
{: style="text-align: justify;"}

More recently, we were able to break our own record by achieving live inference of ptychographic data at 2 kHz. To do so, we used the latest generation [Eiger](https://www.dectris.com/detectors/x-ray-detectors/eiger2/) 2 X detector which has a max framerate of exactly 2 kHz. We cropped the detector image to the size of 128x128 pixels to stay below 0.5 Gbps. And finally we also had to part with the edge device and use a more beefy machine for the inference. The demonstration is recorded in the video below. It turned out that streaming at 2 kHz was too fast for the live display widgets, so we had to calculate and print out the real framerate ourselves. It takes a few seconds at the start for the NN to come online, during which we have accumulated more than 10000 frames. Because the actual inference rate is higher than 2 kHz, the number of buffered frames was gradually reduced and the NN was able to catch up with the live data stream near the end of this video (the inference rate matches the framerate of the detector). 
{: style="text-align: justify;"}
<video src="https://user-images.githubusercontent.com/20727490/178209735-6204d707-8e75-4dc8-b549-195b48465414.mp4" controls="controls" style="max-width: 730px;"></video>

# Acknowledgement
Anakha V Babu (PI), Tao Zhou (moi), Saugat Kandel, Tekin Bicer, Steven Henke, Yi Jiang, Ryan Chard, Yudong Yao, Sinisa Veseli, Zhengchun Liu, Ekaterina Sirazitdinova, Geetika Gupta, Martin V. Holt, Antonino Miceli, Mathew J. Cherukara
{: style="text-align: justify;"}
Work performed at the Center for Nanoscale Materials and Advanced Photon Source, both U.S. Department of Energy Office of Science User Facilities, was supported by the U.S. DOE, Office of Basic Energy Sciences, under Contract No. DE-AC02-06CH11357. 
{: style="text-align: justify;"}


