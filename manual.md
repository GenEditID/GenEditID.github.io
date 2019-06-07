# GenEditID detailed steps


## Step 1: Install dependencies

To run GenEditID, first download and install the necessary packages described below.

- [`fastq-join`](https://github.com/brwnj/fastq-join)
- [`seqkit`](https://github.com/shenwei356/seqkit)

NB. These tools need to be on your path to be executable.


## Step 2: Install and start GenEditID WebApp

Next, clone the GitHub repo [editID](https://github.com/GenEditID/editID.git), start and open the WebApp in a web browser, which displays a homepage with fields to initiate and describe a new project. These data are loaded into the database and facilitate project and sample tracking.

```
git clone https://github.com/GenEditID/editID.git
cd editID/
python3 -m venv venv
source venv/bin/activate
pip install -r python/requirements.txt
```

- Create the database

- Start the WebApp

- To accompany a new project, load a "data and experiment layout submission" excel (.xls) file ([download template](https://github.com/GenEditID/editID/raw/master/data/templates/GEPXXXXX.xlsx)) describing the sample names, plate and well locations, barcodes, and other information. This is loaded into the database and associated with the indicated project.


## Step 3: Create Project folder

- Create a GEPID project folder on a local directory to deposit data files associated with the project (e.g. fastq sequencing files and all output files from ampli_count and/or protein analysis).
```
cd /path/to/my/data/
mkdir GEPID
```

- Copy all NGS scripts onto project folder
```
cp ~/editID/shell/ngs/* /path/to/my/data/GEPID/.
```


## Step 4: Fetch fastq files

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


## Step 5: Run `ampli_count`

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


## Step 6: Identify variants and plot results

- Run [`run_variant_id.py`](../python/scripts/run_variant_id.py) script from the project directory:
  ```
  cd /path/to/my/data/GEPID/
  source ~/editID/venv/bin/activate
  python ~/editID/python/scripts/run_variant_id.py
  ```

- Check results in `editid_variantid/variantid.csv` and `editid_variantid/impacts.csv` and plots
  - `editid_variantid/coverage_[amplicon_id].html`
  - `editid_variantid/koscores_[amplicon_id].html`


## Step 7: Plot koscore heatmap on plates

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
