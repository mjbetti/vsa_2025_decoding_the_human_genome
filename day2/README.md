# Day 2 (6/24/2025)
## Activity 2 (Part 1) - Identifying different classes of promoters in induced pluripotent stem cells (iPSCs) using ENCODE data
If you have not already installed bedtools, do so using miniconda2 (see more detailled instructions in the Day 1 README):
```
conda install bioconda::bedtools
```

You should have learned in class that histone marks measured using ChIP-seq experiments can be used to identify different gene promoter "regulatory states." Based on the regulatory state of a promoter, we can predict the expression level of the gene under its control.

In this activity, we will identify active, bivalent, and silenced gene promoters in iPSCs using publicly available ChIP-seq datasets from the ENCODE project.

1. Navigate to the ENCODE data portal at http://www.encodeproject.org and click on “Functional Genomics.”
![alt text](https://github.com/mjbetti/vsa_2025_decoding_the_human_genome/blob/main/day2/Fig1.png?raw=true)

2. On the lefthand side of the screen, select “Histone ChIP-seq” under “Assay title.”
![alt text](https://github.com/mjbetti/vsa_2025_decoding_the_human_genome/blob/main/day2/Fig2.png?raw=true)

3. Under “Target of assay,” click “H3K4me3” and “H3K27me3.”
![alt text](https://github.com/mjbetti/vsa_2025_decoding_the_human_genome/blob/main/day2/Fig3.png?raw=true)

4. Under “Biosample,” click “iPS-20b.”
![alt text](https://github.com/mjbetti/vsa_2025_decoding_the_human_genome/blob/main/day2/Fig4.png?raw=true)

5. Click “List” at the top to see the available experiments after filering for these selected criteria.
![alt text](https://github.com/mjbetti/vsa_2025_decoding_the_human_genome/blob/main/day2/Fig5.png?raw=true)

6. To download the H3K4me3 ChIP-seq bed file, click on the experiment, scroll down, and click on "File details."
![alt text](https://github.com/mjbetti/vsa_2025_decoding_the_human_genome/blob/main/day2/Fig6.png?raw=true)

7. Download the narrowPeak bed file based on hg38 and rename the downloaded file from ```ENCFF401HOA.bed.gz``` to ```H3K4me3_iPS-20b.bed.gz```.
![alt text](https://github.com/mjbetti/vsa_2025_decoding_the_human_genome/blob/main/day2/Fig7.png?raw=true)

8. To download the H3K27ac ChIP-seq bed file, click on the experiment, scroll down, and click on "File details."
![alt text](https://github.com/mjbetti/vsa_2025_decoding_the_human_genome/blob/main/day2/Fig8.png?raw=true)

9. Download the narrowPeak bed file based on hg38 and rename the downloaded file from ```ENCFF401HOA.bed.gz``` to ```H3K27ac_iPS-20b.bed.gz```.
![alt text](https://github.com/mjbetti/vsa_2025_decoding_the_human_genome/blob/main/day2/Fig9.png?raw=true)

10. Upload both of these files to a new folder in your server home directory called ```~/day2_encode```. (Hint: You will need to use the ```mkdir``` and ```scp``` commands).

11. First find how many H3K4me3 and H3K27ac peaks we are starting with. (Hint: The ```.gz``` suffix of our bed files indicates that they are compressed. We will first need to uncompress each file and then forward this data to the ```wc -l``` command using ```gunzip -c filename | wc -l```).

12. Using the ```bedtools intersect``` command, identify active promoters, bivalent promoters, and silenced promoters in the iPS-20b cell line. (Hint: the bedtools documentation can be found here [here](https://bedtools.readthedocs.io/en/latest/content/tools/intersect.html). To identify active and silenced promoters, you will need to use the ```-v``` argument.) Save each output to a new bed file using ```> output_name.bed```. For example, the output files should be called ```active.bed```, ```bivalent.bed```, and ```silenced.bed```, respectively.

13. Find the final number of active promoters, bivalent promoters, and silenced promoters that you identified.(Hint: Use the ```wc -l``` command.)

## Activity 2 (Part 2) - Visualizing promoter elements using IGV
1. Download the new bed files you just made with active, bivalent, and silenced promoters to your local machine.

2. Navigate to the [IGV genome browser](https://igv.org/app/), and make sure that genome build hg38 is selected.
![alt text](https://github.com/mjbetti/vsa_2025_decoding_the_human_genome/blob/main/day2/Fig10.png?raw=true)

3. Under "Tracks" in the upper menu bar, click "Local File..." and upload the three bed files you made containing the coordinates for active, bivalent, and silenced promoters.
![alt text](https://github.com/mjbetti/vsa_2025_decoding_the_human_genome/blob/main/day2/Fig11.png?raw=true)

4. Look up the gene PRMT6 on chr1.
    * Is the promoter region of this gene a bivalent promoter, an active promoter, or a silenced promoter?
    * Based on this information, is this gene likely expressed or not expressed in the iPS-20b cell line?
    * Go to [GeneCards](https://www.genecards.org) and look up PRMT6. Is this a protein-coding gene? Are there any phenotypes associated with this gene? (Hint: Look under the GeneCards Summary for PRMT6 Gene and Disorders sections.)

5. Look up the gene DDHD2 on chr8.
    * Is the promoter region of this gene a bivalent promoter, an active promoter, or a silenced promoter?
    * Based on this information, is this gene likely expressed or not expressed in the iPS-20b cell line?
    * Go to [GeneCards](https://www.genecards.org) and look up DDHD2. Is this a protein-coding gene? Are there any phenotypes associated with this gene? (Hint: Look under the GeneCards Summary for DDHD2 Gene and Disorders sections.)

6. Look up the gene LOC105378310 on chr10.
    * Is the promoter region of this gene a bivalent promoter, an active promoter, or a silenced promoter?
    * Based on this information, is this gene likely expressed or not expressed in the iPS-20b cell line?
    * Go to [GeneCards](https://www.genecards.org) and look up LOC105378310. Is this a protein-coding gene? Are there any phenotypes associated with this gene? (Hint: Look under the GeneCards Summary for LOC105378310 Gene and Disorders sections.)



