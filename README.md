# forVitaly-mc
HOW-TO guide for GlueX MC generation and analysis for Vitaly 

## Get This How-To in your /work directory 
### Clone this directory
Navigate to where you want to put this How-To and do: 

```git clone https://github.com/tylerviducic/forVitaly-mc.git```

## The How-To
### 1. Check the GlueX environment is working 
Make sure your `gxenv` command is working. 

### 2. Genr8 Generator File
The file `nstar_1440_a2.input` is a config file for the genr8 generator which handles 2-body isobar decays. You can see documentation for that on the wiki. 

The usage, were it not to be part of the MCWrapper process, is given at the top of the file. You can also type `genr8 -h` to see additional information on usage. 

Lines 45-48 control the beam settings. For MCWrapper, you will want to use the `TEAMPBEAMCONFIG` shown on line 48. For CLI testing/debugging, you'll want to use the mono-energetic style given on line 46. 

Lines 49-50 control the target configuration

Lines 51-52 handle the t-slope of the process. 

Lines 53-66 is the bulk of the configuration file where we prescribe the physics process we want to simulate. Some important things to note. The **Baryon** is described by particle "Y" and will always have the index **0**. The **Meson** is described by particle "X" and will always have the index **1**. The ID section is the GEANT PID, which can be referenced at the bottom of the config file or at [this Brookhaven Link](https://www.star.bnl.gov/public/comp/simu/newsite/gstar/Manual/particle_id.html)

I personally let GEANT handle my Ks decay, which is why I have denoted the Ks (PID16) as a non-decaying particle with the `11`. For me, this handles the branching fraction implicitly and I don't have to worry about it down the line. This may not be your usecase, though, and you'd have to change the `11` flag to a `00` or `01` and then prescribe the decay in the same procedure we describe the N* and a2 decays. 

### 3. Jana Config File
The fie `jana_analysis.config` is a config file for JANA. I wish I had more information on JANA, but I really don't. I mostly just know how to use it to run the `ReactionFilter` plugin to select the topology I want from a given dataset.

The file is fairly simple. JANA Analysis config file specifies which plugins you want to run, along with the number of threads to be used for that running. 

Line 5 prescribe the ReactionFilter for the reaction $\gamma p \rightarrow \pi^{-}K^{+}K_{s}$. 

Line 6 prescribes that the $K_{s}$ decays to $\pi^{+}\pi^{-}$. 

Line 7 states that there are to be 4 beam bunches considered - the flag `B4` - and that the $K_{s}$ is not to be constrained by the kinematic fitter - the flag `M16`. 

The ReactionFilter is more robust than what is shown here and much-more-robust documentation can be found on the GlueX wiki. 

### 4. MCWrapper Config File
The file `MC_nstar1440.config` is the MCWrapper config file that we use to run the entire MC Process from the commandline. The usage is given at the top of the file on line 1, but you can also type `gluex_MC.py -help` to see additional information on usage. 

In this file, there are not many un-commented lines, as my use case is fairly simple and straightforward. 

On line 20, I specify the output directory for the output files. 

Line 22 has the numner of cores to use. 

Lines 24-25 specifiy the generator you want to use and the config file for that generator. 

Lines 35-36 control the beam energy range. 

Line 38 prescribes the GEANT version to use. 

Line 41 is the background. I am not sure how you should use this, as of now, but standard usage for me is to use `Random`. 

Line 44 is the analysis environment file you want to use. 

Line 48 is where we put the JANA analysis config file. 

I havent run this yet in batch mode so I am not entirely sure how to use lines 50-72, but they *should* be correct for my hypothetical usage. 

More documentation for the MCWrapper can be found [here on Overleaf](https://www.overleaf.com/project/5bb7b3423bb4c259308b56c5). 

### 5. Analysis of your output files 
The first thing I do is run my DSelector over my files to select the best $\chi^{2}/ndf$ in-time combo per event. 

Documentation for the DSelector can be found on the GlueX wiki.