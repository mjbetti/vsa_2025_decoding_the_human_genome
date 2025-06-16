# Day 2 (6/24/2025)
## Activity 2 - identifying different classes of promoters in induced pluripotent stem cells (iPSCs) using ENCODE data
If you have not already installed bedtools, do so using miniconda2 (see more detailled instructions in the Day 1 README):
```
conda install bioconda::bedtools
```

You should have learned in class that histone marks measured using ChIP-seq experiments can be used to identify different gene promoter "regulatory states." Based on the regulatory state of a promoter, we can predict the expression level of the gene under its control.

In this activity, we will identify active, bivalent, and silenced gene promoters in iPSCs using publicly available ChIP-seq datasets from the ENCODE project.

Navigate to the ENCODE data portal at http://www.encodeproject.org and click on “Functional Genomics.”

On the lefthand side of the screen, select “Histone ChIP-seq” under “Assay title.”

Under “Target of assay,” click “H3K4me3” and “H3K27me3.”

Under “Biosample,” click “iPS-20b.”

Click “List” at the top to see the available experiments.