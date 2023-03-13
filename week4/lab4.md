# Lab Week 4. Sociogenomics

[![Open in Cloud Shell](https://gstatic.com/cloudssh/images/open-btn.png)](https://ssh.cloud.google.com/cloudshell/open?cloudshell_git_repo=https://github.com/nicolabarban/sociogenomics2023&cloudshell_tutorial=week4/lab4.md)

## Description
In this lab we will learn:

* Compute descripitve statistics with PLINK on genetic files 
* Quality control procedure with plink
* Run association analysis with PLINK
* Select independent SNPS
* compute Principal Components Analysis in PLINK
* Calculate IBS and relatedness
* Run a GWAS in plink
## Part I . Managing files and directories.
Let's have a look a the file. 

```
cd $HOME/Sociogenomics
rm Data/*.*

wget -O Data/lab_week4.zip https://www.dropbox.com/s/wum0okiq4dbn9p0/week4.zip?dl=0 
unzip -o -d Data/ Data/lab_week4.zip 
mv Data/week4/*.*  Data/ 
rm -r Data/week4/
```



```
 head Data/1kg_EU_BMI.fam
```

how many observations?

```
 wc -l Data/1kg_EU_BMI.fam
```

how many variants?

```
 wc -l Data/1kg_EU_BMI.bim
```


## Descriptive Statistics



Allele frequency
```
 ./plink --bfile Data/hapmap-ceu  --freq --out Results/Allele_Frequency
```

```
head Results/Allele_Frequency.frq 
```

### Missing values

individuals
```
./plink --bfile Data/hapmap-ceu --missing --out Results/missing_data
```
variants
```

head Data/missing_data.imiss
```

Filter females
```

./plink     --bfile Data/hapmap-ceu \
            --filter-females \
            --make-bed \
       	 	--out Results/hapmap_filter_females

```
## Quality control
```


./plink --bfile Data/1kg_EU_BMI \
		--mind 0.05 \
		--make-bed \
		--out Results/1kg_hm3_mind005
```

Calculate heterozygocity
```

./plink --bfile Data/1kg_EU_BMI \
		--het --out Results/1kg_hm3_het
```

Check discordant sex
```

./plink --bfile Data/hapmap-ceu \
		--check-sex \
		--out Results/hapmap_sexcheck 
```

Low call-rate SNPS
```

./plink --bfile Data/1kg_EU_BMI \
		--geno 0.05 \
		--make-bed \
		--out Results/1kg_hm3_geno
```

Allele frequency
```

./plink --bfile Data/1kg_EU_BMI \
	 	--maf 0.01 \
		--make-bed  --out Results/1kg_hm3_maf
```
deviation from HWE
```

./plink --bfile Data/1kg_EU_BMI \
	 	--hwe 0.00001 \
		--make-bed  --out Results/1kg_hm3_hwe

```

**PLINK QC**. combine different commands in one go
```


./plink     --bfile Data/1kg_EU_BMI \
       	--mind 0.03 \
       	--geno 0.05 \
       	--maf 0.01 \
    	--hwe 0.00001 \
        --make-bed  --out Results/1kg_hm3_QC      
			
```
## Select independent SNPS

Calculate linkage disequilibrium
```
./plink --bfile Data/hapmap-ceu \
	 	--ld rs2883059 rs2777888 \
		--out Results/ld_example

```
Caluclate independent SNPs (Pruning)
```

./plink 	 --bfile Results/1kg_EU_BMI --maf 0.01 \
        	--indep-pairwise 50 5 0.2 \
        	--out  Results/1kg_hm3_qc_pruned
```
Select from original sample independent SNPs
```
./plink		--bfile  Results/1kg_EU_BMI \
			--extract Results/1kg_hm3_qc_pruned.prune.in \
			--make-bed \
 			--out  Results/1kg_hm3_pruned
		
		
```
## Calculate PCA
```
./plink --bfile  Results/1kg_hm3_pruned \
		--pca 10 \
		--out  Results/1kg_pca

```



## Calculate IBS and relatedness
Calculate Identity By State matrix
```
./plink --bfile  Results/1kg_hm3_pruned \
		--keep Data/1kg_samples_EUR.txt \
		--distance --out Results/ibs_matrix
```
Calculate relatedness matrix

```
./plink --bfile Results/1kg_hm3_pruned /
		--keep Data/1kg_samples_EUR.txt /
		--make-rel --out Results/rel_matrix
```




## Association analyss

Linear additive model
```
./plink    	--bfile Data/1kg_EU_BMI \
        	--snps rs9674439 \
       	 	--assoc \
      	 	--linear \
      		--out Results/BMIrs9674439
```
Logistic additive model
```

./plink    	--bfile Data/1kg_EU_Overweight \
        	--snps rs9674439 \
       	 	--assoc \
      	 	--logistic \
      	 	--out Results/Overweight_rs9674439

```
Linear dominant analysis
```
./plink    	 --bfile Data/1kg_EU_BMI \
        	 --snps rs9674439 \
       	 	--assoc \
      	 	--linear dominant \
      	 	--out Results/BMIrs9674439
```		 
	

Genome-Wide Analysis		 
```		 
./plink    	--bfile Data/1kg_EU_BMI \
       	 	--assoc \
      	 	--linear \
      	 	--out Results/BMIgwas
```		 
