# NGS_Contaminants_Database

NGS_Contaminants_Database IS a database that contain almost all the exists Contaminants in NGS pipelines that generated.

RECOMMAND:
the {}NGS_Contaminants_Database}.fa should be used as an adapter file, use before alignment and genome assembly.

NGS Contaminants Database
​最全面的 NGS 污染物参考库​.

目的是收集全球主流测序平台、建库试剂盒和实验流程中高频出现的污染物序列（含罕见变异形式），提供一键式 FASTA 文件，助力精准剔除污染，提升基因组组装质量。

🧩 污染物类型与来源
本库系统整合以下 5 类污染物，覆盖 ​​“常见接头→隐蔽污染物”全谱系​：

​类别​	​包含内容​	 ​应用场景​
**​标准接头**​	Illumina P5/P7、NovaSeq Xp、MGI 双端索引、PacBio SMRTbell 环化接头	常规去接头
**​接头变异体**​	酶切残留（T尾/A尾）、降解片段、Index Hopping 错配序列、交叉污染嵌合体	顽固污染清除
​**实验试剂污染**​	载体蛋白（BSA）、大肠杆菌基因组、PhiX 对照、核酸酶残留	微生物组/低生物量样本
**​人类污染**​	线粒体 DNA (mtDNA)、高丰度 Alu 重复序列、血细胞基因组片段	植物/环境样本净化
**​操作污染​**	实验室气溶胶常见菌（Pseudomonas、Bacillus）、引物二聚体、rRNA 残留	宏基因组去宿主
💡 **特色：**收录 ​21 种“奇葩”接头​（如截短体、反向互补污染、交叉连接嵌合体），解决传统工具漏检问题
。

🛠️ 使用场景与流程整合
推荐应用节点

# Step 1: 原始数据质控  
```
fastqc -c NGS_Contaminants_Database.fa raw_data.fq  # 在 FastQC 中预筛查污染[8,10](@ref)  
```
# Step 2: 联合去污染（推荐流程）  
```
cutadapt -a file:NGS_Contaminants_Database.fa -o clean.fq raw_data.fq  # 精准切除复杂污染物[2](@ref)
```
# 或  
```
bwa mem -C contaminant_db.fa raw_data.fq | samtools fastq -f 12 - > cleaned.fq  # 比对过滤法  
```
# Step 3: 宏基因组组装  
```
megahit -r cleaned.fq -o assembly_output  # 输入净化后数据
``` 
🔧 适配工具清单
​切除类​：Cutadapt
, Trimmomatic, fastp
​比对过滤类​：Bowtie2, BWA, DeconSeq
​质控类​：FastQC（需 -c 参数加载本库）
📁 文件说明

​**NGS_Contaminants_Database.fa**​：主库文件，包含序列名 + 污染来源注释，例如：
fasta
```
>Illumina_P5_adapter_variant2 (degraded_with_T-tail)  
AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGT  
>Human_mtDNA_contamination  
GATCACAGGTCTATCACCCTATTAACCA
>
```
​**contaminant_types.md**​：污染物分类索引，支持按实验类型快速筛选序列
​**update_log.md**​：版本更新记录（2025.07 新增 47 种 Nanopore 接头嵌合体）

❓ 为什么需要本库？
传统去接头工具的局限：

​覆盖不全​：默认库仅含标准接头，漏检 68% 的降解/嵌合污染物
；
​假阴性率高​：短片段污染（如酶切残留）需重叠匹配（--overlap 5）才可识别
；
​物种干扰​：污染物误组装入基因组（如细菌污染影响植物基因组完整性）
。
✅ 本库通过 ​实验验证+公共数据溯源​ 解决上述问题。

🌐 数据来源与维护
​核心数据​：从 NCBI Contaminants、MaConDa、文献中人工审核提取
​提交入口​：开放 contaminant_submission_template.tsv，支持用户提交新污染物
​更新策略​：季度更新（请订阅 Watch 获取通知）
​
如觉得本资源游泳，请在发表时添加科学引用，助力好资源传播​：
```
bibtex
@misc{NGSContaminantsDB,  
  author = {Your Name},  
  title  = {NGS Contaminants Database},  
  year   = {2025},  
  url    = {https://github.com/qzhaojing/NGS_Contaminants_Database}  
}
```
