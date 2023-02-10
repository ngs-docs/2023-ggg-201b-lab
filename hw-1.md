---
tags: ggg, ggg2023, ggg201b
---
# GGG 201(b), Lab Homework 1 - 2023

[![hackmd-github-sync-badge](https://hackmd.io/rI34wge-ScmkSQ0mIx2QTQ/badge)](https://hackmd.io/rI34wge-ScmkSQ0mIx2QTQ)

([Permanent link on github](https://github.com/ngs-docs/2023-GGG201b-lab/blob/main/hw-1.md))

Due by 10pm, Wed Feb 22nd

Please note that you may freely work together with others, but you must hand it in separately.

## 1. Sign up for GitHub Classroom

(Estimate: 10 min)

Sign up for GitHub Classroom using [this link](https://classroom.github.com/a/zK6NrA5X). This may involve creating a (free) GitHub account.

## 2. Log into farm & clone your github repo for hw1

(Estimate: 5 minutes)

Create a personal access token (PAT) [per GitHub instructions](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token); you'll need to select the 'repo' scope for it, and I suggest using a 90 day expiration period. 

::::warning
The PAT is a long string of nonsense characters (usually starting with `ghp_`) that you will use in place of a password, below.

Be sure to save your PAT somewhere where you can find it.

You can run
```
git config credential.helper store
```
once on farm, so that your PAT will be stored in your account for you, too.
::::

Then, on farm, clone your assignment repository, which will be something like `https://github.com/ngs-docs/2023-ggg-201b-lab-hw1-ctb`; do so like this,

```
git clone YOUR_REPO_URL ~/201b-lab-hw1
```
here use your github username along with your PAT as a password.

Change into that directory:
```
cd ~/201b-lab-hw1/
```

and start up a 2 hour session on a compute node:
```
srun -p high2 -t 2:00:00 -c 4 --mem=10GB --pty bash
```

**You'll need to use srun, change directories, and activate the `vc` conda environment every time you log in to farm.**

## 3. Run the current workflow

(Estimate: 15 minutes)

Make sure you can successfully run the current workflow:

```
snakemake -j 1 -p call_variants
```
which should produce a file `SRR2584857_1.x.ecoli-rel606.vcf`.

## 4. Edit and update your Snakefile

::::warning
Warning: GitHub provides an online editor that you can use to edit your Snakefile. You can use it if you wish, but it can get confusing (for you, not me :) if you edit your files on farm and also on github because you will need to synchronize them ("merge" the changes) between github and farm.

Read below for more info.
::::spoiler
git is a version control system that manages changes to text files, and github is a site for hosting git repositories. They can get complicated pretty fast; you can read [this tutorial](https://ngs-docs.github.io/2021-august-remote-computing/keeping-track-of-your-files-with-version-control.html) for a basic introduction.

The tl;dr: if you make changes on GitHub and want to "pull" them to farm, you can do:
```
git pull origin main
```
If you make (commit) changes on farm and want to push them to GitHub, you can do:
```
git push origin main
```
You cannot (1) make changes on GitHub, (2) make independent changes on farm, and (3) push changes from farm to GitHub. You must first merge them by doing a pull.

(Honestly it's easiest if you just edit your files in just one place...)
::::

---

1. (a) Add three more samples to the Snakefile, and create a default rule so that all four samples are run. (b) Rename the final VCF file so that it is named `.sensitive.vcf`. (c) Then add the filter command from the bottom of [Lab 5](https://hackmd.io/GcvRjLFIQqaAViuDNj-1hQ?view) to a new rule that runs on the `.sensitive.vcf` file and creates a file named `.specific.vcf`.

Please revisit [Lab 5 notes](https://hackmd.io/GcvRjLFIQqaAViuDNj-1hQ?view) to copy the four data files into your account.

Add them to your Snakefile so that (a) running `snakemake -j 1` without any targets generates two sets of VCF files for each data set.

Please make sure of the following:

* Running `snakemake -j 1 -p` by itself should go from raw data to final results for all samples;
* All of the generated files (including intermediates) are in at least one 'output:' annotation, so that e.g. `snakemake --delete-all-output` removes all of the generated files in the directory.

## 5. Commit and push your changes back to github.

At any time, do the following to save changes.

```
git commit -am "updated Snakefile"
git push origin main
```
(you can do this as many times as you want, and save intermediate changes, etc. etc.)

One important note is that the only thing you should put in github are
the updated files (Snakefile, in this case). All of the
_generated_ files can be reproduced from the repo and so you don't need
to add them.

**Please only commit changes to the Snakefile, and don't add any other files with `git add`.**

## 6. Relax in knowledge of a job well done.

Please do inspect the Snakefile on github via the Web interface to make sure it has all your changes!

## Questions? Problems?

If you run into any trouble with submission, that's ok - just let me know.

You can reach me via e-mail at ctbrown@ucdavis.edu, or in UC Davis slack under @ctitusbrown.

<!-- tell them: check for number of variants; run commands from scratch in empty directory; check diff sens/spec 
-->