# Lab week 8. Working with summary statistics. Part I

## Manhattan Plots and QQplots 
##  package manhattanly

This first part of the lab will use Rstudio. 

Use R to download the summary statistics
```
download.file("http://ssgac.org/documents/EduYears_Main.txt.gz", dest="EA2_results.txt.gz")
```


 Import the summary statistics in R
```
EAgwasResults<-read.table("EA2_results.txt.gz", header=T)

head(EAgwasResults)
dim(EAgwasRsults)
```

This is a very large file, we can spped up things by selecting ony SNPS with Pvalue <0.005
```
EAgwasResults_sub<-subset(EAgwasResults, Pval<0.0005)
dim(EAgwasResults_sub)

```



We can use a library created to plot manhattan plots
Load the manhattanly library
```
install.packages("manhattanly")
library(manhattanly)
```

Create Manhattan plot 
```
help(manhattanly)
manhattanly(EAgwasResults_sub, snp = "MarkerName" , bp="POS", p="Pval", chr="CHR")
```



We can use the same library to plot QQplots
```
qqly(EAgwasResults_sub, snp = "MarkerName" , bp="POS", p="Pval", chr="CHR")

```
The previous command is wrong as it truncates the distribution of p-value. If you need to limit the number of points, it is better to random select the points. 
```
qqly(EAgwasResults[sample(dim(EAgwasResults)[1],100000),], snp = "MarkerName" , bp="POS", p="Pval", chr="CHR")

```

