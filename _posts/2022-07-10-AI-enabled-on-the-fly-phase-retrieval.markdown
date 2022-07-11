---
layout: post
title:  "AI-enabled on-the-fly phase retrieval"
date:   2022-07-10 19:28:53 -0500
categories: latest news
---
# Background
[Ptychography](https://en.wikipedia.org/wiki/Ptychography) is a high-resolution imaging method that has gained widespread application in [synchrontron](https://en.wikipedia.org/wiki/Synchrotron) x-ray and electron microscopy. Typical ptychographic measurement requires the scanning of a [coherent](https://en.wikipedia.org/wiki/Coherence_(physics)) beam across the sample. To ensure sufficient numerical constraint, the beam illuminated area must overlap with one another for each point of the scan. At the end of the scan, the two-dimensional detector images are then fed together into an interative optimizer for the [phase](https://en.wikipedia.org/wiki/Phase_problem) retrieval process.

We [demonstrated](https://aip.scitation.org/doi/10.1063/5.0013065) a while back that if trained, a deep convolutional neural network (NN) can be used to replace the iterative optimizer. This approach has a few advantages. 
<ol>
  <li>NN inference is performed on a per image basis. That means we no longer need to wait till the end of the scan to perform phase retrieval. Instead, the phase of the illuminated area can be retrieved in real time as the new detector image comes in.</li>
  <li>Because each detector image can now be analyzed independently, spatial overlap of the illuminated areas is no longer required. This allows us to cover a larger area for the same amount of time, and to reduce the beam damage on the sample.</li>
</ol>

Below you can find some recordings of a demonstration we did on the Hard X-ray Nanoprobe (HXN) beamline co-managed by the Center for Nanoscale Materials (CNM) and the Advanced Photon Source (APS). To make things more interesting, the phase retrieval was performed on a palm-size [edge](https://en.wikipedia.org/wiki/Edge_computing) device.


# Workflow
<video src="https://user-images.githubusercontent.com/20727490/178176368-3216b2bf-65e3-44d1-9f55-b42ac557560b.mp4" controls="controls" style="max-width: 730px;">
</video>

We used the [NVIDIA Jetson AGX Xavier developer kit](https://developer.nvidia.com/embedded/jetson-agx-xavier-developer-kit) for the NN 

# Results

# Outlook

# Acknowledgement


