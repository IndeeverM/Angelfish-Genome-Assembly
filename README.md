# Angelfish-Genome-Assembly
This is a pipeline describing how I assembled the complete genome (86.5%) of the freshwater angelfish, *Pterophylllum scalare* with the Oxford Nanopore Technologies MinION Mk1B device.
A special thank you to Christopher Faulk (@dithiii) and his [pipeline](https://github.com/dithiii/ant-pipeline/blob/main/README.md) which tremendously helped me in my work and inspired me to write this pipeline for those who are interested in alternative ways to assemble a genome or just specifically interested in how the angelfish genome was assembled. There is significant overlap between our two pipelines, so do check his out. 

# Angelfish Genome Sequencing
1. Angelfish DNA was extracted from muscle and skin tissue of an aquarium angelfish that died of natural causes. The NEB Monarch Genomic DNA purification kit was used to perform this extraction. 

2. The genomic Library was prepped with the [Ligation Sequencing Kit](https://store.nanoporetech.com/ligation-sequencing-kit-112.html) (SQK-LSK112) following the standard protocol.

3. Two nanopore flow cells (R10.4) were used for the experimentation and were run for 72 hours each. The minimum quality threshold score was 9 with high accuracy guppy basecalling. 

# Angelfish Read Assembly 

## [Nanoq](https://github.com/esteinig/nanoq) read statistics.
After combining all the read files into a single fastq.gz file, Nanoq was used to check statistics about the reads collected. 
`nanoq -v -s -i Pterophyllum_scalare.fastq.gz`

## [Kraken2](https://github.com/DerrickWood/kraken) contamination detection
Kraken2 was used to indentify contaminant sequences in the collected reads. 
`kraken2 --db minikraken --threads 10 --use-names --report Pterophyllum_scalare.FASTQ.unmapped.report.txt --output Pterophyllum_scalare.FASTQ.unmapped.out.txt Pterophyllum_scalare.fastq`
I did have quite a challenge getting kraken to run on my computer, so do be careful while setting it up on your machine. 


## [Flye](https://github.com/fenderglass/Flye) de-novo assembly
Flye was used to perform a de novo assembly of the angelfish genome. Due to the computational memory restrains of my computer (M1 Macbook Pro 2022, 32 GB RAM) the galaxy bioinformatics platform was used instead. You can sign up for a free account [here](https://usegalaxy.org/login). Assembling the angelfish genome required a peak 42 GB of RAM, but system requirements will vary based on various factors including the number of reads and average read length. 

Reads below 1000 bp were dropped as flye had a minimum overlap length of 1000 bp. Assembly of the genome took approximately 2 hours on the TACC Stampede 2 SKX partition compute resource. 

## [BUSCO](https://gitlab.com/ezlab/busco/-/releases#5.4.3) completeness of genome assessment
BUSCO was used to asses the completness of the initial assembly. BUSCO (Benchmarking Universal Single Copy Orthologs) identifies unvieral genes found in organisms. The angelfish genome assembly was assessed against the Actinopterygii lineage to identify highly conserved fish genes. The more USCOs found, the higher the liklihood that your genome is complete. BUSCO analysis was also done through the galaxy platform. 

## [Racon](https://github.com/isovic/racon) genome polishing tool
RACON identifies potential errors in the genome assembly and corrects the alignment of the reads to the genome being built. Interestingly, flye on the galaxy platform does not give a BAM file for the alignments of the sequencing reads. That needs to be generated separately with minimap or another tool. 
What's also interesting is that made practically no difference in genome BUSCO score. RACON was available in the galaxy platform. Using lower quality reads in the assembly also did not improve the genome assembly. 

## Mitochondrial Genome assembly
Let's take a quick detour and talk about the assembly of the mitochondrial genome of the angelfish. [Hao](https://pubmed.ncbi.nlm.nih.gov/26000948/) et al. 
have already assembled the angelfish genome through a PCR based method. This makes it easy to assemble a mirochondrial genome, since we already have a reference template to work with. 

First, the alignment tool built in the MinKnow software was used to align the collected angelfish reads against the reference mitochondrial genome. This step ensures we only use mitochondiral sequences in the assembly and are able to eliminate all the genomic reads. 

Once the mitochondrial reads of interest were identified, the [shasta](https://github.com/chanzuckerberg/shasta) de novo assembler put together the contigs into the reference mitochondrial genome. This de-novo step reduces bias (at least theoretically) in the alignment by assembling the mitochondrial genome from scratch This was done locally since shasta as a mitochondrial genome assembly does not require much computational power (You could technically use flye for the mitochondrial genome assembly as well). 

`shasta-macOS-11-Intel-0.10.0 --input Mitochondrial_Reads.fastq.gz --config Nanopore-May2022 --memoryBacking disk --memoryMode filesystem --Reads.minReadLength 1000 --assemblyDirectory AngelfishMitochondrialGenomeAssembly`

## Genome Cleanup
Now, back to the whole genome assembly. To cleanup the contigs (which had an 88% BUSCO score at this point). All the contigs were screened by Kraken2 once againt to identify contaminant contigs. Once that was complete, the remianing contigs were aligned to the mitochondrial reference genome build in the previous step, to identify contigs that were of mitochondrial in origin. Once all these contigs were removed, the final genome assembly had a BUSCO score of 86.5%


A [partial genome](https://www.ncbi.nlm.nih.gov/nuccore/2277407487) of the angelfish (49.5% BUSCO Score) that i deposited a few months ago is already available through NCBI. I am currently in the process of depositing the final genome and the mitochondrial genome sequence. I'll update this when that occurs!

## [Genome Annotation](https://www.gensas.org) to identify orthologs 
Blastp through the GenSAS platform was used to identify functional orthologs in closely related species. Th Angelfish genome was blasted against the NCBI vertebrate-other database. It took around 1 week to obtain the final blast results. 

If you read through all of this, thank you so very much. Do not hesitate to reach out if you would like me to elaborate further, or if just have comments or concerns!


