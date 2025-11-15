---
title: "Using structures to annotate function"
teaching: 5
exercises: 5
questions:
 - What is the function of this protein?
objectives:
keypoints:
- Similar structures can often share a similar function.
---

> ## Note
> - Much like traditional sequence-based annotation, we can take our AlphaFold2 predicted structure and search a large database of annotated structures.
> - Similar annotated structures could provide a hypothesis about the likely function of our target protein.
{: .prereq}

## Download structure predictions
- From your **local terminal**, download the `sample0_alphafold2.pdb` file located in the `output/alphafold2/standard/sample0/` directory.

``` bash
scp <username>@setonix.pawsey.org.au:/scratch/courses/<username>/exercise2/output/alphafold2/standard/sample0/sample0_alphafold2.pdb ./
```
- **Windows users** can download from WinSCP.

## Foldseek

- The [Foldseek server](https://search.foldseek.com/search) is extremely fast and useful for identifying structural matches across a range of experimental and predicted structure databases.
- Upload our predicted PDB structure to the foldseek server and search for similar structures.

> ## Input form
> {% raw %}
> <p align="center">
> <img src="/assets/img/abacbs-fseek.png" alt="foldseek" width="800"/>
> </p>
> {% endraw %}
{: .keypoints}

> ## Results
> {% raw %}
> <img src="/assets/img/abacbs-fseek-res.png" alt="fseek-res" width="1200"/>
> {% endraw %}
> 
> - This collection of similar structures are annotated with PFAM clans relating to a Type III secretion system adaptor protein. [1]
> - This suggests a related function for our uncharacterized gene.
> - However, SctK is considered to be missing from Chlamydiota in recent reviews. [2]
{: .solution }

## SPfast 
- [SPfast](https://colab.research.google.com/github/tlitfin/SPfast/blob/main/notebooks/SPfast_AFDB_clusters_PFAM.ipynb) is an alternative strategy for structure-based search and provides a minimal set of hits based on non-redundant PFAM clan annotations.
- Upload our predicted PDB structure by leaving the input fields as blank.

> ## Input form
> {% raw %}
> <p align="center">
> <img src="/assets/img/abacbs-spfast1.png" alt="spfast1" width="800"/>
> </p>
> {% endraw %}
{: .keypoints}

> ## Results
> {% raw %}
> | Query                      | Template   | Score | PFAM coverage | InterPro  | PFAM    | Clan            | Count |
> |-----------------------------|------------|-------|----------------|-----------|---------|-----------------|-------|
> | AF-A0A0U5EPG3-F1-model_v4   | A0A7V7WJC0 | 0.972 | 0.851          | IPR023087 | PF01706 | FliG            | 3     |
> | AF-A0A0U5EPG3-F1-model_v4   | A0A5E4W8M8 | 0.903 | 0.941          | IPR009510 | PF06578 | YscK            | 3     |
> | AF-A0A0U5EPG3-F1-model_v4   | A0A2Y0G0F7 | 0.766 | 0.969          | IPR013388 | PF09482 | OrgA_MxiK       | 3     |
> | AF-A0A0U5EPG3-F1-model_v4   | A0A2S7ERH1 | 0.746 | 0.895          | IPR013393 | PF09502 | HrpB4           | 1     |
> | AF-A0A0U5EPG3-F1-model_v4   | Q8ZPP3     | 0.697 | 0.862          | IPR025292 | PF13327 | T3SS_LEE_assoc  | 1     |
> {% endraw %}
> 
> - This collection of similar structures are annotated with PFAM clans relating to a Type III secretion system adaptor protein. [1]
> - This suggests a related function for our uncharacterized gene.
> - However, SctK is considered to be missing from Chlamydiota in recent reviews. [2]
{: .solution }

## Synteny


Structure-based annotation is on the roadmap for another nf-core pipeline - [proteinannotator](https://nf-co.re/proteinannotator/dev/)

> ## Careful
> Structural similarity does not guarantee a related function. Shared structural scaffolds can sometimes adopt highly divergent functions.
{: .discussion}

## References
- [1] 
- [2]

