## C-InterSecture
**C-InterSecture** (*C*omputional tool for *InterS*pecies analysis of genome archit*ecture*) pipline is python 2.7 based utilits to cross-species comparison of Hi-C map. C-InterSecture was designed to liftover contacts between species, compare 3-dimensional organization of defined genomic regions, such as TADs, and analyze statistically of individual contact frequencies.
 
# Prerequisites:
- numpy >= 1.9.0
- scipy >= 1.1.0
- [genome module](https://mirnylab.bitbucket.io/hiclib/_modules/mirnylib/genome.html) from mirnylib package with dependencies (it's minimal required module is included)

# Installation
There is no need for installation.

# Using C-InterSecture
C-InterSecture pipeline involves three step: data preprocessing, contact liftovering and, finally, visualization. Scripts for each step are placed in special folder. 

### Data preprocessing
The data preprocessing includes contacts filtration, statistical analysis and equalization. This step requires genome fasta-files, bin coordinates, raw and normalized contact matrices outputed by HiC-Pro. The corresponding scripts are located in "0_preprocessing" folder.
The preproccessing requires two annotation files: the chromosome order file and N-bases bin coverage. The chromosome order file contains a list of chromosomes with any additional information (for example, chromosome sizes):
```
chr1 something
chr2 something
...
```
THe N-bases bin coverage files are generated by the `unmappedBins.py` utility taking genome fasta-file. 
```
python unmappedBins.py < unmap.ini
```
The file `unmapped.ini` includes a space/tab delimited list of genome fasta files and bin sizes:
```
# any comments
path_to_sp1.fa resolution1 resolution2
path_to_sp2.fa resolution1 resolution2 resolution3
...
```
Output files are (and must been) placed with genome file and named as `geneme_file_name.resolution.unmap`. The N-bases bin coverage are stored as bed-liked format:
```
chrName bin_start bin_end N-bases coverage
```
After generation `.unmap`-files, can run preprocessing
```
python pre.py < pre.ini
```
Ini-file contains all neaded paramaters to performing:

**genome** - the path to genome fast file, folder with it must contains `.unmap`-files generated for given bin_sizes

**chrom_order** - the path to file contained chromosome order

**sample_name** - the name of sample, it are used to generate out-files names.

**genome_bins** - the file with genome coordinate of bins been generated by HiC-Pro

**raw_contacts** - the path to raw matrix

**norm_contacts** - the path to normalized matrix

**statistic** - either *equ* or *prc*, use *equ* to produce equalized contact frequencies, use *prc* to produce contact percentile score (default - *prc*)

**resolution** - the hi-c map resolution / bin size of data (default - 10000)

**unmapped_bases** - the threshold of % N-bases into a bin; if the bins having N-bases coverage > threshold are discarded (default - 33)

**coverage** - the threshold of bin coverage by Hi-C reads; the given % bins with lowest coverage are discarded (default - 1)

**max_distance** - the max genome distance between contacted bins to calculate independent contact frequency distribytion (default - 5)

**out_folder** - the path to output folder

The output files are named as: *out_folder*/*sample_name*.*statistic*.*resolution*kb.*unmapped_bases*N.*covarage*C.*max_distance*Mb.initialContacts
The preprocessed contacts are stored using a simple tab-delimited text format:
```chr1	/ pos1	/ chr2/ 	pos2 /	contact	/ min /	max /	cov1 /	cov2	/ dist```
The *min* and *max* columns reflect a range of contact dispersion. The *cov1* and *cov2* columns reflect a Hi-C read coverage of contacted bin. The *dist* columns store a genome distance between contacted bins. If dist = -1000, this contact is interchromosomal.

