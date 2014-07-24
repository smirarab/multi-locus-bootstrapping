Multi-locus bootstrapping
=========================

This simple script creates the sets of bootstrap replicates for multi-locus bootstrapping. It can create replicates for both site-only and gene/site resampling strategies (see [Seo, 2008](http://www.ncbi.nlm.nih.gov/pubmed/18281270)). 

### How does this work?
Multi-locus bootstrapping as implemented in this package works as follows. Imagine you have genes `g1` to `gk` and also imagine for each gene, you have performed bootstrapping. This script assumes you have a directory (say `dir`) under which there is one directory for each gene (say `g1` to `gk`), and under each `dir/gi` directory, you have a file (say `dir/gi/bootstrap`) that includes `n` the bootstrap replicates for that gene. Also imagine we want to do `m <= n` replicates of multi-locus bootstrapping. 

**site-only resampling:**  When site-only resampling is used, we simply create the following files:

```
    BS.1:  g1[1] g2[1]  ... gk[1]
    BS.2:  g1[2] g2[2]  ... gk[2]
    ...
    BS.m:  g1[m] g2[m]  ... gk[m]
```
where `gi[j]` is line `j` in `dir/gi/bootstrap`. Thus all the first lines are simply put together, all the second lines together, and so on. Now, `BS.1` to `BS.m` each include `k` lines and can be used as input to the summary method. 


**gene/site resampling:** First, for each bootstrap replicate, `k` genes are selected at random with replacement (given a random seed number). Then, for each replicate `1` to `m`, we count how many times each gene is sampled (for example, `g1` might be sampled three times in the first replicate and 0 times in the second replicate). Then, we go through the bootstrap replicates of each gene and add the sampled number of lines to each multi-locus bootstrap replicate. 
For example, if `g1` is sampled three times for `BS.1`, never for `BS.2`, and twice for `BS.3` and `g2` was sampled once for `BS.1`, three times for `BS.2`, and twice for `BS.3`we will have something like:

```
    BS.1:  g1[1] g1[2] g1[3] g2[1] ... 
    BS.2:  g2[2] g2[3] g2[4] ... 
    BS.2:  g1[4] g1[5] g2[5] g2[6] ... 
    ...
    BS.m: ...
```

Thus, here, the number of bootstrap replicates selected from each gene is varied from one multi-locus bootstrap replicate to the next. However, each `BS.i` is guaranteed to have `k` lines in it. Also note that since some genes will be by chance selected more often than others, we need that `m < n`. For example, with `n = 200`, usually `m` can be at most around 160. 

### Code Usage:

To run the code, execute:

```
   multilocus_bootstrap_new.sh [number of replicates] [dir] [FILENAME] [outdir] [outname] [sampling] [weightfile] [random seed]
```
where:

   - `dir`: should be a directory that includes only one directory per gene
   - `FILENAME`: `dir/*/FILENAME` should give the name of gene tree  bootstrap files (one file per gene, and one line per gene bootstrap replicate in each file)
   - `outdir`: is where the results will be placed
   - `outname`: is the prefix of the outuput files
   - `sampling`: can be either `site` or `genesite` (for site-only and gene/site resampling respectively).
   - `weightfile`: if anything other than `-` is given, each gene `go` is multiplied by the number of lines in `dir/gi/weightfile`. This is useful to upweight or down weight some genes. 
   - `seed`: a random seed number (leave blank and it will use `$RANDOM`)

For example, 

```
   multilocus_bootstrap_new.sh 160 allgenes raxmlboot/RAxML_bootstrap.allbs mlbs BS genesite - 424
```

will create 160 bootstrap replicate files under `mlbs/BS.1` to `mlbs/BS.160` using gene bootstrap files under `allgenes/*/raxmlboot/RAxML_bootstrap.allbs`. It will use gene/site resampling, without any gene weighting, and will use 424 as the random seed value. As it runs, it also tells you how many times each gene is sampled overall and what is the weight of that gene (1 for all genes means no weighting is used).

---
Report bugs or questions to `smirarab@gmail.com`.