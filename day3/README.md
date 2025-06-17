# Day 3 (6/25/2025)
## Activity 1 (Part 1) - Global ancestry estimation using ADMIXTURE
This activity will require the following new software tools:
* ADMIXTURE
* R

Both can be installed together using the included ```.yml``` file:
```
conda env create -f admixture_env.yml
```

You are provided with a set of publicly available genotypes for 414 "reference" individuals, which we will assume to have "100%" African, East Asian, or European genetic ancestry, in addition to the following 10 "target" individuals for which we want to estimate genetic ancestry. In parentheses is which global population each individual is from:
* HG02427 (African Caribbean in Barbados)
* HG02107 (African Caribbean in Barbados)
* HGDP00885 (Russian)
* HG03311 (Esan people in Nigeria)
* HG01789 (Great Britain)
* NA19917 (African American)
* NA20278 (African American)
* NA20279 (African American)
* NA20281 (African American)
* NA20282 (African American)

In the sub-folder ```genetic_data```, you are provided with a merged dataset containing genetic data for these reference and target individuals. The following is a brief explanation of each file:
* ```admixture_input.bed``` - Contains the genotype of each SNP across all individuals. This file is compressed and is machine-readable only.
*  ```admixture_input.bim``` - Contains a list of all of the SNPs in the bed file. It is not compressed and can be viewed using the ```less``` command.
* ```admixture_input.fam``` - Contains a list of all of the people in the bed file, along with their sex and phenotype (optional). It is not compressed and can be viewed using the ```less``` command.
* ```admixture_input.pop``` - Required by ADMIXTURE. Contains the population of all samples in your bed file, in the same order as the samples listed in the fam file. Reference populations are listed, and target populations are left blank using    ```-```.

To run global ancestry estimation on this dataset, first activate the miniconda environment containing the ADMIXTURE software:
```
conda activate admixture_env
```

Now, we can run ADMIXTURE:
```
#Declare the path of the input bed. Also within this directory should be the accompanying bim, fam, and pop files
in_bed=~/vsa_2025_decoding_the_human_genome/day3/genetic_data/admixture_input.bed

#Run ADMIXTURE using the --supervised flag and a K of 3, as we are working with three known reference populations (European, East Asian, and African) in this analysis
admixture --supervised $in_bed 3
```

Once ADMIXTURE has successfully completed a run, its two main outputs are files ending in .P and .Q. The Q file is the one containing global ancestry estimates for each individual based on the three reference populations. This file contains only unlabeled estimates, and all of the reference individuals are also still present with annotations of 100% ancestry for their reference population.

## Activity 1 (Part 2) - Plotting and summarizing global ancestry estimates
We can use the following R script to add the individual identifiers back to the global ancestry estimates and prune out the reference individuals so that only those 10 individuals from our target population remain:
```
#Declare the path of the Q file, which was generated from the initial ADMIXTURE analysis
q_path <- "admixture_input.3.Q"

#Read in the Q file as a data frame
q_file <- read.table(q_path, header = FALSE)
q_df <- as.data.frame(q_file)

#Append the individual IDs from the fam file to the Q file
fam_path <- "/home/mbetti/admixture_input.fam"
fam_file <- read.table(fam_path, header = FALSE, stringsAsFactors = FALSE)
fam_df <- as.data.frame(fam_file)

q_df <- data.frame(fam_df[,1], q_df[,1:3])

#Find the number of 1000 Genomes reference individuals in the .pop file used in the ADMIXTURE analysis
pop_path <- "/home/mbetti/admixture_input.pop"
pop_file <- read.table(pop_path, header = FALSE)
pop_df <- as.data.frame(pop_file)
#pop_df_nonref_count <- length(pop_df[(pop_df[,1] == "-"),])

q_df$pop <- pop_df[,1]

q_df <- q_df[(q_df$pop == "-"),]
q_df <- q_df[,1:4]

#So we will exclude these individuals, leaving only the unlabeled cohort individuals
names(q_df) <- c("IID", "EUR", "EAS", "AFR")

#Write the new data frame out to a new .Q file
out_name <- "admixture_input.3.anno.pruned.Q"
write.table(q_df, file = paste("/home/mbetti", out_name, sep = "/"), quote = FALSE, sep = "\t", row.names = FALSE, col.names = TRUE)
```

After generating this pruned, annotated file of global ancestry estimates, let's also sort based on ancestry proportion. The following script contains code that orders results from highest proportion of European ancestry to lowest:
```
#Declare the path of the pruned Q ADMIXTURE output file containing ancestry estimates for the SCCS cohort compared with 1000 Genomes CEU and YRI individuals
pruned_q_path <- "/home/mbetti/admixture_input.3.anno.pruned.Q"

#Open the Q file and read it in as a data frame
pruned_q_file <- read.table(pruned_q_path, header = TRUE)
pruned_q_df <- as.data.frame(pruned_q_file)

#Create a new data frame ordering the individuals from highest to lowest proportion of African ancestry
ordered_pruned_q_df <- pruned_q_df[order(-pruned_q_df[,2]),]

#Write the ordered data frame out to a new file
out_name <- "admixture_input.3.anno.pruned.ordered.Q"
write.table(ordered_pruned_q_df, file = paste("/home/mbetti", out_name, sep = "/"), quote = FALSE, sep = "\t", row.names = FALSE, col.names = TRUE)
```

Our new output file containing sorted global ancestry estimates should have the following name:
```
admixture_input.3.anno.pruned.ordered.Q
```

Often, the best way to understand your results is to visualize them. We can use the following R script to generate a barplot of global ancestry estimates for our 10 target individuals:
```
#Load data
pruned_q_path <- "/home/mbetti/admixture_input.3.anno.pruned.ordered.Q"
pruned_q_file <- read.table(pruned_q_path, header = TRUE, sep = "\t")

#Sample names and ancestry matrix
sample_names <- pruned_q_file[, 1]
ancestry_matrix <- as.matrix(pruned_q_file[, 2:4])

#Give actual population names
pop_labels <- c("EUR", "EAS", "AFR")
ancestry_colors <- c("green", "blue", "magenta")
colnames(ancestry_matrix) <- pop_labels

#Transpose and assign row names
t_ancestry <- t(ancestry_matrix)

rownames(t_ancestry) <- pop_labels

#Start PDF
pdf("admixture_q.10_individuals.pdf", width = 14, height = 7)

#Use layout to reserve space for legend below
layout(matrix(c(1, 2), nrow = 2), heights = c(6, 1))

#Main barplot
par(mar = c(6, 4, 2, 2))  # space for x-axis labels
barplot(t_ancestry,
        col = ancestry_colors,
        names.arg = sample_names,
        las = 2,
        cex.names = 0.6,
        border = NA,
        xlab = "Individuals",
        ylab = "Proportion Genetic Ancestry")

#Legend in separate panel
par(mar = c(0, 0, 0, 0))
plot.new()
legend("center", legend = pop_labels,
       fill = ancestry_colors, horiz = TRUE, bty = "n", cex = 1.2)

dev.off()
```

Transfer this PDF plot to your local machine using the ```scp``` command.

Finally, let's estimate the average estimated roportion of European, East Asian, and African ancestry across these 10 target individuals. (Hint: One way of estimating the mean proportion of European ancestry is by using the ```mean(df$EUR)``` R function.)

## Questions
1. What are the mean estimated proportions of European, East Asian, and African ancestry among these 10 target individuals?
2. Of the 10 individuals for which you estimated global ancestry, which had the highest estimated proportion of European ancestry?
3. Which individual had the highest estimated proportion of East Asian ancestry?
4. Which individual had the highest estimated proportion of African ancestry?
5. Are these results what you would have expected, based on the geographic regions from which these individuals were recruited? Or were you surprised by any of the results?
6. How do you think the global ancestry results would have changed had we chosen a different set of reference populations?