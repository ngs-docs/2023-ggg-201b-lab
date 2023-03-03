---
tags: ggg, ggg2023, ggg201b
---

# Lab 8 NOTES - GGG 201b, March 3rd, 2023

[![hackmd-github-sync-badge](https://hackmd.io/Qk0fJEMMRZ-tZUZ8Wh25wg/badge)](https://hackmd.io/Qk0fJEMMRZ-tZUZ8Wh25wg)

[Permanent link on GitHub: lab-8.md](https://github.com/ngs-docs/2023-ggg-201b-lab/blob/main/lab-8.md)

## Friday Lab links

* [Lab 7 notes](https://hackmd.io/JGa63KT3ReS51c801OUJTA?both)

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

## Making sure you have installed the assembly software

Try:
```
mamba activate assembly
```

:::danger
If it doesn't work, you'll need to run:
```
mamba create -n assembly -y megahit prokka quast
```
in order to install the software in the conda environment.
:::

## Activate software environment and install new software
```
conda activate assembly

mamba install -y snakemake-minimal minimap2 bcftools samtools
```

## Get assembly Snakefile and data

Clone [the github repository](https://github.com/ngs-docs/2023-ggg201b-assembly) for today:
```
cd ~/
git clone https://github.com/ngs-docs/2023-ggg201b-assembly ~/lab8/
cd ~/lab8/
```

Copy in data as well:
```
cp ~ctbrown/data/ggg201b/SRR2584857_*.fastq.gz .
```

## Run snakemake to generate assembly & annotations

```
snakemake -j 4 -p
```

While it's running, let's check out [the Snakefile](https://github.com/ngs-docs/2023-ggg201b-assembly/blob/main/Snakefile).

Things to mention and discuss:
* This is a combination of [the commands from last week's lab](https://hackmd.io/JGa63KT3ReS51c801OUJTA?view) and the variant calling Snakefile.
* isn't it nice that you can see what the inputs and outputs are for each command? ;)
* are we doing variant calling? why or why not?
* what is this `directory` thing about?
    * Answer: this tells snakemake that the command produces the given _collection_ of files, and that it can safely reproduce the directory using this rule.
* what does this `threads` thing mean?
    * Answer: this tells snakemake how much compute power (`{threads}` CPUs) this rule _can_ use.
    * It is passed into the shell command via `{threads}`
    * The _actual_ value that is used is the minimum of how many CPUs you give snakemake to use (`-j N`) and the value in the `threads:` block - this is so that you do not use _more_ CPUs than you have allowed snakemake to use.
    * If you don't do this, sometimes programs will grab all the CPUs (more than specified with srun) and get killed... life is hard!

## Calculating some assembly statistics

### Number of reads that mapped

Run:
```
samtools flagstat SRR2584857_reads.x.SRR2584857_assembly.bam
```

Look at:
* primary mapped
* properly paired
* number of singlletons
* number with mate mapped to a different chr

What does this mean?

Here, what is "chr"?

### N50 and L50

Run:
```
cat SRR2584857_quast/report.txt
```

I asked ChatGPT: 

what do N50 and L50 mean in genome assembly?

ChatGPT responded:

>In genome assembly, N50 and L50 are measures used to evaluate the quality of an assembly.

>N50 is the length of the shortest sequence such that 50% of the entire genome is contained in sequences of that length or longer. In other words, it is the length of the sequence at which half of the total assembled genome is contained in sequences of that length or longer. The higher the N50 value, the better the assembly.

>L50, on the other hand, is the number of sequences that are needed to reach 50% of the total assembled genome. In other words, it is the number of sequences that are required to cover 50% of the total genome. The lower the L50 value, the better the assembly.

>Together, N50 and L50 are useful measures for assessing the contiguity and completeness of a genome assembly. They are widely used in genomics research to compare different assemblies or to evaluate improvements made to an assembly.

### Number of genes

Run:
```
cat SRR2584857_annot/SRR2584857_annot.txt
```

Is this good? Bad? How would we know??

* For microbial genomes, we could use [CheckM](https://ecogenomics.github.io/CheckM/) to assess the number of "essential" genes that are in a genome => completeness.
* For eukaryotic genomes, we could use [BUSCO](https://busco.ezlab.org/), which assesses genome assembly and annotation completeness with single-copy orthologs.

### Coverage (based on mapping)

Run:
```
samtools depth -a SRR2584857_reads.x.SRR2584857_assembly.bam.sorted  \
    |  awk '{sum+=$3} END { print "Average = ",sum/NR}'
```

## Discussion topics

* raw data -> "hypothesis"
* number of mapped reads; number of paired mapped reads; and what do we do with the unmapped reads??
* N50 and L50
* why do assemblies differ between thee and me? Why does everyone have a slightly different set of N50 and L50 numbers?
* how do annotation systems work, anyway?


### What do you do with assemblies?

- first and foremost: annotate to find genes
- then do RNAseq with them ;)
- if assembly is quite good, chr structure and so on
    - but this is usually not possible with short-read assemblies... you need additional information.