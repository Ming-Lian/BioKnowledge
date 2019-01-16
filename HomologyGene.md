<a name="content">目录</a>

[基因的同源性](#title)
- [同源基因的类型与关系](#types-relationship)
- [复制事件置信度分值(Duplication confidence score)](#duplication-confidence-score)
- [进化树中的Gene Split事件](#gene-split)
- [构建基因家族进化树：GeneTree](#construct-genetree)



<a name="types-relationship"><h2>同源基因的类型与关系 [<sup>目录</sup>](#content)</h2></a>

<p align="center"><img src=./picture/HomoGene-Relationship.png width=800 /></p>

**orthologues**: genes in different species and related by a speciation event

有三种类型的orthologues：


> - 1-to-1 orthologues (ortholog_one2one)
> - 1-to-many orthologues (ortholog_one2many)
> - many-to-many orthologues (ortholog_many2many)
> - between-species paralogues – only as exceptions

**paralogues**: genes of the same species and related by a duplication event

有两种类型的paralogues：

- same-species paralogies (within_species_paralog)
- fragments of the same ‐predicted‐ gene (gene_split)

上图中，Hsap2-Hsap2' 和 Mmus2-Mmus2' 两组 ，就是典型的种内旁系同源，它们是在物种形成后发生的同源基因扩增得到的，但是**同源基因扩增不一定要发生在物种形成之后才能形成种内旁系同源关系**，比如 Mmus2' 与 Mmus3' 它们的基因扩增发生在Mmus物种形成之前它的祖先Euarchontoglires时期，它们仍然是种内旁系同源基因

再举个例子说明同源关系：

<p align="center"><img src=./picture/HomoGene-Relationship-example.png width=800 /></p>

从上图的genetree中可以看到，House mouse的Cyp2a12，Cyp2a22，Cyp2a5和Cyp2a4互为paraloggues，Human的CYP2A13，CYP2A7和CYP2A6也是互为paraloggues，而老鼠的那4个基因和人的那3个基因之间互为many2many的orthologues

这个同源性的结果可以从GeneCard数据库中得到验证：

<p align="center"><img src=./picture/HomoGene-Relationship-example-2.png width=800 /></p>

<p align="center"><img src=./picture/HomoGene-Relationship-example-3.png width=800 /></p>

<a name="duplication-confidence-score"><h2>复制事件置信度分值(Duplication confidence score) [<sup>目录</sup>](#content)</h2></a> 

使用Jaccard index来评估这个分值

> Jaccard index简单说明：
> 
> <p align="center"><img src=./picture/HomoGene-duplication_confidence_score-Jaccard_index-venn.png width=300 /></p>
> 
> <p align="center"><img src=./picture/HomoGene-duplication_confidence_score-Jaccard_index-formula.svg width=300 /></p>


<p align="center"><img src=./picture/HomoGene-duplication_confidence_score.png width=800 /></p>

<a name="gene-split"><h2>进化树中的Gene Split事件 [<sup>目录</sup>](#content)</h2></a> 

在某些种内旁系同源基因类型中，可以被归类为Gene Split：例如，在构建基因树的过程中，有两条序列，它们分别比对到了另外一个同源基因的首末两端，因此在建树时，它们会被聚在一起且被放置于那条同源基因的下游

<p align="center"><img src=./picture/HomoGene-gene_split.png width=800 /></p>

之所以会出现这样的情况，可能的原因是基因组拼接不完整，过于碎片化，特别是在建树的目标基因区域，拼接发生了断裂；也可能是基因预测不准确，实际基因长度更长，结果被预测成了短的两个基因

如何判定为Gene Split？

> 在完成多序列比对后，来自同一物种的两条基因序列比对的位置挨得很近（< 1 MB），且位于同一条链上（ 如上图中的GeneA1/GeneA2）

<a name="construct-genetree"><h2>构建基因家族进化树：GeneTree [<sup>目录</sup>](#content)</h2></a> 

<p align="center"><img src=./picture/HomoGene-TreeFam-pipeline.png width=800 /></p>

<p align="center">TreeFam pipeline</p>

- **Sequence Input**

TreeFam uses sequences from the following databases: Ensembl, Ensembl Genomes, JGI, and Wormbase

- **Family Assignment**

TreeFam uses the `HMMER` software to assign new homologs to existing TreeFam families using default settings. We are planning to increase the sequence coverage by building new families

- **Multiple Sequence Alignment**

For each TreeFam family, we re-build a multiple alignment based on protein sequence belonging to that family plus new homologs found in the Family Assignment step. The alignment method uses a combination of multiple aligners, and is implemented in `M-Coffee`. 

- **Phylogenetic Tree**

For each aligned Family, we build a phylogenetic tree using `TreeBeST` using the CDS back-translation of the protein multiple alignment from the original DNA sequences. A rooted tree with internal duplication tags is obtained at this stage, reconciling it with the species tree. A description of the tree building method in TreeBeST: the CDS back-translated protein alignment (i.e., codon alignment) is used to build 5 different trees:

> - a maximum likelihood (ML) tree built using phyml, based on the protein alignment with the WAG model;
> - a ML tree built using phyml, based on the codon alignment with the Hasegawa-Kishino-Yano (HKY) model;
> - a neighbour-joining (NJ) tree using p-distance, based on the codon alignment;
> - a NJ tree using dN distance, based on the codon alignment; and
> - a NJ tree using dS distance, based on the codon alignment.

- **Orthology Calls**

From each gene tree, infer gene pairwise relations of orthology* and paralogy types. 

---

参考资料：

(1) [ENSEMBEL tutorial: Homology types](http://asia.ensembl.org/info/genome/compara/homology_types.html)

(2) [TreeFam数据库：The TreeFam pipeline](http://www.treefam.org/help#tabview=tab1)
