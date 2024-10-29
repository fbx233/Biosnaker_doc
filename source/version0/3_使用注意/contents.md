# 3.使用注意

本工具目前处于测试版本，运行过程中可能会产生报错，如遇到问题，请前往我们的github页面进行问题反馈，我们会第一时间进行解决

3.1 fastq格式文件说明

3.1.1 每一个fastq.gz文件中可能有数十万甚至数百万个reads序列。每一个reads都由以下4部分构成：
最好利用Z-tools工具不解压，去查看fastq.gz文件，因为一旦解压会占用很大的磁盘空间，比如zcat , zgrep, zless, zdiff等。如下，是通过zcat命令查看单端数据的前4行

3.1.2 最好利用Z-tools工具不解压，去查看fastq.gz文件，因为一旦解压会占用很大的磁盘空间，比如zcat , zgrep, zless, zdiff等。如下，是通过zcat命令查看单端数据的前4行。

```sh
zcat SRR3589957_R1.fastq.gz head -n 4
```

因此，要熟练掌握zcat ,zgrep,zless,zdiff的使用方法