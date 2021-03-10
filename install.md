# :memo: Installation steps
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
  - **For changes to take effect, close and re-open your current shell.**

2. Set up the channels, **it is important to add them in this order**, so that the priority is set correctly (that is, conda-forge is highest priority).
  ```
  conda config --add channels defaults
  conda config --add channels bioconda
  conda config --add channels conda-forge
  ```

3. Install our dependencies: [pear](https://www.h-its.org/software/pear-paired-end-read-merger/), [seqkit](https://github.com/shenwei356/seqkit) and [Git LFS](https://git-lfs.github.com/) into a specific conda environment called `geneditid`.
  ```
  conda create -n geneditid python=3.8 pear seqkit git-lfs
  ```
  To activate this environment, use `conda activate geneditid`.
  To deactivate an active environment, use `conda deactivate`.
  To see a list of all of your environments, run `conda env list`.
  To remove this environment, run `conda remove -n geneditid --all`

4. Setup [Git LFS](https://git-lfs.github.com/) for supporting the usage of large files, clone the GitHub repo [GenEditID](https://github.com/GenEditID/GenEditID.git), setup GenEditID
  ```
  cd ~
  conda activate geneditid
  git lfs install
  git clone https://github.com/GenEditID/GenEditID.git
  cd GenEditID/
  ./scripts/setup_geneditid.sh
  ```
  :warning: Due to large reference genomes files within the repository, the cloning step takes can take a while. Please wait till it completes before moving forward, thanks for your patience.

The **active environment you are currently using** is shown in parentheses () or brackets [] at the beginning of your command prompt.
```
(geneditid) $
```
if not, activate it using `conda activate geneditid` before running the next steps.

### :house: Back to the main manual
[GenEditID Manual: detailed steps](manual.md)
