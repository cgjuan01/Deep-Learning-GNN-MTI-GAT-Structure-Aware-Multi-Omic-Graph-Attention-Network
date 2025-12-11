Deep Learning GNN (MTI–GAT): Structure-Aware Multi-Omic Graph Attention Network for Gene Prioritisation

This repository implements a supervised Graph Attention Network (GAT) for prioritising exercise-responsive and ageing-associated genes by integrating causal inference, multi-omic evidence, protein structure, function, and graph topology.

The model learns structure and function-aware MTI embeddings and produces a hybrid MTI + multi-omic gene ranking that improves on raw MR or MTI-derived scores.

Overview:

The model integrates:

1) Mendelian randomisation (MR) causal signals across four omic layers
2) MTI (Modified Trait Importance) as a supervised regression target
3) AlphaFold v6 structural descriptors (pLDDT distributions, uncertainty, disorder/exposure, length)
4) PANTHER functional annotations (protein class, MF, BP, pathway/components; 4-level structure)
5) Topology metrics from a kNN graph (k=15): PageRank, eigenvector centrality, betweenness, clustering, etc.

These heterogeneous data streams are embedded via a two-layer GAT to produce biologically contextualised, structure-aware prioritisation of exercise-responsive genes.

Core Scoring Basis:

Gene prioritisation is driven primarily by:

1. MR-derived causal evidence (input features)

Layer-specific MR statistics collectively inform the latent embedding:

1) β, SE, p, FDR
2) MR causal score
3) nsnp
4) proteomic, CpG/methylation, glycomic, and single-cell MR support

2. MTI score (supervised regression target)

MTI represents integrated, LD-aware multi-omic causal support.
The GAT learns to predict MTI from combined structure/function/topology features, enabling generalisation across partially observed omics.

Hybrid Ranking (NEW):

The final gene ranking is no longer based solely on MTI predictions.

A hybrid score combines:

1) z(MTI_pred) — supervised regression output
2) z(multi_prob) — probability a gene has ≥2 supported omic layers

The hybrid score rewards genes with strong multi-omic evidence, correcting under-ranking of biologically important regulators (e.g., SIRT1) caused by structural biases.

This hybrid ranking is now the primary output used for all prioritisation and downstream analysis.

Feature Groups:

AlphaFold v6 Structural Features (NEW)

Derived from the official Homo sapiens AFv6 proteome dump:
1) modelled length
2) mean and variance of pLDDT
3) fraction above confidence thresholds (≥70, ≥90)
4) oarse disorder / exposure proxies

Old AlphaFold2/4 fields (pLDDT_mean, sd, frac, n_atoms) are automatically removed.

PANTHER Functional Structure (4 layers):
1) Protein class
2) Molecular function
3) Biological process
4) Pathway / component hierarchy

Dimensionality reduced via PCA (pc1, pc2, pc3 for each layer).

Graph Topology (kNN network):

From a similarity-based kNN graph (k=15):
1) degree
2) PageRank
3) eigenvector centrality
4) betweenness
5) closeness
6) clustering coefficient

Topology helps the model recognise context-dependent functional modules.

Model Architecture:

Two-layer GAT encoder
-GATConv → ELU → dropout → GATConv

Regression head
-Predicts MTI (continuous)

Classification head
-Predicts multi-layer label (MTI_n_layers ≥ 2)

Loss Function:
L=MSE(MTI_score)+λ⋅BCE(multi-layer label)
L=MSE(MTI_score)+λ⋅BCE(multi-layer label)

This jointly enforces:
1) alignment with MTI (causal strength)
2) recognition of multi-omic support (robustness)

Summary:

Deep learning meets multi-omic causal inference.
This GAT integrates:
1) MR causal evidence
2) Multi-omic MTI supervision
3) AlphaFold v6 structural properties
4) PANTHER-derived functional categories
5) Graph topology context

to deliver a structure-aware, evidence-integrated prioritisation of exercise-responsive genes.

The new hybrid ranking ensures that genes with strong multi-omic support are correctly elevated in the final prioritisation.
