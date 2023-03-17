---
tags: ggg, ggg2023, ggg201b
---

[toc]

# Lab 10 NOTES - GGG 201b, March 10th, 2023

[![hackmd-github-sync-badge](https://hackmd.io/vx34rLcAQu6GR-_Gv8r9qw/badge)](https://hackmd.io/vx34rLcAQu6GR-_Gv8r9qw)


[Permanent link on GitHub: lab-10.md](https://github.com/ngs-docs/2023-ggg-201b-lab/blob/main/lab-10.md)

## Running the RNAseq workflow on farm, redux

:::warning
You will need to alrady have the RNAseq workflow repository in your account, and the `rnaseq` conda environment, per the instructions from [lab 9](https://hackmd.io/O2KaLpquTxOD8aI-JMEErQ?view#Setting-things-up-on-farm).
:::

Start by sshing into farm.

Then do an srun:
```
srun -p high2 --time=3:00:00 --nodes=1 \
    --cpus-per-task 4 --mem 10GB --pty /bin/bash
```

Activate the RStudio server module:
```
module load rstudio-server/2022.12.0
```

Run `rstudio-launch` in the `rnaseq` environment:
```
conda run -n rnaseq --live rstudio-launch
```

and follow the instructions to set up your ssh tunneling connection, and log in to the RStudio Server.

### Double check the version of R in RStudio

In your Console window for RStudio Server, you can run:

```
R.Version()[13]
```

and you should see:

>[1] "R version 4.0.5 (2021-03-31)"

If you don't see this, RStudio Server is not running in the right conda environment -
* most likely, you didn't run `conda run ...` above; or
* the conda environment `rnaseq` isn't configured properly and needs to be reinstalled.

### Check that your RNAseq workflow files are up to date

In the terminal window, run:
```
conda activate rnaseq
```

and do:
```
snakemake -j 4
```

This should succeed - and may not run anything at all!

## Experimental setup - what is this RNAseq?

<!-- @@ -->

## Walking through the notebook

Discuss:
* CSV table of samples and conditions
* normalization
* results table & adjusted p-value; multiple testing
* MA plot
* individual gene plot
* MDS plot
* heatmap plot; and clustering

### Multiple testing

Example:

* Suppose want to flip a coin 5 times to find out if biased; if number of heads significantly different than 5, => it is biased!
* let's say that you decide cutoff is 5 heads out of 5.
* null hypothesis is: coin is fair.
* for fair coins, 5 heads in a row is going to happen 1 out of 32 times, so your false discovery rate is 1/32.

Good so far, right?

Next:
* now someone gives me 10 coins to test. what do I do?
* should I apply the same cutoffs?? if I do, what is my false discovery rate??
* ...how do we adjust!?

The [Bonferroni correction](https://en.wikipedia.org/wiki/Bonferroni_correction) is a fancy way of saying: divide your cutoff p-value by the number of tests. In practice this is pretty conservative but it is a good intuitive rule of thumb.

## A short summary of the lab section

Perspective:

* actual bioinformatics workflows have many steps, however you slice it
* the individual steps are often not that interesting, but you do need to know how to:
    1. figure out what they are
    2. understand what they're meant to do
    3. know how to adjust parameters
* your future is not 3-6 samples, but rather 100-300; automated workflows help you scale.

General commentary and advice:

* many moving parts to a real workflow (whether automated or not)
* treat it as something you can poke and test and tweak!
* learn where the experimental knobs are: how to add samples, adjust cutoffs, etc.

My main advice to you is this:

* run a complete end-to-end analysis of your data first, before exploring parameters
* only then go back and start thinking about optimizing individual steps;
* this is because the large majority of the time, your data or your interpretation will be more of a problem than your bioinformatics workflow


## Appendix: homework stuff

Use:

```
module load rstudio-server/2022.12.0
rstudio-launch
```
to run RStudio.