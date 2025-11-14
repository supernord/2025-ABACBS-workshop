---
title: "Evaluate outputs of nf-core/proteinfold"
teaching: 10
exercises: 0
questions: 
    - Is this prediction of sufficient quality for downstream analysis?
    - Is the MSA sufficiently deep for a high confidence novel prediction?
objectives:
keypoints:
    - Structure predictions should be interpreted in the context of model confidence.
---

- Download the HTML report to your local machine and open in your browser.
- 

### Predicted local distance difference test (pLDDT)
External resource: [EBI AlphaFold guide - pLDDT](https://www.ebi.ac.uk/training/online/courses/alphafold/inputs-and-outputs/evaluating-alphafolds-predicted-structures-using-confidence-scores/plddt-understanding-local-confidence/)

<p align="center">
<img src="/assets/img/abacbs-plddt.png" alt="plddt" width="800"/>
</p>

- pLDDT is a measure of local model confidence and is typically interpreted in 4 bands:
    - Very high confidence: pLDDT > 90
    - Confident: 90 > pLDDT > 70
    - Low confidence: 70 > pLDDT > 50
    - Very low confidence: pLDDT < 50
- Low pLDDT often coincides with regions of intrinsic disorder.


### Predicted aligned error (PAE)
External resource: [EBI AlphaFold guide - PAE](https://www.ebi.ac.uk/training/online/courses/alphafold/inputs-and-outputs/evaluating-alphafolds-predicted-structures-using-confidence-scores/pae-a-measure-of-global-confidence-in-alphafold-predictions/)

<p align="center">
<img src="/assets/img/abacbs-pae.png" alt="pae" width="600"/>
</p>

### Sequence coverage

<p align="center">
<img src="/assets/img/abacbs-msa.png" alt="msa" width="800"/>
</p>

- Recall that high quality predictions of novel structures rely on co-evolution data derived from multiple sequence alignments.
- There are several other outputs in the `examples/` directory. 



