---
layout: post
title: Principle of Functional Connectivity
subtitle: From the neuronal network to brain dynamics
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/thumb.png
share-img: /assets/img/path.jpg
tags: [theory, neuroimaging, functional connectivity]
author: JC Mariani
---

This post is adapted from the introduction of my PhD thesis. Full version can be find [here](https://theses.hal.science/tel-04420129).

# Functional Connectivity

As we described earlier, the field of neuroimaging inherits from late XIX<sup>th</sup> century advances in physiology and neurosciences. It relies on successive fundamental findings in the physiology of brain cells, coupled with technological advances that allowed to measure their activity. These advances have highlighted the existence of an integrative neuronal network whose understanding bloomed during the XX<sup>th</sup> century. This sections presents how, from the network essence of brain structure, functional connectivity has been operatively defined as the measure of covariance in haemodynamic signals \cite{Friston_93}.

## Principle

### The neuronal network


In its lectures from the $$30s$$, \cite{Sherrington_1934} describes the neuronal organisation complexity. In this description, the brain and its constitutive neurons are the centre of movement control and sensory integration. *Sherrington* argues that a combination of excitatory and inhibitory neurons entangled in a complex fashion gives rise to the diversity of behaviours observed in animals. A key component in *Sherrington*'s description is the "patterned" nature of neuronal network. As surprising as it may seem, the neuronal network dynamical essence is already described. Overall, *Sherrington* describes a vast collection of excitatory and inhibitory neurons whose rule of connection and its evolution in time is the source of behaviour. 


Later on, neurophysiologists focalised on understanding the rules which govern the neuronal network. A main actor in this effort has been *Donald Hebb* with his famous *Hebbian law* which is usually stated as: "What fire together wire together". This concept is probably underlying most of the work on neuronal plasticity and remains a central concept in the study of memory. By this description, *Hebb* gives an insight of how the coupling strength between nodes in the network encodes information, and a simple rule of its evolution which through the integrative property of the network can explain complex principles as memory consolidation or loss.


<img src="https://JCMariani.github.io/assets/img/post-00_Intro_network.svg" alt="drawing" width="75%" class="center"/>

^**Fig1.** Illustration of the neuronal network from \cite{Getting_1989}. Left: individual building blocks of the network (triangle: excitation, dots: inhibition). Right: disctinction between anatomical and functional networks. Top: Example of anatomical network in \emph{Tritonia} escape swiming network. Bottom: two examples of functional network, whether *C2* is deactivated or not.^


Another seminal piece of work comes from *Vernon Mountcastle* whose network structure and dynamics description is summarised in \cite{Mountcastle_1978}. In this review, after introducing the evolutionary meaning of such construct *Mountcastle* describes the cortical column as the functional unit of the neocortex. Microcolumns are vertical juxtaposition of neuronal cells across layers resulting in a cylinder of $$\sim30\mu m$$ diameter for a $$2500\mu m$$ height (for a volume containing approximately $$110$$ cells). He argues based on data from the different cortical regions that the cortex is composed of a mosaic of such microcolumns which reproduce the same circuit. Although, they must not be seen as isolated one from the others, but as an horizontally connected network, even though vertical connections prevail. In the end, the micro columns can be clustered together into minicolumns ($$\sim 500\mu m$$ of diameter) that are commonly associated to function. Without going too much in the details of his findings, a key message *Mountcastle* gives in his discussion is, the modular structure of brain network. By describing the cortex as this assembly of parallel processing units he defines the brain as a distributed network. Scale invariance of an integrative brain model is at the heart of this description. Micro modules build mini modules which make cortical areas. Once again, he concludes by highlighting the dynamical nature of such a network.


\cite{Gerstein_1989} integrates these three historical physiological breakthroughs, and others, to define the concept of "neuronal assembly". In his review he emphasises the abstraction level of this concept. It is such abstractions which are well vulgarised in \cite{changeux_2006}, where *concepts* and *percepts* interact in their physical substract that is the neuronal network. This framework is also formalised in \cite{Getting_1989}. Here, a *hardwire* hypothesis is used, which stipulates that physical connections, or the anatomical network, is assumed fixed. This anatomical network can be described at the lowest level by *building blocks* (typical connections between $$2$$ to $$3$$ neurons). *Getting* argues that from the same network, multiple functions can arise based on modulation of the building block. By tuning the activity of the building blocks, the same elemental bricks can behave differently at the network level. It justifies again the efficiency of such an architecture in engendering complex behaviours in an economic way. Such modulation of the network defines *modes*. "*Each mode represents the functional organisation of the network that gives rise to a function or task*". Finally, in this set of neurons interconnected in a specific mode, at any given time (where the timescale must be defined) the activated part of the network defines a *state*. Past this formal description, \cite{Getting_1989} also describes the practical aspect of measuring such neuronal assemblies. This work with all the studies it inspires from probably build the fundamental bases of modern brain connectomics. Since then models have been proposed to explain resilience of the network and optimised energetic expenditure, among them the small world hypothesis \cite{Bassett_2006} seems quite consensual.


###The functional network
