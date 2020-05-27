# ELIXIR QC proteomics pipeline

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
- out_folder: folder where the output QC metrics files are going to be stored. 
- fasta_tab: file with used FASTA databases. Do not change it. 
- watch: "YES" if you want the pipeline to be automatically started when a file is moved to `zipfiles` folder. 
- email: notification email. 

3. Set up nextflow.config file: modify this file according to your HPC Cluster queues name/s and the memory and CPUs available. 

## Usage: 

1. Run the pipeline in background mode: `nextflow run -bg elixir_proteomics_QC.nf > elixir_qc.log`. The first time you run this command Nextflow will automatically pull the last pipeline container version labeled as `biocorecrg/qcloud:2.2` (1.5GB aprox.), ThermoRawFileParser container `biocorecrg-thermorawparser-0.1.img` (438M) that converts RAW files to mzML format and mzQC converter `biocorecrg-mzqc_conv-0.1.img` (242M), all three files stored at `$baseDir/image`.
- Once all images have been pulled, you can copy any RAW file to the incoming folder. Its filename must be in a specific format to be successfully processed by the pipeline: 
    - All files must be zipped with the same name as the RAW file. 
    - Its name must follow the following convention: `QC02_checksum.raw.zip`, where: 
        - QC02, wich is an internal code to identify complex human samples (HeLa). 
        - Checksum: the md5sum of the RAW file (not the zipped one). 
        - For instance: `QC02_c010cb81200806e9113919213772aaa9.raw.zip`

## Output files: 

Once the pipeline has been processed all the files moved to `$baseDir/incoming`folder, it'll create a folder with the same name as the RAW files in the output folder defined at `params.config`. Inside this folder you'll find: 

- mzQC: the HUPO-PSI standard format file for QC metrics. 
- mzID: the HUPO-PSI standard format file for protein identification. 
- featureXML: OpenMS features. 
- qcML: OpenMS QC metrics. 
- mzML: the HUPO-PSI standard format file for mass spec raw files. 
  
## Credits (specifically for the bioinformatics part): 
- QCloud Pipeline: Luca Cozzuto and Roger Olivella. 
- ThermoFileRawParser: Niels Hulstaert (https://github.com/compomics/ThermoRawFileParser#thermorawfileparser). ). 

## Credits (for the entire project): 

Mathias Walzer, Juan Antonio Vizcaíno and Eduard Sabidó. 

## License: 

Pending. 

#### Last update by @rolivella on 27/05/2020
