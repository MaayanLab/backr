# backr
This is an R package to access the Enrichr API. The package supports listing gene sets, enrichment analysis of selected gene set library with and without background correction. The R package links to a derivation of the enrichr API.

## Installation

In the R console enter the following lines to install from GitHub.
```R
library("devtools")
install_github("MaayanLab/backr/backr")
```

The current server URL is pointing to a lcoal host test URL. The package has to be instructed to point to the correct API endpoint using the `backr::set_server()` function. Alternatively the URL can be changed in the `R/backr.R` file of the R package. For this modify the `pkg.env$server_url` variable.

## Configuration

The R package needs information where to direct requests to. Before queries can be run, the server needs to be specified. This only needs to be run once and is stored as an environmental variable of the package. The default is the EnrichrAPI, same as the Enrichr front end.

```R
library("backr")
backr::set_server("https://maayanlab.cloud/speedrichr")
```

## Examples

### List supported gene set libraries

```R
library("backr")

libraries <- backr::list_libraries()
```

### Enrichment analysis without background correction

Standard enrichment analysis. The function takes in a library as shown to be available under `backr::list_libraries()` and an array of gene identifiers (gene symbols). The function returns all gene sets with at least 1 gene overlap to the query gene set. The result dataframe contains 7 columns (rank, term, pval, fdr, odds, escore, overlap).

```R

library("backr")

library = "GO_Biological_Process_2021"
genes = c('PTPN18','EGF','HSP90AA1','GAB1','NRG1','MATK','PTPN12','NRG2','PTK6','PRKCA','ERBIN','EREG','BTC','NRG4','PIK3R1','PIK3CA','CDC37','GRB2','STUB1','HBEGF','GRB7')

result <- backr::enrich(library, genes)

```

### Enrichment analysis with background correction

The function takes in a library as shown to be available under `backr::list_libraries()`, an array of gene identifiers (gene symbols) and a background gene list. The background gene list will be used to filter the input gene set and all gene sets in the selected gene set library. The function returns all gene sets with at least 1 gene overlap to the query gene set. The result dataframe contains 7 columns (rank, term, pval, fdr, odds, escore, overlap).

```R

library("backr")
backr::set_server("https://maayanlab.cloud/speedrichr")

# download GMT file from Enrichr database
url = "https://maayanlab.cloud/Enrichr/geneSetLibrary?mode=text&libraryName=KEGG_2021_Human"
download.file(url, "KEGG_2021_Human.gmt")
gmt <- backr::read_gmt("KEGG_2021_Human.gmt")

# push GMT file to API
res <- backr::import_gmt("KEGG_2021_Human", gmt)

# Use genes in KEGG gmt as background
background = unique(unlist(gmt))

# enrich gene set
genes = c('TGFB1', 'EOMES', 'TGFB2', 'DDX17', 'NOG', 'FOXF2', 'WNT5A', 'HGF', 'HMGA2', 'HNRNPAB', 'PTPN18','EGF','HSP90AA1','GAB1','NRG1','MATK','PTPN12','NRG2','PTK6','PRKCA','ERBIN','EREG','BTC','NRG4','PIK3R1','PIK3CA','CDC37','GRB2','STUB1','HBEGF','GRB7')
result <- backr::enrich("KEGG_2021_Human", genes, background)

```
