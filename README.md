# ONT2-16s
Full processing of 16s fastQ reads from ONT runs; from QC to pathogen identification.

This bash script (`16s_custom_workflow.sh`) is a workflow that prepares MinION reads for downstream 16S analysis.

The scripts, conda environment, and necessary databases are assumed to be in specific locations on the local machine, please ensure they are in place before running the script.

## Prerequisites
- [Anaconda](https://www.anaconda.com/products/distribution) or [Miniconda](https://docs.conda.io/en/latest/miniconda.html)
- [seqtk](https://github.com/lh3/seqtk)
- [Bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/index.shtml)
- [samtools](http://www.htslib.org/)
- [bcftools](https://samtools.github.io/bcftools/)
- [MegaX](https://www.megasoftware.net/)
- [ete3](http://etetoolkit.org/)
- [NanoStat](https://github.com/wdecoster/NanoStat)
- [NanoFilt](https://github.com/wdecoster/nanofilt)

Please install the above tools using either the package manager associated with your distribution or the instructions provided in the respective links.

## Conda Environment
You need to have a conda environment named `InSilico_PCR` with all the necessary dependencies installed. The script will attempt to activate this environment, and will exit if it doesn't exist.

## Inputs
The script works on fastq files in the current directory. These files should be named as "barcode*.fastq", where * is the barcode number.

## Output
The script generates a number of output files, including:
- Combined reads for each barcode
- Quality filtered reads
- Sequences extracted based on specific primer sets
- BLAST results against the 16S database
- Read classification and genus-level summaries
- Consensus sequences and their BLAST results
- Species typing information and phylogenetic trees

## Running the script
To run the script, navigate to the directory containing your fastq files and the bash script, and enter the following command in your terminal:
```
./16s_custom_workflow.sh
```

Please note that you may need to grant execute permissions to the script file using the following command:
```
chmod +x 16s_custom_workflow.sh
```

## Troubleshooting
If you encounter errors:
- Make sure the script has execute permissions.
- Check that the necessary scripts, databases, and conda environment exist at the specified paths.
- Make sure your input files are correctly named and in the correct format.
- Ensure all prerequisite software is installed and accessible from your PATH.

## Support
For support, please create an issue on the GitHub repository. Please provide as much detail as possible, including the exact command you ran, a description of the issue, and any error messages.
