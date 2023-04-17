# _Trypanosoma cruzi_ on Vero cell - STAR alingment
###### _Trypanosoma cruzi_ genome annotation and analysis

#### 1. Download the GTF file and the genome of _T.cruzi_.


`DNA:`
```
wget https://ftp.ensemblgenomes.ebi.ac.uk/pub/protists/release-56/fasta/protists_euglenozoa1_collection/trypanosoma_cruzi_gca_003719155/dna/Trypanosoma_cruzi_gca_003719155.ASM371915v1.dna.toplevel.fa.gz
```


`GTF:`
```
wget https://ftp.ensemblgenomes.ebi.ac.uk/pub/protists/release-56/fasta/protists_euglenozoa1_collection/trypanosoma_cruzi_gca_003719155/dna/Trypanosoma_cruzi_gca_003719155.ASM371915v1.dna.toplevel.fa.gz
```

#### 1.2 Creating STAR index ([`STAR repository`](https://github.com/alexdobin/STAR))

Unzzip both files, create the index directory and bash STAR genome index [`STAR-genome_index`](https://github.com/Dante-von-Zuben/Trypanosoma-cruzi-genome/blob/main/Run-index-Star)
```ruby
#!/usr/bin/env bash

#Paths:
Output=/your/path/to/index_directory \
Fastqc=/your/path/to/fastqc_files.fa \
GTF=/your/path/to/gtf_files.gtf \

#Index Biuld:
STAR --runThreadN 8 --runMode genomeGenerate --genomeDir $Output \
--genomeFastaFiles $Fastqc --sjdbGTFfile $GTF
```

> **Warning**
> All files must be decompressed
