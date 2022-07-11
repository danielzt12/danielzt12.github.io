---
layout: post
title:  "AI-enabled on-the-fly phase retrieval"
date:   2022-07-10 19:28:53 -0500
categories: latest_news
---
# Background
[Ptychography](https://en.wikipedia.org/wiki/Ptychography) is a high-resolution imaging method that has gained widespread application in [synchrontron](https://en.wikipedia.org/wiki/Synchrotron) x-ray and electron microscopy. Typical ptychographic measurement requires the scanning of a [coherent](https://en.wikipedia.org/wiki/Coherence_(physics)) beam across the sample. To ensure sufficient numerical constraint, the beam illuminated area must overlap with one another for each point of the scan. At the end of the scan, the two-dimensional detector images are then fed together into an interative optimizer for the [phase](https://en.wikipedia.org/wiki/Phase_problem) retrieval process.
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
<video src="https://user-images.githubusercontent.com/20727490/178176368-3216b2bf-65e3-44d1-9f55-b42ac557560b.mp4" controls="controls" style="max-width: 730px;"></video>
Here is an animated version of the workflow. Near the left bottom is a typical ptychographic measurement. A focused beam is being scanned on the sample with a spiral pattern producing, at each point, a diffraction pattern recorded on the detector. Those detector images are then saved on our data server, awaiting the phase retrieval process which begins on a local GPU at the end of each scan.
{: style="text-align: justify;"}

One of our original [idea](https://aip.scitation.org/doi/10.1063/5.0013065) was to use those experimentally retrieved phase to train the NN. By not relying on simulated training data, the inference results are more accurate, and the workflow becomes beamline/probe agnostic. So we prepare the training data in pairs of diffraction pattern + cropped retrieved phase, uploading them to a high performance computing [HPC](https://www.alcf.anl.gov/support-center/theta/theta-thetagpu-overview)cluster for the training, and download the trained model to a NVIDIA Jetson [AGX](https://developer.nvidia.com/embedded/jetson-agx-xavier-developer-kit) Xavier developer kit sitting next to the detector. The training model is further optimized using the [TensorRT](https://developer.nvidia.com/tensorrt) SDK.
{: style="text-align: justify;"}

The live inference part is actually more straightforward. The detector images are streamed directly to the edge device via the [pvacess](https://epics-controls.org/resources-and-support/documents/pvaccess/) protocol. Within microseconds of delay, the edge device predicts the real space image (phase), and streams it to the beamline computer. As you can imagine, the inference accuracy gets better as we improve the model by accumulating more training data. The online training loop stops when the inferred phase is as good as the reconstructed ones.
{: style="text-align: justify;"}

# Demonstration
<video src="https://user-images.githubusercontent.com/20727490/178196540-206126b5-9ab2-41fc-b5ad-6fd5efa40933.mp4" controls="controls" style="max-width: 730px;"></video>
This video was recorded directly on the beamline computer. You have, on the top left the live detector image, and on the top right the live inference result. The diffraction pattern (top left) is they aren't very useful unless analyzed.

# Max Framerate Inference

# Outlook

# Acknowledgement
Anakha V Babu (PI), Tao Zhou (moi), Anakha V Babu, Saugat Kandel, Tekin Bicer, Steven Henke, Yi Jiang, Ryan Chard, Yudong Yao, Sinisa Veseli, Zhengchun Liu, Ekaterina Sirazitdinova, Geetika Gupta, Martin V. Holt, Antonino Miceli, Mathew J. Cherukara
{: style="text-align: justify;"}
Work performed at the Center for Nanoscale Materials and Advanced Photon Source, both U.S. Department of Energy Office of Science User Facilities, was supported by the U.S. DOE, Office of Basic Energy Sciences, under Contract No. DE-AC02-06CH11357. 
{: style="text-align: justify;"}

