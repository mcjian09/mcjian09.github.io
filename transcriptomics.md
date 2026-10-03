## Spatial Transcriptomics Research

I started researching spatial transcriptomics as part of the MIT PRIMES-USA program, under the guidance of [Professor Gil Alterovitz](https://connects.catalyst.harvard.edu/Profiles/display/Person/65201) and [Dr. Shaojun Pei](https://orcid.org/0000-0001-9758-3959).

We developed SpaCoEx, a sparse spatial representation framework that integrates gene-expression levels with spatially varying gene-gene co-expression. 

SpaCoEx first estimates local co-expression matrices from neighboring spatial spots, maps them into a log-Euclidean representation, and performs structured gene selection by retaining or removing the full row and column associated with each gene. The selected genes are then used to construct both expression-level features and local co-expression features, which are combined through an α-weighted joint representation for downstream spatial analysis. Together, it provides a sparse, low-dimensional, and interpretable representation of spatial transcriptomics data that captures complementary aspects of tissue organization beyond expression-based variation alone.

Applying the SpaCoEx method to human data, we found 
(1) SpaCoEx identifies spatially varying co-expression in cutaneous squamous cell carcinoma regions
(2) SpCoEx identified spatially varying gene-gene co-expression and differential co-expression between cancer and non-cancer regions

A manuscript of this work can be found at [BioRxiv.org](https://www.biorxiv.org/content/10.64898/2026.09.05.749559v2.full).
