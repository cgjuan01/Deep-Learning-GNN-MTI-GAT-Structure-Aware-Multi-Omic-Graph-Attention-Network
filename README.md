Deep Learning GNN (MTI–GAT): Structure-Aware Multi-Omic Graph Attention Network for Gene Prioritisation

This repository implements a supervised Graph Attention Network (GAT) for prioritising exercise-responsive and ageing-associated genes by integrating causal inference, multi-omic evidence, protein structure, function, and graph topology.

The model learns structure and function-aware MTI embeddings and produces a hybrid MTI + multi-omic gene ranking that improves on raw MR- or MTI-derived scores.

Overview

The model integrates:

Mendelian randomisation (MR) causal signals across four omic layers

MTI (Modified Trait Importance) as a supervised regression target

AlphaFold v6 structural descriptors (pLDDT distributions, uncertainty, disorder/exposure, length)

PANTHER functional annotations (protein class, MF, BP, pathway/components; 4-level structure)

Topology metrics from a kNN graph (k=15): PageRank, eigenvector centrality, betweenness, clustering, etc.

These heterogeneous data streams are embedded via a two-layer GAT to produce biologically contextualised, structure-aware prioritisation of exercise-responsive genes.

Core Scoring Basis

Gene prioritisation is driven primarily by:

1. MR-derived causal evidence (input features)

Layer-specific MR statistics collectively inform the latent embedding:

β, SE, p, FDR

MR causal score

nsnp

proteomic, CpG/methylation, glycomic, and single-cell MR support

2. MTI score (supervised regression target)

MTI represents integrated, LD-aware multi-omic causal support.
The GAT learns to predict MTI from combined structure/function/topology features, enabling generalisation across partially observed omics.

Hybrid Ranking (NEW)

The final gene ranking is no longer based solely on MTI predictions.

A hybrid score combines:

z(MTI_pred) — supervised regression output

z(multi_prob) — probability a gene has ≥2 supported omic layers

The hybrid score rewards genes with strong multi-omic evidence, correcting under-ranking of biologically important regulators (e.g., SIRT1) caused by structural biases.

This hybrid ranking is now the primary output used for all prioritisation and downstream analysis.

Feature Groups
AlphaFold v6 Structural Features (NEW)

Derived from the official Homo sapiens AFv6 proteome dump:

modelled length

mean and variance of pLDDT

fraction above confidence thresholds (≥70, ≥90)

coarse disorder / exposure proxies

Old AlphaFold2/4 fields (pLDDT_mean, sd, frac, n_atoms) are automatically removed.

PANTHER Functional Structure (4 layers)

Protein class

Molecular function

Biological process

Pathway / component hierarchy

Dimensionality reduced via PCA (pc1, pc2, pc3 for each layer).

Graph Topology (kNN network)

From a similarity-based kNN graph (k=15):

degree

PageRank

eigenvector centrality

betweenness

closeness

clustering coefficient

Topology helps the model recognise context-dependent functional modules.

Model Architecture

Two-layer GAT encoder

GATConv → ELU → dropout → GATConv

Regression head

Predicts MTI (continuous)

Classification head

Predicts multi-layer label (MTI_n_layers ≥ 2)

Loss Function
L=MSE(MTI_score)+λ⋅BCE(multi-layer label)
L=MSE(MTI_score)+λ⋅BCE(multi-layer label)

This jointly enforces:

alignment with MTI (causal strength)

recognition of multi-omic support (robustness)

Inputs
Node table (--node_path)

Must include:

gene_symbol

MTI_score

MR features (all numeric fields)

multi-omic MR features (protein, CpG, glycan, single-cell)

AlphaFold v6 structural metrics (af_*)

PANTHER PC1–PC3 features

kNN-derived topology metrics

Edge table (--edge_path)

kNN edges (from, to) or autodetected equivalents.

Outputs

The script produces a TSV containing:

MTI predictions from GNN

Multi-layer probabilities

Hybrid MTI + multi-omic rank (main ranking)

MTI-only rank (reference)

Scaled scores (0–1)

Full learned embeddings for downstream analyses

Summary

Deep learning meets multi-omic causal inference.
This GAT integrates:

MR causal evidence,

Multi-omic MTI supervision,

AlphaFold v6 structural properties,

PANTHER-derived functional categories, and

Graph topology context

to deliver a structure-aware, evidence-integrated prioritisation of exercise-responsive genes.

The new hybrid ranking ensures that genes with strong multi-omic support—such as key ageing regulators (SIRT1) and enzymatic hubs (B4GALT1, FUT8, ST6GAL1)—are correctly elevated in the final prioritisation.
