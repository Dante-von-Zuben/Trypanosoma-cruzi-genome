# _Trypanosoma cruzi_ on Vero cells culture - STAR alingment
###### _Trypanosoma cruzi_ genome annotation and Data Sanity

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

# _Trypanosoma cruzi_ Quality Control (FastQC / MultiQC)

#### 1. Creating ID list
> **Note**
> Needed only for paired-end data
run this in your data directory
```
cat *.fastq.gz > identf.txt
```
```
sed 's/.\{N\}//' file.txt
```
`where N is the distance betwen the end of file name and the "R1" or "R2" read` #tem que melhorar essa linha sed 
```
uniq file.txt > ID.txt
```
##### Ex:
Fastq files names:

      1-Cas9cepaGAmosta1_S5_L001_R1_001.fastq.gz
      
      1-Cas9cepaGAmosta1_S5_L001_R2_001.fastq.gz
```
cat *.fastq.gz > identf.txt
sed 's/.\{13\}//' file.txt
uniq file.txt > ID.txt
```
ID.txt: 

        1-Cas9cepaGAmosta1_S5_L001_R1
        
        1-Cas9cepaGAmosta1_S5_L001_R2
