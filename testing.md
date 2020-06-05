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
python python/geneditidtools/get_data.py --meta=data/sra_metadata/SRP198899.tsv
```

All the fastq files related to this project will be stored in the `PROJECTS/SRP198899` folder.

### :one: Start GenEditID WebApp, and setup a project for analysis

### :two: Fetch and prepare fastq files

### :three: Run `ampli_count` analysis

### :four: Visualise results

### :house: Back to the main manual
[GenEditID Manual: detailed steps](manual.md)
