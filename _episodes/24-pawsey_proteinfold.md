---
title: "Run nf-core/proteinfold on Setonix"
teaching: 5
exercises: 10
questions:
objectives:
- Locate the resources available to support running alphafold on Setonix.
- Prepare a nextflow configuration file to utilise available resources.
- Run nfcore/proteinfold nextflow workflow on Setonix.
keypoints:
- Software designed for GPU execution is often compiled for Nvidia GPUS.
- Custom images can be built to support execution using AMD GPUs
- Workflows can be configured to use custom images
---

<p align="center">
<img src="/assets/img/abacbs-proteinfold-metromap.svg" alt="pfold" width="800"/>
</p>

## AMD-compatible images
The proteinfold workflow includes several modules that are executed on the GPU. Default hosted containers support Nvidia hardware and will not run on the Setonix AMD gpus. Pawsey provides a number of pre-built, AMD-compatible images which can be used to run structure prediction models. We can configure the workflow to use these images using a custom NextFlow config.

Inspect the `abacbs_workshop.config` from the workshop repo to to see that workflow modules are configured to use non-standard images available on Setonix.

```
withName: 'RUN_ALPHAFOLD2' {
    container = '/software/projects/pawsey1017/tlitfin/ABACBS/containers/alphafold2.sif'
    time = { 12.h }
    cpus = 8
}
```

## Reference data
Recall that structure prediction relies on collecting homologous proteins in a multiple sequence alignment (MSA) to identify coevolutionary information indicating likely structural contacts. These homologs are identified from enormous reference sequence databases. Check that these databases are available on Setonix.

```bash
tree /scratch/references/alphafold_minidbs/databases/
```

You should see that alphafold databases are available here.

```
databases/
    ├── bfd
    ├── mgnify
    ├── params
    ├── pdb70
    ├── pdb_mmcif
    ├── pdb_seqres
    ├── small_bfd
    ├── uniprot
    ├── uniref30
    └── uniref90 
```

> ## Note
> Today, we are using miniature versions of the databases to reduce execution time for the purpose of the workshop but real versions are available at `/scratch/references/alphafold_feb2024/databases/`.
{: .keypoints}

## Prepare samplesheet

We can prepare a nextflow samplesheet containing our protein input in fasta format. Multiple proteins can be predicted with a single workflow execution by adding rows to an input samplesheet.

``` csv
id,sequence
prot1,fasta/A0A0U5EPG3.fasta
```

## Basic run

<p align="center">
<img src="/assets/img/abacbs-af2-normal.png" alt="af2normal" width="600"/>
</p>


``` bash
nextflow run nf-core/proteinfold/ --input samplesheet.csv \
    --outdir test-out --db /scratch/references/alphafold_minidbs/databases/ --mode alphafold2 --use_gpu --alphafold2_mode "standard" \
    -c abacbs_profile.config --slurm_account $PAWSEY_PROJECT -r 09ac089
```

> ## 

> ## Job monitoring
> We can confirm that our job is running with: 
> 
> ~~~
> squeue --me
> ~~~
> {: .source}
>
> ~~~
> JOBID        USER ACCOUNT                   NAME EXEC_HOST ST     REASON START_TIME       END_TIME  TIME_LEFT NODES   PRIORITY       QOS
> 34776177  tlitfin pawsey1017-gpu  nf-NFCORE_PROT nid002166  R       None 08:15:36         20:15:36   11:36:58     1      75399    normal
> ~~~
>
> Similarly, we can connect to  
{: .prereq}

A basic run of AlphaFold2 using the official implementation consumes XX SUs on the Setonix system. However, a significant portion of execution time is used to search large sequence databases and does not utilize the allocated GPU.

## Split MSA

<p align="center">
<img src="/assets/img/abacbs-af2-split.png" alt="af2split" width="800"/>
</p>

``` bash
nextflow run ../workflow/proteinfold/ --input samplesheet.csv \
    --outdir test-out --db ../databases/ --mode alphafold2 --use_gpu --alphafold2_mode "split_msa_prediction" \
    -c abacbs_profile.config --slurm_account $PAWSEY_PROJECT
```

Compare the SU rate of XX node compared to YY node.

```
XX: 
YY: 
```

As a result, running AlphaFold2 in split mode whereby CPU-bound search is conducted on a CPU node significantly reduces SU consumption.