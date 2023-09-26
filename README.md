# SSCB project _In_silico_ tissue stratification

This repository serves as the deliverable for our assignment project during the [Summer School in Computational Biology](https://www.uc.pt/en/events/computationalbiology/) at the Universidade de Coimbra in September 2023. The project aims to address an important challenge in computational biology: predicting the percentage of each cell type within bulk tissue samples. This prediction is valuable for reducing result interpretation bias and optimizing sequencing costs.

## The challenge 

Biological samples, in different health conditions (healthy and disease), may consist of varying numbers of distinct cell types. The composition of these cell types within a tissue can significantly impact the determination of differentially expressed genes. When analyzing tissue samples as a whole (bulk analysis), gene expression values are averaged across all cells within the sample. However, if individual cells within the same tissue are sequenced (single-cell analysis), it becomes possible to determine the expression of each gene by cell type. This introduces a critical challenge: relative gene expression from bulk data may not reveal differences between conditions, potentially leading to biased result interpretation and/or identification of false negatives or false positive. For example, if we do not know the percentage of each cell type in the sample, then we cannot infer if the changes in gene expression are due to changes in the molecular processes within the cells or due to shifts in cellular composition.

In essence, the challenge aims to solve two problems:
- **Reduced Bias in Interpretation:** Accurate estimation of cell type composition within a bulk-RNA tissue sample enables us to mitigate the bias introduced by cell type variations when identifying differentially expressed genes.
- **Optimizing Sequencing Costs:** Precise cell type composition prediction can help optimize sequencing resources by reducing the need for costly single-cell RNA-seq experiments, especially when such information can be inferred from bulk RNA-seq data.

### Data Resources

For this assignment, we leverage data related to Huntington's disease from publicly available publications:

|publication | data source | GEO | Organism
|:-:|:-: |:-:|:-:|
|[Hodges et al 2006]([link](https://pubmed.ncbi.nlm.nih.gov/16467349/)) |bulk RNA (Affymetrix)  |[GSE3790](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE3790) | *H. sapiens*|
|[Matsushima et al 2023]([link](https://pubmed.ncbi.nlm.nih.gov/36650127/)) |single-cells RNA-seq (Illumina) | [GSE152058](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE152058) |*H. sapiens*

### Our approach

To tackle thi challenge, we initially envisioned an ideal experiment scenario: dividing each tissue sample equally. One part would undergo bulk analysis, while the other would undergo single-cell analysis. This would provide insights into how single cells contribute to the overall gene expression by examining differences in gene expression for identified cell types. However, for obvious reasons, this experiment is out of the scope of the course, and our stating point was the available data mentioned above.

Nowing that, we propose a machine learning approach. Specifically, we plan to train a neural network to learn the weights of each gene while considering tissue composition (the percentage of each cell type) as the target variable. Using our single-cell sample data, we calculate the percentage of each cell type in the tissue and the corresponding gene expression levels. Subsequently, we compute an overall expression value for each gene, considering all cells within that gene's group. This gene expression matrix becomes our input for the neural network, formatted similarly to the bulk tissue sample data.

For the sake of simplicity and time-efficiency, we design a neural network with two layers, with the number of nodes equal to the number of genes. The final layer employs a `softmax()` function, yielding probabilities for each cell type. Since our target variables are matrices representing cell compositions, where the `sum()` equals 1, these probabilities represent the estimated percentage of each cell type within the tissue, derived from the bulk RNA data.

The following flowchart summarizes our approach, with solid lines representing the problem flow and dashed lines indicating our proposed solution:


````mermaid
flowchart TD
%% problem, solid line %%
A((tissue sample)) ===> B[Bulk RNA-seq] 
B ---> E[unkown % cell type]
A ===> C[single-cell RNA-seq]
B --> |tissue average|D[gene relative expression]
C --> |cell type average|D
C ---> F[known % cell type]
E ---> G[unknown tissue composition]
F --> H[known tissue composition]
%% solution, dashed line %%
H -.-> I1[compute tissue gene expression average]
I1 -.-> I2[build neuronal net]
J1[features = genes, age, condition] -...- I2
J2[targets = % cell type] -...- I2
I2 -.- K1[model]
K1 -.-> G
G -.-> I[tissue composition estimmation]
```

------

Work under supervision of [prof. Matthias Futschik]([link](https://github.com/MatthiasFutschik)) and Sofia Torres.