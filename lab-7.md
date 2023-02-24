---
tags: ggg, ggg2023, ggg201b
---

# Lab 7 NOTES - GGG 201b, Feb 24, 2023

## Friday Lab links

* [Last week's assembly exercise notes](https://hackmd.io/-BPAegsvQqeB5yET0esbJA?both)
* [GGG201B Lab: Visual exploration of genomic data with the Integrative Genomics Viewer (IGV)](https://hackmd.io/@dcsoto/S1mDm3H0s)

## Logging in

:::spoiler **Logging into farm and running RStudio Server**

## Quick start: logging into farm and running RStudio Server

Log into farm with ssh/MobaXterm - [link](https://hackmd.io/n7_pXRiiRQ-YpQBQ93uW9Q?view#1-Logging-into-farm).

Then run:
```
srun -p high2 --time=3:00:00 --nodes=1 \
    --cpus-per-task 4 --mem 10GB --pty /bin/bash
```
and wait for a new prompt. Then run:
```
module load spack/R/4.1.1
module load rstudio-server/2022.07.1
rserver-farm
```

Then, 
* in a new shell, run the ssh tunneling command output by `rserver-farm` above;
* connect to the `localhost` URL output by `rserver-farm`;
* Login with your username and the one-time password output by `rserver-farm`;
* start a Terminal.
:::

## Install assembly software

Create a new conda/mamba environment like so:
```
mamba create -n assembly -y megahit prokka quast
```

Question: for those of you familiar with the modules subsystem, what modules would you want to load here?

## Activate software environment & change to lab7 working directory

```
conda activate assembly
mkdir -p ~/lab7/
cd ~/lab7/
```

## Copy in some raw data files

Let's copy in some paired-end sequencing from [the E. coli Long Term Evolution Experiment](https://www.ebi.ac.uk/ena/browser/view/PRJNA295606):
```
cp ~ctbrown/data/ggg201b/SRR2584857_*.fastq.gz .
```

This gives us two files, containing paired end sequencing:
```shell
$ ls -lh

>total 180M
>-rw-rw-r-- 1 datalab-02 datalab-02 180M Feb 24 >07:41 SRR2584857_1.fastq.gz
>-rw-rw-r-- 1 datalab-02 datalab-02 180M Feb 24 07:41 SRR2584857_2.fastq.gz
```

## Generate an assembly

[MEGAHIT](https://github.com/voutcn/megahit) is an assembler for microbial genomes and metagenomes that is fast and memory efficient; it's my first suggestion for looking at data, even though you might end up getting better results with a different assembler in the end.

Run the megahit assembler:
```
megahit -1 SRR2584857_1.fastq.gz -2 SRR2584857_2.fastq.gz -f -m 5e9 -t 4 -o SRR2584857_assembly
```
and check out the contents of the assembly file:
```
head SRR2584857_assembly/final.contigs.fa
```

Save the file into a nicer name:
```
cp SRR2584857_assembly/final.contigs.fa SRR2584857-assembly.fa
```

## Summarize the assembly

The [quast software](https://quast.sourceforge.net/) is a nice way to generate some summary statistics.

Run `quast` to summarize the assembly:
```
quast SRR2584857-assembly.fa
```
and look at the results:
```
cat quast_results/latest/report.txt
```

## Annotate the assembly using prokka:

The [prokka software](https://github.com/tseemann/prokka) does a really nice job of annotating bacterial genomes!

```
prokka --prefix SRR2584857_annot SRR2584857-assembly.fa
```

We will use this set of annotations and information for the next step - viewing the genome assembly!

## Using IGV

[GGG201B Lab: Visual exploration of genomic data with the Integrative Genomics Viewer (IGV)](https://hackmd.io/@dcsoto/S1mDm3H0s)
