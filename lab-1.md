---
tags: ggg, ggg2023, ggg201b
---

[![hackmd-github-sync-badge](https://hackmd.io/cD4Gl7uET9SLF3LxvKDTTg/badge)](https://hackmd.io/cD4Gl7uET9SLF3LxvKDTTg)



([Permanent URL](https://github.com/ngs-docs/2023-ggg201b-lab/blob/main/lab-1.md))

# GGG 201b, Jan 2023 - Lab 1 NOTES

## Friday Lab Links - 1/13

* [syllabus](https://hackmd.io/MCWqGjO3S0KdxJP10KPKNw?view)
* our first workflow: variant calling!!!!!!

## Introduction

## Introductions!

Hello! I'm Titus!

* prof in Vet Med
* research focus is metagenomics specifically and genomics more broadly
* emphasis on data analysis, data reuse, automation, reproducibility, open science, open source, etc. etc.
* also very community focused (open, online)
* Faculty Director of Community Initiatives here at DataLab

I use everything I'll be teaching you here on a daily basis. It's very much _au courant_ in terms of data science and data analysis in bioinformatics.

On a personal level, I'm old but I think not like super old; I have two kids; I like cats and dogs both; I'm an infovore; and I like reading SF&F as well as (recently...) LitRPG.

I love swimming, like yoga, and hate running, but do all three. :shrug:

I blog at http://ivory.idyll.org/blog/ and I tweet at http://twitter.com/ctitusbrown and I mastodon at https://genomic.social/@ctb.

## Day 1: Variant Calling

learning goals:
- introduce basic concepts of variant calling
- understand the basics of running shell commands via snakemake
- download and examine some data

### A very brief discussion of variant calling

...

### Start a binder

We'll be working with the files in [this repository](https://github.com/ngs-docs/2023-ggg-201b-variant-calling/tree/lab1), but we'll be doing it in an interactive "binder".

Click below and open in a new tab:

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/ngs-docs/2023-ggg-201b-variant-calling/lab1?urlpath=rstudio)

wait 20 or 30 seconds...

### Open a terminal

Go to the 'Terminal' tab. You'll end up with a long prompt that starts
with 'jovyan@...'.  This is the command line, or "shell", or "UNIX
shell". (See
[Lesson 1 in the Remote Computing workshop series](https://ngs-docs.github.io/2021-august-remote-computing/index.html)
for background info.)

First, reset the prompt to be shorter:
```
PS1='$ '
```

### Run some stuff!

There are lots of "rules" in the Snakefile.

Try:

```
grep rule Snakefile
```

and open the Snakefile in the RStudio editor to look through the file; you can also [(view it on GitHub, too)](https://github.com/ngs-docs/2023-ggg-201b-variant-calling/blob/lab1/Snakefile)!

The file consists of a list of rules.  We will discuss:

* what is the structure of a rule?
  * 'rule' block
  * indentation
  * "shell" and "conda" as well as others
* what does shell mean here?
* what is the "conda" block? we'll talk about that in a sec...

----

Let's run a rule! Try the following command:
```
snakemake -p -j 1 map_reads
```

This says, "run the shell command in the Snakefile under the 'map_reads' rule".

`-p` means "show the shell command that you're running".

`-j 1` means "run one command a time". This will be relevant later ;).

BUT. This command will fail! Why?

Because we don't have any data! We need to download some stuff and prepare it.

Start with

```
snakemake -p -j 1 download_data
```

That worked - that's what the green text signifies.

What does this command do? It creates the file `SRR2584857_1.fastq.gz`,
which contains a bunch of sequencing data; we'll look at this file in more
detail later. For now, notice that this file appeared in the lower right window of the RStudio page!

Now run some more rules, one at a time.

```
snakemake -p -j 1 download_genome
```
This downloads the file `ecoli-rel606.fa.gz`.

Now run:
```
snakemake -p -j 1 map_reads
snakemake -p -j 1 sam_to_bam
snakemake -p -j 1 sort_bam
snakemake -p -j 1 call_variants
```

This runs a complete (if fairly simple :) variant calling workflow.

The result is a file called `SRR2584857_1.ecoli-rel606.vcf`.

View the results in the RStudio window by clicking on that file in the file
browser.

You can also go to the shell prompt and execute:

```
samtools index SRR2584857_1.x.ecoli-rel606.bam.sorted
samtools tview -p ecoli:4314717 --reference ecoli-rel606.fa SRR2584857_1.x.ecoli-rel606.bam.sorted
```
which will show you the actual aligned reads.

In the alignment,
* the top line are the positions in the E. coli genome (specified in the command line to samtools tview)
* the second line is the sequence in the reference genome
* the third lines and below are reads mapped to that position
* a `.` means "this read agrees with the reference, in the forward direction"
* a `,` means "this read agrees with the reference, in the reverse direction"
* an A/C/T/G means "this read **disagrees* with the reference"

Questions?

A few things to discuss about snakemake:

* these are just shell commands, with a bit of "decoration". You could run them yourself if you wanted, outside of snakemake.
* the order of the rules in the Snakefile doesn't matter, with one exception - the first one is what's run if no rule name is specified. More on that later.
* rules can have one or more shell commands, one after the other on their own lines - see the last rule!
* `snakemake -p` prints the command that is being executed
* `-j 1` says "run only one thing at a time". More on this later.
* snakemake prints things out in red if the command fails.
* it's all case sensitive...
* tabs and spacing matter!

Some foreshadowing:

* wouldn't it be nice to not have to run each rule one by one, in the right order?
* wouldn't it be nice if the command didn't rerun if it had already run?

### End of class recap

* Snakefile contains a snakemake workflow definition
* the rules specify steps in the workflow
* At the moment (and in general), they run shell commands - this can include R and Python scripts, for example.

Revisit: what workflow are we running?
