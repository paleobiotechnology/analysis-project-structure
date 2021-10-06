# Opinionated Bioinformatics Project Directory Structure

## Preamble

> ⚒️ CURRENTLY UNDER CONSTRUCTION ⚒️

To ensure reproducibility and readability within bioinformatics projects, we propose the following project repository structure.

This structure we hope will make it easier for other researchers to more easily follow how data was processed and analyses were performed.

This structure has been developed out of (meta)genomics projects, however it should be generic enough for other fields to adopt.

## General Organisation

The three main concepts for this structure is as follows:

1. Everything should be self-contained (as far as possible) within a single git repository
2. Minimise hard-disk footprint: link, do not copy
3. Every component should follow a logical flow, being easily be linked together, and followable by an outsider

For **points 1 and 2**, this means that all links should be relative to the repository itself. By doing, so, this prevents ballooning hard-disk space usage that occurs by repeatedly copying the same large files used in input for different analyses.

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

## Structure

### Overall

The overall structure is as in the tree structure below, with the main top-level folders listed in numerical order, alongside a main repository README and a couple of additional useful files.

Brief summary descriptions of the main folders are as follows:

- `01-documentation`: Contains initial metadata about samples and data files that are used for downstream analysis. This includes locations of comparative data.
- `02-scripts`: Contains all scripts and code notebooks used in the day-to-day analysis during the project. Can optionally include sub-directories for each language (e.g., R, python).
- `03-data`: Contains all the large raw, or common-starting point files for all downstream analyses. For (meta)genomics these are normally files such as BAM, SAM, FASTQ, FASTA etc.
- `04-analysis`: Contains all the output from software, tools, and notebooks of all analyses. This is the main 'working' directory of the project.
- `05-results`: Contains copies of all final output from all `04-analysis` (i.e. without intermediate files). These will be used for the bare-minimal reproducible results for reports and publication.
- `06-reports`: Contains presentations, summary notebooks of particular stages or packages of the project. Used for informing the final publication.
- `07-publication`: Contains main text, figures, supplementary files and data. Optionally can formatted with bookdown for pretty online rendering with direct links to intermediate files in `04-analysis`.

```bash
README.md                      ## Main description each directory
conda_environment.yml          ## Single or multiple files allowing recreation of software environment used for analysis.
.gitignore                     ## Allows for efficient exclusion of files that should NOT be uploaded to GitHub (e.g., large sequencing files for genomics)
01-documentation/              ## initial metadata information, created outside of bioinformatics, public data lists (i.e. not created by the project), SIs of other publications (converted to tabular format)
├──document_1.txt
└──document_2.tsv
02-scripts                     ## Central script/notebook repository, with optional sub-directories for each language e.g., python, R, bash
├──ANA-script.sh
├──ANA-notebook.Rmd
├──QUAL-script.sh
└──QUAL-notebook.Rmd
03-data/                       ## Raw_data and final processed data used for everything in 04-analysis, reference genomes, databases - large files you don't upload to github.
├──raw_data
├──published_data
├──reference_genomes
└──databases/
    └──<database_1>/
04-analysis/                   ## symlinks OR just relative paths, doesn't go to github either(!)
├──analysis_1/                 ## But all final results files, results figures, for downstream analyses or publication, COPIED into results
│  ├──sub-step
│  └──sub-step
└──analysis_2/
   ├──sub-step
   └──sub-step
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
   ├──final_rmarkdown.Rmd
   └──final_rmarkdown.html
07-publication/                ## Optional, could be written in bookdown for nice rendering
├──figures
├──supplementary_figures/
├──supplementary_files/
├──sequencingdata_upload/
└──final_paper.Rmd
```

An important component of this structure to note is within  scripts, results, and reports, that files are prefixed with short three or four letter codes in all capitals (e.g. QUAL, ANA). This indicates batches of files that are all related to a single analysis component or question that is addressed in the project. These prefixes can also be replaced with specific directories instead. The purpose of this structure is to make it easier for readers to rapidly find scripts of interest, and possible scripts of interest. This system is preferable over, e.g., numbering as it allows more flexibility in case analysis components change in order in the final publication, or morph and merge.

### Repository main README

This markdown (`.md`) file acts as the main description of the repository as a whole.

This should be the main port-of-call for anyone who want to explore, replicate, or try and find any analysis or results file. Generally you can describe the contents of each top-level directory, point to the original publication and other related metadata.

### Repository .gitignore

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

It is critical, however, that all files can be reconstructed. This comes through having highly descriptive, easy-to-follow, and reproducible notebooks and scripts.

Furthermore, we recommend providing sufficient information or automated scripts available at publication that allows a user to download the raw data uploaded to public repositories (such as the ENA or SRA for (meta)genomics) and replicates the repository structure of ignored directories that was on the original machine used for analysis.

### .conda_environment.yml

This is a file that utilises the [`conda`](https://docs.conda.io/en/latest/) (mostly) portable packaging system. You use this, or multiple files, to define all software, and specific versions of said software, used in the project or analysis packages.

> 💡 This can alternatively be replaced with a [Docker](https://docker.com) description file, for even more robust reproducibility.

This allows visitors of the repository who wish to recreate steps or all your analysis but using the same software configuration. Generally use of the [`bioconda`](https://bioconda.github.io) software channel is recommended for defining software and versions, as this is designed specifically for bioinformatics, and will likely contain the majority of tools will need for projects particularly in (meta)genomics.

After installation of conda, the environment can be created and activated as follows

```bash
conda env create -f conda_environment.yml
conda activate <NAME_OF_PROJECT>
```

Note that you may need to update this overtime, as you add more analyses. If this happens often, it is recommended to make multiple conda environmental files for different analyses components.

> ⚠️ In large projects with many software, you may end up getting dependency conflicts, where two software require two different versions of the same software. In such a case we recommend splitting up the conda environment into multiple for each component.

## 01-documentation

This directory contains primarily files that you need before you can do any analyses. This often includes files that contain information of wet lab processing of new samples, or metadata files of publicly available datasets used as comparative datasets.

In addition, we recommend on final publication of the project, placing in this directory a final table that contains the location(s) of all novel data generated in this project on public archives. For sequencing data this will normally be the table that can be exported from the ENA or SRA that has the FTP or Aspera URLs of the uploaded FASTQ files.

Most of these files will be simple text files, in tabular format such as CSV, TSV, XLSX, or TXT files.

## 02-scripts

This contains all scripts and notebooks used in the 'day-to-day' analysis of the project. All of these scripts/notebooks produce both intermediate and final files used in the analysis of the project.

These can be things such as simple shell/bash scripts (`.sh`), python (`.py`), or R (`.R`) scripts, or notebooks such as RMarkdown/Notebooks (`.Rmd`) or Jupyter notebooks (`.jyp`). It is optional how this is structured internally, whether by analysis component prefixes, directories per analysis component, or by programming language.

These scripts and notebooks should only use relative links to refer to input and output files present in the directory, and not to point to anything else present on your given machine or infrastructure.

File names should be descriptive so readers can find relevant files. Abbreviations or acronyms are not recommended, as these are often difficult to understand what they mean for people not intimately involved in the project.

## 03-data
