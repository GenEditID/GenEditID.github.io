# GenEditID detailed steps


This only needs to be done once. To run GenEditID, first download and install the dependencies:
- [Python3](https://www.python.org/downloads/)
- [fastq-join](https://github.com/brwnj/fastq-join)
- [seqkit](https://github.com/shenwei356/seqkit)
- [Git LFS](https://git-lfs.github.com/)
- **Note** - *These tools need to be on your path and executable.*
- Here are some platform specific instructions:
  - [Specific help for macOS](macos.md)
  - [Specific help for Windows 10](windows.md)

Now clone the GitHub repo and set up the GenEditID webapp.

- Clone the GitHub repo [GenEditID](https://github.com/GenEditID/GenEditID.git)
```
git clone https://github.com/GenEditID/GenEditID.git
cd GenEditID/
```
- Setup [Git LFS](https://git-lfs.github.com/) for supporting the usage of large files
```
git lfs install
```

- Setup GenEditID
```
./scripts/setup_geneditid.sh
```


## :one: Start GenEditID WebApp, and setup a project for analysis

- Start GenEditID WebApp
```
./scripts/start_webapp.sh
```

- Go to [http://localhost:8080](http://localhost:8080)

- Create a **new project** by filling the form on the home page of the WebApp and save it into the database by clicking on 'Create project' button.
  - Take note of the **GEPID** (e.g. GEP00001) associated with the new project created at the step above. GEPIDs (starting with GEP and ending in five digits) are unique identifiers of individual projects. They are listed in the **projects** table and will be referred to throughout the analysis.  
  - A `GEPID` folder has been created on your local disk in `GenEditID/PROJECTS/` to store data files associated with the project (e.g. fastq sequencing files and all output files from ampli_count and/or protein analysis). Replace `GEPID` with the identifier of the project (e.g. GEP00001) in all steps below.

- Click on the 'GEPID' of the project created in the table of projects below and go to the Setup tab

- To **upload the data and layout spreadsheet** associated to this project: upload the [Excel file template](https://github.com/GenEditID/GenEditID/raw/master/data/templates/GEPXXXXX.xlsx), add project data: targets, guides, amplicons and layout and save the file. Then click on 'Browse...' button, select the excel file created, then click on 'Upload' button. This is then loaded into the database and associated with the current project. All the tables in the Overview tab should now be filled with the data entered from the spreadsheet.


## :two: Fetch and prepare fastq files

- Save all fastq files related to your project into the `fastq` folder of the GEPID project folder. In the code, replace GEPID in `GenEditID/PROJECTS/GEPID` with the identifier of the project.

- Combine paired-end reads by merging or joining reads to generate `.fqjoin.gz` files for ampli_count analysis. File formats for paired-end reads should end in `*.s_1.r_1.fq.gz` and `*.s_1.r_2.fq.gz`.
  - Reads should be joined when target size is bigger than read length (`fastq-join` needs to be installed)
  ```
  cd GenEditID/PROJECTS/GEPID
  ~/GenEditID/scripts/run_joinreads.sh
  ```
  - or merged when target size is smaller than the read length (`seqkit` needs to be installed)
  ```
  cd GenEditID/PROJECTS/GEPID
  ~/GenEditID/scripts/run_mergereads.sh
  ```


## :three: Run `ampli_count` analysis

The project "data and layout" spreadsheet (step 1) has been loaded and the combined paired-end reads generated (step 2). Amplicon sequences uploaded are used to automatically generate an `GenEditID/PROJECTS/GEPID/amplicount_config.csv` file that enables downstream analysis. Note that this requires association with a reference genome that is in the `GenEditID/data/reference/` folder ([detailed information about the reference genome files](ref-genome.md)). This allows the `ampli_count` tool to be ran which will generate an `amplicount.csv` file. Sequences associated with each amplicon are counted and quality controlled to discard low frequency and low quality reads.


- Run [geneditid_run_amplicount](https://github.com/GenEditID/GenEditID/blob/master/python/geneditidtools/run_ampli_count.py) script on all fasta files from your project directory. Replace `GEPID` with the identifier of the project.
```
cd GenEditID/
source venv/bin/activate
cd PROJECTS/GEPID
geneditid_run_amplicount
```


## :four: Visualise results

- View the output of the analysis in the output result file `GenEditID/PROJECTS/GEPID/amplicount.csv`

- Generate and visualise plots in the GenEditID WebApp [http://localhost:8080](http://localhost:8080). Click on the 'GEPID' of the project in the table of projects on the home page and navigate to the Reads, Variants and Scores tabs. Plot and results files are generated and stored in the project folder for convenience:
  - Read coverage: `GenEditID/PROJECTS/GEPID/geneditid_plots/coverage.html`
  - Variants impact frequency: `GenEditID/PROJECTS/GEPID/geneditid_plots/koscores.html`
  - Scores on plate:
    - `GenEditID/PROJECTS/GEPID/geneditid_plots/heatmap.html`
    - `GenEditID/PROJECTS/GEPID/geneditid_plots/heatmap_protein_expression.html` (if data available)
    - `GenEditID/PROJECTS/GEPID/geneditid_plots/heatmap_combined_data.html` (if data available)

## :microscope: Trying with real data

[Detailed steps for testing with real data](testing.md)


## :speech_balloon: Ask a question

- :mag: Ensure your question has not already been answered, please [:book: read closed issues](https://github.com/GenEditID/GenEditID/issues?q=is%3Aissue+is%3Aclosed) first.

- [:speech_balloon: Ask a question](https://github.com/GenEditID/GenEditID/issues/new)
  - :memo: Use a succinct title and description.
  - :bug: Bugs & feature requests should be opened with the corresponding label when possible.

- :+1: Thanks for your interest in this project
