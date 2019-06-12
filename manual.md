# GenEditID detailed steps


## Step 1: Install dependencies and setup GenEditID

To run GenEditID, first download and install the necessary dependencies:

- [Python3](https://www.python.org/downloads/)
- [fastq-join](https://github.com/brwnj/fastq-join)
- [seqkit](https://github.com/shenwei356/seqkit)

NB. These tools need to be on your path to be executable.

- Clone the GitHub repo [editID](https://github.com/GenEditID/editID.git)
```
git clone https://github.com/GenEditID/geneditid.git
cd geneditid/
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

- Create a new project by filling the form on the home page of the WebApp and save it into the database by clicking on 'Create project' button.

- Then click on 'edit' in the table of projects below, to upload data and experiment layout associated to this project: load project data (targets, guides and amplicons), its experiment layout, list of plates and sequencing library details from an Excel file using this ([template](https://github.com/GenEditID/editID/raw/master/data/templates/GEPXXXXX.xlsx)). Browse your computer and select the excel file corresponding to this project, then click on 'UploadData' button. This is then loaded into the database and associated with the indicated project.


- Create a GEPID project folder on a local directory to store data files associated with the project (e.g. fastq sequencing files and all output files from ampli_count and/or protein analysis).
```
cd /path/to/my/data/
mkdir GEPID
```

- Copy all NGS scripts onto project folder
```
cp ~/editID/shell/ngs/* /path/to/my/data/GEPID/.
```


## Step 3: Fetch fastq files

For sequence analysis, combine reads from fastq files by merging or joining to generate a `.fqjoin.gz` files.

- Check sequencing information: read length PE150 or PE300
- Combine reads
  - Reads should be joined when target size is bigger than read length (`fastq-join` needs to be installed)
  ```
  cd /path/to/my/data/GEPID/
  ./job_joinreads.sh
  ```
  - or merged when target size is smaller than the read length (`seqkit` needs to be installed)
  ```
  cd /path/to/my/data/GEPID/
  ./job_mergereads.sh
  ```


## Step 4: Run `ampli_count`

The "data and experiment layout submission" form (step 2) and combined sequences (step 4) are integrated and amplicon sequences are fetched to generate a `amplicount_config.csv` file that enables downstream analysis. Note that this requires association with a reference genome that should be loaded locally. This allows the `ampli_count` tool to be run to generate an `amplicount.csv` file, which is loaded back to the database. Sequences associated with each amplicon are counted and quality controlled to discard low frequency and low quality reads.

- Extract amplicons and targets coordinates from the database using script `create_pipeline_files.py`, and config file:
  ```
  cd /path/to/my/data/GEPID/
  source ~/editID/venv/bin/activate
  python ~/editID/python/scripts/create_ampli_count_conf.py --project=GEPID --genome=/path/to/hsa.GRCh38_hs38d1.fa
  ```

- Run [`run_ampli_count.py`](https://github.com/GenEditID/editID/blob/master/python/scripts/run_ampli_count.py) script on all fasta files
  ```
  cd /path/to/my/data/GEPID/
  ./job_amplicount.sh
  tail -f amplicount.out
  ```

- Check results in `amplicount.csv`


## Step 5: Identify variants and plot results

- Run [`run_variant_id.py`](../python/scripts/run_variant_id.py) script from the project directory:
  ```
  cd /path/to/my/data/GEPID/
  source ~/editID/venv/bin/activate
  python ~/editID/python/scripts/run_variant_id.py
  ```

- Check results in `editid_variantid/variantid.csv` and `editid_variantid/impacts.csv` and plots
  - `editid_variantid/coverage_[amplicon_id].html`
  - `editid_variantid/koscores_[amplicon_id].html`


## Step 6: Plot koscore heatmap on plates

- Retrieve sample location on plates from the database and add them onto the necessary files
```
cd /path/to/my/data/GEPID/
source ~/editID/venv/bin/activate
python ~/editID/python/scripts/get_sample_loc.py GEPID
python ~/editID/python/scripts/add_sample_loc.py
```

- Plot heatmap on plates
```
cd /path/to/my/data/GEPID/
source ~/editID/venv/bin/activate
python ~/editID/python/scripts/plot_scores.py
```

- Visualise plots
  - `editid_variantid/heatmap_[amplicon_id].html`
  - `editid_variantid/heatmap_protein_expression.html` (if data available)
  - `editid_variantid/heatmap_combined_data.html` (if data available)
