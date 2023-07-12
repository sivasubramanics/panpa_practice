# PanPA workout repo

In this repo, I have the files related to the excercise in trying out PanPA for DNA to DNA mapping. The purpose of this excersice is to investigate the possibility of using PanPA to map short reads on the transcriptome GFA graphs.

### Data
Here I took a subset of a genes from yeast, tp use as a pilot test data. For this, I did a mmseq-easyclust of yeast rna.fna and picked a cluster containing 10 transcripts.

|transcripts
|-----------
|NM_001180862.2
|NM_001178666.2
|NM_001180050.2
|NM_001184379.4
|NM_001184388.4
|NM_001184390.4
|NM_001184402.2
|NM_001184411.4
|NM_001184419.4
|NM_001184423.4

For reads simulation I used **`randomreads.sh`** from bbmap package.

### contents

```bash 
data/
|-- NM_001180862.2.fasta (transcripts containing multifasta file)
|-- pilot_gfa (output of build_gfa.sh run. see below)
|-- pilot_gfa.gaf (output of PanPA mapping. see below)
|-- pilot_minimap2.paf (output of minimap2 mapping. see below)
|-- reads.fa (simulated reads from the transcripts multifasta file)
```

### multifasta -> gfa
For this I wrote a short bash scripts **`build_gfa.sh`**. This script takes care of clustering multifasta (mmseq-clust), spliting (faSomeRecords), msa (clusto), msa_to_gfa (PanPA), and Indexing the GFA (PanPA).

```bash 
build_gfa.sh NM_001180862.2.fasta pilot_gfa 10
```
output diretory contains the following direcories
```bash
data/pilot_gfa
|-- clusters (output of mmseq-clust run)
|-- genes (extracted transcripts multifasta files)
|-- graphs (gfa graphs for each genes msa file)
|-- index_k5_w3_no_limit.index (index of msas)
|-- logs (logs for each step)
|-- msa (msa files for each genes multifasta file)
`-- sh (shell files containing cmdlines for parallel runs for msa and faSomeRecords)
```

### mapping by PanPA
```bash
PanPA align -d pilot_gfa/graphs \
    --index pilot_gfa/index_k5_w3_no_limit.index \
    -c 20 \
    --sub_matrix dna \
    -o pilot_gfa.gaf \
    --gap_score -1 \
    -r reads.fa
```

### mapping by minimap2
```bash
minimap2 -xsr -t 10 -c -o pilot_minimap2.paf NM_001180862.2.fasta reads.fa
```

