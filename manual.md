# GenEditID detailed steps


## Step 1: Install dependencies and setup GenEditID

To run GenEditID, first download and install the necessary dependencies:

- [Python3](https://www.python.org/downloads/)
- [fastq-join](https://github.com/brwnj/fastq-join)
- [seqkit](https://github.com/shenwei356/seqkit)
- [Download Reference Genome (release 95): Homo_sapiens.GRCh38.dna.toplevel.fa.gz](ftp://ftp.ensembl.org/pub/release-95/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.toplevel.fa.gz)

NB. These tools need to be on your path to be executable.

- Clone the GitHub repo [GenEditID](https://github.com/GenEditID/GenEditID.git)
```
git clone https://github.com/GenEditID/GenEditID.git
cd GenEditID/
```

- Setup GenEditID
```
./shell/setup_geneditid.sh
```

## Step 2: Start GenEditID WebApp, and setup a project for analysis

- Start GenEditID WebApp
```
./shell/start_webapp.sh
```

- Go to [http://localhost:8080](http://localhost:8080)

- Create a **new project** by filling the form on the home page of the WebApp and save it into the database by clicking on 'Create project' button.

- Then click on 'edit' in the table of projects below, to **upload data and experiment layout** associated to this project: load project data (targets, guides and amplicons), its experiment layout, list of plates and sequencing library details from an Excel file using this ([template](https://github.com/GenEditID/GenEditID/raw/master/data/templates/GEPXXXXX.xlsx)). Browse your computer and select the excel file corresponding to this project, then click on 'UploadData' button. This is then loaded into the database and associated with the indicated project.


- Create a GEPID project folder on your local disk to store data files associated with the project (e.g. fastq sequencing files and all output files from ampli_count and/or protein analysis).
```
cd /path/to/my/data/
mkdir GEPID
```

## Step 3: Fetch fastq files

- Get all fastq files related to your project into the GEPID project folder into a `fastq` folder

- Combine paired-end reads by merging or joining reads to generate `.fqjoin.gz` files for ampli_count analysis:
  - Reads should be joined when target size is bigger than read length (`fastq-join` needs to be installed)
  ```
  cd /path/to/my/data/GEPID/
  ~/GenEditID/shell/ngs/job_joinreads.sh
  ```
  - or merged when target size is smaller than the read length (`seqkit` needs to be installed)
  ```
  cd /path/to/my/data/GEPID/
  ~/GenEditID/shell/ngs/job_mergereads.sh
  ```

## Step 4: Run `ampli_count`

The "data and experiment layout submission" form (step 2) and combined sequences (step 4) are integrated and amplicon sequences are fetched to generate a `amplicount_config.csv` file that enables downstream analysis. Note that this requires association with a reference genome that should be loaded locally. This allows the `ampli_count` tool to be run to generate an `amplicount.csv` file, which is loaded back to the database. Sequences associated with each amplicon are counted and quality controlled to discard low frequency and low quality reads.

- Extract amplicons and targets coordinates from the database to produce the config file to run `ampli_count`:
  NB. check your disk space, before unziping the reference genome `gunzip Homo_sapiens.GRCh38.dna.toplevel.fa.gz`
  ```
  cd /path/to/my/data/GEPID/
  source ~/GenEditID/venv/bin/activate
  geneditid_create_amplicount_config --project=GEPID --genome=Homo_sapiens.GRCh38.dna.toplevel.fa
  ```

- Run [geneditid_run_amplicount](https://github.com/GenEditID/GenEditID/blob/master/python/scripts/run_ampli_count.py) script on all fasta files from your project directory
  ```
  cd /path/to/my/data/GEPID/
  source ~/GenEditID/venv/bin/activate
  geneditid_run_amplicount --fastqdir=fastq/
  ```

- Check results in `amplicount.csv`


## Step 5: Identify variants and plot results

- Run [geneditid_run_variantid](https://github.com/GenEditID/GenEditID/blob/master/python/scripts/run_variant_id.py) script from your project directory:
  ```
  cd /path/to/my/data/GEPID/
  source ~/GenEditID/venv/bin/activate
  geneditid_run_variantid
  ```

- Check results in `editid_variantid/variantid.csv` and `editid_variantid/impacts.csv` and plots
  - `editid_variantid/coverage_[amplicon_id].html`
  - `editid_variantid/koscores_[amplicon_id].html`


## Step 6: Plot koscore heatmap on plates

- Retrieve sample location on plates from the database and add them onto the necessary files
```
cd /path/to/my/data/GEPID/
source ~/GenEditID/venv/bin/activate
geneditid_add_sample_location GEPID
```

- Plot heatmap on plates
```
cd /path/to/my/data/GEPID/
source ~/GenEditID/venv/bin/activate
geneditid_plot_scores
```

- Visualise plots
  - `editid_variantid/heatmap_[amplicon_id].html`
  - `editid_variantid/heatmap_protein_expression.html` (if data available)
  - `editid_variantid/heatmap_combined_data.html` (if data available)
