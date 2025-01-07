---
layout: post
title: Preprocessing for Functional Connectivity
subtitle: Mathematical formulation of the FC hypotheses
cover-img: /assets/img/post-02_preprocessing_FC_cover.png
thumbnail-img: /assets/img/post-02_preprocessing_FC_thumb.png
share-img: /assets/img/post-02_preprocessing_FC_cover.png
tags: [implementation, neuroimaging, functional connectivity]
author: JC Mariani
mathjax: true
---

This post is adapted from the introduction of my PhD thesis. Full version can be find [here](https://theses.hal.science/tel-04420129).

# Introduction

In this section I detail the methodological aspects regarding the analysis of **Functional Connectivity (FC)** patterns. In the last $$40$$ years, from the first methods developed for **Positon Emission Tomography (PET)** imaging, the field of neuroimaging has strongly standardised its approach. As a result, a set of shared practices has emerged. We present here the theoretical framework behind each of the steps used to compute FC measures. One of the pioneering team in this effort is probably *Karl Friston*'s which proposed the **Statistical Parametric Mapping (SPM)** software, still broadly used in many publications. Most of the step I described below are inspired from this seminal work. These main steps of the analysis, which can be generalised to most analyses of haemodynamics, can be enumerated as follow:


1. **Standardisation** is the first step of the analysis which consists in structuring the dataset. If you are interested, don't hesitate to check the [fUSI-BIDS extension proposal document](https://docs.google.com/document/d/1W3z01mf1E8cfg_OY7ZGqeUeOKv659jCHQBXavtmT-T8/edit?usp=sharing).
2. **Registration** consists in aligning all images in a common coordinate system. To do so a common space must be defined, *i.e.* a reference image. Then, a transform between the image and the reference must be estimated. Finally, the image is transformed to be mapped on this referential. The result of this transform shall ensure equivalence between the same voxels of two different images.
3. **Level 1**, is the model at the individual level. It encodes the hypothesis used to build an object of interest. This object of interest is an observable, with usually reduced degrees of freedom as compared to the initial image. This object represents the quantity studied associated to each subject, in our case connectomes.
4. **Level 2**, is the model at the population level. It gathers the objects of interest computed at the level $$1$$ and estimates the significance of their difference after gathering them in groups of interest. In other terms it is the measure of the effect based on the hypothesis.

We will focus in this document on the last two steps in the case of FC. We remind here that FC results are observables of dynamical properties of the brain function. Based on a generative model of brain dynamics, from neuronal activity to neurovascular coupling, we expect covariance structures to emerge between individual timeseries of our image voxels. Under a stationarity hypothesis we can measure this covariance (over typically 10min windows) with the Pearson's correlation coefficient (most classically). Based on the model of FC we can interprete this observable (correlation between voxels or parcels) as connectivity between regions of the brain. As such, it is critical to remember that FC can be influenced by any stage of the model. Therefore, interpretation of FC changes must be always carried with caution as a drop of connectivity could mean loss of information transfer between two regions but also reduction of neurovascular coupling or network influence on regional dynamics. The interpretation of these FC variations is an open field of research as conventional (static) FC distil many aspects of the dynamics in a powerfull but simplified metrics.

# FC model

The model of FC is detailed in another page of this website. I summarize here the different steps of the reasoning behind the metric.

1. The brain is a complex network of neurons which acts as integrator of information that propagates along axons and dendrite
    - This network possess scaling properties which allow to identify more or less homogeneous regions
    - These regions can be approximated as a local interaction between two populations of excitatory and inhibitory neurons which result in a local dynamic of the network that can be modelled as a dynamical system driven by differential equations (like an oscillator) [Deco 2011]
    - There exist long range connections between these regions which introduces a coupling between the different regions
3. The vascular system densely perfuses the brain tissue and blood flow is locally enslaved by neuronal activity as a low pass transfer function with a characteristic time of a few seconds
    - In a feedforward model local activity of neurons results in an increase of blood perfusion to maintain homeosthasis through the neurovascular coupling [Iadecola 2017, Schaeffer et Iadecola 2021]
    - Alternatively other models of neurovascular coupling propose that oscillations of local neuronal population (gamma oscillations) entrain vasomotion fluctuations [Mateo et al. 2017]

Using this framework, if two regions possess a strong "connectivity", as formulated by the Hebbian principle, it is assumed that they would share some synchronous neuronal activity. Finally, if two regions haemodynamic signals are synchronous, as these signals are driven by neuronal activity they must be connected.

<img src="https://JCMariani.github.io/assets/img/post-02_preprocessing_FC_cover.png" alt="drawing" class="center"/>
<small>**Fig1.** Illustration of the FC model. Local population of excitatory and inhibitory neurons drive local blood flow. Long range connectivity drive synchronicity of between brain regions inducing correlation in haemodynamic signal. Middle, example of CBV bilateral correlation between two two regions of the rat cortex. Top at the baseline, bottom after injection of cannabinoids which perturb the synchronicity.</small>

<!---    

        \subsubsection{First Level}
        \label{sssec::intro-first-level}


The first level analysis has already be conceptually introduced in the first section of this document. Here are reminded the hypotheses we make, the objects we use and their formal definition. 


Functional connectivity is the measure of second order statistics of the haemodynamic signal. It was demonstrated that infra-slow oscillations of the \Gls{cbv} timecourse is the ideal spectral window where to measure the \Gls{fc}. Moreover, during acquisition, many confounds have been identified. These artefactual signals bias the estimate of \Gls{fc}. In the end, the first part of the model we make at the first level is that the signal of interest can be decomposed as:

\begin{align}
y = y_{FC} + y_{confounds} + \epsilon
\label{eq:linear-signa-confound}
\end{align}

Where $$y$$ is the signal, $$y_{FC}$$ the neuronally driven \Gls{fc} oscillation in the infra-slow band, $$y_{confounds}$$ a linear combination of coherent artefactual sources and $$\epsilon$$ some white noise. In other terms, we hypothesise that it is possible to extract a good estimate of neuronally driven vascular fluctuations by removing confounding nuisances and filtering in the infra-slow band. It is why developing optimal methods for nuisance removal has become a main subfield of neuroimaging. This question is later discussed in \niceRef{ssec::nuisance}. In the meantime it is worth mentioning that the main nuisance removal strategies are: detrend for removing slow drifts expected to come from hardware sources \cite{Liu_2017}, cardiac and breathing regression \cite{Bhattacharyya_2004, Shmueli_2007, Liu_2016}, motion regression \cite{Friston_95}, the global signal regression \cite{Power_2017}, principal component based regression \cite{Behzadi_2007}, censoring of artefactual frames or scrubbing \cite{Power_2012}.

Finally, given $$y_{FC}$$ we hypothesise that the measure of covariance in two signals gives is proportional to, or at least monotonous in, the underlying functional connectivity of the regions it was extracted from. In our case we mostly used the \emph{Pearson}'s correlation coefficient:

\begin{align}
c(i,j) = \frac{<y_i, y_j>}{\sqrt{<y_i, y_i><y_j, y_j>}}
\end{align}
    
Where $$y_i$$ and $$y_j$$ are two signals respectively extracted from region $$i$$ and region $$j$$ if $$y_i$$ and $$y_j$$ are centred (null mean).


Under validity of the model, by combining these two steps we can infer the connectivity between two regions. In practice two canonical objects are used to gather these properties of the brain. With correlation matrices \Gls{roi}s are defined, either based on the brain anatomy or functional criterion (identification of regional clusters or \gls{ica} based). The signals are extracted from these \Gls{roi}s and the connectome encoded in a correlation matrix which represents pair wise correlation. Alternatively we can define one signal of reference, called the seed, and measure the connectivity of any other voxel with this seed. The seed signal is usually also extracted from a \Gls{roi} in which case we refer the voxel wise correlation distribution as seed based map. Alternatively the seed can be artificial in which case the object is an activation map. 


Alternatively to correlation based establishment of connectomes, in this later case, it possible to use a general linear model to apply all these processing steps all at once. The idea is to leverage \ref{eq:linear-signa-confound} linear property to infer directly the covariance structure of each voxel. Using this method \cite{Friston_1996}, it is possible to regress multiple regressors at ones while, modelling the residual term for removing autocorrelation structure from the observed effect for example. As a result the \Gls{glm} can be used to compute seed based maps or activation maps. In this case \ref{eq:linear-signa-confound} becomes:


\begin{align}
y = \sum_{i} \beta_i y_i + \epsilon
\label{eq:linear-GLM}
\end{align}

Where $$y_i$$ are the different regressors used for the regression. Usually a single regressor is associated to a seed of interest, while the other represent noise. It is interesting to notice that by introducing a family of cosine regressors, high pass filtering can be performed simultaneously. In the end, the measure of covariance with the seed is given by the value $$\beta$$ for this regressor.


        \subsubsection{Second Level}
        \label{sssec::intro-level2}


As we illustrated in the introduction, the further we go in the analysis, the more options are available. Consequently, there is no way to even get close to an exhaustive list of potential second levels. We will therefore only remind the fundamental concept which is shared with most other scientific areas. From the first level we obtain a descriptor of each individual in the shape of correlation matrix or a seed based map. The question we ask at the group level is whether two such collections are different, and if it is the case what are these differences. The classical methods for correlation matrices and seed based maps rely on mass univariate approaches. All voxels are independently compared between the two groups. This method has pitfalls which are detailed in \niceRef{sssec:ParallelTTesting}. Alternatively multivariate analysis could be used like \Gls{pca} for example.


In the end, the method to use depends on the question asked. It is the real translation of the working hypothesis in terms of statistics. If the effect is expected to have multilinear basis, an other \Gls{glm} could be used. With the advances in machine learning and related tools, it becomes more common to use predictors and classifiers to perform such second level statistics. 


        \subsubsection{Summary}


We showed here how the concepts introduced in the previous chapter could be translated into mathematical formulations. Neuroimaging methods allows the measure of blood flow properties which ultimately is digitalised. From these formalised images, a succession of programs and algorithms permit to apply the fundamental principles of functional connectivity. By this succession of transforms, the image is ouput as simple objects that can be studied in the frameworks of classical statistics. The description of our implementation is detailed in the method part.
        
-->
