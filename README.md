### Introduction

deGSM is a memory-flexible and multi-threaded de bruijn graph constructor. It is suitable for building and compacting de bruijn graph for multiple reference genomes and large re-sequencing datasets.

deGSM is a parallel algorithm and it solved thoroughly the memory bottleneck problem using the method of block sorting and multi-way merging. deGSM gets the k-mers from the k-mer counter and build graph compacting directly in BWT sequence. deGSM also supports to parallelly transform BWT string to uni-paths in .fasta format.

deGSM is successful to construct graph for GenBank sequence database at level Contig (305Gbp) and level Scaffold (1.1Tbp) and Picea abies sequencing dataset (9.7Tbp), while maintaining small and flexible memory usage.

deGSM is mainly designed by Bo Liu and Hongzhe Guo, developed by Hongzhe Guo in Center for Bioinformatics, Harbin Institute of Technology, China.

### Memory requirement

The memory usage and disk space usage of deGSM can fit the configurations of most modern servers and workstations. Its peak memory footprint can be configured by user and the peak disk space usage depends on the size of dataset and k-mer size, i.e.,1.7 TeraBytes for k=22 and 5.4 TeraBytes for k=62 on the GenBank Contig dataset; 14 TeraBytes for k=29 (abundance cutoff equals to 3)on the Picea abies re-sequencing dataset, on a server with Intel Xeon CPU at 2.00 GHz, 100 Gigabytes RAM running Linux CentOS 14.04.

The wall time of the deGSM constructing graph for different datasets using diverse k-mer sizes is as follows. The time is in minutes.


| No. | Dataset | K-mer size| Time |
|---|---|---|---|
| 1 | GenBank Contig	| 22 | 1044 |
| 2 | GenBank Contig	| 62 | 1561 |
| 3 | GenBank Scaffold	| 22 | 9059 |
| 4 | GenBank Scaffold	| 62 | 10812 |
| 5 | Picea abies	| 29 | 6937 |


### Installation

Current version of deGSM needs to be run on Linux operating system.  
The source code is written in C, and can be directly download from: https://github.com/hitbc/deGSM  
The makefile is attached. Use the make command for generating the executable file.  
Jellyfish2 should be properly installed on the system and can be added in the environment variables using following command.
export LD_LIBRARY_PATH=jellyfish_path/.libs

Note: 
please make sure that you have the permission to execute command 'ulimit' when you run deGSM on large dataset. 
Please use ulimit command to increase the default maximum number of openfiles like "ulimit -s 65536", if it triggers the error due to too many input files under source folder, e.g., "Argument list is too long"
Plesea make sure that there are no other folders under source path.

deGSM can also support input files in compressed format,e.g., .gz(option -g) or .sra(option -s). The compressed files should be created by gzip or .
The input can be source path or source file. All input files in source path must be in the same compressed format or uncompressed foramt.
zcat must be installed if the input file is in .gz format.
fastq-dump must be installed if the input file is in .sra format.

Some small test files are in test_data folder.

### Synopsis

deGSM [options] \<jellyfish_path\> output_file \<source_path\>
Build graph for reference or re-sequencing dataset

ubwt <command> [options]

Commands: 
         unipath     generate uni-path sequence from BWT string
         index       build BWT index for uni-path sequence
         query       find exact match of query sequence with BWT index
		 
### Options (could be updated in the future for adding new functions)
```
deGSM 
-k,                     INT k-mer size of each vertex of de bruijn graph and the size is within the range [20-253][55].    

-t,                     INT the number of threads. The current version of name supports upto 32 threads in graph construction[8].

-m,                     INT the max memory usage in de bruijn graph building and the size is within the range [4-32GB][32G].

-l,                     INT the abundance-min cutoff for a k-mer’s occurrence. When –l is set, name will filter out the k-mers lower than the abundance-min cutoff[1].    

-u,                     INT the abundance-max cutoff for a k-mer’s occurrence. When –u is set, name will filter out the k-mers upper than the abundance-max cutoff[0Xffffff].   

-q,                     STR when –q is set, the k-mer will be filtered out if there is at least base with quality under this character on this k-mer.  

-g,                     the format of input file is .gz format.

-s,                     the format of input file is .sra format.
						
-noCom,                 when –noCom option is set, name do not count the k-mers on complementary-reverse strand in the k-mer counte.    

ubwt unipath  
-t,                     INT the number of threads. The current version of ubwt supports upto 32 threads in bwt string transforming to uni-paths[8]. 

-f,                     STR the format of source bwt string and it can be in binary with 4-bit for each bp or plan text[Binary]. 
                               "B": binary file, 4-bit per bp, 0/1/2/3/4:A/C/G/T/#(first 64-bit: length).
                               "P": plain text.
-e                      STR Edge sequence file in binary format. Required when output GFA format. [NULL]
-k                      INT Length of k-mer. Required when output GFA format.
-a                      STR Format of output file. [F]
                               "F": FASTA format.
                               "G": GFA format.
-o,                     STR the output file as the result uni-paths in the format of .fasta. 
  
```

### Quick start

Graph constructing:
deGSM jellyfish_path output_file source_path 

BWT string transforming to uni-paths:
ubwt unipath BWT-STR

### Example
deGSM -k 30 -m 32G ./jellyfish-2.1.4 source.bwt ../source_path

ubwt unipath source.ubwt -e edges.seq -k 30 -o result.unipath.gfa -a G

For more detailed knowledge about ubwt, please check https://github.com/hitbc/ubwt.
### Simulation benchmarking
We simulated a dataset from Picea abies genome through ART Simulator (version 2.5.8). The 200 bp Illumina-like pair-end reads(70 X coverage and the mean and standard deviation of the insert size are respectively 500 bp and 25 bp) were simulated for evaluation. This dataset helped us to evaluate the performance of deGSM. 


### Reference

deGSM: memory scalable construction of large scale de Bruijn Graph.

### Contact

For advising, bug reporting and requiring help, please contact ydwang@hit.edu.cn; hzguo@hit.edu.cn

