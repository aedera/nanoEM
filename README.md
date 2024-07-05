# NanoEM

This repository contains utilities to call DNA methylation from samples treated with EM-seq and sequenced by Nanopore.

## Requirements

* Python 3
* [Parasail-python](https://github.com/jeffdaily/parasail-python)

## Usage

### 1. Base calling

```
dorado basecaller --no-trim /path/to/dna_r10.4.1_e8.2_400bps_sup@v4.2.0 /path/to/pod5_files > myreads.bam
```

### 2. Demultiplexing

For instance, to get reads from the sample with indexes: I5=GCACACAA & I7=CTTGACGA

```
bin/dmux myreads.sam GCACACAA CTTGACGA mysample.sam
```

### 3. Nanopore adaptor trimming

```
dorado trim --emit-fastq mysample.bam > trimmed_mysample.fastq
```

### 4. EM-seq adaptor trimming

```
cutadapt -j 80 -g TGACTGGAGTTCAGACGTGTGCTCTTCCGATCT -g ACACTCTTTCCCTACACGACGCTCTTCCGATCT -o tmp_file.tmp.gz trimmed_mysample.fastq
cutadapt -j 80 -a AGATCGGAAGAGCACACGTCTGAACTCCAGTCA -a AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGT -o trimmed2_mysample.fastq.gz tmp_file.tmp.gz
```

### 5. Read splitting

```
bin/readsplit trimmed2_mysample.fastq.gz > splitted_mysample.fastq.gz
```

### 6. DNA methylation calling

```
bismark --non_directional --local reference_genome_dir splitted_mysample.fastq.gz
```

This command generates a BAM file from which DNA methylation is called
using bismark_methylation_extractor
