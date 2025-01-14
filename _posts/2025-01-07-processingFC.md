---
layout: post
title: Preprocessing for Functional Connectivity
subtitle: Mathematical formulation of the FC hypotheses
cover-img: /assets/img/post-02_preprocessing_FC_cover.png
thumbnail-img: /assets/img/post-02_preprocessing_FC_thumb.png
share-img: /assets/img/post-02_preprocessing_FC_thumb.png
tags: [implementation, neuroimaging, functional connectivity]
author: JC Mariani
mathjax: true
permalink: /processingFC.html
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

The model of FC is detailed in [another page of this website](http://jcmariani.github.io/principleFC.html). I summarize here the different steps of the reasoning behind the metric.

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

$$y = y_{FC} + y_{confounds} + \epsilon$$

Where $$y$$ is the signal, $$y_{FC}$$ the neuronally driven FC oscillation in the infra-slow band, $$y_{confounds}$$ a linear combination of coherent artefactual sources and $$\epsilon$$ some white noise. In other terms, we hypothesise that it is possible to extract a good estimate of neuronally driven vascular fluctuations by removing confounding nuisances and filtering in the infra-slow band. It is why developing optimal methods for nuisance removal has become a main subfield of neuroimaging. This question, ad most notably the motion artefact, are further discussed below. In the meantime it is worth mentioning that the main nuisance removal strategies are: detrend for removing slow drifts expected to come from hardware sources [Liu_2017], cardiac and breathing regression [Bhattacharyya_2004, Shmueli_2007, Liu_2016], motion regression [Friston_95], the global signal regression [Power_2017], principal component based regression [Behzadi_2007], censoring of artefactual frames or scrubbing [Power_2012].

Finally, given $$y_{FC}$$ we hypothesise that the measure of covariance in two signals is proportional to, or at least monotonous in, the underlying functional connectivity of the regions it was extracted from. For this purpose the most metric is probably the \emph{Pearson}'s correlation coefficient (if you are curious [Liu 2024](https://www.biorxiv.org/content/10.1101/2024.05.07.593018v1) lists 238 alternative pairwise interaction statistics):

$$c(i,j) = \frac{<y_i, y_j>}{\sqrt{<y_i, y_i><y_j, y_j>}}$$

Where $$y_i$$ and $$y_j$$ are two signals respectively extracted from region $$i$$ and region $$j$$ if $$y_i$$ and $$y_j$$ are centred (null mean).

Under validity of the model, by combining these two steps we can infer the connectivity between two regions. In practice two canonical objects are used to gather these properties of the brain. With correlation matrices **Region of Interest (ROI)** are defined, either based on the brain anatomy or functional criterion (identification of regional clusters or **Independent Component Analysis (ICA)** based). The signals are extracted from these ROIs and the connectome encoded in a **Correlation Matrix (CM)** which represents pair wise correlations. Alternatively we can define one signal of reference, called the seed, and measure the connectivity of any other voxel with this seed. The seed signal is usually also extracted from a ROI in which case we refer the voxel wise correlation distribution as a **Seed Based Map (SBM)**. Alternatively the seed can be artificial in which case the object is usually referred to as an activation or correlation map. 

# Second Level

The further down we go in the analysis, the more options are available. Consequently, there is no way to even get close to an exhaustive list of potential second levels. As a result, here, we only remind the fundamental concept, which is also shared with most other scientific areas, we also give below suggestion to implement your own statistical pipeline. 

From the first level we obtain a descriptor of each individual in the shape of a CM or a SBM. The question we ask at the group level is whether two such collections are different within a population, and if it is the case, what are these differences. The classical methods for CM and SBM rely on mass univariate approaches. All voxels are independently compared between the two groups. This method has pitfalls which are more detailed below. Alternatively, multivariate analysis could be used like **Principal Component Analysis (PCA)** for example.

In the end, the method to use depends on the question you ask. It is the real translation of the working hypothesis in terms of statistics. If the effect is expected to have multilinear basis, an other GLM could be used. With the advances in machine learning and related tools, it becomes more common to use predictors and classifiers to perform such second level statistics.

## Mass Univariate T-test

The underlying idea behind this method is to consider the different components of the object of interest, whether its is a CM pixel or a SBM voxel, as independent. In this case, the null hypothesis can be spread across them all. A null hypothesis is tested element wise. The most common practice is to compute the difference between two distributions of correlation coefficient (a control group and experimental one or a baselive VS an active phase). From this test a $$t-value$$ is computed. This $$t-value$$ can be translated into a $$p-value$$ whose threshold, in absence of *prior* knowledge on the system can be arbitrarily defined at $$5\%$$. Interpreting this parallel testing requires corrections for multiple comparisons (see below). The *Benjamini Hochberg* procedure permits to control the false discovery rate [Benjamini_1995]. After this step, significance is given element wise. For example CM significant changes between two conditions is given in the form of a matrix whose pixels are binary value. The value of an elemnt informs if the CM pixel has changed significantly in respect to the overall distribution of the changes.

As an example [Mariani et al. 2024], most classically, the connectome of a group of $$9$$ mice after drug injection is compared to the connectomes of a group of $$11$$ mice after saline injection. If the connectome is defined as a correlation matrix between $$10$$ ROIs, a test is performed for each edge of the network or pixel of the lower triangle of the matrix ($$n_{test}= n_{vox}(n_{vox}-1)/2 =\ 45$$ tests). Each test compares the coefficients at the same position in the matrix for all $$9$$ mice for the first distribution, and $$11$$ mice for the second distribution. A $$t-value$$ is computed to quantify the distance between both distributions. Each $$t-value$$ can be translated into a $$p-value$$ to infer the chance that both distributions are coming from the same sample given the number of individuals in each group and the values of their coefficients. Because under the null hypothesis, the chance to reject it is proportional to the number of repeated tests, $$p-values$$ tend to overestimate the number of discoveries. The p-value must be corrected to control this risk of false discovery. We use *Benjamini Hochberg* procedure to do so. All significant changes correspond to $$q-values\ < 0.05$$. 

## Fisher transform

In the case of the *Pearson's correlation coefficient, all the aforementioned procedures must follow a *Fisher* transform of the coefficient. Indeed, because the correlation coefficient is included in $$[-1,1]$$ its variance is reduced for extreme values. The fisher transform $$r \rightarrow arctanh(r)$$ spreads correlation coefficients on the whole $$\mathbb{R}$$. It does so by giving a distribution almost normal whose variance is independent of $r$. By using the $Fisher$ transform, the correlation coefficient can be handled as a $$z-vlalue$$. Nevertheless, one must keep in mind that a *Fisher* transformed coefficient is not a $$z-value$$ *per se*. The space where correlation coefficient are transformed is called the *Fisher*'s space. For visualisation purposes it is more natural to observe the correlation coefficient in the correlation space ($$r\in [-1, 1]$$). Therefore, usually, coefficients are first sent in the *Fisher* space where then can be handled as $$z-score$$ to be compared or to compute an average, before being transformed back on the correlation space for visualisation.

## Statistical Decision Tree

Based on [Rahal2020], you can define your "$$t-test$$" strategy are based on a decision tree:

- A $$Shapiro Wilk$$ test is performed with $$\alpha = 0.1$$
    - If the null hypothesis is rejected *i.e.* distribution is not Gaussian a non parametric *Wilcoxon-Mann-Whitney* test is performed.
    - If the null hypothesis is not rejected, variance equality is tested with a $$F-test$$, two-tailed $$\alpha\ =\ 0.05$$.
        - If the variances are equal, an equal variance $$t-test$$ is performed, two-tailed $$\alpha\ =\ 0.05$$
        - If the variances are not equal, an unequal variance $$t-test$$ is performed, two-tailed $$\alpha\ =\ 0.05$$

Whatever the object of interest, all its degrees of freedom (individual pixel of the SBM or CM) are tested independently following this procedure. After this step, the collection of $$p-values$$ are corrected to $$q-values$$ for multiple comparisons induced false positive rate control (see below). Then, the null hypothesis is rejected with $$\alpha\ =\ 0.05$$.

## Multiple comparison

The problem of multiple comparison is perfectly illustrated in [Bennett_2009] who identified significant clusters of voxels responding to a visual task in the brain of a dead salmon. The underlying principle rests in the fact that the number of false positives is linearly linked to the number of independent tests performed. For classical neuroimaging studies the number of tests is equal to the number of voxels, in the order of magnitude of $$\sim10^6$$ ($$10^4$$ for $$2D$$ fUSI). 


The most common approach to correct statistical results obtained for parallel testing is *Bonferroni*'s correction method. In this case, the linear increase in false positives is countered by inversely reducing the threshold for $$Type I$$ errors $$\alpha = \alpha_0/n$$ where $$\alpha_0$$ is the single test threshold (usually $$0.05$$) and $$n$$ the number of tests. **Bonferroni**'s correction is known to be a peculiarly stringent correction as it considers all tests independent, which is not necessarily the case and peculiarly for neuroimaging where correlation structures exist between neighbouring voxels [Lindquist_2015]. 


Alternatively, applying the **Random Field Theory** *Euler*'s characteristic was found a good quantifier to control the **Family Wise Error Rate (FWER)** which is the $$\alpha$$ equivalent when multiple tests are performed. Without entering in the details of this procedure the *Euler*'s characteristic estimates the number of connected clusters above a certain threshold inside an image of a certain smoothness. As a result, using this strategy controls for correlation structure, dealing with clusters instead of independent tests. The \RFT assumes that images are discrete sampling of continuous smooth random fields. This assumption is quite strong, to ensure this characteristic, smoothing kernels are usually applied to the images as a required step of pre-processing. Finally, *Euler*'s characteristic is function of this estimated smoothness of the random field. It was shown for low smoothness, that RFT based FWER is more stringent than *Bonferroni*'s correction [Nichols_2003].


Finally, two methods have bee proposed to increase power of *Bonferroni*'s correction. *Holm*'s step-down method and *Benjamini*'s step up method or *Benjamini-Hochberg* procedure [Benjamini_1995] use an iterative process over sorted $$p-values$$ to refine research for maxima, well suited in the case of correlated tests [Nichols_2003]. The idea here is not to control the FWER, *i.e.* the overall number of false positive, but the **False Discovery Rate** *i.e.* the number of false discovery in the population of tests who rejected the null hypothesis, the ones for which the test is considered significant. For example, with *Benjamini*'s method if we call $$p_0\ <\ p_1\ <\ ...\ <p_i\ <\ ...<\ p_n$$ the set of our $$n$$ $$p-values$$ sorted in increasing values. To correct for a FDR of $$q=0.05$$ (*i.e.* we want to control that the number of false positive within rejected tests is below $$5\%$$), if $$r$$ is the maximal value so that $$p_r \leq \frac{i}{n}q$$, then all tests for which $$p_i\ \leq\ p_r$$ reject the null hypothesis. As explained in [Lindquist_2015], any procedure controlling FWER controls the FDR yielding to increased power of FDR, moreover it doesn't assume any *a priori* information on the distributions tested as opposed to RFT based control. In the end, for all these reasons, FDR correction is qmong the most popular method to solve the multiple comparison problem [Nichols_2003]. It is why wer recommend it here.


# Summary

We showed here how the concepts introduced in [the FC principle post](http://jcmariani.github.io/principleFC.html) could be translated into mathematical formulations. Neuroimaging methods allow the measure of blood flow properties which ultimately is digitalised. From these images a succession of programs and algorithms permit to apply the fundamental principles of functional connectivity. By this succession of transforms, the image is ouput as simple objects that can be studied in the frameworks of classical statistics. We finally introduced the main statistical caveat associated with neuroimaging analyses.


<!--

        \subsubsection{Parallel t-testing}\text{}\newline
        \label{sssec:ParallelTTesting}
        

The underlying idea behind this method is to consider the different components of the object of interest, whether its is a \Gls{cm} pixel or a \Gls{sbm} voxel, as independent. In this case, the null hypothesis can be spread across them all. A null hypothesis is tested element wise. The difference between two distributions of correlation coefficient is usually tested. From this test a $t-value$ is output. The $t-value$ can be translated into a $p-value$ whose threshold, in absence of \emph{prioir} knowledge on the system can be arbitrarily defined at $5\%$. Interpreting this parallel testing requires corrections for multiple comparisons \niceRef{par:multiple-comparison}. The \emph{Benjamini Hochberg} permits to control the false discovery rate \cite{Benjamini_1995}. After this step significance is given element wise. For example \Gls{cm} significant changes between two conditions is given in the form of a matrix whose pixels are binary value. The value informes if the \Gls{cm} pixel has changed significantly in respect to overall distribution of the changes.


As an example, most classically, the connectome of a group of $9$ mice after drug injection is compared to the connectomes of a group of $11$ mice after saline injection. If the connectome is defined as a correlation matrix between $10$ \Gls{rois}, a test is performed for each edge of the network or pixel of the lower triangle of the matrix ($n_{test}= n_{vox}(n_{vox}-1)/2 =\ 45$ tests). Each test compares the coefficients at the same position in the matrix for all $9$ mice for the first distribution, and $11$ mice for the second distribution. A $t-value$ is computed to quantify the distance between both distributions. Each $t-value$ can be translated into a $p-value$ to infer the chance that both distributions are coming from the same sample given the number of individuals in each group and the values of their coefficient. Because under the null hypothesis, the chance to reject it is proportional to the number of repeated tests, $p-values$ tend to overestimate the number of discoveries. The p-value must be corrected to control this risk of false discovery \niceRef{par:fisher-transform}. We use \emph{Benjamini Hochberg} procedure to do so. All significant changes correspond to $q-values\ < 0.05$. 

            \paragraph{Fischer transform}
            \label{par:fisher-transform}

In the case of the \emph{Pearson} coefficient of correlation all the aforementioned procedures must follow a \emph{Fisher} transform of the coefficient. Indeed, because the correlation coefficient is included in $[-1,1]$ its variance is reduced for extreme values. The fisher transform $r \rightarrow arctanh(r)$ spreads correlation coefficients on $\mathbb{R}$ as whole. It does so by giving a distribution almost normal whose variance is independent of $r$. By using the \emph{Fisher} transform the correlation coefficient can be handled as a \emph{z-vlalue}. Nevertheless, one must keep in mind that a \emph{Fisher} transformed coefficient is not a \emph{z-value per se}. The space where correlation coefficient are transformed is called the \emph{Fisher}'s space. For visualisation purposes it is more natural to observe the correlation coefficient in the correlation space ($r\in [-1, 1]$). Therefore, usually, coefficients are first sent in the \emph{Fisher} space where then can be handled as $z-score$ to be compared or to compute an average before being transformed back on the correlation space for visualisation.



            \paragraph{Statistical paradigm}\text{}\newline
            \label{par:statitical-paradigm}


\begin{figure}[ht]
\begin{center}
    \includesvg[width = \linewidth]{Content/02_Methods/02_Methods_Figures/Statistics-tree.svg}
    \caption{Illustration of the statistical paradigm. When two distributions are given. First their gaussianity is assessed with \emph{Shapiro Wilk}. If failed, a non parametric \emph{Wilcoxon-Mann-Whitney} test is applied. Else is tested the variance equality. Depending on the result either an equal variance $t-test$ is applied else an unequal variance test is applied. Finally, multiple comparisons are corrected with the \emph{Benjamini-Hochberg} correction.}
    \label{fig::statistical-tree}
\end{center}
\end{figure}


Based on \cite{Rahal2020}, \guillemotleft \verb!t-test! \guillemotright are based on a decision tree:

\begin{itemize}
    \item A \emph{Shapiro Wilk} test is performed $\alpha = 0.1$
    \begin{itemize}
        \item If the null hypothesis is rejected \emph{i.e.} distribution is not Gaussian a non parametric \emph{Wilcoxon-Mann-Whitney} test is performed.
        \item If the null hypothesis is not rejected variance equality is tested with a $F-test$, two-tailed $\alpha\ =\ 0.05$.
        \begin{itemize}
            \item If the variances are equal, an equal variance $t-test$ is performed, two-tailed $\alpha\ =\ 0.05$
            \item If the variances are not equal, an unequal variance $t-test$ is performed, two-tailed $\alpha\ =\ 0.05$
        \end{itemize}
    \end{itemize}
\end{itemize}

Whatever the object of interest, all its degrees of freedom (individual pixel of the \Gls{sbm} or \Gls{cm}) are tested independently following this procedure. After this step, the collection of \verb!p-values! are corrected to \verb!q-values! for multiple comparisons induced false positive rate control (see \niceRef{par:multiple-comparison}). Then, the null hypothesis is rejected with $\alpha\ =\ 0.05$.

            \paragraph{Multiple comparisons problem}\text{}\newline
            \label{par:multiple-comparison}


The problem of multiple comparison is perfectly illustrated in \cite{Bennett_2009} who identified significant clusters of voxels responding to a visual task in the brain of a dead salmon. The underlying principle rests in the fact that the number of false positives is linearly linked to the number of independent tests performed. For classical neuroimaging studies the number of tests is equal to the number of voxels, in the order of magnitude of $\sim10^6$ ($10^4$ for $2D$ \gls{fus}). 


The most common approach to correct statistical results obtained for parallel testing is \emph{Bonferroni}'s correction method. In this case, the linear increase in false positives is countered by inversely reducing the threshold for $Type I$ errors $\alpha = \alpha_0/n$ where alpha is the single test threshold (usually $0.05$) and $n$ the number of tests. \emph{Bonferroni}'s correction is known to be a peculiarly stringent correction as it considers all tests independent, which is not necessarily the case and peculiarly for neuroimaging where correlation structures exist between neighbouring voxels \cite{Lindquist_2015}. 


Alternatively, applying the \Gls{rft} \emph{Euler}'s characteristic was found a good quantifier to control the \Gls{fwer} which is the $\alpha$ equivalent when multiple tests are performed. Without entering in the details of this procedure the \emph{Euler}'s characteristic estimates the number of connected clusters above a certain threshold inside an image of a certain smoothness. As a result, using this strategy controls for correlation structure, dealing with clusters instead of independent tests. The \Gls{rft} assumes that images are discrete sampling of continuous smooth random fields. This assumption is quite bold, to ensure this characteristic, smoothing kernels are usually applied to the images as a required step of pre-processing. Finally, \emph{Euler}'s characteristic is function of this estimated smoothness of the random field. It was shown for low smoothness, that \Gls{rft} based \Gls{fwer} is more stringent than \emph{Bonferroni}'s correction \cite{Nichols_2003}.


Finally, two methods have bee proposed to increase power of \emph{Bonferroni}'s correction. \emph{Holm}'s step-down method and \emph{Benjamini}'s step up method or \emph{Benjamini-Hochberg} procedure \cite{Benjamini_1995} use an iterative process over sorted $p-values$ to refine research for maxima, well suited in the case of correlated tests \cite{Nichols_2003}. The idea here is not to control the \Gls{fwer}, \emph{i.e.} the overall number of false positive, but the \Gls{fdr} \emph{i.e.} the number of false discovery in the population of tests who rejected the null hypothesis, the ones for which the test is considered significant. For example, with \emph{Benjamini}'s method if we call $p_0\ <\ p_1\ <\ ...\ <p_i\ <\ ...<\ p_n$ the set of our $n$ $p-values$ sorted in increasing values. To correct for a \Gls{fdr} of $q=0.05$ (\emph{i.e.} we want to control that the number of false positive within rejected tests is below $5\%$), if $r$ is the maximal value so that $p_r \leq \frac{i}{n}q$, then all tests for which $p_i\ \leq\ p_r$ reject the null hypothesis. As explained in \cite{Lindquist_2015}, any procedure controlling \Gls{fwer} controls the \Gls{fdr} yielding to increased power of \Gls{fdr}, moreover it doesn't assume any \emph{a priori} information on the distributions tested as opposed to \Gls{rft} based control. In the end, for all these reasons, \Gls{fdr} correction is believed to become soon the most popular method to solve the multiple comparison problem \cite{Nichols_2003}. For our analyses we decided to use \emph{Benjamini-Hochberg} procedure.

        



        \subsubsection{Motion artefact}


Another main distinction between \gls{fus} data and \gls{fmri} data can be found in the so called \guillemotleft \textit{motion artefact}\guillemotright. We will see in this section and the following ones, how motion has intrinsically different properties in \gls{fus} signal. We discuss that the same phenomenon can result in quite different effects while the tools to deal with it stay the same. Finally, it is argued why motion doesn't seem to interfere with our measure of \Gls{fc} patterns and how this nuisance can be generalised in our case as the \gls{fus} specific \guillemotleft \textit{clutter artefact}\guillemotright. These considerations opens the way to physiological consideration on the source of our observations.


            \paragraph{From \gls{fmri}...} \text{}\newline
            \label{par:motion_fmri}


Artefacts associated to movement were identified from the early history of \Gls{mri}. These artefacts influence on intrinsic patterns came to light around $2010$ with the parallel studies of \cite{Van_Dijk_2012, Power_2012, Satterthwaite_2012}. In mid $90s$ \cite{Friston_1996} describes the different effects of motion on \gls{fmri} timeseries. Two distinct effects can be identified. 


The first class of movement artefact consists in actual motion of the brain which is associated with macroscale displacements of the image intensity distribution. As a result of this artefact, activation in a voxel does not necessarily come from an underlying increase in \Gls{bold} signal but potentially the displacement of an intense source inside this voxel at a given time. This artefact is fairly easy to deal with, by the application of \emph{realignment} algorithm \cite{Friston_95_reg} that register all frames of an image to a reference one (typically the first frame or the average one). 


The second class of movement is more pernicious, dealing with the essence of \gls{fmri} signal. Head movements within the scanner induce alteration of the magnetific field resulting in instability of the measure. This movements induces memory effects of the spin, notably in regions neighbouring inhomogeneities such as the skull \cite{Friston_1996}. Without entering in the details, motion of the brain induces correlated shifts in the recorded phase of the signal, that would be the source of a covarying signal among regions impacted by the same movement. This motion was particularly investigated in the case of task based \gls{fmri} and regression of movement estimators (based on realignment) yielded good results in its mitigation \cite{Friston_1996}.


It seems that the observations of \cite{Van_Dijk_2012, Power_2012, Satterthwaite_2012}, in the case of \Gls{fc} this time, come from such a second class artefact. They demonstrate that \Gls{sbm} measures are artefacted in presence of head motion. They also show that the artefact is not homogeneously distributed, all three studies report increased local connectivity and reduced long range correlations. They explain this inhomogeneity by favoured pitch movement due to head/neck articulation.


This discovery was particuliarly annoying as the exact opposite effect had been observed between children and adult connectomes \cite{Buckner_2010}. This reduced local synchronicity, to the benefit of long range connectivity with age, was interpreted as a biomarker of ageing, or more precisely the natural evolution of connectivity architecture associated with maturation of brain network. Although, under the scrutiny of motion artefact, this connectivity shift became a potential confound. Indeed, children are known to be more agitated during \gls{fmri} sessions than adult. This controversy motivated further investigation on correcting for such motion and the introduction of scrubbing strategies \cite{Power_2012}. It was also observed that such artefactual influence is highly modulated by the processing used in the analysis \cite{Jo_2013} and their order \cite{Carp_2013}. 


            \paragraph{... To \gls{fus}} \text{}\newline
            \label{par:motion_fus}


It is this particular history of \gls{fmri} that suggested many comments regarding our results. We argue here that \gls{fus} signal, without being absent of motion related nuisance, due to its very specific acquisition cascade presents a different type of such noise. The argumentation is here focused on awake imaging, although some arguments could be generalised to other cases, while attached probe freely moving preparation \cite{Tiran_2017} could raise additional concerns, and quiet others in some cases.


Indeed, as a first comment, relatively to the first class of motion artefacts, we expect the head fixation to significantly reduce macro movements of the brain. The head fixation of \emph{Neurotar} systems has proved to leave way for very reduced movement of the tissue. This is illustrated for optical imaging which not always require any realignment \cite{Krogsgaard_2022, Shi_2022}, suggesting tissue movements around or below the $10 \mu m$ scale. In addition, for \gls{fus}, attempts in realignment methods to deal with potential below voxel displacement failed. Typical \Gls{pd} intensity increases tend to overtake basal intensity of \Gls{pd}. In other terms, burst of \Gls{cbv} are associated to brain movements by the algorithm, while they only consist in increased number of activated voxels. These trials ended up with more induced movements than corrected ones. These observation have been shared with other \gls{fus} users during the \emph{fUS brain 2022} conference. In the end, based on \gls{fmri} literature and the risks of confound, it seems crucial for the \gls{fus} community to find an efficient method of realignment in the future. In the meantime, based on our observations, we expect potential confounds to be marginal as compared to our next point, the clutter artefact.


Regarding the second class of motion induced artefact, distinctions between modalities is higher. This first observation can be first defended by a simple argument. \gls{fmri} motion artefact is associated with a great variability of signature, with positive but also negative and mixed effects \cite{Power_2014}. In \gls{fus} signal, to our knowledge, only burst of positive signal has been reported. On a more mechanistic point of view, co-displacement of the tissue is intrinsically filtered in the case of \gls{fus}. As we saw in \niceRef{ssec::clutter-filter}, the clutter filter role is to distinguish signal from the tissue and signal from the blood. In this case the motion associated covariance of \gls{fmri} phase signal should be found in ultrafast ensembles signal (\Gls{iq}) as well. Although, this signal source is expected to be dealt with as tissue clutter. If we saw that such movement could be a problem for temporal based filters which only separate tissue and blood space based on velocity, we can expect the \Gls{svd} based filter to remove any signal presenting spatial coherence. In the end, under the assumption of proper clutter filtering, \gls{fus} signal should be exempt of this artefact. The coherent shift of backpropagated wave phase could potentially shift the spectrum of \Gls{iq} signal, but not change its area. And based on \niceRef{eq::power_scatter} \gls{fus} signal shall remain unchanged.


Nevertheless, as we saw with the \Gls{tg}-artefact, nominal regime of the clutter filter is not always guaranteed, notably in the case of awake imaging. The formulation of second class artefact becomes non linear in the case of \gls{fus}. As long as nominal regime of the clutter filter is maintained, the signal shall remain proportional to \Gls{cbv}. Although, whenever an ultrafast ensemble features result in failure of the \Gls{svd} to separate blood and tissue space (usually associated with outlier frames and often motion), the generated frame presents massive clutter artefact with positive signal in most voxels. \emph{In fine}, the second class artefact present in \gls{fus} appears less linear than the one observed in \gls{fmri} for which regression of movements parameters give relatively good correction. The artefact appears also more general as it is not always associated with physical movement. This artefact is framed under the classification \emph{clutter artefacts} and is present in all frames associated with failure of the clutter filter. This failure being linked either to motion or other sources like the \Gls{tg} (both possess potential colinearities). At the moment, similarly to what \cite{Power_2012} presents, scrubbing appears as the only alternative to correcting such non linear artefactual frames. Although research on more effective acquisition filters (adaptative clutter filters),currently underway, gives the promise of ameliorated stability of \gls{fus} signal in the future.  


These arguments remain at the moment speculative. Projects in the lab and collaborators are currently aiming at more causal demonstrations to converge towards more efficient processing pipelines. We discuss in the next section the possible implications of motion to our \Gls{fc} read-outs. 


            \paragraph{Implication for awake pharmaco-\gls{fus}} \text{}\newline
            \label{par:GSR_pharmaco-fUS}



\begin{figure}[h!]
\begin{center}
    \includesvg[width = \linewidth]{Content/04_Discussions/04_Discussions_Figures/Discussion_motion_FC.svg}
    \caption{Illustration of the influence of movement on \Gls{fc}. Average mobilities and \Gls{fc} connectomes are displayed for $4$ different conditions. The $4$ treatments are associated to either high (left) or low (right) mobility while connectomes are either positive (top) (strong absolute values of correlation) or negative (bottom) (low connectivity).}
    \label{fig:motion_FC}
\end{center}
\end{figure}


From the results presented in this thesis, we expect to rule out motion as the main driver of our observations. \niceFigRef{fig:motion_FC} shows that motion is decorrelated of the observed fingerprints. Here are displayed all combinations of mobility and connectome characteristics. Methadone is associated with a high mobility and a positive network (see \niceFigRef{sec:results_comparison}), similarly, cocaine has an increased locomotion at $P3$ but its effect on the connectome is considered negative with reduced absolute values of correlation. Conversely, $CP$ induces hypomobility in the mice while it increases significantly the absolute values of correlations in the brain connectome. Finally, psilocybine, with similar reduced locomotion induces a negative effect on the connectome. From this opposed effects for different mobilities, we conclude that motion does not reliably influence functional connectivity under different pharmacological treatments. 


 Nevertheless, it is worth mentioning the case of buprenorphine. Under its influence mice are moving more than $55\%$ of the time for more than an hour. One can wonder if in such pathological cases estimation of \Gls{fc} is not biased. It could explain the absence of any positive influence on the connectome which distinguishes this compound from the other opioids. Although, if mobility could have an influence in this specific case, the observation is quite different from the effect described in \cite{Van_Dijk_2012, Power_2012, Satterthwaite_2012} where motion is, in a reproducible fashion, associated with increased local connectivity and reduced distal one. 


 \begin{figure}[h!]
\begin{center}
    \includesvg[width = \linewidth]{Content/04_Discussions/04_Discussions_Figures/Discussion_FCburst.svg}
    \caption{Illustration of the transient hyperconnectivity following some treatments injection. Average network for the whole follow up of each treatments connectome. From top to bottom: fentanyl, buprenorphine, ketamine, and isoflurane. Grey bar represents treatment injection, for isoflurane, the first bar represents beginning of inhalation and the second one the end.}
    \label{fig:Discussion_FCburst}
\end{center}
\end{figure}


 Finally, the transition state observed in some treatments can rise some concerns regarding potential interactions with increased mobility. Indeed \niceFigRef{fig:Discussion_FCburst} displays multiple treatments for which injection is immediately followed by a strong increased connectivity across the whole network. This state only lasts for the first $10min$, and is usually uncorrelated with following \Gls{fc} patterns. For fentanyl, buprenorphine and ketamine, the burst is associated with increased locomotion and increased perfusion. This increased correlation could be interpreted as potential covariance explained by the vascular burst common to all regions. The burst induced correlation fits observations on isoflurane. For isoflurane, this state occurs at both anaesthesia induction and animal wakening. We saw that the induction can be associated to movement (see \niceRef{ssec:consciousness_tracking}), with animal reacting to the isoflurane mask for $1-4min$ before reaching immobility. Although, wakening is absent of any increased mobility. But, regardless of movement, isoflurane induces a massive hyper perfusion in all regions. In the end, a succession of events can be identified. The injection induces increased mobility. The movement seems to generate a vascular burst. Finally, this vascular burst after our processing would be in the range of our temporal filter window, inducing global covariance across the whole network. Isoflurane suggests that the vascular burst itself (whether it is upstep of downstep) is sufficient to induce the seemingly hyper-connected state. 


 From this observation the causation of the effect must be questioned. The isoflurane case demonstrates that the hyper connected state does not require movement to exist. Aternatively, ketamine/xylazine condition and morphine injections induce similar vascular bursts without showing such an hyper connected state.
 
 Therefore three models can be proposed:

 \begin{itemize}
    \item Isoflurane burst and the others are non related. Fentanyl, buprenorphine and ketamine induce movement independently of the vascular burst. The movements induce clutter filter artefacts non dealt with the overall pipeline resulting in artefactual hyper connectivity measure.
    \item Whether it is through movement or vasodilation, vascular bursts can occur independently of neuronal function. This state is associated with neurovascular decoupling. Depending on the vascular burst dynamics it sometimes survives the different processing steps resulting in vascular induced estimated hyper connectivity.
    \item Finally the vascular burst is causally induced by neuronally driven synchronicity across the whole brain. The state is associated with drug induced state transition (whether it is upstep or downstep). The transitioning brain induces increased locomotion in some cases.
 \end{itemize}


Overall, the first model could be discarded based on a \emph{parcimoni} argument. Indeed, it suggests different mechanisms for the same observation. In addition, other hyper mobility states like the overall buprenorphine follow up lack this hyper connectivity state, ruling out the purely clutter artefactual source of this pattern. From this argument, the vascular burst seems a requisite. Whether this vascular burst represents an actual neuronally driven event or a systemic vascular one remains an open question. To test this causality, additional recordings, like concomitant electrophysiology and \gls{fus} recordings \cite{Nunez-Elizalde_2022} could bring key information regarding such kinds of event. 


To conclude this part and open the discussion in this sense, these consideration start to tackle the question on neuronal substrate of the observed phenomenons. In this case whether \Gls{fc} represents actual increased synchronicity in the network or not remains an open question. On the perspective of pharmacological fingerprinting, the observation can still be of interest. Based on considerations like the ones described in the introduction on the vasoneuronal coupling \niceRef{sssec:vasoneuronal_coupling}, there is no doubt that the hyper mobile, hyper perfused brain following a drug injection is in a peculiar state. More investigations are still required to decipher its role and its precise physiology. Although, whether the first, second or third model best explain these observations, this state seems characterisable and precedes longer terms shifts in connectivity patterns whose causality are less challenged. \emph{In fine}, it calls for further improvements of \gls{fus} processing and establishment of its causal link with underlying neuronal activity, but the current state of the technology already allows to clearly identify the phenomenon and to lay the future hypotheses to be tested.   

-->
