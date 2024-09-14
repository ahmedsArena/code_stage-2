Differential Expression Analysis:
This project provides a guide to visualizing gene expression data from a glioblastoma dataset. We employ heatmaps to visualize the expression patterns and conduct differential expression analysis to compute fold changes and p-values for significant genes.

Table of Contents
Requirements
Loading Data
Heatmap Generation
Differential Expression Analysis
Results and Visualization

Requirements
This project uses the following R libraries:

gplots
RColorBrewer
hcl.colors
ggplot2
You can install these libraries by running:
install.packages(c("gplots", "RColorBrewer", "ggplot2"))

Loading Data
The dataset is loaded directly from a public URL. This data contains gene expression profiles of glioblastoma samples.
url <- "https://raw.githubusercontent.com/HackBio-Internship/public_datasets/main/Cancer2024/glioblastoma.csv"
gene_data <- read.csv(url, row.names = 1)
head(gene_data)  # Preview the data


The dataset consists of samples as columns and genes as rows. Each value represents the expression level of a gene in a specific sample.

Heatmap Generation:

1.Scaling and Clustering
To highlight differences in gene expression, we can scale the data across rows (genes) and cluster samples based on their expression profiles.
heatmap.2(as.matrix(gene_data), trace = 'none', 
          scale='row', dendrogram = 'col', 
          Colv = TRUE, Rowv = FALSE)
4. Customizing Colors
You can enhance the visual representation of the heatmap by applying sequential or diverging color palettes.
# Diverging colors
heatmap.2(as.matrix(gene_data), trace = 'none', 
          scale='row', dendrogram = 'col', 
          col=hcl.colors(100, palette = 'green-brown'))

# Sequential colors
heatmap.2(as.matrix(gene_data), trace = 'none', 
          scale='row', dendrogram = 'col', 
          col=hcl.colors(100, palette = 'Blues3'))
Differential Expression Analysis
1. Defining Groups
The data is split into two groups based on sample characteristics. Group 1 contains samples 1-5, while Group 2 contains samples 6-10.
group1 <- c(1,2,3,4,5)
group2 <- c(6,7,8,9,10)

group1_data <- gene_data[, group1]
group2_data <- gene_data[, group2]

2. Calculating Fold Change
We compute the log2 fold change for each gene between the two groups.
group1_mean <- rowMeans(group1_data)
group2_mean <- rowMeans(group2_data)

# Log fold change
fold_change <- log2(group2_mean) - log2(group1_mean)
3. Calculating P-values
To identify significant genes, we perform a t-test between the two groups and compute p-values for each gene.
pvalues <- apply(gene_data, 1, function(row) {
  t.test(row[1:5], row[6:10])$p.value
})
4. Visualization of Results
The fold changes and p-values are plotted to create a volcano plot that helps visualize the significance of the changes.
plot(fold_change, -log10(pvalues), 
     xlab = "log2(Fold Change)", 
     ylab = "-log10(p-value)", 
     main = "Volcano Plot")
5. Filtering Significant Genes
We can filter the results based on specific thresholds for fold change and p-values to identify upregulated and downregulated genes.
df <- data.frame(fold_change, pvalues)
significant_genes <- df[df$fold_change > 0.6 & df$pvalues < 0.05, ]
