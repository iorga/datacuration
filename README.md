# AMRrulesR

AMRFinderPlus (3.12.8, DB 2024-01-31.1) output for all samples in [AllTheBacteria](https://github.com/AllTheBacteria/AllTheBacteria/tree/main/reproducibility/All-samples/AMR/AMRFinderPlus) can be downloaded from here: [https://osf.io/zgexh](https://osf.io/zgexh) (0.5GB .tsv.gz file, expands to 6.2GB)

This has >26 M lines, one per genome-gene hit, but as it's the raw AMRfinderplus output it doesn't have any column to indicate the species for each row

Species calls were made separately for AllTheBacteria using sylph, and can be downloaded from [here](https://ftp.ebi.ac.uk/pub/databases/AllTheBacteria/Releases/0.2/metadata/species_calls.tsv.gz) (13.4 MB .tsv.gz file)

The script `ATB.Rmd` shows how we used R to pull out AMRfinderplus results for genomes belonging to a particular species, like this:

```
# get list of E. coli samples
species_calls <- read_tsv("species_calls.tsv.gz")
ecoli <- species_calls %>% filter(Species=="Escherichia coli") %>% pull(Sample)

# read in only those lines matching these samples
f <- function(x, pos) subset(x, Name %in% ecoli)
ecoli_AFP <- read_tsv_chunked("AMRFP_results.tsv.gz", DataFrameCallback$new(f), chunk_size = 10000)

# select key columns to keep output file size small-ish
ecoli_AFP %>% select(Name, `Gene symbol`, `Hierarchy node`, Class, Subclass, `% Coverage of reference sequence`, `% Identity to reference sequence`) %>%
  write_csv(file="ATB_Ecoli_AFP.csv")
```

This repo contains the processed AMRfinderplus output files for 
_Escherichia coli_: `ATB_Ecoli_AFP.csv.gz` - 9,179,705 lines for 314,978 unique genomes
_Salmonella enterica_: `ATB_Salmonella_AFP.csv.gz` - 6,133,567 lines for 534,667 unique genomes
_Pesudomonas aeruginosa_: `ATB_Pseudomonas_aeruginosa_AFP.csv.gz` - 389,574 lines for 25,057 unique genomes
