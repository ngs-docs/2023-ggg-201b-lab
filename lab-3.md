---
tags: ggg, ggg2023, ggg201b
---

# Lab 3 NOTES - GGG 201b, Jan 2023

[toc]

([Permanent URL](https://github.com/ngs-docs/2023-ggg201b-lab/blob/main/lab-3.md))

## Friday Lab Links - 1/27

@@preamble

## Updating our Snakefile with more input/output

Start your binders -

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/ngs-docs/2023-ggg-201b-variant-calling/lab3?urlpath=rstudio)

### Upgrading our Snakefile by adding `output` and `input:` to more rules:

To the `download_genome` rule, add:
```python=
    output: "ecoli-rel606.fa.gz"
```

and to the `map_reads` rule, adjust it to look like this:

```python=
    input:
        ref="ecoli-rel606.fa.gz", 
        reads="SRR2584857_1.fastq.gz"
```

What does this do?

This tells snakemake that `map_reads` depends on having the input files `ecoli-rel606.fa.gz` and `SRR2584857_1.fastq.gz` in this directory, and that `download_genome` can produce the ecoli file!!

What's extra cool is that snakemake will now automatically run the rule that outputs this file (which is `download_genome`) before running `map_reads`!

Try it:

```
rm *.gz *.sam
snakemake -p -j 1 map_reads
```
and you'll see that it runs two things: first the `download_genome` rule, then the `map_reads` rule.

What is the output of the rule `map_reads`, and how would you find that out in general?

### In-class exercise A

Add "output:" with the correct filename to the rule `map_reads`.

Hint: use the RStudio file browser window to look at what changes in the directory when you run the command. You can also use the RStudio terminal window to run `ls -l --sort=time` after running the rule.

### Formatting snakefiles

A few quick notes:
* input: and output: blocks (and other blocks) can be in any order, as long as they are before shell.
* you can either put things all on one line, or form a block by indenting.
* you can make lists for multiple input or output files by separating filenames with a comma
* rule names can be any valid variable, which basically means letters and underscores; you can use numbers after a first character.

### Rewriting the rules to have less duplication by using `{input}` and `{output}`

If you look at the rules, you'll see that input and output filenames are
specified multiple times. That's rude! (And potentially confusing, if you
change the `input:` block and forget to change the `shell:` block, or
something. Not that I've ever done that.)

Luckily, snakemake lets you use *template variables* in the `shell:` block
so that you can just say `{output}` and it will figure out what to put there.

For `download_data`, change the shell command to be:
```
wget https://osf.io/4rdza/download -O {output}
```

(This will also help us out later in other ways, as you'll see!)

You can do the same with `{input}`.

### In-class exercise B

How would you fix the rules `download_genome` and `map_reads` to have
shell commands that make use of `{input}` and `{output}`?

Note: you can rerun everything by doing `rm *.sam *.gz` followed by
```
snakemake -j 1 map_reads
```

### In-class exercise C

How would you fix the rules `sam_to_bam`, `sort_bam` and `call_variants` to have the appropriate input: and output:, and `{input}` and `{output}`?

Hint: look at the shell command for those rules.

### Running lots of commands all at once

If you've fixed the rules properly, you should be able to run everything up to the rule `call_variants` by just specifying `call_variants` - try it!

```
rm *.gz *.sam
snakemake -p -j 1 call_variants
```

### Re-running everything

You can tell snakemake to delete everything it knows how to make for a particular rule (including all preceding rules) by running
```
snakemake -j 1 --delete-all-output call_variants
```

(This can also be a good way to check to make sure you have all the output: information right, because you'll have files left over if you forgot to put them in output: :)

### Using filenames instead of rule names

You don't actually need to use the rule names (this will be important
later on!). You can just tell snakemake what file you want produced,
and run that.

So:
```
snakemake -p -j 1 SRR2584857_1.ecoli-rel606.vcf
```
will also work to run the rule `call_variants`, but you don't have to remember the rule name.

(Later on we'll see why this is important for other things.)

### Bonus: create a default rule

By default, if you don't specify a rule snakemake runs the first rule in the file.  (This is actually the only case where the order of rules in the Snakefile matters!)

So it's conventional to define a rule named `all` at the top; try it!

```
rule all:
    input:
        "SRR2584857_1.ecoli-rel606.vcf"
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

...and why is this all useful, anyway? We'll explore that in a bit more detail next Friday!

### Reminder

Once you're done creating that sorted bam file, you can also run

```
samtools index SRR2584857_1.ecoli-rel606.bam.sorted
samtools tview -p ecoli:4314717 --reference ecoli-rel606.fa SRR2584857_1.ecoli-rel606.bam.sorted
```

to actually _look_ at the aligned reads.

### End of class recap

* Snakefile contains a snakemake workflow definition
* the rules specify steps in the workflow
* At the moment (and in general), they run shell commands - this can include R and Python scripts, for example.
* You can "decorate" the rules to tell snakemake how they depend on each other.
* There are other reasons to do this that we'll get to next week!

Revisit: what workflow are we running?

