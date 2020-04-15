# GenEditID detailed steps


## Step 1: Install dependencies and setup GenEditID

To run GenEditID, first download and install the necessary dependencies:

- [Python3](https://www.python.org/downloads/)
- [fastq-join](https://github.com/brwnj/fastq-join)
- [seqkit](https://github.com/shenwei356/seqkit)

NB. These tools need to be on your path to be executable. Due to the improved gatekeeper on macOS Catalina the system settings might have to be changed to allow program installation. If installation is blocked follow these steps:

- In terminal type the following command syntax
```
sudo spctl --master-disable
```

- Authenticate with your admin password.
- Go into your System preferences and choose 'Security & Privacy'. Tap the lock at the bottom left of the screen.
- Enter your password to unlock Security and Privacy.
- Choose 'Anywhere' under 'Allow apps downloaded from'.
- Click again on the lock to keep the changes.

Now you can clone the GitHub repo and set up the GenEditID webapp.

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

- Take note of the **GEPID** (e.g. GEP00001) associated with the new project created at the step above. GEPIDs (starting with GEP and ending in five digits) are unique identifiers of individual projects. They are listed in the **projects** table and will be referred to throughout the analysis.  

- Then click on 'edit' in the table of projects below, to **upload data and experiment layout** associated to this project: load project data (targets, guides and amplicons), its experiment layout, list of plates and sequencing library details from an Excel file using this ([template](https://github.com/GenEditID/GenEditID/raw/master/data/templates/GEPXXXXX.xlsx)). Browse your computer and select the excel file corresponding to this project, then click on 'UploadData' button. This is then loaded into the database and associated with the indicated project.

- Create a GEPID folder on your local disk to store data files associated with the project (e.g. fastq sequencing files and all output files from ampli_count and/or protein analysis). Replace `/path/to/my/data` below with the path to the directory you want to create the GEPID folder in, and replace `GEPID` with the identifier of the project (e.g. GEP00001).
```
cd /path/to/my/data/
mkdir GEPID
```

## Step 3: Fetch fastq files

- Get all fastq files related to your project into the GEPID project folder containing a `fastq` folder. In the code, replace `/path/to/my/data/GEPID` with the path to the directory where the GEPID folder is located.

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

All the following steps should be ran in the **GEPID project folder**:
```
cd /path/to/my/data/GEPID/
source ~/GenEditID/venv/bin/activate
```

## Step 4: Run `ampli_count`

The "data and experiment layout submission" form (step 2) and combined sequences (step 3) are integrated and amplicon sequences are fetched to generate a `amplicount_config.csv` file that enables downstream analysis. Note that this requires association with a reference genome that is in `data/reference/` folder. This allows the `ampli_count` tool to be ran to generate an `amplicount.csv` file, which is loaded back to the database. Sequences associated with each amplicon are counted and quality controlled to discard low frequency and low quality reads.

- The Reference Genome (release 95): [Homo_sapiens.GRCh38.dna.toplevel.fa.gz](ftp://ftp.ensembl.org/pub/release-95/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.toplevel.fa.gz) is stored in `GenEditID/data/reference/` and has been compressed using `bgzip`. If you wish to work with another reference, please download, uncompress, and re-compress it with bgzip utility from samtools [htslib-1.10.2](https://github.com/samtools/htslib/releases/download/1.10.2/htslib-1.10.2.tar.bz2)
```
gunzip Homo_sapiens.GRCh38.dna.toplevel.fa.gz
bgzip < Homo_sapiens.GRCh38.dna.toplevel.fa > Homo_sapiens.GRCh38.dna.toplevel.fa.gz
```

- Extract amplicons and targets coordinates from the database to produce the config file to run `ampli_count`. In the code, replace `GEPID` with your GEPID from the the 'projects' table in the webapp. Replace `/path/to/` with the path to the directory where `GenEditID/data/reference/Homo_sapiens.GRCh38.dna.toplevel.fa.gz` is located.
```
geneditid_create_amplicount_config --project=GEPID --genome=/path/to/GenEditID/data/reference/Homo_sapiens.GRCh38.dna.toplevel.fa.gz
```

- Run [geneditid_run_amplicount](https://github.com/GenEditID/GenEditID/blob/master/python/scripts/run_ampli_count.py) script on all fasta files from your project directory. Replace `fastq/` with the path to the directory the fastq files are located in.
```
geneditid_run_amplicount --fastqdir=fastq/
```

- Check results in `amplicount.csv`


## Step 5: Identify variants and plot results

- Run [geneditid_run_variantid](https://github.com/GenEditID/GenEditID/blob/master/python/scripts/run_variant_id.py) script from your project directory:
```
geneditid_run_variantid
```

- Check results in `editid_variantid/variantid.csv` and `editid_variantid/impacts.csv` and plots
  - `editid_variantid/coverage_[amplicon_id].html`
  - `editid_variantid/koscores_[amplicon_id].html`


## Step 6: Plot koscore heatmap on plates

- Retrieve sample location on plates from the database and add them onto the necessary files. Replace GEPID with the GEPID from the projects table in the webapp.
```
geneditid_add_sample_location GEPID
```

- Plot heatmap on plates
```
geneditid_plot_scores
```

- Visualise plots in your browser
  - `editid_variantid/heatmap_[amplicon_id].html`
  - `editid_variantid/heatmap_protein_expression.html` (if data available)
  - `editid_variantid/heatmap_combined_data.html` (if data available)
