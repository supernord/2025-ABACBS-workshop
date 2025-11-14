---
title: "Setup nf-core/proteinfold on Setonix"
teaching: 5
exercises: 5
questions:
objectives:
- Locate the resources available to support running AlphaFold2 on Setonix.
- Prepare a Nextflow configuration file to utilise available resources.
keypoints:
- Software designed for GPU execution is often compiled for Nvidia GPUS.
- Custom images can be built to support execution using AMD GPUs.
- Workflows can be configured to use custom images.
---

<p align="center">
<img src="/assets/img/abacbs-proteinfold-metromap.svg" alt="pfold" width="800"/>
</p>

## nf-core/proteinfold
- [proteinfold](https://github.com/nf-core/proteinfold/tree/dev) is a Nextflow pipeline designed to support numerous models for molecular structure prediction.
- Today, we will use proteinfold to predict the structure of our uncharacterised protein using the AlphaFold2 model.
- We will use a development branch of proteinfold to access some of the latest features that are not yet available in the current release.

```bash
#module load nextflow/25.04.6 # This should still be loaded from the previous exercise
nextflow pull nf-core/proteinfold
```

```bash
tree ~/.nextflow/assets/nf-core/proteinfold/
```

> ## Setup environment
> - The previous exercise didn’t use containers, but they are one of the most effective ways to manage software in workflow development, especially with Nextflow.
> - In this exercise, we will be using singularity containers and so we need to load the corresponding module on Setonix.
>
> ```bash
> module load singularity/4.1.0-slurm
> ```
> <br>
> > ## **Why containers?**
> >
> > A container is a lightweight, portable environment that bundles an application together with everything it needs to run such as libraries, dependencies, and system tools. It is a simple and reliable alternative to installing software directly on your system or HPC environment (which often requires managing dependencies manually).
> > On HPC systems, this means isolation from other environments, reproducibility across platforms, and simplified maintenance without manual installs or dependency troubleshooting. 
> >
> > It is recommended to pull containers from trusted sources like [BioContainers](https://biocontainers.pro/), [quay.io](quay.io) or [Seqera](https://seqera.io/containers/). During execution, Nextflow automatically pulls required images, often from these repositories, and stores them in the work directory.
> {: .solution} 
>
> - Before executing the workflow, we will define a number of environment variables.
> - These variables tell Nextflow and Singularity where to find and store container images so you don’t waste time and space downloading them repeatedly.
> - **Note: These environment variables need to be set each time you log in to the HPC system (or include them in your job submission script before running Nextflow).**
>
> ~~~
> mkdir $MYSCRATCH/containers
> export SINGULARITY_CACHEDIR=$MYSCRATCH/containers
> export SINGULARITY_LIBRARYDIR=/scratch/references/abacbs2025/containers
> export NXF_SINGULARITY_CACHEDIR=$MYSCRATCH/containers
> export NXF_SINGULARITY_LIBRARYDIR=/scratch/references/abacbs2025/containers
> ~~~
> {: .source}
> <br>
>
> - Confirm that several images are visible to nextflow by:
>
> ~~~
> ls $NXF_SINGULARITY_LIBRARYDIR
> ~~~
> {: .source}
> <br>
>
> - If you execute a Nextflow workflow that requires a container that is not located in the shared $NXF_SINGULARITY_LIBRARYDIR, the pipeline will attempt to pull the container from a hosted repository and store the image in your personal $NXF_SINGULARITY_CACHEDIR.
>
> > ## **Why Environment Variables?**
> > When using containers on HPC systems, Nextflow needs to know where to store and retrieve container images. By default, it downloads containers into the workflow’s work/ directory, which can be inefficient and waste storage if you run multiple workflows.
> >
> > Setting environment variables allows you to:
> > - Cache container images in a shared location → Avoid repeated downloads and speed up execution.
> > - Control storage paths → Prevent filling up your home directory or job scratch space.
> > - Ensure reproducibility → Use the same cached image across multiple runs and workflows.
> {: .solution}
> 
>
{: .keypoints}

## AMD-compatible images
- The proteinfold workflow includes several modules that are executed on the GPU. 
- Default containers [hosted](https://quay.io/organization/nf-core) by the nf-core organisation support Nvidia hardware and will not run on the Setonix AMD gpus. 
- Pawsey provides a number of AMD-compatible [containers](https://quay.io/organization/pawsey) which can be used to run structure prediction models. 
- Pre-built images have been provided for the workshop today at `/scratch/references/abacbs2025/containers`. 
- We can configure the workflow to use these non-standard images by defining their path in a custom Nextflow config.

Open the `abacbs_workshop.config` file to confirm that the workflow modules are configured to use non-standard images available on Setonix.

```
withName: 'RUN_ALPHAFOLD2' {
    container = '/scratch/references/abacbs2025/containers/alphafold2.sif'
    time = { 12.h }
    cpus = 8
    memory = 32.GB
}
```

## Reference data
- Recall that structure prediction relies on collecting homologous proteins in a multiple sequence alignment (MSA) to identify coevolutionary information.
- These homologs are identified from enormous reference sequence databases (>1TB).
- Searching these large databases can be a performance bottleneck. 
- Check that these databases are available on Setonix.

```bash
tree /scratch/references/abacbs2025/databases/
```

You should see that alphafold databases are available here.

```
databases/
    ├── mgnify
    ├── params
    ├── pdb70
    ├── pdb_mmcif
    ├── **pdb_seqres**
    ├── small_bfd
    ├── **uniprot**
    ├── uniref30
    └── uniref90 
```
**TOM TODO: MULTIMER DBS**

> ## Note
> Today, we are using miniature versions of the databases to reduce execution time for the purpose of the workshop. These databases will **NOT** generate high-quality predictions for other protein targets. Full size databases are available at `/scratch/references/alphafold_feb2024/databases/`.
{: .keypoints}