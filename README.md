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
#### 2.1 Creating ID list

Run this file:
```ruby
#Paths
fastq=Put/your/fastq/path

#Criar identificadores
ls $fastq > tmp.txt
sed 's/.\{N\}$//' tmp.txt > tmp2.txt
uniq tmp2.txt > identificadores.txt
rm tmp.txt tmp2.txt
```
`where N is the distance betwen the end of file name and the "R1" or "R2" read` (:NUMBER:) #tem que melhorar essa linha sed 

#### Example:
Input - Fastq files names:

      1-Cas9cepaGAmosta1_S5_L001_R1_001.fastq.gz
      
      1-Cas9cepaGAmosta1_S6_L001_R2_001.fastq.gz
```
#!/usr/bin/env bash

#Paths
fastq=/samples/cas9

#Criar identificadores
ls $fastq > tmp.txt
sed 's/.\{17\}$//' tmp.txt > tmp2.txt
uniq tmp2.txt > identificadores.txt
rm tmp.txt tmp2.txt
```
Output: 

        1-Cas9cepaGAmosta1_S5_L00
        
        1-Cas9cepaGAmosta1_S6_L00
	
	
Concatenate with this command:
        
```ruby
#!/usr/bin/env bash

#Paths
fastq=samples/cas9
id=/identificadores.txt
output=/cas9-concatenada

#Concatenate fastq files
cd $fastq

for i in $(cat $id)
do

#READ 1:
zcat ${i}1_R1_001.fastq.gz ${i}2_R1_001.fastq.gz \
> ${output}/"concat_"${i}_R1_001.fastq

#READ 2:
zcat ${i}1_R2_001.fastq.gz ${i}2_R2_001.fastq.gz \
> ${output}/"concat_"${i}_R2_001.fastq

done
```
>**Note**
>May be good to compress the output fastq files

# 3. _Trypanosoma cruzi_ Quality Control (FastQC / MultiQC)

id list
> **Note**
> Needed only for paired-end data


 
 #### 3.2 FastQC / MultiQC / seqkit
 
 Remove the small reads
 ```ruby
 cd fastq/files/directory
 for i in $ (ls *.fastq.gz)
 do
 
 seqkit seq ${i} -m 90 -M101 -g -o output/directory/"QC_"${i}
 
 done
 ```
 
 
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
 
 ### --3.3 Trim data with FastP-- #NOT NEEDED
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
--out1 ${Output}/"trimmed_"${i}"_1".fastq.gz \
--out2 ${Output}/"trimmed_"${i}"_2".fastq.gz \
--detect_adapter_for_pe -w 8 -q 25 --cut_front 3 --cut_tail 3

done
 ```

# 4. _Trypanosoma cruzi_ Alingment 
## 4.1 with STAR
[`STAR manual`](https://github.com/alexdobin/STAR/blob/master/doc/STARmanual.pdf)

Align with this command:

```ruby
#Paths
reads=path/to/fastq/reads
idx=/path/to/index-star
output=path/to/output/STAR_alignment

#IF PAIRED-END:
#read1
R1=s1read1.fastq.gz,\
s2read1.fastq.gz,\
s3read1.fastq.gz,\
s4read1.fastq.gz,
#(...)

#read2
R2=s1read2.fastq.gz,\
s2read2.fastq.gz,\
s3read2.fastq.gz,\
s4read2.fastq.gz,
#(...)

#Alingment
STAR --genomeDir ${idx} --runThreadN 12 --genomeLoad LoadAndExit

STAR --readFilesIn ${reads}/${R1} ${R2} \
--readFilesCommand zcat \
--outReadsUnmapped Fastx \
--genomeDir ${idx} \
--runThreadN 12 \
--outSAMtype BAM SortedByCoordinate \
--twopassMode \
--quantMode GeneCounts \
--limitBAMsortRAM 64

STAR --genomeDir ${idx} --genomeLoad Remove
```
>**Note**
>If you have too many files, must be good to change --readFilesIn to --readfilesManifest (manifest must be created, see more at [STAR manual](https://github.com/alexdobin/STAR/blob/master/doc/STARmanual.pdf) 

## 4.2 With Bowtie2
```
data=/path/to/reads
idx=/path/to/index
id=/path/to/identifiers.txt

for i in $(cat $id)
do
bowtie2 \
-t \
-p 6 \
-x $idx/idx-Bowtie2 \
-1 ${data}/${i}1.fastq.gz \
-2 ${data}/${i}2.fastq.gz \
-S Align-Unmapped/"Aligned_"${i}.sam \
--very-sensitive \
2> Align-Unmapped/${i}.log;
done
```
## 4.3 Gene count with featureCounts (Only for bowtie2 alignment)
```
featureCounts -t exon -a /path/to/gtfFile.gtf -o /path/for/outputfile.txt  BAMorSAMfiles.sam
```
# 4. _Trypanosoma cruzi_ Data Sanity
