## Step 4: Run `ampli_count`

### Detailed information about the reference genome files

The Reference Genome (release 95): [Homo_sapiens.GRCh38.dna.toplevel.fa.gz](ftp://ftp.ensembl.org/pub/release-95/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.toplevel.fa.gz) is stored in `GenEditID/data/reference/` and has been compressed using `bgzip`. If you wish to work with another reference, please download, uncompress, and re-compress it with bgzip utility from samtools [htslib-1.10.2](https://github.com/samtools/htslib/releases/download/1.10.2/htslib-1.10.2.tar.bz2)
```
gunzip Homo_sapiens.GRCh38.dna.toplevel.fa.gz
bgzip < Homo_sapiens.GRCh38.dna.toplevel.fa > Homo_sapiens.GRCh38.dna.toplevel.fa.gz
```

### Back to the main manual
[GenEditID Manual: detailed steps](manual.md)
