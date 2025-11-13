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

## Setup environment

```bash
module load singularity/4.1.0-slurm
#module load nextflow/25.04.6 # This should still be loaded from the previous exercise
```

> ## Containers in Nextflow Workflows
> The previous exercise didn’t use containers, but they are one of the most effective ways to manage software in workflow development, especially with Nextflow.
>
> “Friends don’t let friends use conda/mamba with Nextflow, ESPECIALLY on HPC”
> **-Sarah Beecroft, Pawsey**
>
> **Why containers?**
>
> A container is a lightweight, portable environment that bundles an application together with everything it needs to run such as libraries, dependencies, and system tools. It is a simple and reliable alternative to installing software directly on your system or HPC environment (which often requires managing dependencies manually).
> On HPC systems, this means isolation from other environments, reproducibility across platforms, and simplified maintenance without manual installs or dependency troubleshooting. 
>
> It is recommended to pull containers from trusted sources like [BioContainers](https://biocontainers.pro/), [quay.io](quay.io) or [Seqera](https://seqera.io/containers/). During execution, Nextflow automatically pulls required images, often from these repositories, and stores them in the work directory.
> 
> **Why Set Environment Variables Before Submitting Nextflow Jobs?**
> When using containers on HPC systems, Nextflow needs to know where to store and retrieve container images. By default, it downloads containers into the workflow’s work/ directory, which can be inefficient and waste storage if you run multiple workflows.
>
>Setting environment variables allows you to:
> - Cache container images in a shared location → Avoid repeated downloads and speed up execution.
> - Control storage paths → Prevent filling up your home directory or job scratch space.
> - Ensure reproducibility → Use the same cached image across multiple runs and workflows.
> 
> How to set environmental variables:
> ```bash 
> export SINGULARITY_CACHEDIR=/path/to/cache
> export SINGULARITY_LIBRARYDIR=/path/to/library
> export NXF_SINGULARITY_CACHEDIR=/path/to/cache
> export NXF_SINGULARITY_LIBRARYDIR=/path/to/library
> ```
>
> These variables tell Nextflow and Singularity where to store container images so you don’t waste time and space downloading them repeatedly.
> 
>**Note: These environment variables need to be set each time you log in to the HPC system Or include them in your job submission script before running Nextflow.**
>
{: .keypoints}

## AMD-compatible images
- The proteinfold workflow includes several modules that are executed on the GPU. 
- Default containers [hosted](https://quay.io/organization/nf-core) by the nf-core organisation support Nvidia hardware and will not run on the Setonix AMD gpus. 
- Pawsey provides a number of AMD-compatible [images](https://quay.io/organization/pawsey) which can be used to run structure prediction models. 
- Pre-built images have been made provided for the workshop today at `/scratch/references/abacbs2025/containers`. 
- We can configure the workflow to use these images by defining their path in a custom NextFlow config.

Inspect the `abacbs_workshop.config` from the workshop repo to to see that workflow modules are configured to use non-standard images available on Setonix.

```
withName: 'RUN_ALPHAFOLD2' {
    container = '/scratch/references/abacbs2025/containers/alphafold2.sif'
    time = { 12.h }
    cpus = 8
}
```

## Reference data
- Recall that structure prediction relies on collecting homologous proteins in a multiple sequence alignment (MSA) to identify coevolutionary information indicating likely structural contacts. 
- These homologs are identified from enormous reference sequence databases which can be a performance bottleneck. 
- We have prepared miniature databases for this workshop with only the minimum number of sequences required 
- Check that these databases are available on Setonix.

```bash
tree /scratch/references/abacbs2025/databases/
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
> Today, we are using miniature versions of the databases to reduce execution time for the purpose of the workshop. These databases will NOT generate high-quality predictions for other protein targets. Full size databases are available at `/scratch/references/alphafold_feb2024/databases/`.
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
    --outdir output/ --db /scratch/references/alphafold_minidbs/databases/ --mode alphafold2 --use_gpu --alphafold2_mode "standard" \
    -c abacbs_profile.config --slurm_account $PAWSEY_PROJECT -r 09ac089
```



> ## Job monitoring
> We can confirm that our job is running with: 
> 
> ~~~
> squeue --me
> ~~~
> {: .source}
> <br>
> ~~~
> JOBID        USER ACCOUNT                   NAME EXEC_HOST ST     REASON START_TIME       END_TIME  TIME_LEFT NODES   PRIORITY       QOS
> 34776177  tlitfin pawsey1017-gpu  nf-NFCORE_PROT nid002166  R       None 08:15:36         20:15:36   11:36:58     1      75399    normal
> ~~~
> <br>
> - Similarly, we can connect to the node that is executing the job to check the status (ie the node id under EXEC_HOST).
> - Replace <node> with the id under EXEC_HOST (eg nid002166 from the example above)
>
> ~~~
> ssh <node>
> ~~~
> {: .source}
> - Type `yes` when prompted
>
> ~~~
> watch rocm-smi
> ~~~
> {: .source}
{: .prereq}

A basic run of AlphaFold2 using the official implementation consumes XX SUs on the Setonix system. However, a significant portion of execution time is used to search large sequence databases and does not utilize the allocated GPU.

## Job Accounting
- Download the `execution_timeline` HTML file located in the `output/pipeline_info/` directory.

> ## Execution timeline
> {% raw %}
> <img src="/assets/img/abacbs-af2-timeline.png" alt="af2tl" width="1200"/>
> {% endraw %}
>
>
{: .solution }

- We can use the Pawesey [calculator](https://pawseysc.github.io/su-calculator/) to estimate the service unit (SU) cost of our workflow execution.


## Split MSA

<p align="center">
<img src="/assets/img/abacbs-af2-split.png" alt="af2split" width="800"/>
</p>

``` bash
nextflow run ../workflow/proteinfold/ --input samplesheet.csv \
    --outdir test-out --db ../databases/ --mode alphafold2 --use_gpu --alphafold2_mode "split_msa_prediction" \
    -c abacbs_profile.config --slurm_account $PAWSEY_PROJECT
```

> ## Execution timeline
> {% raw %}
> <img src="/assets/img/abacbs-af2split-timeline.png" alt="af2-splittl" width="1200"/>
> {% endraw %}
>
>
{: .solution }

Compare the SU rate of XX node compared to YY node.

```
XX: 
YY: 
```

As a result, running AlphaFold2 in split mode whereby CPU-bound search is conducted on a CPU node significantly reduces SU consumption.