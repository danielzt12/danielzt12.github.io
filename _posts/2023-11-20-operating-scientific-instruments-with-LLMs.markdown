---
layout: post
title:  "augmenting scientific instrumentation with LLMs"
date:   2023-11-20 14:40:53 -0500
categories: latest_news
---
[Paper link](TBD)

CALMS is a retrieval and tool augmented large language model (LLM) to assist scientists, design experiments around and perform science using complex scientific instrumentation. In the following example we have demonstrated moving a diffractometer at the 34-ID-C beamline of the Advanced Photon Source with a single chat message.

Upon receiving the user prompt "Get the lattice parameters of WSe2 and set the instrument to peak 0 1 2", a first LLM call was made to retrieve the lattice constants of WSe2 through the Materials Project API. The LLM then interfaced with the spec<sup>TM</sup>, entering commands that will set the lattice constants with the retrieved values. It then requested a move of the motor to the user specified 012 Bragg reflection.

These calls were implemented via the LangChain’s “Structured input ReAct” framework. This library API allows developers to define the parameters and types for multi-parameter input and requires the model to use valid .json to perform a call.

<img width="946" alt="llm_fig4" src="https://github.com/danielzt12/danielzt12.github.io/assets/20727490/4ed31eac-71f5-43f0-b7ed-ed72fe8caae6">

# Demonstration
<video src="https://user-images.githubusercontent.com/20727490/284342340-91d8b8c6-9bac-4562-8b82-34e402064561.mp4" controls="controls" style="max-width: 730px;"></video>


