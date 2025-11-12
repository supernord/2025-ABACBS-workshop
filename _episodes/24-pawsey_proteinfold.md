---
title: "Run nfcore/proteinfold on Setonix"
teaching: 5
exercises: 10
questions:
objectives:
- Run nfcore/proteinfold on Setonix
keypoints:
- Custom images support AMD GPUs
- Workflows can be configured to use custom images
---
### AMD-compatible images
The proteinfold workflow includes several modules that are executed on the GPU. Default hosted containers support Nvidia hardware and will not run on the Setonix AMD gpus. Pawsey provides a number of pre-built, AMD-compatible images which can be used to run structure prediction models. We can configure the workflow to use these images using a custom NextFlow config.

Inspect the `abacbs_workshop.config` from the workshop repo to to see that workflow modules are configured to use non-standard images available on Setonix.

```
withName: 'RUN_ALPHAFOLD2' {
    container = '/software/projects/pawsey1017/tlitfin/ABACBS/containers/alphafold2.sif'
    time = { 12.h }
    cpus = 8
}
```

### Reference data
Recall that structure prediction relies on collecting homologous proteins in a multiple sequence alignment (MSA) to identify coevolutionary information indicating likely structural contacts. These homologs are identified from enormous reference sequence databases. Check that these databases are available on Setonix.

```
ls /scratch/references/alphafold_minidbs/databases/
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
> 
> We are using minituare versions of the databases for the purpose of the workshop but real versions are available at `/scratch/references/alphafold_feb2024/databases/`
{: .caution}

### Prepare samplesheet

We can prepare a nextflow samplesheet containing our protein input in fasta format. Multiple proteins can be predicted with a single workflow execution by adding rows to an input samplesheet.

``` csv
id,sequence
prot1,fasta/A0A0U5EPG3.fasta
```

### Basic run

``` bash
nextflow run ../workflow/proteinfold/ --input samplesheet.csv \
    --outdir test-out --db ../databases/ --mode alphafold2 --use_gpu --alphafold2_mode "standard" \
    -c abacbs_profile.config --slurm_account pawsey1017
```

A basic run of AlphaFold2 using the official implementation consumes XX SUs on the Setonix system. However, a significant portion of execution time is used to search large sequence databases and does not utilize the allocated GPU.

### Split MSA

proteinfold supports 

``` bash
nextflow run ../workflow/proteinfold/ --input samplesheet.csv \
    --outdir test-out --db ../databases/ --mode alphafold2 --use_gpu --alphafold2_mode "split_msa_prediction" \
    -c abacbs_profile.config --slurm_account pawsey1017
```

Compare the SU rate of XX node compared to YY node.

```
XX: 
YY: 
```

As a result, running AlphaFold2 in split mode whereby CPU-bound search is conducted on a CPU node significantly reduces SU consumption.