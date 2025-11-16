---
title: "Using structures to annotate function"
teaching: 5
exercises: 5
questions:
objectives:
- Learn about services (Foldseek and SPfast) for searching large structure databases.
- Interpret functional consequences of structural similarity.
- Find supporting evidence for functional annotations.
keypoints:
- Similar structures can often share a similar function.
- Structure-based annotation should be supported by complementary evidence.
---

> ## Note
> - Now that we have our high-confidence predicted structure, we can use it to search a large database of annotated structures.
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
> - Ensure that the check-boxes for all databases are selected.
{: .keypoints}

> ## Results
> {% raw %}
> <img src="/assets/img/abacbs-fseek-res.png" alt="fseek-res" width="1200"/>
> {% endraw %}
> 
> - Browse the tabs to see the top hits in various protein structure databases.
> - The most similar proteins in AFDB50 are also uncharacterised proteins.
> - There are some hits to proteins with various annotations (MgtE, FliG, F-box).
> - Many fringe hits are annotated with FliG, YscK and SctK - a component of the Type III secretion system.
>
{: .solution }

## SPfast 
[SPfast](https://colab.research.google.com/github/tlitfin/SPfast/blob/main/notebooks/SPfast_AFDB_clusters_PFAM.ipynb) is an alternative strategy for structure-based search and provides a minimal set of hits based on non-redundant PFAM clan annotations.

1. Upload our predicted PDB structure by **leaving the input fields blank.** 
2. Select **Run all** to execute search for annotated structures using SPfast.
3. **Wait ~3 minutes** for the file upload prompt to appear under the 3rd cell and upload your predicted structure (`sample0_alphafold2.pdb`).

> ## Input form
> {% raw %}
> <p align="center">
> <img src="/assets/img/abacbs-spfast1.png" alt="spfast1" width="800"/>
> </p>
> {% endraw %}
{: .keypoints}

- SPfast will take about ~7 minutes after uploading the query structure.

> ## Results
> {% raw %}
> | Query                      | Template   | Score | PFAM coverage | InterPro  | PFAM    | Clan            | Count |
> |-----------------------------|------------|-------|----------------|-----------|---------|-----------------|-------|
> | sample0_alphafold2          | A0A7V7WJC0 | 0.972 | 0.851          | IPR023087 | PF01706 | FliG            | 3     |
> | sample0_alphafold2          | A0A5E4W8M8 | 0.903 | 0.941          | IPR009510 | PF06578 | YscK            | 3     |
> | sample0_alphafold2          | A0A2Y0G0F7 | 0.766 | 0.969          | IPR013388 | PF09482 | OrgA_MxiK       | 3     |
> | sample0_alphafold2          | A0A2S7ERH1 | 0.746 | 0.895          | IPR013393 | PF09502 | HrpB4           | 1     |
> | sample0_alphafold2          | Q8ZPP3     | 0.697 | 0.862          | IPR025292 | PF13327 | T3SS_LEE_assoc  | 1     |
> {% endraw %}
> 
> - This collection of similar structures are annotated with PFAM clans which all relate to a Type III secretion system adaptor protein (SctK gene).
> - This suggests a potential related function for our uncharacterized gene.
{: .solution }

> ## Careful
> - Structural similarity does **NOT** guarantee a related function. 
> - Shared structural scaffolds can sometimes adopt highly divergent functions.
> - We can look for complementary evidence to support structure-based annotations.
{: .discussion}

## Synteny





> ## Note:
> Structure-based annotation is on the roadmap for another nf-core pipeline - [proteinannotator](https://nf-co.re/proteinannotator/dev/)
{: .prereq}

