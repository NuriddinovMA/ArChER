# C-InterSecture
**C-InterSecture** (*C*omputional tool for *InterS*pecies analysis of genome archit*ecture*) pipline is python 2.7 based utilits to cross-species comparison of Hi-C map. C-InterSecture was designed to liftover contacts between species, compare 3-dimensional organization of defined genomic regions, such as TADs, and analyze statistically of individual contact frequencies.
 
## Prerequisites:
- numpy >= 1.9.0
- scipy >= 1.1.0
- [genome module](https://mirnylab.bitbucket.io/hiclib/_modules/mirnylib/genome.html) from mirnylib package with dependencies (it's minimal required module is included)
- [Juicer Tools](https://github.com/aidenlab/juicer) (to generate .hic-files)

## Installation
There is no need for installation.

## Using C-InterSecture
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
The file `unmapped.ini` includes a space/tab delimited list of genome fasta files and bin sizes (see example):
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
After generation `.unmap`-files, can run the preprocessing:
```
python pre.py < pre.ini
```
Ini-file contains all neaded paramaters to performing (see example). 
The preprocessed contacts (`.initialContacts`-files) are stored using a simple tab-delimited text format:
```
chr1 \ pos1 \ chr2 \ pos2 \ contacts \ min \ max \ coverages1 \ coverages2	\ distances
```
The *min* and *max* columns reflect a range of contact deviation. The *cov1* and *cov2* columns reflect a Hi-C read coverage of contacted bin. The *dist* columns store a genome distance between contacted bins. If distances = -1000, this contact is interchromosomal.

### Contact liftovering
This step requires a pair of `.initialContacts`-files from compared species and a pair of files containing synteny map. 
The synteny files (`.mark`) contain the mapping rules between the compared species as a simple tab-delimited text format:
```
chr_species_1 \ start \ end \ chr_species_2 \ start \ end
```
The synteny map may be generated from `.net`-files [see UCSC pairwise alignments](http://hgdownload.soe.ucsc.edu/downloads.html) using 'net2mark.py' utilty or any others ways:
```python net2mark.py < net.ini```
The file `net.ini` includes a space/tab delimited list of `net.`-files (see example):
```
path_to_folder_containing_net-files 
net1 net2
net3 net4 net5
...
```
After generation `.mark`-files, can run the contact lifovering:
```python lift.py < lift.ini```
Ini-file contains all neaded paramaters to performing (see example).
The pipeline produce **two** `.allContacts`-files with liftovered contacts: species_1 to species_2 and species_2 to species_1.
The lifoveres contacts are stored using a simple tab-delimited text format:
```
chr1_reference \ pos1_reference \ chr2_reference \ pos2_reference \ remap1_query \ remap2_query \ reference_contacts \ query_contacts \ reference_deviations \ query_deviations	\ reference_coverages \ query_coverages	\ query_contact_distances
```

### Postprocessing
This step produces user-friendly ouput.

`python allContacts2hic.py < hic.ini` - generates .hic-files for JuiceBox for full genome comparing, this option requires Juicer Tools;

`python allContacts2plot.py < plot.ini` - generates a heat-map to given region reflected difference between a chromatin architecture of comparing species and statistical significance;

`python allContacts2metric.py < metric.ini` - calcultate a quantitatively estimation of difference between a chromatin architecture of comparing species  and generate coresponded .bedGraph-files.
