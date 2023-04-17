# Trypanosoma-cruzi with STAR alingment
Trypanosoma cruzi genome annotation and analysis
First you need to download the GTF file and the genome of T.cruzi

DNA:
```
wget https://ftp.ensemblgenomes.ebi.ac.uk/pub/protists/release-56/fasta/protists_euglenozoa1_collection/trypanosoma_cruzi_gca_003719155/dna/Trypanosoma_cruzi_gca_003719155.ASM371915v1.dna.toplevel.fa.gz
```

GTF:
```
wget https://ftp.ensemblgenomes.ebi.ac.uk/pub/protists/release-56/fasta/protists_euglenozoa1_collection/trypanosoma_cruzi_gca_003719155/dna/Trypanosoma_cruzi_gca_003719155.ASM371915v1.dna.toplevel.fa.gz
```

Now unzzip both files and create the index directory.

Run STAR:
```ruby
#!/usr/bin/env bash

#Warning: All files must be decompressed

#Paths:
Output= /storages/parnamirim/iarasouza/tcruzi/genome/tcruzi/index-star \
Fasta= /storages/parnamirim/iarasouza/tcruzi/genome/tcruzi/genome-tcruzi/Trypanosoma_cruzi_gca_003719155.ASM371915v1.dna.toplevel.fa \
GTF= /storages/parnamirim/iarasouza/tcruzi/genome/tcruzi/gtf/Trypanosoma_cruzi_gca_003719155.ASM371915v1.56.gtf

#Index Biuld:
STAR --runThreadN 8 --runMode genomeGenerate --genomeDir $Output \
--genomeFastaFiles $Fasta --sjdbGTFfile $GTF
```
