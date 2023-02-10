---
tags: ggg, ggg2023, ggg201b
---

# Lab 5 NOTES - GGG 201b, Feb 10, 2023

[![hackmd-github-sync-badge](https://hackmd.io/GcvRjLFIQqaAViuDNj-1hQ/badge)](https://hackmd.io/GcvRjLFIQqaAViuDNj-1hQ)


[toc]

([Permanent URL](https://github.com/ngs-docs/2023-ggg201b-lab/blob/main/lab-5.md))

## Friday Lab Links - 2/10

Today we're going to further _generalize_ our Snakefile so that we can re-use the same rules for different files. And we're going to work with larger (higher coverage) data sets!


## Getting started - logging into farm!

We suggest using RStudio to run the below commands as it will give you an editor and a terminal. **Follow [these instructions]((https://hackmd.io/n7_pXRiiRQ-YpQBQ93uW9Q?view#2-Start-and-connect-to-an-RStudio-Server-on-farm))** or see the spoiler below for a quickstart.

:::spoiler **Logging into farm and running RStudio Server**

## Quick start: logging into farm and running RStudio Server

Log into farm with ssh/MobaXterm - [link](https://hackmd.io/n7_pXRiiRQ-YpQBQ93uW9Q?view#1-Logging-into-farm).

Then run:
```
srun -p high2 --time=3:00:00 --nodes=1 \
    --cpus-per-task 1 --mem 5GB --pty /bin/bash
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

## Moving forward!

When you are all set, your prompt should look like this:


>~~~
>(base) datalab-01@farm:~$
>~~~

If it doesn’t, please alert me!

### Make sure software is installed

Next, run:
```
mamba create -n vc -y snakemake-minimal \
    bcftools=1.8 samtools=1.6 minimap2=2.24
```

You may get an error if it already exists - that's ok!

Now activate this mamba environment to use the installed collection of software:
```
mamba activate vc
```
and you're now ready to go!

### Download Snakefile for lab 5:

Run:
```
cd
git clone https://github.com/ngs-docs/2023-ggg-201b-variant-calling \
    lab5 -b lab5 
cd ~/lab5/
```

:::danger
### Logging back in if you get disconnected

You'll need to run two commands if you log out and log back in.

First, activate the `vc` conda environment:
```
mamba activate vc
```
Second, change to the ``~/lab5/`` directory:
```
cd ~/lab5/
```
:::

## Updating our Snakefile with a default rule & wildcard rules

### Revisit: Running the whole workflow all at once

Reminder: now that we've connected all the rules with input/output, we can run everything _up to_ the rule `call_variants` by just specifying `call_variants`:

```
snakemake -p -j 1 call_variants
```

### Revisit: what are we running?

![diagram of workflow](https://github.com/ngs-docs/2023-ggg-201b-lab/blob/main/lab-4/snakemake-graph.png?raw=true)

* what does each step do?
* what's the goal?

### Summarizing information from the alignments

Try:

```
samtools flagstat SRR2584857_1.x.ecoli-rel606.bam
```

:::warning
Why does this command not work?
```
samtools depth SRR2584857_1.x.ecoli-rel606.bam
```

Why does this last command not work?

What's the command that _does_ work?
:::

### Using filenames instead of rule names

You don't actually need to use the rule names (this will be important
later on!). You can just tell snakemake what file you want produced,
and run that.

So:
```
snakemake -p -j 1 SRR2584857_1.x.ecoli-rel606.vcf
```
will also work to run the rule `call_variants`, but you don't have to remember the rule name.

(Later on we'll see why this is important for other things.)

### Create a "default" rule

By default, if you don't specify a rule snakemake runs the first rule in the file.  (This is actually the only case where the order of rules in the Snakefile matters!)

So it's conventional to define a rule named `all` at the top; try it!

```
rule all:
    input:
        "SRR2584857_1.x.ecoli-rel606.vcf"
```

Question: why specify the _input_ for this rule, and no output or shell command!?

(There's no good reason. it's just a cute trick that matches the way
snakemake thinks. :shrug:)

Once you're done creating that sorted bam file, you can also run

```
samtools index SRR2584857_1.x.ecoli-rel606.bam.sorted
samtools tview -p ecoli:4314717 --reference ecoli-rel606.fa SRR2584857_1.x.ecoli-rel606.bam.sorted
```

to actually _look_ at the aligned reads.

### What's next? Fix the last rule up a bit.

Let's split the last rule up into two different rules:

The first rule will create `ecoli-ref.fa` by gunzipping `ecoli-ref.fa.gz`.

The second rule will call variants and create the vcf file.

FIRST, create a new rule `uncompress_ref`.

THEN, move the relevant input, output, and shell command stuff out of `call_variants` into the new rule.

### Further generalizing with wildcards!

search/replace:
* `SRR2584857_1` => `{sample}`
* `ecoli-rel606` => `{genome}`

Does the resulting Snakefile work?

What's going on here?

CTB: put full Snakefile here.

### Adding the complete file, and more files.

So far we've been working with a cut-down file; let's work with more data, and more files!

First, copy files over from my account on farm:
```
cp ~ctbrown/data/ggg201b/SRR258*_1.fastq.gz .
```

* rerun pipeline
* assessing depth
