---
tags: ggg, ggg2023, ggg201b
---

# Lab 10 NOTES - GGG 201b, March 10th, 2023

[Permanent link on GitHub: lab-10.md](https://github.com/ngs-docs/2023-ggg-201b-lab/blob/main/lab-10.md)

## Running the RNAseq workflow on farm

Start by sshing into farm and do an srun:
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

and follow the instructions to set up your ssh tunneling connection, and then log in to the RStudio Server.

### Double check the version of R in RStudio

In your Console window for RStudio Server, you can run:

```
R.Version()[13]
```

and you should see:

>[1] "R version 4.0.5 (2021-03-31)"

If you don't see this, RStudio Server is not running in the right conda environment -
* you either didn't run `conda run ...` above, or
* the conda environment `rnaseq` isn't configured properly.

### Check that your RNAseq workflow files are up to date

In the terminal window, run:
```
conda activate rnaseq
