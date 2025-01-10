---
layout: post
title: Preprocessing for Functional Connectivity
subtitle: Mathematical formulation of the FC hypotheses
cover-img: post-02_preprocessing_FC_cover.png
thumbnail-img: /assets/img/post-02_preprocessing_FC_thumb.png
share-img: post-02_preprocessing_FC_thumb.png
tags: [implementation, neuroimaging, functional connectivity]
author: JC Mariani
mathjax: true
---

This post is adapted from the introduction of my PhD thesis. Full version can be find [here](https://theses.hal.science/tel-04420129).

# Introduction

In this section I detail the methodological aspects regarding the analysis of **Functional Connectivity (FC)** patterns. In the last $$40$$ years, from the first methods developed for **Positon Emission Tomography (PET)** imaging, the field of neuroimaging has strongly standardised its approach. As a result, a set of shared practices has emerged. We present here the theoretical framework behind each of the steps used to compute FC measures. One of the pioneering team in this effort is probably *Karl Friston*'s which proposed the **Statistical Parametric Mapping (SPM)** software, still broadly used in many publications. Most of the step I described below are inspired from this seminal work. These main steps of the analysis, which can be generalised to most analyses of haemodynamics, can be enumerated as follow:


1. **Standardisation** is the first step of the analysis which consists in structuring the dataset. Do not take this as granted, proper standardisation can save you hours of work. If you are interested, don't hesitate to check the [fUSI-BIDS extension proposal document](https://docs.google.com/document/d/1W3z01mf1E8cfg_OY7ZGqeUeOKv659jCHQBXavtmT-T8/edit?usp=sharing).
2. **Registration** consists in aligning all images in a common coordinate system. To do so a common space must be defined, *i.e.* a reference image. Then, a transform between the image and the reference must be estimated. Finally, the image is transformed to be mapped on this referential. The result of this transform shall ensure equivalence between the same voxels of two different images.
3. **Level 1**, is the model at the individual level. It encodes the hypothesis used to build an object of interest. This object of interest is an observable, with usually reduced degrees of freedom as compared to the initial image. This object represents the quantity studied associated to each subject, in our case connectomes.
4. **Level 2**, is the model at the population level. It gathers the objects of interest computed at the level $$1$$ and estimates the significance of their difference after gathering them in groups of interest. In other terms it is the measure of the effect based on the hypothesis.

We will focus in this document on the last two steps in the case of FC. We remind here that FC results are observables of dynamical properties of the brain function. Based on a generative model of brain dynamics, from neuronal activity to neurovascular coupling, we expect covariance structures to emerge between individual timeseries of our image voxels. In easier terms some voxels tend to covary, we assume these voxels to be connected, FC is the metrics of these covariance structures. Under a stationarity hypothesis we can measure this covariance (over typically 10min windows) with the Pearson's correlation coefficient (most classically). Based on the model of FC we can interprete this observable (correlation between voxels or parcels) as connectivity between regions of the brain. As such, it is critical to remember that FC can be influenced by any stage of the model. Therefore, interpretation of FC changes must be always carried with caution as a drop of connectivity could mean loss of information transfer between two regions but also reduction of neurovascular coupling or network influence on regional dynamics. The interpretation of these FC variations is an open field of research as conventional (static) FC distil many aspects of the dynamics in a powerfull but simplified metrics.

# FC model

The model of FC is detailed in <a href="https://JCMariani.github.io/2024-07-27-FC.html">another page of this website</a>. I summarize here the different steps of the reasoning behind the metric.

1. The brain is a complex network of neurons which acts as integrator of information that propagates along axons and dendrite
    - This network possess scaling properties which allow to identify more or less homogeneous regions
    - These regions can be approximated as a local interaction between two populations of excitatory and inhibitory neurons which result in a local dynamic of the network that can be modelled as a dynamical system driven by differential equations (like an oscillator) [Deco 2011]
    - There exist long range connections between these regions which introduces a coupling between the different regions
3. The vascular system densely perfuses the brain tissue, and blood flow is locally enslaved by neuronal activity as a low pass transfer function with a characteristic time of a few seconds
    - In a feedforward model local activity of neurons results in an increase of blood perfusion to maintain homeosthasis through the neurovascular coupling [Iadecola 2017, Schaeffer et Iadecola 2021]
    - Alternatively other models of neurovascular coupling propose that oscillations of local neuronal population (gamma oscillations) entrain vasomotion fluctuations [Mateo et al. 2017]
    - Overall we make the approximation (independently of the direct mechanism) that if we observe covariance in the haemodynamic signal, due to the neuronal drive over blood flow, this feature translates neuronal coupling. This point is critical and requires that no other source of covariance exists in our signal, hence the decades of research in the best way to denoise timeseries. 

Using this framework, if two regions possess a strong "connectivity", as formulated by the Hebbian principle, it is assumed that they would share some synchronous neuronal activity. Finally, if two regions haemodynamic signals are synchronous, as these signals are driven by neuronal activity they must be connected.

<img src="https://JCMariani.github.io/assets/img/post-02_introduction_FC_principle.png" alt="drawing" class="center"/>
<small>**Fig1.** Illustration of the FC model. Local population of excitatory and inhibitory neurons drive local blood flow. Long range connectivity drive synchronicity of between brain regions inducing correlation in haemodynamic signal. Middle, example of CBV bilateral correlation between two two regions of the rat cortex. Top at the baseline, bottom after injection of cannabinoids which perturb the synchronicity.</small>

# First Level analysis

As a consequence of the first section we can conclude that a good metric for in-between regions connectivity is the measure of correlation of blood dynamics in a slow regime due to latency of the vascular system. More formally speaking FC can be defined as the measure of second order statistics of the haemodynamic signal. We can use the **Cerebral Blood Volume (CBV)** filtered in the infra-slow regime ($$<0.1Hz$$), as any higher frequency could not come from slow neurovascular coupling and can not be explained by neuronal source. In addition, we must eliminate alternative sources of slow covariance that are independent of neuronal activity. Otherwise, the FC could be artificially inflated, as demonstrated by studies showing the influence of motion or cardiac and respiratory rhythms [Power et al 2017]. In the end, the first part of the model we make at the first level is that the signal of interest can be decomposed as:


