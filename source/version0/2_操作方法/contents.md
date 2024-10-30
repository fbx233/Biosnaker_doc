# 2.操作方法

Biosnaker基于Snakemake进行分析，具有自动化分析，断点重连，可扩展性高等优点。

## 工具结构

本工具含有3个文件夹，整体结构如下：

```txt
├── data
├── results
└── scripts
    ├── bowtie2.sh
    ├── cluster.json
    ├── cluster.yaml
    ├── config
    │   ├── bioinfo.yaml
    │   ├── cluster.json
    │   ├── cluster.yaml
    │   ├── config.yaml
    │   └── jobscript.sh
    ├── config.yaml
    ├── Count_matrix.py
    ├── Count_matrix.sh
    ├── debug1.sh
    ├── deseq2.R
    ├── envs
    │   ├── bioinfo.yaml
    │   ├── py.yaml
    │   └── sc.yaml
    ├── generate_sample_info.py
    ├── jobscript.sh
    ├── logs
    │   └── debug.log
    ├── mamba.sh
    ├── merge_counts.yaml
    ├── modify_files.sh
    ├── py.yaml
    ├── README.md
    ├── refer
    │   ├── genetic_screen_human_metabolism_genes.txt
    │   ├── human_metabolic_genes.csv
    │   └── pathway_class_information.csv
    ├── remove_rRNA.sh
    ├── Rscripts
    │   ├── heatmap.r
    │   ├── multi_group_DEA.R
    │   ├── PLSDA.R
    │   ├── README.md
    │   └── RNA-seq-DEA-function-repos.R
    ├── run_snakemake.pbs
    └── snakefile
```

### 文件夹里面放什么？
三个文件夹，对应输入、输出、脚本三个部分
- data文件夹，待分析的序列数据应存放于二级文件夹下，并以`文件夹名_R1.fq.gz`和`文件夹名_R2.fq.gz`的形式进行展示，如以下树状结构所示：

```sh
data
├── P7-DTP-D12-1
    ├──P7-DTP-D12-1_R1.fq.gz
    ├──P7-DTP-D12-1_R2.fq.gz
├── P7-DTP-D12-2
├── P7-DTP-D12-3
├── P7-DTP-D12-4
```
- results文件夹，存放本工具输出结果，包括
    - fastqc质控结果
    - fastp质控结果
    - 比对结果，以.bam的形式进行展示
    - 表达矩阵，以matrix.txt的表格展示
    - 自动生成的分组信息，本工具基于自然语言处理方法，根据前缀相似性，会形成自动分类，并储存在`sampleinfo.csv`文件中
    - 差异分析结果：输出差异基因表格及火山图
    - 基因富集分析：输出差异基因通路及包含通路富集数据的表格

- scripts文件夹，存放本工具的核心————Snakefile文件及其所涉及的脚本，其中，通过修改`config/config.ymal`文件，可以适应对不同数据的处理，在后续的更新中，我们打算加入配套的GUI，使得输入数据能够可视化进行。


## 第一步，确保相关的待分析数据及参考数据下载全面

保证下载数据位于data文件夹中，并按照以下格式排列：

```sh
data
├── P7-DTP-D12-1
    ├──P7-DTP-D12-1_R1.fq.gz
    ├──P7-DTP-D12-1_R2.fq.gz
├── P7-DTP-D12-2
├── P7-DTP-D12-3
├── P7-DTP-D12-4
```

## 第二步，按照提示修改config/config.ymal文件

config.yaml 文件包含了几个重要的参数配置，这些配置有助于自动化下游数据分析工作流程。关键参数如下：

样本列表：samples 部分包含了一系列样本的标识符，这些样本是数据分析的重点。这些标识符被分类为不同的类别，每个类别进一步细分为子样本。每个样本序列都有多个重复，如 "1"、"2"、"3"、"4"，以确保实验的可靠性和可重复性。

数据路径：path 指定了原始组学数据的存储位置。

输出路径：output_path 是分析结果的目标位置。

基因组索引路径：genome_index_path 用于存储基因组索引，支持转录组数据的比对。

rRNA 索引路径：rRNA_index 提供了 rRNA 的参考索引，用于从数据中过滤掉 rRNA 片段。

GTF 文件路径：GTF 参数指定了注释文件，用于精确的基因定位和功能注释。

计数矩阵脚本：count_matrix 指示生成基因表达计数矩阵的脚本路径。

样本信息脚本：sample_info 提供了生成样本信息的脚本路径，帮助组织样本的元数据。

通路信息参考文件：pathway_info 包含了生物学通路分类信息的文件路径，可在分析中参考，位于scripts/refer/pathway_class_information.csv，**可根据所需分析修改**

人类代谢基因参考文件：hm_gene 列出了参与代谢过程的人类基因，文件在 /scripts/refer/human_metabolic_genes.csv，**可根据所需分析修改**


```sh
samples:
  - P7-DTP-D12-1  
  - P7-DTP-D4-1  
  - P7-DTP-D9-1
  
path: "/public5/home/t6s001510/data1/omics_data/Rawdata/"
output_path: "/public5/home/t6s001510/data1/omics_data/outputs/"
genome_index_path: "/public5/home/t6s001510/data1/omics_data/ref/index/STAR/genome"
rRNA_index: "/public5/home/t6s001510/data1/omics_data/ref/index/rRNA/Homo_sapiens.rRNA"
GTF: "/public5/home/t6s001510/data1/omics_data/ref/index/GTF/Homo_sapiens.GRCh38.110.gtf"
count_matrix: "/public5/home/t6s001510/data1/omics_data/scripts/Count_matrix.py"
sample_info: "/public5/home/t6s001510/data1/omics_data/scripts/generate_sample_info.py"
pathway_info: "/public5/home/t6s001510/data1/omics_data/scripts/refer/pathway_class_information.csv"
hm_gene: "/public5/home/t6s001510/data1/omics_data/scripts/refer/human_metabolic_genes.csv"
```

## 第三步，运行Snakemake
将`config/config.ymal`文件修改好后，在linux终端执行以下命令：
```sh
cd 本脚本的路径/scripts
snakemake --cores <调用的线程数> --rerun-incomplete --keep-going
```

最终，结果会包含在`results`文件夹中


