# ELIXIR QC proteomics pipeline

QCloud is a cloud-based system to support proteomics laboratories in daily quality assessment using a user-friendly interface, easy setup, automated data processing and archiving, and unbiased instrument evaluation. https://qcloud.crg.eu, paper: https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0189209

With this tutorial you'll we able to install all the QCloud system in your local server and HPC Cluster.  

## Global System Requirements: 
    - HPC Cluster running CentOS 7 x64. Do not install it in a desktop PC.
    - Singularity container >= 2.6.1.
    - Nextflow >= 20.01.0.5264.
    - At least 3GB free disk space.  

## Installation: 

1. `git clone https://github.com/proteomicsunitcrg/qcloud2-pipeline.git`, checkout "local" branch and `chmod -R 770` at the pipeline root folder. 
2. Set up Nextflow params.config file: 
```
params {
    qconfig          = "$baseDir/qc.config"
    zipfiles         = "$baseDir/incoming/*.zip"
    out_folder       = "output"
    fasta_tab        = "$baseDir/fasta.tsv"
    watch            = "YES"
    email            = "roger.olivella@crg.eu"
}
```
Where: 
- qconfig: path to qc.config file. Do not change it.  
- zipfiles: incoming folder where the RAW files (zipped) will be put to be processed by the pipeline. 
- outfolder: folder where the output QC metrics JSON files are going to be stored. 
- fasta_tab: file with used FASTA databases. Do not change it. 
- watch: "YES" if you want the pipeline to be automatically started when a file is moved to `zipfiles` folder. 
- email: notification email. 

3. Set up nextflow.config file: modify this file according to your HPC Cluster queues name/s and the memory and CPUs available. 

## Usage: 

- Run the pipeline in background mode: `nextflow run -bg elixir_proteomics_QC.nf > elixir_qc.log`. The first time you run this command Nextflow will automatically pull the last QCloud container version labeled as `biocorecrg/qcloud:2.2` (1.5GB aprox.), ThermoRawFileParser container `biocorecrg-thermorawparser-0.1.img` (438M) that converts RAW files to mzML format and mzQC converter `biocorecrg-mzqc_conv-0.1.img` (242M), all three files stored at `$baseDir/image`.
- Once all images have been pulled, you can copy any RAW file. Its filename must be in a specific format to be successfully processed by the pipeline: 
    - All files must be zipped with the same name as the RAW file. 
    - Its name must follow the following convention: `QC02_checksum.raw.zip`, where: 
        - QC02, wich is an internal code to  (HeLa). 
        - checksum: the md5sum of the RAW file (not the zipped one). 
        - For instance: `20200514_LUMOS1_d2fc2cbf-e632-4f39-ba5a-6f59de0b7c4e_QC01_c010cb81200806e9113919213772aaa9.zip`

## Global Final steps: 

- Troubleshooting: check JAR and Nexftlow logs. We're working to extend this section.  


## Credits (specifically for the bioinformatics part of QCloud): 
- QCloud Pipeline: Luca Cozzuto and Roger Olivella. 
- ThermoFileRawParser: Niels Hulstaert (https://github.com/compomics/ThermoRawFileParser#thermorawfileparser). 
- rawDiag: Christian Panse (https://github.com/fgcz/rawDiag). 

## Credits (for the entire QCloud project): 

Cristina Chiva, Eva Borràs, Guadalupe Espadas, Olga Pastor, Amanda Solé, Eduard Sabidó.

## License: 

Pending. 

#### Last update by @rolivella on 27/05/2020
