# BCB546_R_assignemnt_final

---
title: "R-assignment"
author: "Ahmed_Raslan"
date: "2023-03-12"
output: html_document
---

## Data Inspection

Download files

```{r}
library(tidyverse)

url <- "https://raw.githubusercontent.com/EEOB-BioData/BCB546_Spring2023/main/assignments/UNIX_Assignment/fang_et_al_genotypes.txt"
 download.file(url, destfile = "fang_et_al_genotypes.txt")

url <- "https://raw.githubusercontent.com/EEOB-BioData/BCB546_Spring2023/main/assignments/UNIX_Assignment/snp_position.txt"
 download.file(url, destfile = "snp_position.txt")
```

Reading files

```{r}
library(readr)
readable_fang_et_al_genotypes <- read_tsv("fang_et_al_genotypes.txt") 
write.csv(readable_fang_et_al_genotypes, "readable_fang_et_al_genotypes.csv", row.names = FALSE)
readable_snp_position <- read_tsv("snp_position.txt") 
write.csv(readable_snp_position, "readable_snp_positio.csv", row.names = FALSE)
```

Inspection

```{r}
View(readable_snp_position)
View(readable_fang_et_al_genotypes)
head(readable_fang_et_al_genotypes)
head(readable_snp_position)
tail (readable_snp_position)
tail (readable_fang_et_al_genotypes)
str(readable_fang_et_al_genotypes)
str(readable_snp_position)
summary(readable_fang_et_al_genotypes)
summary(readable_snp_position)
dim(readable_fang_et_al_genotypes)
dim(readable_snp_position)
names(readable_fang_et_al_genotypes)
names(readable_snp_position)
nrow(readable_fang_et_al_genotypes)
nrow(readable_snp_position)
ncol(readable_fang_et_al_genotypes)
ncol(readable_snp_position)
```

## Data processing

Extracting columns; SNP_ID, Chromosome and Position

```{r}
cut_snp_position <- select(readable_snp_position, SNP_ID, Chromosome, Position)
View(cut_SNP_position)
write.csv(cut_snp_position, "cut_snp_position.csv", row.names = FALSE)

```

## Maize

Extracting maize groups, transposing output file, then taking off the first 2 rows.

```{r}
library(stringr)
maize_fang <- readable_fang_et_al_genotypes %>% 
    filter(if_any(everything(), ~ str_detect(as.character(.), "Group|ZMMIL|ZMMLR|ZMMMR")))
     write.csv(maize_fang, "maize_fang.csv", row.names = FALSE)
transposed_maize_fang <- t(maize_fang)
write.csv(transposed_maize_fang, "transposed_maize_fang.csv", row.names = FALSE)
headsoff_transposed_maize_fang <- transposed_maize_fang[-c(1:2),]
write.csv(headsoff_transposed_maize_fang, "headsoff_transposed_maize_fang.csv", row.nam
```

Tidying " headsoff_transposed_maize_fang" so the first row becomes the columns names row.

Joining "cut_snp_position" and "headsoff_transposed_maize_fang"

Extracting "multiple" and "unknown" matches from the output file (joined_maize_snp).

```{r}
colnames(headsoff_transposed_maize_fang) <- headsoff_transposed_maize_fang[1, ]
headsoff_transposed_maize_fang <- headsoff_transposed_maize_fang[-1, ]





joined_maize_snp <- cbind(cut_snp_position, headsoff_transposed_maize_fang)
row.names(joined_maize_snp) <- NULL
write.csv(joined_maize_snp, "joined_maize_snp.csv", row.names = FALSE)


unknown_joined_maize_snp <- grepl("unknown", joined_maize_snp$Chromosome) | grepl("unknown", joined_maize_snp$Position)
unknown_joined_maize_snp <- joined_maize_snp[unknown_joined_maize_snp, ]
write.csv(unknown_joined_maize_snp, "unknown_joined_maize_snp.csv", row.names = FALSE)


multiple_joined_maize_snp    <- grepl("multiple", joined_maize_snp$Chromosome) | grepl("multiple", joined_maize_snp$Position)
multiple_joined_maize_snp <- joined_maize_snp[multiple_joined_maize_snp, ]
write.csv(multiple_joined_maize_snp, "multiple_joined_maize_snp.csv", row.names = FALSE)
```

Extracting each of the 10 chromosomes. repeating the next chunk for all 10 chromosomes

```{r}
maize1_snp    <- grepl("^1$", joined_maize_snp$Chromosome) 
maize1_snp <- joined_maize_snp[maize1_snp, ]
write.csv(maize1_snp, "maize1_snp.csv", row.names = FALSE)
```

Setting the position column as numeric, then arranging in decreasing order based on "Position". Repeat for all 10 chromosomes

```{r}
maize1_snp$Position <- as.numeric(maize1_snp$Position)
maize1_snp <- maize1_snp[order(desc(maize1_snp$Position)),]
write.csv(maize1_snp, "down_maize1_snp.csv", row.names = FALSE)

```

Replacing ?/? with -\-. Repeat for all 10 down chromosmes
```{r}
down_maize1_snp <- read.csv("down_maize1_snp.csv")
down_maize1_snp <- read.csv("down_maize2_snp.csv")
down_maize1_snp[down_maize1_snp == "?/?"] <- "-/-"
write.csv(down_maize2_snp, "edited-down_maize1_snps.csv", row.names = FALSE)
```
Arranging in increasing order based on "Position". Repeat for all 10 chromosomes

```{r}
maize1_snp <- maize1_snp[order(maize1_snp$Position),]
write.csv(maize1_snp, "up_maize1_snp.csv", row.names = FALSE)
```


## Teosinte

Running the same commands for teosinte data processing.

```{r}
teosinte_fang <- readable_fang_et_al_genotypes %>% 
     filter(if_any(everything(), ~ str_detect(as.character(.), "Group|ZMPBA|ZMPIL|ZMPJA")))
write.csv(teosinte_fang, "teosinte_fang.csv", row.names = FALSE)
transposed_teosinte_fang <- t(teosinte_fang)
write.csv(transposed_teosinte_fang, "transposed_teosinte_fang.csv", row.names = FALSE)
headsoff_transposed_teosinte_fang <- transposed_teosinte_fang[-c(1:2),]
write.csv(headsoff_transposed_teosinte_fang, "headsoff_transposed_teosinte_fang.csv", row.names = FALSE)
colnames(headsoff_transposed_teosinte_fang) <- headsoff_transposed_teosinte_fang[1, ]
headsoff_transposed_teosinte_fang <- headsoff_transposed_teosinte_fang[-1, ]
joined_teosinte_snp <- cbind(cut_snp_position, headsoff_transposed_teosinte_fang)
row.names(joined_maize_snp) <- NULL
write.csv(joined_teosinte_snp, "joined_teosinte_snp.csv", row.names = FALSE)
unknown_joined_teosinte_snp    <- grepl("unknown", joined_teosinte_snp$Chromosome) | grepl("unknown", joined_teosinte_snp$Position)
unknown_joined_teosinte_snp <- joined_teosinte_snp[unknown_joined_teosinte_snp, ]
write.csv(unknown_joined_teosinte_snp, "unknown_joined_teosinte_snp.csv", row.names = FALSE)
multiple_joined_teosinte_snp    <- grepl("multiple", joined_teosinte_snp$Chromosome) | grepl("multiple", joined_teosinte_snp$Position)
multiple_joined_teosinte_snp <- joined_teosinte_snp[multiple_joined_teosinte_snp, ]
write.csv(multiple_joined_teosinte_snp, "multiple_joined_teosinte_snp.csv", row.names = FALSE)
teosinte1_snp    <- grepl("^1$", joined_teosinte_snp$Chromosome) 
teosinte1_snp <- joined_teosinte_snp[teosinte1_snp, ]
write.csv(teosinte1_snp, "teosinte1_snp.csv", row.names = FALSE)
teosinte1_snp$Position <- as.numeric(teosinte1_snp$Position)
teosinte1_snp <- teosinte1_snp[order(desc(teosinte1_snp$Position)),]
write.csv(teosinte1_snp, "down_teosinte1_snp.csv", row.names = FALSE)
teosinte1_snp$Position <- as.numeric(teosinte1_snp$Position)
teosinte1_snp <- teosinte1_snp[order(teosinte1_snp$Position),]
write.csv(teosinte1_snp, "up_teosinte1_snp.csv", row.names = FALSE)
down_teosinte1_snp <- read.csv("down_teosinte1_snp.csv")
down_teosinte1_snp <- read.csv("down_teosinte1_snp.csv")
down_teosinte1_snp[down_teosinte1_snp == "?/?"] <- "-/-"
write.csv(down_teosinte1_snp, "edited-down_teosinte1_snps.csv", row.names = FALSE)
```

## Data visualization

**SNPS distribution on each chromosome**

Setting the position column as numeric and filtering "multiple", "unknown" and "NA" out of the "Chromosome" and "Position" columns

```{r}
cut_snp_position$Position <- as.numeric(cut_snp_position$Position)
cut_snp_data_filtered <- cut_snp_position %>%
     filter(Chromosome != "multiple", Chromosome != "unknown", Chromosome != "NA", Position != "unknown", Position != "multiple", Position != "NA" )
```

Creating a density plot of the SNP positions on Chromosome 1 and adds labels and a title to the plot using ggplot2. Repeat for all 10 chromosomes

```{r}
install.packages("gplots")
library(gplots)
ggplot(data = cut_snp_data_filtered[cut_snp_data_filtered$Chromosome == 1, ], aes(x = Position)) +
     geom_density() +
     labs(x = "SNP Position on Chromosome 1", y = "Density") +
     ggtitle("Distribution of SNPs on Chromosome 1")

ggplot(data = cut_snp_data_filtered[cut_snp_data_filtered$Chromosome == 2, ], aes(x = Position)) +
     geom_density() +
     labs(x = "SNP Position on Chromosome 2", y = "Density") +
     ggtitle("Distribution of SNPs on Chromosome 2")

ggplot(data = cut_snp_data_filtered[cut_snp_data_filtered$Chromosome == 3, ], aes(x = Position)) +
     geom_density() +
     labs(x = "SNP Position on Chromosome 3", y = "Density") +
     ggtitle("Distribution of SNPs on Chromosome 3")

ggplot(data = cut_snp_data_filtered[cut_snp_data_filtered$Chromosome == 4, ], aes(x = Position)) +
     geom_density() +
     labs(x = "SNP Position on Chromosome 4", y = "Density") +
     ggtitle("Distribution of SNPs on Chromosome 4")

ggplot(data = cut_snp_data_filtered[cut_snp_data_filtered$Chromosome == 5, ], aes(x = Position)) +
     geom_density() +
     labs(x = "SNP Position on Chromosome 5", y = "Density") +
     ggtitle("Distribution of SNPs on Chromosome 5")

ggplot(data = cut_snp_data_filtered[cut_snp_data_filtered$Chromosome == 6, ], aes(x = Position)) +
     geom_density() +
     labs(x = "SNP Position on Chromosome 6", y = "Density") +
     ggtitle("Distribution of SNPs on Chromosome 6")

ggplot(data = cut_snp_data_filtered[cut_snp_data_filtered$Chromosome == 7, ], aes(x = Position)) +
     geom_density() +
     labs(x = "SNP Position on Chromosome 7", y = "Density") +
     ggtitle("Distribution of SNPs on Chromosome 7")

ggplot(data = cut_snp_data_filtered[cut_snp_data_filtered$Chromosome == 8, ], aes(x = Position)) +
     geom_density() +
     labs(x = "SNP Position on Chromosome 8", y = "Density") +
     ggtitle("Distribution of SNPs on Chromosome 8")


ggplot(data = cut_snp_data_filtered[cut_snp_data_filtered$Chromosome == 9, ], aes(x = Position)) +
     geom_density() +
     labs(x = "SNP Position on Chromosome 9", y = "Density") +
     ggtitle("Distribution of SNPs on Chromosome 9")


ggplot(data = cut_snp_data_filtered[cut_snp_data_filtered$Chromosome == 10, ], aes(x = Position)) +
     geom_density() +
     labs(x = "SNP Position on Chromosome 10", y = "Density") +
     ggtitle("Distribution of SNPs on Chromosome 10")
```

**density distribution across the 10 chromosomes**

creating a facetted density plot using "cut_snp_data_filtered", which displays the density of SNP positions for each chromosome. The facets are arranged in a grid with five columns, and each facet is labeled with the corresponding chromosome.

```{r}
ggplot(data = cut_snp_data_filtered, aes(x = Position, fill = factor(Chromosome))) +
     geom_density(alpha = 0.5) +
     scale_fill_discrete(name = "Chromosome") +
     labs(x = "SNP Position", y = "Density") +
     facet_wrap(~Chromosome, ncol = 5)
```

**Missing data and amount of heterozygosity**

Reshaping "readable_fang_et_al_genotypes" by pivoting the columns from 4 to the last column of the data frame into two columns: "Sample" and "Genotype", and then converts the numbers in the "Sample" column from character to numeric.

```{r}
single_column_fang_et_al_genotypes <- readable_fang_et_al_genotypes %>%
    pivot_longer(cols = 4:length(readable_fang_et_al_genotypes), names_to = "Sample", values_to = "Genotype") %>%
    mutate(Sample = as.numeric(str_extract(Sample, "\\d+")))

write.csv(single_column_fang_et_al_genotypes, "single_column_fang_et_al_genotypes.csv", row.names = FALSE)
```

Sorting Genotype data into "homozygous" ,"heterozygous" and " missing" in a new column named "type"

```{r}
sorted_single_column_fang_et_al_genotypes <- single_column_fang_et_al_genotypes %>% 
    mutate(
        type = if_else(
            Genotype == "A/A" | Genotype == "C/C" | Genotype == "G/G" | Genotype == "T/T", 
            "homozygous", 
            if_else(Genotype == "?/?", "missing", "heterozygous")
        )
    )

write.csv(sorted_single_column_fang_et_al_genotypes, "sorted_single_column_fang_et_al_genotypes.csv", row.names = FALSE)
```

calculating summary statistics in "sorted_single_column_fang_et_al_genotypes", including the total number of sites, the number of homozygous, heterozygous, and missing sites, as well as the proportions of each site type for each sample.

```{r}
sample_summary_data <- sorted_single_column_fang_et_al_genotypes %>%
  group_by(Sample_ID) %>%
  summarize(
    total_sites = n(),
    homozygous_sites = sum(type == "homozygous"),
    heterozygous_sites = sum(type == "heterozygous"),
    missing_sites = sum(type == "missing"),
    prop_homozygous = homozygous_sites / total_sites,
    prop_heterozygous = heterozygous_sites / total_sites,
    prop_missing = missing_sites / total_sites
  )
write.csv(sample_summary_data, "sample_summary_data.csv", row.names = FALSE)
    
```

Creating a stacked bar plot using the summarized data frame "sample_summary_data", which displays the proportion of homozygous, heterozygous, and missing sites for each sample ID.

```{r}
ggplot(sample_summary_data, aes(x = Sample_ID)) +
    geom_bar(aes(y = prop_homozygous, fill = "Homozygous"), alpha = 0.8, stat = "identity") +
    geom_bar(aes(y = prop_heterozygous, fill = "Heterozygous"), alpha = 0.8, stat = "identity") +
    geom_bar(aes(y = prop_missing, fill = "Missing"), alpha = 0.8, stat = "identity") +
    scale_fill_manual(values = c("Homozygous" = "darkgreen", "Heterozygous" = "yellow", "Missing" = "gray")) +
    scale_y_continuous(labels = scales::percent_format(accuracy = 1)) +
    labs(x = "Sample ID", y = "Proportion", fill = "Site Type", 
         title = "Proportion of Homozygous, Heterozygous, and Missing Sites by Sample ID")
```

Calculating summary statistics in "sorted_single_column_fang_et_al_genotypes", including the total number of sites, the number of homozygous, heterozygous, and missing sites, as well as the proportions of each site type for each Group.

```{r}
Group_summary_data <- sorted_single_column_fang_et_al_genotypes %>%
     group_by(Group) %>%
     summarize(
         total_sites = n(),
         homozygous_sites = sum(type == "homozygous"),
         heterozygous_sites = sum(type == "heterozygous"),
         missing_sites = sum(type == "missing"),
         prop_homozygous = homozygous_sites / total_sites,
         prop_heterozygous = heterozygous_sites / total_sites,
         prop_missing = missing_sites / total_sites
     )
write.csv(Group_summary_data, "Group_summary_data.csv", row.names = FALSE)
```

Creating a stacked bar plot using the summarized data frame "Group_summary_data", which displays the proportion of homozygous, heterozygous, and missing sites for each Group.

```{r}
ggplot(Group_summary_data, aes(x = Group)) +
  geom_bar(aes(y = prop_homozygous, fill = "Homozygous"), alpha = 0.8, stat = "identity") +
  geom_bar(aes(y = prop_heterozygous, fill = "Heterozygous"), alpha = 0.8, stat = "identity") +
  geom_bar(aes(y = prop_missing, fill = "Missing"), alpha = 0.8, stat = "identity") +
  scale_fill_manual(values = c("Homozygous" = "darkgreen", "Heterozygous" = "yellow", "Missing" = "gray")) +
  scale_y_continuous(labels = scales::percent_format(accuracy = 1)) +
  labs(x = "Group", y = "Proportion", fill = "Site Type", 
       title = "Proportion of Homozygous, Heterozygous, and Missing Sites by Group")


```

### My own visualization

**Proportion of homozygous, heterozygous and missing data across all groups**

```{r}
ggplot(sorted_single_column_fang_et_al_genotypes, aes(x = Group, fill = type)) + 
    geom_bar(position = "fill") +
    labs(x = "Group", y = "Proportion", fill = "Genotype Type") +
    ggtitle("Proportion of Homozygous, Heterozygous, and Missing Data Across All Groups")
```
#BCB546-R-Exercise

