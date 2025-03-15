# Learning-bioinformatics  
For learning bioinformatics  
# FASTAQ and FASTAQC:  
高通量测序数据的质量控制工具: 'http://www.bioinformatics.babraham.ac.uk/projects/fastqc/' 
MultiQC  
### 了解 fastq 文件：
fastq 格式文件处理2大工具：seqkit seqtk 'https://bioinf.shenwei.me/seqkit/'   
##### 1、压缩与解压缩  
###### 解压缩  
`gunzip illumina_1.fastq.gz`  
`gunzip -d illumina_2.fastq.gz`  
###### 压缩  
`gzip illumina_1.fastq`  
`gzip illumina_2.fastq`   
##### 2、fastq 文件统计（统计有多少条seq,序列长度）  
`seqkit stats illumina_1.fastq.gz illumina_2.fastq.gz`   
##### 3、统计 fastq 文件每条序列 ATCG 四种碱基组成以及质量值分布  
`seqtk comp illumina_1.fastq.gz illumina_2.fastq.gz`  
##### 4、ATCG 以及质量值分布  
`seqtk fqchk illumina_1.fastq.gz`  
`seqtk fqchk illumina_2.fastq.gz`  
##### 5、交叉合并 pairend 文件  
`seqtk mergepe illumina_1.fastq.gz illumina_2.fastq.gz >merge.fastq` 
##### 6、过滤短的序列         
过滤小于 150bp 序列，并压缩输出             
`seqkit seq -m 150 nanopore.fastq.gz | gzip - >filter_150.fq.gz`  
保留小于 150bp 序列  
`seqkit seq -M 150 nanopore.fastq.gz`  
##### 7、转换为列表格式  
`seqkit fx2tab nanopore.fastq.gz`  
##### 8、分别统计每一条序列长度   
`seqkit fx2tab nanopore.fastq.gz |awk -F "\t" '{print length($2) }'`#(fx2tab 转 为 表 格 式 （ 4 列 ）-F:按\t制表符分割，将第2列的长度打印到屏幕length($2),z直接打印第一行名称内容：$1)  
##### 9、质量值转换  
 将 illumina 1.8 转换为 1.5  
`seqkit convert --to Illumina-1.5+ illumina_1.fastq.gz |head -4`    
 将 illumina 1.5 转换为 1.8，什么都不加就是转换为 1.8  
`seqkit convert illumina_illmina1.5.gz`  
##### 10、排序，按照长度 
`seqkit sort -l -r nanopore.fastq.gz`  #l长度，默认从小到大， -r从大到小
##### 11、seqkit 抽样，按照百分比  
`seqkit sample -p 0.1 illumina_1.fastq.gz`  
##### 12、seqkit 抽样，按照条数  
`seqkit sample -n 1000 illumina_1.fastq.gz`  
##### 13、拆分数据  
`seqkit split2 -1 illumina_1.fastq.gz -2 illumina_2.fastq.gz -p 2 -f`  
##### 14、转换为 fasta   
**seqkit 工具**  
`seqkit fq2fa nanopore.fastq.gz >nanopore.fasta`   
##### 15、只输出 20 行 ID   
`seqkit seq -n -i nanopore.fastq.gz |head -20 >id.list`
##### 16、提取序列  
`seqkit grep -f id.list nanopore.fastq.gz`
##### 17、截取头尾  
`seqtk trimfq -b 15 -e 15 -Q illumina_1.fastq.gz`
##### 18、修改 reads ID  
`seqkit replace -p "SRR8494939\.sra" -r 'reads' nanopore.fastq.gz`
##### 19、长度分布直方图  
`seqkit watch -L -f ReadLen hairpin.fa`
##### 20、平均质量直方图  
`seqkit watch -p 500 -O qhist.pdf -f MeanQual nanopore.fastq.gz`
##### 21、选取固定范围  
`seqkit range -r 200:300 nanopore.fastq.gz`
##### 22、移除重名序列  
`seqkit rmdup -n nanopore.fastq.gz -o clean.fa.gz`
##### 23、将小于 Q20 的替换为小写字母  
`seqtk seq -q 20 illumina_1.fastq.gz`
  
## illumina 数据质控及过滤

### 1、利用 fastqc 进行质量控制
fastqc 质控(可以用conda安装，-f 什么格式的文件，-o 输出到什么文件（_qc）/输入文件)    
`mkdir illumina_qc`  
`fastqc -f fastq -o illumina_qc/ illumina_1.fastq.gz illumina_2.fastq.gz`
### 2、数据过滤
#利用 fastp 进行数据过滤  
#fastp 数据过滤  
`fastp -i illumina_1.fastq.gz -I illumina_2.fastq.gz -o clean.1.fq.gz -O clean.2.fq.gz -z 4 -q 20 -u 40 -n 10 -f 15 -t 15 -F 15 -T 15 -h fastp.html`  
### 3、过滤完质控  
`mkdir illumina_clean`  
`fastqc -f fastq -o illumina_clean/ clean.1.fq.gz clean.2.fq.gz`  

### pacbio 数据质控及过滤
#fastqc 质控  
`mkdir pacbio_qc/`  
`fastqc -f fastq -o pacbio_qc/ pacbio.fastq.gz`  
#过滤数据  
`filtlong --min_length 300 --min_mean_q 90 pacbio.fastq.gz|gzip >pacbio.filtlong.fq.gz`  
#质控完过滤  
`mkdir pacbio_clean`  
`fastqc -f fastq -o pacbio_clean/ pacbio.filtlong.fq.gz`  









