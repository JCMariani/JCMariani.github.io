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

In this section I detail the methodological aspects regarding the analysis of **Functional Connectivity (FC)** patterns. In the last $$40$$ years, from the first methods developed for **Positon Emission Tomography (PET)** imaging, the field of neuroimaging has strongly standardised its approach. As a result, a set of shared practices has emerged. We present here the theoretical framework behind each of the steps used to compute FC measures. One of the pioneer team in this effort is probably *Karl Friston*'s which proposed the **Statistical Parametric Mapping (SPM)** software, still broadly used in many publications. Most of the step I described below are inspired from this seminal work. The main steps of the analysis can be enumerated as follow:


\begin{enumerate}
    \item \textsc{Standardisation} is the first step of the analysis which consists in structuring the dataset. As a particular effort has been given to this step, a specific section is dedicated to this method later (see \niceRef{ssec:Data_Structuring}).
    \item \textsc{Registration} consists in aligning all the images in a common coordinate system. To do so a common space must be defined, \emph{i.e.} a reference image. Then, a transform between the image and the reference must be estimated. Finally, the image is transformed to be mapped on this referential. The result of this transform shall ensure equivalence between the same voxels of two different images.
    \item \textsc{Level 1}, as we saw, is the model at the individual level. It encodes the hypothesis used to build an object of interest. This object of interest is an observable, with usually reduced degrees of freedom as compared to the initial image. This object represents the quantity studied associated to each subject, in our case connectomes.
    \item \textsc{Level 2}, is the model at the population level. It gathers the objects of interest computed at the level $$1$$ and estimates the significance of their difference after gathering them in groups of interest. In other terms it is the measure of the effect based on the hypothesis.
\end{enumerate}

    
    \subsection{Analysis pipelines}
    \label{ssec::intro-analysis-pipelines}


Here are presented the theoretical frameworks for each of these steps.
    

        \subsubsection{Registration}
        \label{sssec::intro-registration}


\begin{figure}[ht]
\begin{center}
    \includesvg[width = \linewidth]{Content/01_Introduction/01_Introduction_Figures/Intro_Registration.svg}
    \caption{Illustration of registration transform. Left: Different types of geometric transforms. Similarities preserve angles and ratios, affine preserve paralellism, projections preserve colinearities, conformation preserve angles, equiareal transforms preserve the area, homeomorphism preserves neighbourhood and diffeomorphism are invertible bidifferentiable transforms. Right (adapted from \href{Elastix manual}{https://elastix.lumc.nl/download/elastix-5.0.1-manual.pdf}): Example of registration of an \Gls{fmri} image, top left: the reference, top right the image, bottom left: affine transform of the moving image on the fixed image, bottom right: diffeomorphism solution for the same images.}
    \label{fig:registration}
\end{center}
\end{figure}

        
The registration problem is formally described in \cite{Klein2009-oc}. Taking one reference image called the \emph{fixed image} or $$I_F$$ and one image to be registered called the \emph{moving image} or $$I_M$$, the registration is solved by finding the optimal geometrical transform $$T_{\hat{\mu}}$$, parametrised with $$\hat{\mu}$$, aligning $$I_M$$ on $$I_F$$. Formally we look for the set of parameters $$\hat{\mu}$$ such as:

\begin{align}
\hat{\mu} = \underset{\mu}{argmin}\{ \mathcal{C}(T_{\mu},I_F,I_M) \}
\end{align}

Where $$\mathcal{C}$$ is a cost function defining the \guillemotleft distance\guillemotright between $$I_F$$ and $$I_M$$. In order to solve this problem, one must chose properly: the reference image, the family of transform, the cost function and the most adapted optimiser. 


For the reference image, common templates can be found in the case of \gls{fmri}: \cite{Mazziotta_2001} for humans, \cite{Ma_2005} for mice. These templates are averages over tens to hundreds of co-registered images which represents a canonical image. These templates are expected to possess all the features required for the registration algorithm to converge to it, starting from any other image. 


Without going too much in the details of transforms, two classes can be distinguished. On the one side \guillemotleft \textbf{linear} \guillemotright transforms on the other side \guillemotleft \textbf{non linear} \guillemotright transforms. Intuitively the \emph{linear} class is the one that preserves straight lines, the different types of transforms are illustrated in \niceFigRef{fig:registration}. The members of this first class are usually referred to as \emph{affine} transforms, even though projective transforms could fit as well. The \emph{non linear} class are usually referring to general diffeomorphisms which allow local deformation of the image. 


Similarly, for the cost function various options are could be chose. The cost function is usually based on a distance like $$L_2$$ euclidean distance but can be also correlation based of other similar metrics. The most common one is probably the mutual information.

\begin{align}
MI(I_F, I_M) = \sum_{i\in \mathcal{I},j\in \mathcal{J}} P_{(I_F,I_M)}(i,j)log(\frac{P_{(I_M, I_F)}(i,j)}{P_{I_M}(i) P_{I_F}(j)})
\end{align}

Where $$(i,j)\forall i\in \mathcal{I},\ \forall j\in \mathcal{J}$$ are values of paired voxels, $$P_{(I_F,I_M)}$$ is the join probability mass function and $$P_{I_M}(i)$$, $$ P_{I_F}(j)$$ are the respective intensity probability for each image. For the choice of optimisers see \cite{Klein_2007} where the main algorithms are compared in the case of non linear transform and mutual information function.


It is interesting to highlight that in practice, the direct tranform is not computed but rather the one from fixed image to the moving image, which is later inversed. Because images are finite, the risk of the direct transform would be to lack antecent in the moving image. In other sense, by mapping the moving image on the fixed image, some voxels would not be missing. This risk is minimised by performing the inverse transform \cite{Klein2009-oc}. 

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
        
