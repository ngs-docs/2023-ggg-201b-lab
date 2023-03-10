---
tags: ggg, ggg2023, ggg201b
---

# Lab 9 NOTES - GGG 201b, March 10th, 2023

[Permanent link on GitHub: lab-9.md](https://github.com/ngs-docs/2023-ggg-201b-lab/blob/main/lab-9.md)

## Friday Lab links

* [Lab 8 notes](https://hackmd.io/Qk0fJEMMRZ-tZUZ8Wh25wg?view)
* [Repository for RNAseq workflow](https://github.com/ngs-docs/2020-ggg-201b-rnaseq) - [Snakefile](https://github.com/ngs-docs/2020-ggg-201b-rnaseq/blob/latest/Snakefile) and [RMarkdown report](https://github.com/ngs-docs/2020-ggg-201b-rnaseq/blob/latest/rnaseq-workflow.pdf)

## Setting things up on farm

Today we're going to do things a little differently. Let's hope it all works!

We need to install a bunch of things _before_ running RStudio today.

So, ssh into farm and do an srun:
```
srun -p high2 --time=3:00:00 --nodes=1 \
    --cpus-per-task 4 --mem 10GB --pty /bin/bash
```

Clone the RNAseq github repo into ``~/lab9-rnaseq`:
```
git clone https://github.com/ngs-docs/2020-ggg-201b-rnaseq ~/lab9-rnaseq
cd ~/lab9-rnaseq
```

Install [a bunch of R software](https://github.com/ngs-docs/2020-ggg-201b-rnaseq/blob/latest/binder/environment.yml) into a conda environment called `rnaseq`:
```
mamba env create -n rnaseq -f binder/environment.yml
```
(This will take a while!)

Activate that conda environment:
```
mamba activate rnaseq
```

Activate the RStudio server module:
```
module load rstudio-server/2022.12.0
```

Run `rstudio-launch`:
```
rstudio-launch
```

and follow the instructions to set up your ssh tunneling connection and log in to the RStudio Server.

### Double check things

In your Console window for RStudio Server, you should be able to scroll up and see that you're using the version of R that was installed by mamba:

>R version 4.0.5 (2021-03-31) -- "Shake and Throw"
Copyright (C) 2021 The R Foundation for Statistical Computing
Platform: x86_64-conda-linux-gnu (64-bit)

## Run the RNAseq workflow at the command line

From within RStudio, start a terminal and activate your conda environment:
```
mamba activate rnaseq
```

Change to the lab 9 directory we created above:
```
cd ~/lab9-rnaseq
```

Now, run the snakemake workflow:
```
snakemake -j 4 --use-conda
```

@@examine files

## Load the RMarkdown document

Open the `rnaseq-workflow.Rmd` file in `lab9-rnaseq`.

Select `knit`, `knit to HTML`.

(Say "yes" to installing new RMarkdown packages.)

It may fail the first time; if so, just rerun things by selecting `knit`, `knit to HTML`.




