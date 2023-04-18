# 1. Data Sanity of _Trypanosoma cruzi_ on Vero cells culture - STAR alingment
###### _Trypanosoma cruzi_ genome annotation and Data Sanity

#### 1.1 Download the GTF file and the genome of _T.cruzi_.


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

# 2. Samples concatenate

```ruby
#!/usr/bin/env bash

#Paths
fastq=/storages/parnamirim/iarasouza/tcruzi/samples/cas9
id=/storages/parnamirim/iarasouza/tcruzi/genome/tcruzi/scripts/identificadores.txt
output=/storages/parnamirim/iarasouza/tcruzi/genome/tcruzi/cas9-concatenada

#Concatenar fastq files
cd $fastq

for i in $(cat $id)
do

#READ 1:
zcat ${i}1_R1_001.fastq.gz ${i}2_R1_001.fastq.gz \
> <(gzip ${output}/"concat_"${i}_R1_001.fastq)

#READ 2:
zcat ${i}1_R2_001.fastq.gz ${i}2_R2_001.fastq.gz \
> <(gzip ${output}/"concat_"${i}_R2_001.fastq)


done

```

# 3. _Trypanosoma cruzi_ Quality Control (FastQC / MultiQC)

#### 3.1 Creating ID list
> **Note**
> Needed only for paired-end data

Run this command in your Fastq data directory:
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
#### Example:
Input - Fastq files names:

      1-Cas9cepaGAmosta1_S5_L001_R1_001.fastq.gz
      
      1-Cas9cepaGAmosta1_S5_L001_R2_001.fastq.gz
```
cat *.fastq.gz > identf.txt
sed 's/.\{13\}//' file.txt
uniq file.txt > ID.txt
```
Output - ID.txt: 

        1-Cas9cepaGAmosta1_S5_L001_R1
        
        1-Cas9cepaGAmosta1_S5_L001_R2
	
	

        
 
 #### 3.2 FastQC / MultiQC
 Create a directory named fastqc_results
 
 Run FastQC [`do_fastqc`](https://github.com/Dante-von-Zuben/Trypanosoma-cruzi-genome/blob/main/do_fastqc)
 ```ruby
 #!/usr/bin/env bash

#Paths
ID=put/your/data/fastqID.txt
Output=put/your/fastqc/output/directory/fastqc_results
cd put/your/fastq/data/directory

for i in $(cat $ID)

do
	fastqc $i -o $Output
done
 ```
 
 Run multiQC in fasqc_results directory:
 
 ```
 multiqc .
 ```
To do the data quality control analisys, copy multiqc_report.html to your home:

```
scp your.user@00.0.00.00:/path/to/multiqc_report.html ./Downloads/multiqc_report-Tcruzi.html
```
 
 ### 3.3 Trim data with FastP
 create a directory named fastp_results
 
> **Note**
> If paired-end data:
 
 Run FastP [`do_fastp`](https://github.com/Dante-von-Zuben/Trypanosoma-cruzi-genome/blob/main/do_fastp):
 ```ruby
#!/usr/bin/env bash

#Paths
ID=put/your/directory/ID.txt
Output=put/your/output/directory/fastp_results
cd put/your/fastq/directoy


for i in $(cat $ID)
do

fastp --in1 ${i}_1.fastq.gz --in2 ${i}_2.fastq.gz \
--out1 ${ID}/"trimmed_"${i}"_1".fastq.gz \
--out2 ${ID}/"trimmed_"${i}"_2".fastq.gz \
--detect_adapter_for_pe -w 8 -q 25 --cut_front 3 --cut_tail 3

done
 ```
 
### 3.4 Run FastQC and MultiQC for Trimmed data
> **Warning**
>Remember to change paths to fastp_results

# 4. _Trypanosoma cruzi_ Alingment with STAR
# 4. _Trypanosoma cruzi_ Data Sanity
