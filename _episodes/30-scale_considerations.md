---
title: "Executing at scale"
teaching: 5
exercises: 5
questions:
objectives:
keypoints:
---

### Disk footprint


### Afterscript

withName: 'RUN_ALPHAFOLD2_MSA' {
    cpus   = { 12                      }
    memory = { 124.GB                  }
    time   = { 48.h                    }
    afterScript = """
        rm pdb_seqres/pdb_seqres.txt
        find . -type f -name '*.sto' -exec zstd -19 --rm {} \\;
    """
}

withName: 'RUN_ALPHAFOLD2_PRED' {
    afterScript = """
        find . -type f -name '*.pkl' -exec zstd -19 --rm {} \\;
    """
    clusterOptions  = {
        "-A kod_proteinfold -l select=1:ngpus=1:ncpus=6:mem=250gb:gpu_model=H200 -l walltime=12:00:00"
    }
}
