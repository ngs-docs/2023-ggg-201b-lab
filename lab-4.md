---
tags: ggg, ggg2023, ggg201b
---

# Lab 4 NOTES - GGG 201b, Feb 3, 2023

[![hackmd-github-sync-badge](https://hackmd.io/xfIznmofSIeYXk-F76qIJg/badge)](https://hackmd.io/xfIznmofSIeYXk-F76qIJg)

[toc]

([Permanent URL](https://github.com/ngs-docs/2023-ggg201b-lab/blob/main/lab-4.md))

## Friday Lab Links - 2/3

Today we're going to continue _generalize_ our Snakefile so that we can re-use the same rules for different files.

We'll also revisit the variant calling pipeline at the end. Promise

## But first - logging into farm and running RStudio there!

If you're not in GGG 298, you should have received an e-mail and password from me this morning.

(If you're in GGG 298, you can reuse that same account!)

Please follow [these intructions](https://hackmd.io/n7_pXRiiRQ-YpQBQ93uW9Q?view#1-Logging-into-farm) to log into farm and start up RStudio Server.

If you've done this successfully, please look around you and see if you can help others. I'll set up Windows and Mac UNIX breakout rooms for support, too :).

### Logging into farm - a short summary.

FIRST: log into farm with ssh.

Then run:
```
srun -p high2 --time=3:00:00 --nodes=1 \
    --cpus-per-task 1 --mem 5GB --pty /bin/bash
```
when that succeeds, run:
```
module load spack/R/4.1.1
module load rstudio-server/2022.07.1
rserver-farm
```

Take the information in the output, and then run the custom `ssh -L ...` command in a _new_ Terminal or MobaXterm window on your laptop.

Then go visit the localhost URL printed out by the rserver farm command, and use the password printed out by `rserver-farm`.

Once you're into RStudio, start a Terminal window. Voila!

Now run:
```
mamba create -n vc -y snakemake-minimal \
    bcftools=1.8 samtools=1.6 minimap2=2.24
```

and then
```
mamba activate vc
```
and you're now ready to go!

You'll need to run `mamba activate vc` if you log out and log back in.

You can read more about what's going on [here](https://hackmd.io/4Tm5i97QT5iDlZL-IC7U8A?view#Running-RStudio-Server).

## Alternate for this week only: binder.

Start your binders -

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/ngs-docs/2023-ggg-201b-variant-calling/lab4?urlpath=rstudio)

open a Terminal tab (not Console!),

and adjust your prompt if you like:
```
PS1='$ '
```

(For next week, you will want to have the farm login and RStudio above working.)

## Updating our Snakefile with a default rule & wildcard rules

### Revisit: Running lots of commands all at once

Now that we've connected all the rules with input/output, we can run everything _up to_ the rule `call_variants` by just specifying `call_variants` - try it!

```
rm *.gz *.sam
snakemake -p -j 1 call_variants
```

### Re-running everything

You can tell snakemake to delete everything it knows how to make for a particular rule (including all preceding rules) by running
```
snakemake -j 1 --delete-all-output call_variants
```

:::info
This can also be a good way to check to make sure you have all the output: information right, because you'll have files left over if you forgot to put them in `output:` :)
:::

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

### snakemake recap

* Snakefile contains a snakemake workflow definition
* the rules specify steps in the workflow
* At the moment (and in general), they run shell commands
* You can "decorate" the rules to tell snakemake how they depend on each other.
* This decoration comes in the form of "input:" and "output:" lists of one vor more files, quoted, separated by commas.
* You can then use `{input}` and `{output}` in your shell commands, too!
* input and output are how you connect rules: by saying which rules take which files as inputs and/or produce what outputs.
* Snakemake cares about tabs :)

### Reminder - viewing the reads and alignment.

Once you're done creating that sorted bam file, you can also run

```
samtools index SRR2584857_1.x.ecoli-rel606.bam.sorted
samtools tview -p ecoli:4314717 --reference ecoli-rel606.fa SRR2584857_1.x.ecoli-rel606.bam.sorted
```

to actually _look_ at the aligned reads.

### Recap thus far

* Snakefile contains a snakemake workflow definition
* the rules specify steps in the workflow
* At the moment (and in general), they run shell commands - this can include R and Python scripts, for example.
* You can "decorate" the rules to tell snakemake how they depend on each other.
* There are other reasons to do this that we'll get to next week!

### What's next? Fix the last rule up a bit.

Let's split the last rule up into two different rules:

* create ecoli-ref.fa
* call variants

FIRST, create a new rule `uncompress_ref`.

THEN, move the relevant input, output, and shell command stuff out of `call_variants` into the new rule.

### Then, further generalizing with wildcards!

search/replace:
* `SRR2584857_1` => `{sample}`
* `ecoli-rel606` => `{genome}`

Does the resulting Snakefile work?

What's going on here?