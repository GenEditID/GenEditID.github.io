# GenEditID Manual: detailed steps


## :memo: Installation steps
This only needs to be done once.
To run GenEditID, first download and install these dependencies using [bioconda](https://bioconda.github.io/user/install.html). Here are some platform specific instructions [for MacOS](macos.md) and [for Windows 10](windows.md).

1. Install miniconda3
  - on MacOS run
  ```
  curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh
  sh Miniconda3-latest-MacOSX-x86_64.sh
  ```
  - on Linux, run:
  ```
  curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
  sh Miniconda3-latest-Linux-x86_64.sh
  ```

  **==> For changes to take effect, close and re-open your current shell. <==**

1. Set up the channels, **it is important to add them in this order**, so that the priority is set correctly (that is, conda-forge is highest priority).

  ```
  conda config --add channels defaults
  conda config --add channels bioconda
  conda config --add channels conda-forge
  ```

1. Install our dependencies: [fastq-join](https://github.com/brwnj/fastq-join), [seqkit](https://github.com/shenwei356/seqkit) and [Git LFS](https://git-lfs.github.com/) into a specific conda environment called `geneditid`.

  ```
  conda create -n geneditid python=3.8 fastq-join seqkit git-lfs
  ```
  To activate this environment, use `conda activate geneditid`.

  To deactivate an active environment, use `conda deactivate`.

  To see a list of all of your environments, run `conda env list`.

  To remove this environment, run `conda remove -n geneditid --all`

1. Setup [Git LFS](https://git-lfs.github.com/) for supporting the usage of large files, clone the GitHub repo [GenEditID](https://github.com/GenEditID/GenEditID.git), setup GenEditID
  ```
  cd ~
  conda activate geneditid
  git lfs install
  git clone https://github.com/GenEditID/GenEditID.git
  cd GenEditID/
  ./scripts/setup_geneditid.sh
  ```

  :warning: Due to large reference genomes files within the repository, the cloning step takes few minutes. Please wait till it completes before moving forward, thanks for your patience.

The active environment, **the one you are currently using**, is shown in parentheses () or brackets [] at the beginning of your command prompt.

```
(geneditid) $
```
if not, activate it using `conda activate geneditid` before running the next steps.

## :one: Start GenEditID WebApp, and setup a project for analysis

- Start GenEditID WebApp
```
cd ~/GenEditID/
./scripts/start_webapp.sh
```

- Go to [http://localhost:8080](http://localhost:8080)

- Create a **new project** by filling the form on the home page of the WebApp and save it into the database by clicking on 'Create project' button.
  - Take note of the **GEPID** (e.g. GEP00001) associated with the new project created at the step above. GEPIDs (starting with GEP and ending in five digits) are unique identifiers of individual projects. They are listed in the **projects** table and will be referred to throughout the analysis.  
  - A `GEPID` folder has been created on your local disk in `GenEditID/PROJECTS/` to store data files associated with the project (e.g. fastq sequencing files and all output files from ampli_count and/or protein analysis). Replace `GEPID` with the identifier of the project (e.g. GEP00001) in all steps below.

- Click on the 'GEPID' of the project created in the table of projects below and go to the Setup tab

- To **upload the submission spreadsheet** associated to this project: upload the [Excel file template](https://github.com/GenEditID/GenEditID/raw/master/data/templates/GEPXXXXX.xlsx), add project data: targets, guides, amplicons and layout and save the file. Then click on 'Browse...' button, select the excel file created, then click on 'Upload' button. This is then loaded into the database and associated with the current project. All the tables in the Overview tab should now be filled with the data entered from the spreadsheet.


## :two: Fetch and prepare fastq files

- Save all fastq files related to your project into the `fastq` folder of the GEPID project folder. In the code, replace GEPID in `GenEditID/PROJECTS/GEPID` with the identifier of the project.

- Combine paired-end reads by merging or joining reads to generate `.fqjoin.gz` files for ampli_count analysis. File formats for paired-end reads should end in `*.s_1.r_1.fq.gz` and `*.s_1.r_2.fq.gz`.
  - Reads should be joined when target size is bigger than read length (`fastq-join` needs to be installed)
  ```
  cd ~/GenEditID/PROJECTS/GEPID
  ~/GenEditID/scripts/run_joinreads.sh
  ```
  - or merged when target size is smaller than the read length (`seqkit` needs to be installed)
  ```
  cd ~/GenEditID/PROJECTS/GEPID
  ~/GenEditID/scripts/run_mergereads.sh
  ```


## :three: Run `amplicount` analysis

The project submission spreadsheet (step 1) has been loaded and the combined paired-end reads generated (step 2). Amplicon sequences uploaded are used to automatically generate an `GenEditID/PROJECTS/GEPID/amplicount_config.csv` file that enables downstream analysis, as well as `GenEditID/PROJECTS/GEPID/amplicount_config_tsearch.csv` if targeted search submitted. Note that this requires association with a reference genome that is in the `GenEditID/data/reference/` folder ([detailed information about the reference genome files](ref-genome.md)). This allows the `amplicount` tool to be ran which will generate an `amplicount.csv` file. Sequences associated with each amplicon are counted and quality controlled to discard low frequency and low quality reads.


- Run [geneditid_run_amplicount](https://github.com/GenEditID/GenEditID/blob/master/python/geneditidtools/run_ampli_count.py) script on all fasta files from your project directory. Replace `GEPID` with the identifier of the project.
```
cd ~/GenEditID/
source venv/bin/activate
cd PROJECTS/GEPID
geneditid_run_amplicount
```
Turn off sleep mode on your computer for analysis to run smoothly.


## :four: Visualise results

- View the output of the analysis in the output result file `GenEditID/PROJECTS/GEPID/amplicount.csv` and `GenEditID/PROJECTS/GEPID/amplicount_tsearch.csv` (if targeted search submitted)

- Generate and visualise plots in the GenEditID WebApp [http://localhost:8080](http://localhost:8080). Click on the 'GEPID' of the project in the table of projects on the home page and navigate to the Reads, Variants and Scores tabs. Plot and results files are stored in the project folder for convenience after being visualised in the WebApp, triggering their generation on disk:
  - Read coverage: `GenEditID/PROJECTS/GEPID/geneditid_plots/coverage.html`
  - Variants impact frequency: `GenEditID/PROJECTS/GEPID/geneditid_plots/impacts.html`
  - Scores on plate: `GenEditID/PROJECTS/GEPID/geneditid_plots/koscores.html`
  - Targeted search: `GenEditID/PROJECTS/GEPID/geneditid_plots/targeted_search.html` (if targeted search submitted)


## :microscope: Trying with real data

[Detailed steps for testing with real data](testing.md)


## :speech_balloon: Ask a question

- :mag: Ensure your question has not already been answered, please [:book: read closed issues](https://github.com/GenEditID/GenEditID/issues?q=is%3Aissue+is%3Aclosed) first.

- [:speech_balloon: Ask a question](https://github.com/GenEditID/GenEditID/issues/new)
  - :memo: Use a succinct title and description.
  - :bug: Bugs & feature requests should be opened with the corresponding label when possible.

- :+1: Thanks for your interest in this project
