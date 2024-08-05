---
layout: page
title: PhD
subtitle: 5 years of my life summarised in 600p.
permalink: /PhD.html
---

<a href="https://theses.hal.science/tel-04420129">
<img src="https://JCMariani.github.io/assets/img/PhD_thesis_cover.png" alt="drawing" width="15%" class="center"/>
</a>

My thesis: [Quantification and modelling of functional connectivity perturbation in ultrafast power doppler imaging of the awake mice brain : pharmacological fingerprints on awake brain activity](https://theses.hal.science/tel-04420129) is openly accessible online. If the main chatper is probaly **part-V** with the first version of [Mariani et al. 2024](https://www.biorxiv.org/content/10.1101/2024.07.30.604249v1). I used this document to detail as much as I could the theoretical context of this study (principles of: **Functional Ultrasound Imaging (fUSI)**, **Functional Connectivity (FC)**, philosophy of data sharing...). I also wrote a lot of technical details for curious fUS experimenters and analysts. This work was performed under the supervision of *Zsolt Lenkei* at [IPNP - INSERM U1266](https://ipnp.paris5.inserm.fr/english-language). My work relies on $$4$$ datasets:

- `Opioids` acquired by **Andrea Kliewer** (main study of my PhD)
- `Canabinoids` acquired by **Andrea Kliewer**
- `Psilocybin` acquired by **Miguel Ferreira**
- `Conscious states` acquired by me

None of this work would have been possible without the expert hands of experiementalist with whom I developed this imaging protocol or whom I trained to perform the aquisitions: **Andrea Kliewer**, **Miguel Ferreira**, **Louis Barthe** and **Laurianne Beynac**. FInally The whole analysis piepelin eis a joined effort of **Samuel Diebolt** and I. [Sam](https://github.com/sdiebolt) is continuing this daunting effort of understanding, implementing and standardising the analysis of fUSI signal, his contribution to these project is absolutely fundamental.

To digest this bruteforce document, I propose below some reading recommendations depending on your interests in the shape of an extended table of content:

## I Introduction
<br/>
<h3 style = "text-indent: 50px;"> 1 Preamble </h3>
<h3 style = "text-indent: 50px;">  2 General Introduction </h3>
<h3 style = "text-indent: 50px;">  3 fUS </h3>
<h3 style = "text-indent: 50px;">  4 Neurovascular Coupling </h3>
<h3 style = "text-indent: 50px;">  5 Functional Connectivity </h3>
<h3 style = "text-indent: 50px;">  6 Pharmacology </h3>
<h3 style = "text-indent: 50px;">  7 Summary </h3>
<br/>
## II Preparation for awake imaging
<br/>
<h3 style = "text-indent: 50px;">  8 Article: Whole-Brain 3D activation and functional connectivity mapping in mice using transcranial functional ultrasound imaging </h3>
<h3 style = "text-indent: 50px;">  9 Protocols </h3>
<h3 style = "text-indent: 50px;">  10 Results </h3>
<h3 style = "text-indent: 50px;">  11 Discussions </h3>
<br/>
## III Awake rs-FC with fUS
<br/>
<h3 style = "text-indent: 50px;">  12 artefacts </h3>
<h3 style = "text-indent: 50px;">  13 Analysis Benchmark </h3>
<br/>
## IV Opioid fingerprint
<br/>
<h3 style = "text-indent: 50px;">  14 Article: Fingerprint of opioids on functional connectivity in awake behaving mice using functional ultrasound </h3>
<h3 style = "text-indent: 50px;">  15 Discussions </h3>
<br/>
## V Conclusions
<br/>
<h3 style = "text-indent: 50px;">  References </h3>
<h3 style = "text-indent: 50px;">  List of Figures </h3>
<br/>
## VI Appendices
<br/>
<h3 style = "text-indent: 50px;">  A Articles </h3>
<h3 style = "text-indent: 50px;">  B Setup </h3>
<h3 style = "text-indent: 50px;">  C Acquisition </h3>
<h3 style = "text-indent: 50px;">  D Analysis pipeline </h3>
<h3 style = "text-indent: 50px;">  E Preparation details </h3>
<h3 style = "text-indent: 50px;">  F Data Structures </h3>
<h3 style = "text-indent: 50px;">  G Opioids fingerprint </h3>
<h3 style = "text-indent: 50px;">  H Cannabinoids fingerprint </h3>
<h3 style = "text-indent: 50px;">  I Psilocybin fingerprint </h3>
<h3 style = "text-indent: 50px;">  J Conscious states fingerprint </h3>
<h3 style = "text-indent: 50px;">  K Comparison of various fingerprints </h3>
<h3 style = "text-indent: 50px;">  L Co-Activation Pattern analysis </h3>
<br/>
<br/>


# For General NI ensthusiastics

Some sections that I wrote don't refer exculsively to fUSI, despite being inspired from my experience in this field only. For the sake of explanation, I tried to develop a modality agnostic framework for NI and FC.

I recommend persons without any specific interest in fUSI to read in priority:

**Introduction**: 
<br/>
- **2 General Introduction** gives a general framework for imaging to introduces main principles of **Neuro Imaging (NI)** giving outlines for typical studies (acquisition and analysis) and an associated terminology.
- **4 Neurovascular coupling** presents the anatomical features of brain vascularisation, it details the historiographical description of the *neurovascular unit* recently reframed as *neurovascular complex*. The functional implication of this key component of non invasive NI such as fMRI and fUSI are also developed.
<br/>
**Appendices**
<br/>
- **F Data Structure** discusses the philosophy of data sharing in the scope of open science and presents one of the first iteration of the [fUS-BIDS directives](https://docs.google.com/document/d/1W3z01mf1E8cfg_OY7ZGqeUeOKv659jCHQBXavtmT-T8/edit#heading=h.4k1noo90gelw).
- **K Comparison of various fingerprints** summarises the fingerprints of all the compounds we tested to show specificity of our pipeline.
<br/>

# For pharmacology enthusiastics

I must apologize as unfortunately my limited competences on this topics cleary bounded the discussions in this direction. My work was mainly targeted on fUSI signal and how to characterise FC perturbation in such recordings. Nevertheless, as my main application is pharmacology, I humbly tried to present this concept. Practically I explain my little understanding of it and present how this model fits (extremely adequately for proof-of-concept stage of trancranial awake fUSI) the more general framework of FC perturbation. In addition to this formal introduction, I show many results from the different experiments that we performed which might make more sense for pharamcologists than for me, simple engineer. For those interested I recommand to read:

**Introduction**: 
<br/>
- **6 Pharmacology** introduces the concepts borrowed from the field of pharmacology that motivated the application of this thesis. By merging these principles with the framework of FC we show the relevance of awake fUSI for fingerprinting of pharmacological compounds.
<br/>

**Part IV**:
<br/>
- **14 Article-2** first version of [Mariani et al. 2024](https://www.biorxiv.org/content/10.1101/2024.07.30.604249v1), demonstrates how to use fUSI to investigate FC through the skull in awake behaving mice. It aggregates results from **part-II** and **part-III** in a practical example studying the fingerprint of opioids.
- **15 Discussion** compares our main results with **pharmaco-MRI** literature. We also show the current limitations (intersubject variability, 3D coverage, resolution), proposing alternative solutions when necessary, but also future promess of the technology to aleviate these constrains as well as required future research to improve the current method.
<br/>

**Appendices**
<br/>

- **C Acquisition** merges a practical guide of linear probe acquisition setup and description of pharmaco-fUS protocol details. **C.25** and **C.4** display details about our neuronavigation scheme on the experimental perspective (how to find vascular landmarks, specific steps of acquisition session, tips for field of view optimisation, parameters of INSERM ART acquisition software...). The other sections give more information on our pharmacological scheme (compounds, exact dataset content...).
- **G Opioids fingerprint** presents the systematic analysis of fUSI based fingerprint of opioid compounds in awake behaving mice.
- **H Cannabinoids fingerprint** presents the systematic analysis of fUSI based fingerprint of cannabinoid compounds in awake behaving mice.
- **I Psilocybin fingerprint** presents the systematic analysis of fUSI based fingerprint of psilocybin and non halucinogenic *5HT-2AR* agonist lisuride in awake behaving mice.
- **J Conscious states fingerprint** presents the systematic analysis of fUSI based fingerprint of two anaesthetics in awake to sedation transition in behaving mice.
- **K Comparison of various fingerprints** summarises the fingerprints of all the compounds we tested to show specificity of our pipeline.

# For fUSI enthusiastics

I distinguish two types of fUSI enthusiastics: the `experimentalists` and the `analysts`. During my PhD I had the opportunity to build the awake fUSI setup from scratch, practically and conceptually. As a consequence I gathered expertise in both aspects that I tried to share as much as possible in this document. I detailed all along my thesis, the motivations for the various (arbitrary or not) choices that we made, as well as tips for researchers that would like to reproduce or extend our work. 

## For Experimentalists

I believe that a good undersanding of fUSI acquisition cascade is a necessary start. From our perspective (limited to linear probe) most of our experimental protocols are explained in [Bertolo et al. 2021](https://pubmed.ncbi.nlm.nih.gov/33720137/). In my thesis I show some more data that motivate these choice and discuss extensively the influence of experimental decisions on signal quality and later on analysis requirements. I also compare our preparation to the other ones published or almost at the time of my PhD.

If you want to do some transcranial fUSI and wonder where to start, I recommend to read in priority:

**Introduction**: 
<br/>
- **3 fUS** explains the theory of fUSI signal (the full acquisition pipeline from emission to acquisition).
<br/>

**Part II**:
<br/>
- **8 Article-1** [Bertolo et al. 2021](https://pubmed.ncbi.nlm.nih.gov/33720137/) details acquisition protocols for linear probe in both anaesthetised and awake, task-based and resting states.
- **9 Protocols** emphasizes the awake case as reported in [Mariani et al. 2024](https://www.biorxiv.org/content/10.1101/2024.07.30.604249v1) with refinement we developed all along the opioid cannabinoid and psilocybin studies.
- **10 Results** shows preliminary results regarding efficiency and controls over our awake preparation.
- **11 Discussion** discusses these result about our preparation.
<br/>

**Part III**:
<br/>
- **12 Artefacts** discusses an important artefact found in awake recording, these results are preliminary but remain a huge limitation for awake transcranial fUSI.
<br/>

**Part IV**:
- **14 Article-2** first version of [Mariani et al. 2024](https://www.biorxiv.org/content/10.1101/2024.07.30.604249v1), demonstrates how to use fUSI to investigate FC through the skull in awake behaving mice. It aggregates results from **part-II** and **part-III** in a practical example studying the fingerprint of opioids.
- **15 Discussion** compares our main results with **pharmaco-MRI** literature. We also show the current limitations (intersubject variability, 3D coverage, resolution), proposing alternative solutions when necessary, but also future promess of the technology to aleviate these constrains as well as required future research to improve the current method.
<br />

**Appendices**
<br/>
- **B Setup** discusses recommandation for the building and use of awake fUSI setup. We discuss the acquisition and data management architecture, but also constrains associated with awake imaging (temperature), we later give recommendation to add peri-acquisition measurements (videography, tracking, ...) for better control of experimental conditions. This section can be seen as a detailed guide of how the experiments are performed with our complete setup, with many advices based on our feedbacks for experimentalists.
- **C Acquisition** merges a practical guide of linear probe acquisition setup and description of pharmaco-fUS protocol details. **C.25** and **C.4** display details about our neuronavigation scheme on the experimental perspective (how to find vascular landmarks, specific steps of acquisition session, tips for field of view optimisation, parameters of INSERM ART acquisition software...). The other sections give more information on our pharmacological scheme (compounds, exact dataset content...).
- **E Preparation details** details protocols for housing, habituation and surgery for awake mice and anesthtized rats. This gives a lot of tips based on our experience to help experimenter in their daily practice.
- **F Data Structure** discusses the philosophy of data sharing in the scope of open science and presents one of the first iteration of the [fUS-BIDS directives](https://docs.google.com/document/d/1W3z01mf1E8cfg_OY7ZGqeUeOKv659jCHQBXavtmT-T8/edit#heading=h.4k1noo90gelw).
<br/>

## For analysts

Analysis is a wide field of debate in which it is easy to get lost for a very long time (my personal record is two years). We tried during my PhD to motivate systematically the choices we made for the analysis of transcranial awake behaving fUSI. I recommend to refer to \[Diebol et al. *in prep]\ who pushed these preliminary analyses order of magnitudes further and recapitulate much more effectively our previous findings. If you are wondering how to analyse fUSI data you can start with these section of my thesis. You could find there some information that could prove to be relevant for you:

**Introduction**: 
<br/>
- **2 General Introduction** gives a general framework for imaging to introduces main principles of **Neuro Imaging (NI)** giving outlines for typical studies (acquisition and analysis) and an associated terminology.
- **3 fUS** explains the theory of fUSI signal (the full acquisition pipeline from emission to acquisition).
- **5 Functional Connectivity** explains the story of the FC concept from the initial theroretical perspectives and the introduction of network in neuroscience to physical measurements with the emergence of NI, stative FC and later dynamic FC.
<br/>

**Part II**:
<br/>
- **8 Article-1** [Bertolo et al. 2021](https://pubmed.ncbi.nlm.nih.gov/33720137/) details acquisition protocols for linear probe in both anaesthetised and awake, task-based and resting states.
<br/>

**Part III**:
<br/>
- **12 Artefacts** discusses an important artefact found in awake recording, these results are preliminary but remain a huge limitation for awake transcranial fUSI.
- **13 Benchmark** introduces a brief benchmark of the analysis pipeline we developped all along my PhD, it is the preliminary analysis behind of \[Diebolt et al. *in prep*\]. In addition this section presents many results on templates, parcellation and neuronavigatin in general.<br/>
<br/>

**Part IV**:
- **14 Article-2** first version of [Mariani et al. 2024](https://www.biorxiv.org/content/10.1101/2024.07.30.604249v1), demonstrates how to use fUSI to investigate FC through the skull in awake behaving mice. It aggregates results from **part-II** and **part-III** in a practical example studying the fingerprint of opioids.
- **15 Discussion** compares our main results with **pharmaco-MRI** literature. We also show the current limitations (intersubject variability, 3D coverage, resolution), proposing alternative solutions when necessary, but also future promess of the technology to aleviate these constrains as well as required future research to improve the current method.
<br/>

**Appendices**
<br/>
- **D Analysis pipeline** details the methods used during the analysis of fUSI data. We also present the various types of display used all along the thesis, as well as exact parameters used for `Elastix` based registration.
- **F Data Structure** discusses the philosophy of data sharing in the scope of open science and presents one of the first iteration of the [fUS-BIDS directives](https://docs.google.com/document/d/1W3z01mf1E8cfg_OY7ZGqeUeOKv659jCHQBXavtmT-T8/edit#heading=h.4k1noo90gelw).
- **L Co-Activation Patterns** shows some preliminary results using a naive method to estimate co-activation patterns in fUSI signals.
<br/>

<!--
**Introduction**: 
<br/>
- **2 General Introduction** gives a general framework for imaging to introduces main principles of **Neuro Imaging (NI)** giving outlines for typical studies (acquisition and analysis) and an associated terminology.
- **3 fUS** explains the theory of fUSI signal (the full acquisition pipeline from emission to acquisition).
- **4 Neurovascular coupling** presents the anatomical features of brain vascularisation, it details the historiographical description of the *neurovascular unit* recently reframed as *neurovascular complex*. The functional implication of this key component of non invasive NI such as fMRI and fUSI are also developed.
- **5 Functional Connectivity** explains the story of the FC concept from the initial theroretical perspectives and the introduction of network in neuroscience to physical measurement with the emergence of NI, stative FC and later dynamic FC.
- **6 Pharmacology** introduces the concepts borrowed from the field of pharmacology that motivated the application of this thesis. By merging these principles with the framework of FC we show the relevance of awake fUSI for fingerprinting of pharmacological compounds.
- **7 Summary** integrates all these theoretical concepts to give a quick summary of a unified framework for fUSI based FC investigation of pharamcological action on the central nervous system.
<br/>

**Part II**:
<br/>
- **8 Article-1** [Bertolo et al. 2021](https://pubmed.ncbi.nlm.nih.gov/33720137/) details acquisition protocols for linear probe in both anaesthetised and awake, task-based and resting states.
- **9 Protocols** emphasizes the awake case as reported in [Mariani et al. 2024](https://www.biorxiv.org/content/10.1101/2024.07.30.604249v1) with refinement we developed all along the opioid cannabinoid and psilocybin studies.
- **10 Results** shows preliminary results regarding efficiency and controls over our awake preparation.
- **11 Discussion** discusses these result about our preparation.
<br/>

**Part III**:
<br/>
- **12 Artefacts** discusses an important artefact found in awake recording, these results are preliminary but remain a huge limitation for awake transcranial fUSI.
- **13 Benchmark** introduces a brief benchmark of the analysis pipeline we developped all along my PhD, it is the preliminary analysis behind of \[Diebolt et al. *in prep*\]. In addition this section presents many results on templates, parcellation and neuronavigatin in general.<br/>

**Part IV**:
- **14 Article-2** first version of [Mariani et al. 2024](https://www.biorxiv.org/content/10.1101/2024.07.30.604249v1), demonstrates how to use fUSI to investigate FC through the skull in awake behaving mice. It aggregates results from **part-II** and **part-III** in a practical example studying the fingerprint of opioids.
- **15 Discussion** compares our main results with **pharmaco-MRI** literature. We also show the current limitations (intersubject variability, 3D coverage, resolution), proposing alternative solutions when necessary, but also future promess of the technology to aleviate these constrains as well as required future research to improve the current method.
<br/>

**Appendices**
<br/>
- **A.1 Article-3** fUSI was only used to one figure confirming the functional role of microglia in neurovascular coupling. Microglia depletion diet impairs the shape of stereotypical response to whisker stimulation in aneasthetised mice.
- **A.2 Article-4** shows how to measure **Cortical Spreading Depolarisation (CSD)** using fUSI in anaesthetised rats after thinning of the skull.
- **B Setup** discusses recommandation for the building and use of awake fUSI setup. We discuss the acquisition and data management architecture, but also constrains associated with awake imaging (temperature), we later give recommendation to add peri-acquisition measurements (videography, tracking, ...) for better control of experimental conditions. This section can be seen as a detailed guide of how the experiments are performed with our complete setup, with many advices based on our feedbacks for experimentalists.
- **C Acquisition** merges a practical guide of linear probe acquisition setup and description of pharmaco-fUS protocol details. **C.25** and **C.4** display details about our neuronavigation scheme on the experimental perspective (how to find vascular landmarks, specific steps of acquisition session, tips for field of view optimisation, parameters of INSERM ART acquisition software...). The other sections give more information on our pharmacological scheme (compounds, exact dataset content...).
- **D Analysis pipeline** details the methods used during the analysis of fUSI data. We also present the various types of display used all along the thesis, as well as exact parameters used for `Elastix` based registration.
- **E Preparation details** details protocols for housing, habituation and surgery for awake mice and anesthtized rats. This gives a lot of tips based on our experience to help experimenter in their daily practice.
- **F Data Structure** discusses the philosophy of data sharing in the scope of open science and presents one of the first iteration of the [fUS-BIDS directives](https://docs.google.com/document/d/1W3z01mf1E8cfg_OY7ZGqeUeOKv659jCHQBXavtmT-T8/edit#heading=h.4k1noo90gelw).
- **G Opioids fingerprint** presents the systematic analysis of fUSI based fingerprint of opioid compounds in awake behaving mice.
- **H Cannabinoids fingerprint** presents the systematic analysis of fUSI based fingerprint of cannabinoid compounds in awake behaving mice.
- **I Psilocybin fingerprint** presents the systematic analysis of fUSI based fingerprint of psilocybin and non halucinogenic *5HT-2AR* agonist lisuride in awake behaving mice.
- **J Conscious states fingerprint** presents the systematic analysis of fUSI based fingerprint of two anaesthetics in awake to sedation transition in behaving mice.
- **K Comparison of various fingerprints** summarises the fingerprints of all the compounds we tested to show specificity of our pipeline.
- **L Co-Activation Patterns** shows some preliminary results using a naive method to estimate co-activation patterns in fUSI signals.
<br/>
-->

