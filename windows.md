## Install dependencies and setup GenEditID

### Specific help for Windows 10

- Install [Windows Subsystem for Linux (WSL)](https://docs.microsoft.com/en-us/windows/wsl/install-win10) to get Ubuntu running on Windows 10 by enabling WSL option in the settings

- Go to the Ubuntu terminal and install the dependencies

- Access local Windows directories from Ubuntu
  - check how it is mounted using `df -h` e.g. `C:` drive is `/mnt/c` in Ubuntu

- Access Ubuntu directories from Windows
  try in an Ubuntu terminal:
  ```
  explorer.exe .
  ```
  if you have issue, you're not alone https://github.com/microsoft/WSL/issues/4027

- Get the fastq `*.fq.gz` files into the project folder under `fastq` subfolder, and find the path to the project folder to be used in the analysis.


### :house: Back to the main manual
[GenEditID Manual: detailed steps](manual.md)
