**先放到Linux的.sh文件里面编辑，不然会有因为win和linux的空格格式不同而产生的报错。**

**执行：（以run.sh为文件名为例，数据来源为：genome5 数据ID为：a01）**
>在终端中输入:
>**nohup sh run.sh genome5 a01  &**
（然后敲两次回车）

# run.sh 中的代码

>**\# 设定变量名**
>
>**\## 数据来源** 
SOURCE=\$1      
**\## 数据ID**    
>ID=\$2   
**\## assembly 的存储路径**       
> ASSEMBLY=/home/liucongcong/data/testAllMethods/datasets/\${SOURCE}-\${ID}.assembly
>**\## bam 的存储路径**
>BAM=/home/liucongcong/data/testAllMethods/datasets/\${SOURCE}-\${ID}.*.bam

>**\# 为所有bam文件建立索引, 生成bai文件**
>for bam in \`ls ${BAM}`
>do
samtools index \${bam}
>done

#========CONCOCT - v1.0.0========
>**\# 创建CONCOCT - v1.0.0对应临时文件夹并进入该文件夹**
`mkdir ${SOURCE}-${ID}.concoct-1.0.0 && cd ${SOURCE}-${ID}.concoct-1.0.0`
>
>**\# 执行软件CONCOCT - v1.0.0**
`cut_up_fasta.py ${ASSEMBLY} -c 10000 -o 0 --merge_last -b contigs_10K.bed > contigs_10K.fa`
`concoct_coverage_table.py contigs_10K.bed ${BAM} > coverage_table.tsv`
`concoct -t 50 --composition_file contigs_10K.fa --coverage_file coverage_table.tsv -b concoct_output/`
`merge_cutup_clustering.py concoct_output/clustering_gt1000.csv > concoct_output/clustering_merged.csv`
`mkdir concoct_output/fasta_bins`
`extract_fasta_bins.py ${ASSEMBLY} concoct_output/clustering_merged.csv --output_path concoct_output/fasta_bins`
>
>\# **将需要保留的文件留在临时文件夹的上一层**
> `mv coverage_table.tsv ../${SOURCE}-${ID}.concoct-1.0.0.coverage`
`mv concoct_output/fasta_bins ../${SOURCE}-${ID}.concoct-1.0.0.clusters`
`cd ..`
>
>\# **移除临时文件夹**
`rm -rf ${SOURCE}-${ID}.concoct-1.0.0`

#============================Metabat2============================
>**\# 创建Metabat2对应临时文件夹并进入该文件夹**
`mkdir ${SOURCE}-${ID}.metabat2-2.12.1 && cd ${SOURCE}-${ID}.metabat2-2.12.1`
**\# 执行软件**
`runMetaBat.sh ${ASSEMBLY} ${BAM}`
**\# 将需要保留的文件留在临时文件夹的上一层**
`mv *.depth.txt  ../${SOURCE}-${ID}.metabat2-2.12.1.depth`
`mv *.metabat-bins ../${SOURCE}-${ID}.metabat2-2.12.1.clusters`
`cd ..`
**\# 移除临时文件夹**
`rm -rf ${SOURCE}-${ID}.metabat2-2.12.1`

<!-- #======================SemiBin2-1.5.1============================
#==========(https://semibin.readthedocs.io/en/latest/usage/)====================
ASSEMBLY=/home/liucongcong/data/testAllMethods/datasets/${SOURCE}-${ID}.assembly
BAM=/home/liucongcong/data/testAllMethods/datasets/${SOURCE}-${ID}.*.bam
OUTPUT=${SOURCE}-${ID}.semibin2-1.5.1

#生成data.csv/data_split.csv
SemiBin2 generate_sequence_features_single -i ${ASSEMBLY} -b ${BAM} -o ${OUTPUT}
#SemiBin2 generate_cannot_links -i ${ASSEMBLY} -o ${OUTPUT} --taxonomy-annotation-tabl
#SemiBin2 generate_cannot_links -i ${ASSEMBLY} -o ${OUTPUT}

#生成model.h5cd
#SemiBin2 train --mode single -i ${ASSEMBLY} --data ${OUTPUT}/data.csv --data-split ${OUTPUT}/data_split.csv -c ${OUTPUT}/cannot/cannot.txt -o ${OUTPUT}
SemiBin2 train_self --data ${OUTPUT}/data.csv --data-split ${OUTPUT}/data_split.csv -o ${OUTPUT}

#生成Bin
SemiBin2 bin -i ${ASSEMBLY} --model ${OUTPUT}/model.h5 --data ${OUTPUT}/data.csv -o ${OUTPUT} -->

#================SemiBin2 - v1.5.1================
<!-- 临时变量存储地
SOURCE=genome5
ID=a01
ASSEMBLY=/home/liucongcong/data/testAllMethods/datasets/${SOURCE}-${ID}.assembly
BAM=/home/liucongcong/data/testAllMethods/datasets/${SOURCE}-${ID}.*.bam 
-->
>**\# 创建SemiBin2文件夹**
`mkdir ${SOURCE}-${ID}.semibin2-1.5.1 `
**\# 跑程序**
`SemiBin2 single_easy_bin -i ${ASSEMBLY} -b ${BAM} --self-supervised -o ${SOURCE}-${ID}.semibin2-1.5.1`
**\# 只保留contig_bins.tsv文件 储存为.clusters**
`mv ${SOURCE}-${ID}.semibin2-1.5.1/contig_bins.tsv ${SOURCE}-${ID}.semibin2-1.5.1.clusters`
**\# 删除临时文件夹**
`rm -rf ${SOURCE}-${ID}.semibin2-1.5.1`


#======================MetaDecoder - v1.0.3======================
>**\#创建MetaDecoder对应的文件夹并进入**
>`mkdir ${SOURCE}-${ID}.metadecoder-1.0.3 && cd ${SOURCE}-${ID}.metadecoder-1.0.3`
**\#处理数据**
``for bam in `ls ${BAM}` ``
`do`
``samtools view -@ 30 -h -o `date -u +%Y%m%d%H%M%S%N`.sam ${bam}``
`done`
**\#执行程序**
`metadecoder coverage -s *.sam -o coverage`
`metadecoder seed --threads 50 -f ${ASSEMBLY} -o seed`
`metadecoder cluster --no -f ${ASSEMBLY} -s seed -c coverage -o metadecoder-1.0.3`
`mv coverage ../${SOURCE}-${ID}.metadecoder-1.0.3.coverage`
`mv metadecoder-1.0.3.cluster ../${SOURCE}-${ID}.metadecoder-1.0.3.clusters`
`cd ..`
`rm -rf ${SOURCE}-${ID}.metadecoder-1.0.3`


#================VAMB - v4.1.3#================
<!-- 临时变量存储地
SOURCE=genome100
ID=a01
ASSEMBLY=/home/liucongcong/data/testAllMethods/datasets/${SOURCE}-${ID}.assembly
BAM=/home/liucongcong/data/testAllMethods/datasets/${SOURCE}-${ID}.*.bam 
-->
>**#VAMB要用新的数据集：**
>**SOURCE=genome100
ID=a01      
> ASSEMBLY=/home/liucongcong/data/testAllMethods/datasets/\${SOURCE}-\${ID}.assembly
>BAM=/home/liucongcong/data/testAllMethods/datasets/\${SOURCE}-\${ID}.*.bam**
\
**\# 创建vamb对应临时文件夹**
`mkdir ${SOURCE}-${ID}.vamb-4.1.3 && cd ${SOURCE}-${ID}.vamb-4.1.3`
**\# 运行程序**
`vamb --cuda --outdir ./out --fasta ${ASSEMBLY} --bamfiles ${BAM} -o C`
> <!-- **\# 保留abundance.npz文件，abundance.npz -> ${SOURCE}-${ID}.vamb-4.1.3.abundance.npz** 
> **/#保留vae_clusters.tsv文件，vae_clusters.tsv -> ${SOURCE}-${ID}.vamb-4.1.3.clusters**
> -->
>**\# 保留abundance.npz文件**
`mv ./out/abundance.npz ../${SOURCE}-${ID}.vamb-4.1.3.abundance.npz`
**\#保留vae_clusters.tsv文件为 .cluster**
`mv ./out/vae_clusters.tsv ../${SOURCE}-${ID}.vamb-4.1.3.clusters`
**\#移除文件夹**
`cd ..`
`rm -rf ${SOURCE}-${ID}.vamb-4.1.3`


