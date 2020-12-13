## Detailed steps for testing with real data


### :memo: Demo steps
If you only wish to run a demo instead of a full analysis, use this submission spreadsheet [`data/submission_spreadsheets/demo.xlsx`](https://github.com/GenEditID/GenEditID/raw/master/data/submission_spreadsheets/demo.xlsx), download the fastq files using `./scripts/get_data_demo.sh` and go to the `cd PROJECTS/demo` project to combine paired-end reads.

Running the full analysis below will take more than four hours on a laptop.


### :one: Start GenEditID WebApp, and setup a project for analysing SRP198941_SLX15021_GEP00005

Create a project, upload the submission spreadsheet associated with this project from [`data/submission_spreadsheets/SRP198941_SLX15021_GEP00005.xlsx`](https://github.com/GenEditID/GenEditID/raw/master/data/submission_spreadsheets/SRP198941_SLX15021_GEP00005.xlsx) into the WebApp.

- Start GenEditID WebApp
```
conda activate geneditid
cd ~/GenEditID/
./scripts/start_webapp.sh
```

- Go to [http://localhost:8080](http://localhost:8080)

- Create a **new project**, note the `GEPID`

- Upload the **submission spreadsheet** associated to this project

The active environment, **the one you are currently using**, is shown in parentheses () or brackets [] at the beginning of your command prompt.

```
(geneditid) $
```
if not, activate it using `conda activate geneditid` before running the next steps.

### :two: Download and prepare fastq files

Download the public fastq files:
```
cd ~/GenEditID/
./scripts/get_data_SRP198941_SLX15021_GEP00005.sh
```

All the fastq files related to this project will be stored in the `PROJECTS/SRP198941` folder. Combine paired-end reads by merging reads to generate `.fqjoin.gz` files for `amplicount` analysis (`seqkit` needs to be installed).

```
cd PROJECTS/SRP198941
../../scripts/run_mergereads.sh
```

Move the joined fastq files into the `PROJECTS/GEPID/fastq/` replacing `GEPID` with the identifier of this project.

```
mv *.fqjoin.gz ../GEP00001/fastq/.
```


### :three: Run `amplicount` analysis


```
cd ~/GenEditID
source venv/bin/activate
cd PROJECTS/GEP00001
geneditid_run_amplicount
```
Replace `GEP00001` with the identifier of this project.


### :four: Visualise results

- View the output of the analysis in the output result file `GenEditID/PROJECTS/GEPID/amplicount.csv`

- Generate and visualise plots in the GenEditID WebApp [http://localhost:8080](http://localhost:8080). Click on the 'GEPID' of the project in the table of projects on the home page and navigate to the Reads, Variants and Scores tabs. Plot and results files are generated and stored in the project folder for convenience:
  - Read coverage: `GenEditID/PROJECTS/GEPID/geneditid_plots/coverage.html`
  - Variants impact frequency: `GenEditID/PROJECTS/GEPID/geneditid_plots/impacts.html`
  - Scores on plate: `GenEditID/PROJECTS/GEPID/geneditid_plots/koscores.html`
  - Targeted search: `GenEditID/PROJECTS/GEPID/geneditid_plots/targeted_search.html`


### :memo: Two more real data projects to analyse SRP199742_SLX15026_GEP00009 and SRP199742_SLX15025_GEP00010 if you wish

- To analyse project **SRP199742_SLX15026_GEP00009**, use this submission spreadsheet [`data/submission_spreadsheets/SRP199742_SLX15026_GEP00009.xlsx`](https://github.com/GenEditID/GenEditID/raw/master/data/submission_spreadsheets/SRP199742_SLX15026_GEP00009.xlsx), download the fastq files using `./scripts/get_data_SRP199742_SLX15026_GEP00009.sh` and go to the `cd PROJECTS/SRP199742_GEP00009` project to combine paired-end reads.

- To analyse project **SRP199742_SLX15025_GEP00010**, use this submission spreadsheet [`data/submission_spreadsheets/SRP199742_SLX15025_GEP00010.xlsx`](https://github.com/GenEditID/GenEditID/raw/master/data/submission_spreadsheets/SRP199742_SLX15025_GEP00010.xlsx), download the fastq files using `./scripts/get_data_SRP199742_SLX15025_GEP00010.sh` and go to the `cd PROJECTS/SRP199742_GEP000010` project to combine paired-end reads.

### :house: Back to the main manual
[GenEditID Manual: detailed steps](manual.md)
