## Detailed steps for testing with real data


### :memo: Install SRA toolkit

- Download and install the [SRA Toolkit](https://ncbi.github.io/sra-tools/install_config.html)
- Configure the toolkit
```
~/sratoolkit/bin/vdb-config --interactive
```
- Test the toolkit configuration
```
~/sratoolkit/bin/fastq-dump -X 5 -Z SRR390728
```

### :open_file_folder: Download the public fastq files
```
cd GenEditID/
source venv/bin/activate
python python/geneditidtools/get_data.py --meta=data/sra_metadata/SRP198941_SLX15021.tsv
```

All the fastq files related to this project will be stored in the `PROJECTS/SRP198941` folder.

### :one: Start GenEditID WebApp, and setup a project for analysis

Upload the data and layout spreadsheet associated to this project from `data/sra_metadata/SRP198941_SLX15021_GEP00005.xlsx`.

### :two: Fetch and prepare fastq files

Move the fastq files downloaded earlier into the `PROJECTS/GEPID/fastq/` replacing GEPID with the identifier of this project.

```
cd GenEditID/PROJECTS/GEPID
mv ../SRP198941/*.fq.gz fastq/.
```

Combine paired-end reads by merging reads to generate `.fqjoin.gz` files for ampli_count analysis (`seqkit` needs to be installed).

```
cd GenEditID/PROJECTS/GEPID
~/GenEditID/scripts/run_mergereads.sh
```


### :three: Run `ampli_count` analysis

```
cd GenEditID/
source venv/bin/activate
cd PROJECTS/GEPID
geneditid_run_amplicount
```
Replace `GEPID` with the identifier of this project.

### :four: Visualise results

- View the output of the analysis in the output result file `GenEditID/PROJECTS/GEPID/amplicount.csv`

- Generate and visualise plots in the GenEditID WebApp [http://localhost:8080](http://localhost:8080). Click on the 'GEPID' of the project in the table of projects on the home page and navigate to the Reads, Variants and Scores tabs. Plot and results files are generated and stored in the project folder for convenience:
  - Read coverage: `GenEditID/PROJECTS/GEPID/geneditid_plots/coverage.html`
  - Variants impact frequency: `GenEditID/PROJECTS/GEPID/geneditid_plots/koscores.html`


### :house: Back to the main manual
[GenEditID Manual: detailed steps](manual.md)
