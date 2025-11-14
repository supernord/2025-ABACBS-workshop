---
title: "Run nf-core/proteinfold on Setonix"
teaching: 5
exercises: 10
questions:
objectives:
- Locate the resources available to support running AlphaFold2 on Setonix.
- Prepare a Nextflow configuration file to utilise available resources.
- Run nfcore/proteinfold Nextflow workflow on Setonix.
keypoints:
- Software designed for GPU execution is often compiled for Nvidia GPUS.
- Custom images can be built to support execution using AMD GPUs
- Workflows can be configured to use custom images
---

## Prepare samplesheet

We can prepare a nextflow samplesheet containing our protein input in fasta format. Multiple proteins can be predicted with a single workflow execution by adding rows to an input samplesheet.

``` csv
id,sequence
prot1,fasta/PNK_0205.fasta
```

## Basic run

<p align="center">
<img src="/assets/img/abacbs-af2-normal.png" alt="af2normal" width="600"/>
</p>


``` bash
nextflow run nf-core/proteinfold/ --input samplesheet.csv \
    --outdir output/ --db /scratch/references/abacbs2025/databases/ \
    --mode alphafold2 --use_gpu --alphafold2_mode "standard" \
    -c abacbs_profile.config --slurm_account $PAWSEY_PROJECT -r 09ac089
```

> ## Job monitoring
> - In our second terminal we can confirm that our job is running with: 
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
>
> - Type `yes` when prompted and then enter your workshop account password at the password prompt.
> - **Note: you can only connect to nodes where you have an active job running**
>
> ~~~
> watch rocm-smi
> ~~~
> {: .source}
{: .prereq}

### Job Accounting
- Download the `execution_timeline` HTML file located in the `output/pipeline_info/` directory.

> ## Execution timeline
> {% raw %}
> <img src="/assets/img/abacbs-af2-timeline.png" alt="af2tl" width="1200"/>
> {% endraw %}
{: .solution }

> ## Service units
> - We can again use the Pawsey [calculator](https://pawseysc.github.io/su-calculator/) to estimate the service unit (SU) cost of our workflow execution.
> - A full scale execution was completed in 0.75 hours using a single GPU (neglible CPU time).
>
> ~~~
> Calculation Breakdown
> SUs = Partition Charge Rate × Max Proportion × Nodes × Hours
> 512 × 0.1250 × 1 × 0.75 = 48 SUs
> GPU Proportion: 1 GCDs / 8 total GCDs = 0.1250
> ~~~
> 
> - A basic run of AlphaFold2 using the official implementation consumes 48 SUs on the Setonix system. 
> - Compare this with the execution time for the demo run from this workshow using the miniature databases.
> - **Conclusion: A large portion of the execution time is spent searching the sequence databases which does not require using a GPU.**
>
{: .solution}


## Split MSA run

<p align="center">
<img src="/assets/img/abacbs-af2-split.png" alt="af2split" width="800"/>
</p>

``` bash
nextflow run ../workflow/proteinfold/ --input samplesheet.csv \
    --outdir output-split --db /scratch/references/abacbs2025/databases/ \
    --mode alphafold2 --use_gpu --alphafold2_mode "split_msa_prediction" \
    -c abacbs_profile.config --slurm_account $PAWSEY_PROJECT
```

### Job Accounting
- Download the `execution_timeline` HTML file located in the `output-split/pipeline_info/` directory.

> ## Execution timeline
> {% raw %}
> <img src="/assets/img/abacbs-af2split-timeline.png" alt="af2-splittl" width="1200"/>
> {% endraw %}
>
>
{: .solution }

> ## Service units
> - We can again use the Pawsey [calculator](https://pawseysc.github.io/su-calculator/) to estimate the service unit (SU) cost.
> - A full scale execution was completed in 0.16 hours using a single GPU and 0.3 hours of CPU time.
>
> **CPU**
> ~~~
> SUs = Partition Charge Rate × Max Proportion × Nodes × Hours
> 128 × 0.1391 × 1 × 0.3 = 5.343 SUs
>
> Core Proportion: 8 cores / 128 total cores = 0.0625
> Memory Proportion: 32 GB / 230 GB total for accounting = 0.1391
> Max Proportion (Memory): 0.1391
> ~~~
> <br>
> **GPU**
> ~~~
> SUs = Partition Charge Rate × Max Proportion × Nodes × Hours
> 512 × 0.1250 × 1 × 0.16 = 10.24 SUs
> GPU Proportion: 1 GCDs / 8 total GCDs = 0.1250
> ~~~
>
> Compare the SU rate of XX node compared to YY node.
>
> ~~~
> Standard:               48   SUs
> Split MSA: 5.3 + 10.2 = 15.5 SUs
> ~~~
> **Running AlphaFold2 in split mode (whereby CPU-bound search is conducted on a CPU node) can significantly reduce SU consumption.**
{: .solution}