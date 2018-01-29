---
layout: post
title: The metagenome centric gene classifier
modified: 2018-01-01
excerpt: ""
comments: true
authors: [alon]
categories: [anvio]
---

{% include _toc.html %}

{:.notice}
This post will only work for anvi'o `v4` or later.




## Working with the MCGC as a programmer

### I just want a table of gene presence/absence in samples

The MCGC class can generate a table with `True`/`False`/`None` for each gene in each sample.
 
`True` - the gene is present in the sample.
 
`False` - the gene is not present in the sample.
 
`None` - the presence/absence of the gene was ambiguous and could not be determined.

This is generated using a threshold for the detection value for a gene, where, dy default:

    0-0.25 -> False
    0.25-0.75 -> None
    0.75-1 -> True

This could be changed by assigning a different value to `args.alpha` (by default, `alpha=0.25`, which leads to the aforementioned thresholds).

To get this table, all you need is a list of your samples, and a `gene_level_coverage_stats_dict`.
Below you can find an exmple of how to get the gene presence/absence table for a given bin:

``` python
# setting up the args for MetagenomeCentricGeneClassifier
args = argparse.Namespace(profile_db = 'PROFILE.db', \
                          contigs_db = 'TEST.db', \
                          output_file_prefix = 'TEST12', \
                          collection_name = 'TEST')

# Start the summary object
# This is needed to access gene_level_coverage_stats_dict, and split_coverage_values_per_nt_dict in bins
args.init_gene_coverages = True
args.populate_nt_level_coverage = True
summary = summarizer.ProfileSummarizer(args)
summary.init()
# let's take 'Bin_1' for example
bin_name = 'Bin_1'

mcg = MetagenomeCentricGeneClassifier(args)
mcg.init_samples(summary.p_meta['samples'])
_bin = summarizer.Bin(summary, bin_name)
mcg.init(_bin.gene_level_coverage_stats_dict)

mcg.init_gene_presence_absence_in_samples()
```

