---
title: "EXTRA: Executing at scale"
teaching: 
exercises: 
questions:
objectives:
keypoints:
---

### Disk footprint


### Afterscript

```
withName: 'RUN_ALPHAFOLD2_MSA' {
    cpus   = { 8                      }
    memory = { 16.GB                  }
    time   = { 12.h                   }
    afterScript = """
        rm pdb_seqres/pdb_seqres.txt
        find . -type f -name '*.sto' -exec zstd -19 --rm {} \\;
    """
}
```

```
withName: 'RUN_ALPHAFOLD2_PRED' {
    afterScript = """
        find . -type f -name '*.pkl' -exec zstd -19 --rm {} \\;
    """
}
```
