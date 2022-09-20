# Angelfish-Genome-Assembly
This is a pipeline describing how I assembled the complete genome (86.5%) of the freshwater angelfish, *Pterophylllum scalare* with the Oxford Nanopore Technologies MinION Mk1B device.
A special thank you to Christopher Faulk (@dithiii) and his [pipeline](https://github.com/dithiii/ant-pipeline/blob/main/README.md) which tremendously helped me in my work and inspired me to write this pipeline for those who are interested in alternative ways to assemble a genome or just specifically interested in how the angelfish genome was assembled. There is some overlap between our two pipelines, so do check his out. 

# Angelfish Genome Sequencing
1. Angelfish DNA was extracted from muscle and skin tissue of an aquarium angelfish that died of natural causes. The NEB Monarch Genomic DNA purification kit was used to perform this extraction. 

2. The genomic Library was prepped with the [Ligation Sequencing Kit](https://store.nanoporetech.com/ligation-sequencing-kit-112.html) (SQK-LSK112) following the standard protocol.

3. Two nanopore flow cells (R10.4) were used for the experimentation and were run for 72 hours each. The minimum quality threshold score was 9 with high accuracy guppy basecalling. 

# Angelfish Read Assembly 

## [Nanoq](https://github.com/esteinig/nanoq) read statistics.
After combining all the read files into a single fastq.gz file, Nanoq was used to check statistics about the reads collected. 
`nanoq -v -s -i Pterophyllum_scalare.fastq.gz`

## [Kraken](https://github.com/DerrickWood/kraken) contamination detection
Kraken as used to indentify contaminant contigs in the collected reads. 
`kraken2 --db minikraken --threads 10 --use-names --report Pterophyllum_scalare.FASTQ.unmapped.report.txt --output Pterophyllum_scalare.FASTQ.unmapped.out.txt Pterophyllum_scalare.fastq`
I did have quite a challenge getting kraken to run on my computer, so do be careful while setting it up on your computer. 


## [Flye](https://github.com/fenderglass/Flye) de-novo assembly
Flye was used to perform a de novo assembly of the angelfish genome. Due to the computational memory restrains of my computer (M1 Macbook Pro 2022, 32 GB RAM) the galaxy bioinformatics platform was used instead. You can sign up for a free account [here](https://usegalaxy.org/login). Assembling the angelfish genome required a peak 42 GB of RAM, but system requirements will vary based on various factors including the number of reads and average read length. 

Reads below 1000 bp were dropped as flye had a minimum overlap length of 1000 bp. Assembly of the genome took approximately 2 hours on the TACC Stampede 2 SKX partition compute resource. 

## [BUSCO](https://gitlab.com/ezlab/busco/-/releases#5.4.3) completeness of genome assessment
BUSCO was used to asses the completness of the initial assembly. BUSCO (Benchmarking Universal Single Copy Orthologs) identifies unvieral genes found in organisms. The angelfish genome assembly was assessed against the Actinopterygii lineage to identify highly conserved fish genes. The more USCOs found, the higher the liklihood that your genome is complete. BUSCO analysis was also done through the galaxy platform. 


