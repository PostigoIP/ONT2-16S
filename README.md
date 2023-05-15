# ONT2-16s
Full processing of 16s fastQ reads from ONT runs; from QC to pathogen identification.

This bash script (`16s_custom_workflow.sh`) is a workflow that prepares MinION reads for downstream 16S analysis.
The pipeline performs several steps to process the demultiplexed fastQ files obtained for each run and organize them into folders based on barcodes. The following is a description of the pipeline's functionality:

1. **Demultiplexing**: The pipeline separates the fastQ files into individual folders based on their barcode sequences.

2. **Read Filtering**: The reads within each barcode folder are combined into a single file and subjected to quality and length control. This step ensures that only high-quality and appropriately sized reads are retained for further analysis.

3. **Primer Mapping**: The pipeline maps 16S primers onto the filtered reads to extract the 16S regions 1 to 3. This step isolates the specific regions of interest within the reads.

4. **Conversion to FASTA**: The extracted 16S regions are converted to FASTA format, which is a standard format for biological sequence data.

5. **BLAST Search**: The pipeline performs a BLAST search against the 16S rRNA database from NCBI using the FASTA-formatted 16S regions. This search helps identify similar sequences in the database.

6. **Binning and Drafting**: The BLAST results are organized and grouped by gIDs (gene identifiers) in separate folders. For each gID hit, a reference 16S gene is drafted from the database. These reference genes provide a representative sequence for each identified gene.

7. **Read Mapping**: The original FastQ 16S reads that match the BLAST results are retrieved and mapped with Bowtie2 against the 16S reference genes. This mapping step creates a consensus sequence that incorporates all three 16S regions.

8. **Further BLAST Search**: The consensus sequence is subjected to another BLAST search against the 16S NCBI database. This step helps to validate and provide additional information about the consensus sequence.

The pipeline generates reports containing information on the BLAST results and quality control. These reports undergo thorough assessment to determine the hits, which are sequences that meet specific criteria. The criteria include a BLAST identity match over 98%, 16S bacterial gene coverage of more than 600 nucleotides, a Phred score above 30 over at least 60% of the 16S consensus sequence, and a barcode score surpassing 85.9.

## Workflow diagram
![250423_FigureS1](https://github.com/PostigoIP/ONT2-16s/assets/91267553/1a7dc763-a3c6-4ef2-a0c3-8a162fad7a8b)

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
You need to have a conda environment named [InSilico_PCR](https://anaconda.org/bioconda/ispcr) with all the necessary dependencies installed. The script will attempt to activate this environment, and will exit if it doesn't exist.

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
./ONT2-16S.sh
```

Please note that you may need to grant execute permissions to the script file using the following command:
```
chmod +x ONT2-16S.sh
```
## Generating reports

To generate a report from the run use the generate_16S_report.sh script. This bash script generates a comprehensive report of the 16S sequencing pipeline. The report includes metrics like the number of total reads, filtered reads, extracted reads, and blast hits. It also includes information about pathogen matches and an in-depth analysis of top matches.

## Requirements

The script requires the following software to be installed and available in your `PATH`:

- `bash`
- `awk`
- `grep`
- `seqtk`
- `samtools`
- `guppy_barcoder`
- `mafft`
- `megacc`
- `seqkit`
- `zcat`
- `column`

## How it works

1. The script begins by creating an `output.txt` file where it writes the report.

2. It then navigates into each folder that matches the pattern `barcode**_filtered**`.

3. Inside each folder, the script calculates the total number of reads, the number of quality-filtered reads, the number of 16S-extracted reads, and the number of blast hits. These values are calculated using a combination of the `grep`, `zcat`, `awk`, and `wc` commands. 

4. It then identifies and lists the pathogen matches at the genus level that make up more than 1% of total reads.

5. For the top matches, the script performs an in-depth analysis, including mapping reads, calculating the average barcode score, and estimating the p-distance to the closest match. 

6. The script then compiles these results and appends them to the report file (`output.txt`).

7. Finally, it performs a multiple sequence alignment of the consensus sequences using `mafft` and estimates distances using `megacc`.

## Output

The script outputs an `output.txt` file that includes the report, and a `summary_all.txt` file that includes a summary of the top matches analysis. The `output.txt` file includes the number of total reads, quality-filtered reads, 16S-extracted reads, and blast hits for each barcode, as well as an in-depth analysis of the top matches. The `summary_all.txt` file includes a tab-separated summary of the top matches analysis, which includes metrics like the number of reads mapped, the average barcode score, and the p-distance to the closest match.

The output would look like this:

```
16s sequencing pipeline report
#############################################
Specimen: barcode01
#############################################
Nº of reads_total: 506806.0
Nº reads_q-filter: 272613	53.7904
Nº reads_16s-extraction: 117735	43.1876
Nº reads_blast-hits: 91466	77.688
#############################################
Pathogen matches (Genus) (>1% of total reads):
-----------------------------------------------
Pathogen_ID, nº reads_blast, % reads_total
Pseudomonas     61592  67.3387
Achromobacter   22227  24.3008
Bordetella      1419   1.5514
Castellaniella  786    0.859336
###################################################################################################################################################################
Top 99% in depth analysis (species):
------------------------------------
bc_ID      cns_Blast_hit                     reads_mapped(%)  gen_hits(n)  of_reads_blast(%)  HQ_cns(%)  aln_sites(n)  avg_bc-score(%)  p-distance_closest(%)  species_assigned              support
barcode01  Pseudomonas aeruginosa            99.8692          64219        70.119             92.7       850           94.2215          99.7428                Pseudomonas_aeruginosa        0.8580
barcode01  Achromobacter insolitus           99.7835          23090        25.1897            91.6       1108          94.1182          99.913                 Achromobacter_insolitus       0.3840
barcode01  Achromobacter insolitus           99.1886          1479         1.60387            92.3       1157          94.2334          99.681                 Bordetella_muralis            0.9840
barcode01  Castellaniella defragrans 65Phen  100              788          0.861522           88.7       1206          94.0421          99.5709                Castellaniella_denitrificans  0.7840
barcode01  0                                 100
###################################################################################################################################################################


#############################################
Specimen: barcode02
#############################################
Nº of reads_total: 503452.0
Nº reads_q-filter: 267631	53.1592
Nº reads_16s-extraction: 125875	47.033
Nº reads_blast-hits: 83618	66.4294
#############################################
```


In parallel, a combined file in csv with all the hits can be created to be used with other classifiers like kraken or centrifuge.

## Troubleshooting
If you encounter errors:
- Make sure the script has execute permissions.
- Check that the necessary scripts, databases, and conda environment exist at the specified paths.
- Make sure your input files are correctly named and in the correct format.
- Ensure all prerequisite software is installed and accessible from your PATH.

## Support
For support, please create an issue on the GitHub repository. Please provide as much detail as possible, including the exact command you ran, a description of the issue, and any error messages.
