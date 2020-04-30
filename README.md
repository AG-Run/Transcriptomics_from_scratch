# Transcriptomics_from_scratch
This is a pipeline to perform data filtering, and mapping using RNA-Seq data

## Data filtering
This process is made for trimming and low quality removal data

```
#!/bin/bash
#SBATCH -D .
#SBATCH -o trimm_all_files.out
#SBATCH -e trimm_all_files.err
#SBATCH -J trimm_all_f
#SBATCH -n 3
#SBATCH -p gpu

module load software/bioinformatics/trimmomatic/0.36

time perl trimpoly_batch.pl AM.list;
```

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |


[I'm an inline-style link](https://www.google.com)


