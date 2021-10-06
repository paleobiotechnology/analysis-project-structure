# Opinionated Bioinformatics Project Directory Structure

## Preamble

> ⚒️ CURRENTLY UNDER CONSTRUCTION ⚒️

To ensure reproducibility and readability within bioinformatics projects, we propose the following project repository structure. 

This structure we hope will make it easier for other researchers to more easily follow how data was processed and analyses were performed.

This structure has been developed out of (meta)genomics projects, however it should be generic enough for other fields to adopt.

## General Organisation

The three main concepts for this structure is as follows:

1. Everything should be self-contained (as far as possible) within a single git repository
2. Minimise harddisk footprint: link, do not copy
3. Every component should follow a logical flow, being easily be linked together, and followable by an outsider

For **points 1 and 2**, this means that all links should be relative to the repository itself. By doing, so, this prevents ballooning harddisk space usage that occurs by repeatedly copying the same large files used in input for different analyses.

Scripts, notebooks, and similar should utilise relative paths where the repository folder is the top-level part of the directory tree, and refer to files or directories that traverse between different internal folders (such as from raw data to analysis, or between analyses folders) should be with shortcuts such as `../../`. 

> :open_book: Example: an analysis command for analysis B, which uses output from analysis A as input, should be written as follows: 
>
>```bash
>tool_b --input ../analysis_A/output_A.txt --output output_b
>```

Alternatively, symbolic links (symlinks) can also be alternatively utilised, e.g. generated with `ln -s`, to refer to files within a given directory, even if stored in a different one. Symlinks should still be made relative within the repository.

> :open_book: Example:
> 
>```bash
>mkdir analysis_b/ && cd analysis_b/
>mkdir input_b/ output_b/
>cd input_b/
>ln -s ../../analysis_a/output/*.txt .
>cd ..
>tool_b --input input_b/*.txt --output output_b
>```

Likewise, the use of a single conda environment or container (docker, singularity, etc.), is recommended. This means that all software versions are also exactly the same as used by researchers aiming to reproduce the analysis.

For **point 3**, this is actualised here by following a semi-strict directory structure that follows the major steps of a typical academic project of approximately raw data → processing and cleaning → analysis → publication, and heavy use of documentation via README files and reproducible notebooks (e.g., RNotebook, jupyter notebooks etc.). 

Ultimately, write and structure everything with the vision that you are another researcher (or yourself!) trying to re-run your analyses in the future


# Structure



## Overall

<!-- usage of READMES in each major folder -->

```bash
README.md                      ## 
01-documentation/              ## initial metadata information, created outside of bioinformatics, public data lists (i.e. not created by the project), SIs of other publications (converted to tabular format)
├──document_1.txt
└──document_2.tsv
02-scripts                     ## sub-directories for each language e.g., python, R, bash
├──ANA-script.sh               ## 
├──ANA-notebook.Rmd
├──QUAL-script.sh              ## 
└──QUAL-notebook.Rmd
03-data/                       ##  raw_data and final processed data used for everything in 04-analysis, reference genomes, databses - large files you don't upload to github
├──raw_data                    ## 
├──published_data
├──reference_genomes
└──databases/                  ## 
    └──<database_1>/
04-analysis/                   ## symlinks OR just relative paths, doesn't go to github either(!)
├──analysis_1/                 ## But all final results files, results figures, for downstream analyses or publication, COPIED into results
│  ├──substep                  ## 
│  └──substep                  ##  
└──analysis_2/      
   ├──substep
   └──substep
05-results/                    ## all FINAL results files, results figures, for downstream analyses or publication, COPIED into results
├──ANA-final_file.tsv 
├──ANA-final_file.Rdata
├──QUAL-tool_output.csv
└──QUAL-tool_output.Rdata
06-reports/                    ## Intermediate presentations (e.g., in group meetings), or general preliminary results summaries used for structuring paper
├──ANA/
│  ├──final_rmarkdown_figures/
│  ├──final_rmarkdown.Rmd      ## Note: ANA/QUAL codes are for general analysis sections for fast look up, make sure describe in top README 
│  └──final_rmarkdown.html
└──QUAL/
   ├──final_rmarkdown_figures/
   ├──final_rmarkdown.Rmd      ## 
   └──final_rmarkdown.html
07-publication/                ## bkdown
├──figures
├──supplementary_figures/
├──supplementary_files/
├──sequencingdata_upload/
└──final_paper.Rmd
conda_environment.yml
.gitignore
```
## Repository main README

This markdown (`.md`) file acts as the main description of the repository as a whole.

This should be the main port-of-call for anyone who want to explore, replicate, or try and find 

## Repository .gitignore

This top-level repository file tells Git which files or folders should not be uploaded to GitHub. This is particularly important not just for data-sensitive files (login tokens, etc.), but also for ensuring very large files are not uploaded. GitHub has a strict limit of a single file can be no more than 100MB.

In particular, the directories that should be added to the .gitignore`:

- `03-data`
- `04-analysis`

For safety, we also recommend adding the following file formats to `.gitignore`, when working on (meta)genomics projects.

- `*.bam`
- `*.sam.gz`
- `*.sam`
- `*.fastq.gz`
- `*.fq.gz`
- `*.fastq`
- `*.fq`
- `*.fasta.gz`
- `*.fna.gz`
- `*.fa.gz`
- `*.fasta`
- `*.fna`
- `*fa`

It is criticial, however, that all files can be reconstructed. This comes through having highly descriptive, easy-to-follow, and reproducible notebooks and scripts. 

Furthermore, we recommend providing sufficient information or automated scripts available at publication that allows a user to download the raw data uploaded to public repositories (such as the ENA or SRA for (meta)genomics) and replicates the repository structure of ignored directories that was on the original machine used for analysis.
