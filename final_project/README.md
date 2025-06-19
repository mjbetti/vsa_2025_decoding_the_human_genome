# Final project - Running a TWAS of your own
## Selecting a complex trait GWAS
Choose any complex trait that you find interesting and download the GWAS summary statistics from the GWAS Catalog [here](https://www.ebi.ac.uk/gwas/). If you need help brainstorming, a list of 20 potential traits will be posted to Brightspace.

## Project overview
1. Using the GWAS summary statistics you have selected, run a TWAS on the server in 49 cell and tissues and plot the results as a Manhattan plot.
2. Prepare a short (~5-minute) presentation that includes the following information:
    * A brief background about your trait, including what it is, its population prevalence, and its estimated heritability
    * When the GWAS you used was published, what the sample size was, and which associations (if any) were discovered
    * Which gene-tissue associations you discovered in your TWAS (if any) and what is their known function?
    * Have any of the genes you identified previously been linked to your phenotype, or are they novel associations?
    * If you do not identify any significant associations, why do you think this might be?

## Setting up TWAS software
To run TWAS, we will use a piece of software written in Python called S-PrediXcan. S-PrediXcan requires several Python dependencies, which can be installed together using the included ```.yml``` file:
```
conda env create -f spredixcan_env.yml
```

The S-PrediXcan script itself is available on the server and can be executed from the following path:
```
/mnt/4tb/MetaXcan/software/SPrediXcan.py
```

To run a TWAS, S-PrediXcan requires the following components:
* <strong>GWAS summary statistics</strong> - These contain the effect size and significance of each SNP's association with your complex trait of interest
* <strong>TWAS models</strong> - You can think of these as "machine learning models" that have "learned" how much each SNP in the genome affects the expression (either positively or negatively) of each gene in each human cell/tissue type. In TWAS, we basically use these weights to predict how our GWAS SNPs impact gene expression.
* <strong>SNP covariance matrices</strong> - Each TWAS model has an accompanying SNP covariance matrix. Basically, this tells PrediXcan how correlated each SNP is with all other SNPs, so that it does not "double count" SNPs that are highly correlated and over-estimate their effect on gene expression.

The <strong>TWAS models</strong> and <strong>SNP covariance matrices</strong> can be accessed in the following server directory:
```
/mnt/4tb/gtex8_models
```
The TWAS model files end in the prefix ```.db```, and the covariance matrices end in the prefix ```.txt.gz```.

## Running the TWAS analysis
In your home directory, make a new folder in which these analyses will be run:
```
mkdir ~/twas
```

Next, run the TWAS using the code below. You will need to modify the paths for ```GWAS_PATH``` and ```OUR_DIR```, along with the arguments for ```--gwas_file```, ```--snp_column ID```, ```--effect_allele_column```, ```--non_effect_allele_column```, ```--pvalue_column```, ```--or_column```, and ```--output_file```.

GWAS effect sizes can be quantified using either an odds ratio (OR) or beta coefficient. If your GWAS summary statistics are beta values, use ```--beta_column``` instead of ```--or_column```.
```
cd ~/twas

conda activate spredixcan_env

#Declare the path of the source directory containing the MetaXcan scripts, as well as the directories of the inputs, outputs, and TWAS models

SCRIPT_DIR=/mnt/4tb/MetaXcan/software

MODEL_COV_DIR=/mnt/4tb/gtex8_models

GWAS_PATH=/home/bettimj/aldrich_rotation/summary_stats_hwe_1e-40/lc_afr_gwas_plink_ever_smokers_remove_missing_covs_and_unknown_sex_hwe_1e-40.PHENO1.glm.logistic.reformat.arm.txt

OUT_DIR=/home/mbetti/twas

#Run MetaXcan using the prepared GTEx v8 models and AALC GWAS results
for i in $(ls $MODEL_COV_DIR\/*.db); do
	tissue=`echo $i | cut -c27- | sed 's/...$//'`
	python $SCRIPT_DIR\/SPrediXcan.py \
	--model_db_path $i \
	--covariance $MODEL_COV_DIR\/$tissue\.txt.gz \
	--gwas_file $GWAS_PATH \
	--snp_column ID \
	--effect_allele_column REF \
	--non_effect_allele_column ALT \
	--pvalue_column P \
	--or_column OR \
	--output_file $OUT_DIR\/twas_results_$tissue\.csv \
	--keep_non_rsid
done
```

Once this script has finished running, you should have a set of 49 ```.csv``` files, one for each TWAS tissue.

## Plotting and interpreting your TWAS results
This portion of the analysis will use code written in the R programming language. To install R and several of its packages that we will need for statistical analysis and plotting, install the miniconda environment included in the ```r_env.yml``` file:
```
conda env create -f r_env.yml  
```

In many standard statistical analysis, it is common to use a p-value theshold of <em>p</em> < 0.05 to identify a significant result. Basically, this means that there is a less than 5% chance that your result is due to random chance.

If you remember from our in-class discussions, though, in GWAS and TWAS, we are performing thousands or even millions of individual statistical tests, and the probability of one of those being significant by chance increases with each additional test we perform. In order to correct for this, we can use a <strong>Bonferroni correction</strong>, where we will divide the nominal p-value threshold of 0.05 by the number of tests we are performing.

For your TWAS, you can compute Bonferroni-corrected significance thresholds using the following R code. Here, we will compute two thresholds:
1. A "genome-wide significant" threshold dividing 0.05 by the total number of gene-tissue associations we tested for
2. A more relaxed "suggestive significance" threshold dividing 0.05 by the total number of unique genes we tested for

We will also compute the -log<sub>10</sub>(<em>p</em>) value for each, which is simply a mathematically transformed version of the p-value commonly used for plotting Manhattan plots:
```
#Declare the path of the results directory
source_dir <- "/home/mbetti/twas"

cat_df <- data.frame(matrix(ncol = 12, nrow = 0))
names(cat_df) <- c("gene", "gene_name", "zscore", "effect_size", "pvalue", "var_g", "pred_perf_r2", "pred_perf_pval", "pred_perf_qval", "n_snps_used", "n_snps_in_cov", "n_snps_in_model")

for (i in list.files(source_dir, pattern = "*.csv")) {
	file <- read.csv(i, header = TRUE, stringsAsFactors = FALSE)
	df <- as.data.frame(file)
	cat_df <- rbind(cat_df, df)
}

genes <- cat_df$gene

print("Genome-wide significance")
print("p-value threshold:")
print(0.05 / length(genes))
print("-log10(p) threshold:")
print(-log10(0.05 / length(genes)))
print("\n")

uniq_genes <- unique(cat_df$gene)

print("Suggestive significance")
print("p-value threshold:")
print(0.05 / length(uniq_genes))
print("-log10(p) threshold:")
print(-log10(0.05 / length(uniq_genes)))
```

What are the p-value thresholds that you see printed out?

The ```.csv``` files containing your TWAS results contain the results for all genes, even those that are not significant. To compile a list of <strong>only significant TWAS associations</strong>, we can use the following R script:
```
library("dplyr")
library("data.table")

#Declare the output directory to which these results will be saved
out_dir <- "/home/mbetti/twas"

#Declare the path of the AALC GWAS results file
in_dir <- "/home/mbetti/twas"

near_sig_df <- data.frame()
sig_df <- data.frame()
cat_df <- data.frame()

tissues <- c("Adipose_Subcutaneous", "Adipose_Visceral_Omentum", "Adrenal_Gland", "Artery_Aorta", "Artery_Coronary", "Artery_Tibial", "Brain_Amygdala", "Brain_Anterior_cingulate_cortex_BA24", "Brain_Caudate_basal_ganglia", "Brain_Cerebellar_Hemisphere", "Brain_Cerebellum", "Brain_Cortex", "Brain_Frontal_Cortex_BA9", "Brain_Hippocampus", "Brain_Hypothalamus", "Brain_Nucleus_accumbens_basal_ganglia", "Brain_Putamen_basal_ganglia", "Brain_Spinal_cord_cervical_c-1", "Brain_Substantia_nigra", "Breast_Mammary_Tissue", "Cells_Cultured_fibroblasts", "Cells_EBV-transformed_lymphocytes", "Colon_Sigmoid", "Colon_Transverse", "Esophagus_Gastroesophageal_Junction", "Esophagus_Mucosa", "Esophagus_Muscularis", "Heart_Atrial_Appendage", "Heart_Left_Ventricle", "Kidney_Cortex", "Liver", "Lung", "Minor_Salivary_Gland", "Muscle_Skeletal", "Nerve_Tibial", "Ovary", "Pancreas", "Pituitary", "Prostate", "Skin_Not_Sun_Exposed_Suprapubic", "Skin_Sun_Exposed_Lower_leg", "Small_Intestine_Terminal_Ileum", "Spleen", "Stomach", "Testis", "Thyroid", "Uterus", "Vagina", "Whole_Blood")

for (tissue in tissues) {
    print(tissue)
    file <- read.csv(paste(in_dir, paste0("twas_results_", tissue, ".csv"), sep = "/"), header = TRUE, stringsAsFactors = FALSE)
    df <- as.data.frame(file)
    df$tissue <- tissue
    cat_df <- rbind(cat_df, df)
}

#Loop through and split up the coordinates by chromosome
cat_df <- cat_df[(cat_df$pvalue < (0.05 / unique(cat_df$gene))),]
cat_df <- na.omit(cat_df)

final_df_ordered <- cat_df[order(cat_df$pvalue),]

#Print out the unique genes that reach at least suggestive significance
unique(final_df_ordered$gene_name)

#Write out your results to a new
final_out_name <- "twas_results.near_significant.txt"
write.table(final_df_ordered, file = paste(out_dir, final_out_name, sep = "/"), quote = FALSE, sep = "\t", row.names = FALSE, col.names = TRUE)
```

Finally, we can plot the TWAS results visually, as a Manhattan plot, together with their corresponding GWAS results. To do this, you can use the included ```plot_twas_results.R``` R script. You will need to modify the following portions of the script to get it to run:
* <strong>Line 6</strong>: Modify ```gwas_summary <- fread("/path/to/gwas_summary_stats.txt", header= TRUE, sep = "auto")``` to use the file path of your GWAS summary statistics
* <strong>Line 11</strong>: Modify ```gwas_df <- new_xaxis_func_v2(gwas_summary, chr_name = "CHROM", pos_name = "POS", p_val_name = "P", snp_name = "ID")``` to use the proper names of the chromosome, SNP position, p-value, and SNP ID columns in your GWAS summary statistics
* <strong>Lines 16 and 17</strong>: Comment out one of these two lines, depending on whether your GWAS summary statistics use genome build hg19 or hg38.

You can run this script using two different approaches:
1. Type ```Rscript plot_twas_results.R``` in your terminal window to run the script interactively. You cannot close your terminal while the script is running, or the job will be killed.
2. Submit a job using the following command:
```
sbatch \
--job-name=chr$i \
--nodes=1 \
--ntasks=1 \
--cpus-per-task=1 \
--mem=8G \
--wrap="Rscript plot_twas_results.R"
```
This command will submit your script to the SLURM job scheduler, where it will run remotely in the background, even if you log out or disconnect from the server.

Your final plot should look something like this:
![alt text](https://github.com/mjbetti/vsa_2025_decoding_the_human_genome/blob/main/final_project/Fig1.png?raw=true)

Once you have your list of significant associations and Manhattan plot, compile prepare a short presentation describing your findings, as described above.