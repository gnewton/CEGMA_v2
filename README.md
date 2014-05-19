# CEGMA v2.5 README File #

Released: 2014-05-19

[![DOI 10.5281](https://zenodo.org/badge/4042/KorfLab/CEGMA_v2.png)](https://zenodo.org/deposit/6329/)

### Contents ###

+ A. What's CEGMA ?
+ B. Installing CEGMA
+ C. File Listing
+ D. Compiling CEGMA
+ E. To run CEGMA
+ F. Authors and help
+ G. Citing CEGMA

***************************************

### A. What's CEGMA? ###


CEGMA (Core Eukaryotic Genes Mapping Approach) is a pipeline for building a set
of high reliable set of gene annotations in virtually any eukaryotic genome. The
strategy relies on a simple fact: some highly conserved proteins are encoded in
essentially all eukaryotic genomes. We use the KOGs database to build a set of
these highly conserved ubiquitous proteins. We define a set of 458 core
proteins, and the protocol, CEGMA, to find orthologs of the core proteins in new
genomes and to determine their exon-intron structures.

A local version of CEGMA can be installed on UNIX platforms and it requires
pre-installation of Perl, NCBI BLAST+, HMMER, GeneWise and geneid. The procedure
uses information from the core genes of six model organisms by first using
TBLASTN to identify candidate regions in a new genome. It then proposes and
redefines gene structures using a iterative combination of GeneWise, HMMER and
geneid. The system includes the use of a profile for each core gene to ensure
the reliability of the final predicted gene structure.

CEGMA source code, compiled binaries and documentation are available 
under the GNU GENERAL PUBLIC LICENSE.



***************************************

### B. Installing CEGMA ###


The CEGMA distribution contains several directories and files. Source 
code and documentation files are included in the distribution.

The distribution is archived and compressed in a single file using the
command tar -zcvf. The compressed file name is CEGMA.tar.gz (or
something similar depending on compiled binaries included). The CEGMA
files can be extracted following these instructions:

Type: 

	tar -zxvf cegma_v2.5.tar.gz

After executing these commands, the directory 'cegma_v2.5' will be created 
in your working directory. 

CEGMA needs the pre-installation of the following software:

- [geneid (geneid v1.4)](http://genome.imim.es/software/geneid/)
- [genewise (wise2.2.3-rc7)](http://www.ebi.ac.uk/Wise2/) or [use our copy](http://korflab.ucdavis.edu/Datasets/cegma/wise2.2.3-rc7.tar.gz)
- [hmmer (HMMER v3.0)](http://hmmer.janelia.org/)
- [NCBI BLAST+ (tested using v2.2.29)](ftp://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/)

Note that genewise requires glib to be present for correct installation





***************************************

### C. File Listing ###


The CEGMA distribution contains the following files and directories:

+ `bin/` — The executable scripts
+ `data/` — Core proteins, core profiles and cutoff and generic parameter file for geneid
+ `sample/` — A DNA and protein file which can be used to test CEGMA.
+ `sample_output/` — The results generated by CEGMA when using the included sample datasets. 
+ `src/` — Source code of CEGMA.
+ `GNULicense` — This software is registered under GNU license.
+ `Makefile` — This file is required to build CEGMA executable files.
+ `README.md` — This file.

The CEGMA distribution contains a set of independent programs that are
used by cegma.pl:

+ parsewise - a parser for the genewise outputs.  
+geneid-train and make_paramfile - build a parameter file for geneid

***************************************

### D. Compiling CEGMA ###


Change directory to the CEGMA directory and run the following to compile CEGMA:

	make 

This should generate the CEGMA executable files within the `bin/` subdirectory. Now run:

	cegma -h 

This will test that the main CEGMA file can be executed.


***************************************

### E. To run CEGMA ### 


There are two environmental variables that can be set by users to
their preferences:

+ You must specify the path where CEGMA can find the default files with the 'CEGMA' shell 
variable.
+ CEGMA needs to write few temporary files in a directory with permissions for current user 
to read and write. Default temporary directory path is set to '/tmp' but you can assign a 
different temporary directory path using the variable 'CEGMATMP'.
+ CEGMA uses some custom Perl modules (e.g. FAlite.pm, Cegma.pm). You must set the 
PERL5LIB environment variable to use CEGMA's 'lib' directory *or* you can copy the modules 
to another Perl module directory that is available to your Perl installation.

#### Setting environment variables in the Bourne-Shell (e.g. bash):

	export CEGMA="path"
	export CEGMATMP="path"
	export PERL5LIB="$PERL5LIB:$CEGMA/lib"


#### Setting environment variables in the C-Shell

	setenv CEGMA "path"
	setenv CEGMATMP "path"
	setenv PERL5LIB "$PERL5LIB:$CEGMA/lib"

Genewise will also require that you set the $WISECONFIGDIR environment variable



To run CEGMA using the 458 default proteins type:

	cegma --genome <genomic_fasta_sequence>

If you have multiple cores on your computer, you can speed things up by using the 
`-threads n` option which passes the number of specified threads (specified by 'n') 
to the TBLASTN and hmmsearch programs.

#### TESTING CEGMA

Run the following and compare the final output with the sample files `sample_output/`

	cegma --genome sample.dna --protein sample.prot -o sample


CEGMA generates some intermediate files in the process. The files that contain the final 
predictions, in GFF and the fasta files of the corresponding genome and protein sequences are:

+ sample.cegma.fa               - predicted CEGs proteins
+ sample.cegma.gff              - coordinates in the genomic sequences
+ sample.cegma.id               - KOG ids for the selected proteins
+ sample.cegma.local.dna        - local fragments of DNA containing the genes
+ sample.cegma.local.gff        - coordinates in the local fragments
+ sample.completeness_report    - statistics of the percentages of CEGs
+ sample.cegma.errors           - may contain error messages produced by some programs


#### RUNNING OTHER SETS OF PROTEINS WITH CEGMA ####

If you have a set of proteins that you want to use instead of the KOGs provided by CEGMA, 
you can do that easily. You have to create a HMM profile with HMMER, chose a cutoff for 
each profile and use the following options when running CEGMA:

     -p, --protein     fasta file of the protein sequences.

     --prot_num        Number of proteins per family/profile. 
                       They have to be in consecutive order in the fasta file.
                          (default: 6)
     --cutoff_file     File with the cutoff for the HMMER alignments.
                          (default: \$CEGMA/data/profiles_cutoff.tbl) 
     --hmm_prefix      Each protein ID must have "___" followed by the hmmprefix 
                       and a number (ex. At3g02190___KOG1762).
                          (default: KOG)
     --hmm_directory   Directory that contains the hmm files. The files must be
                       named hmm_prefix(number).hmm  ex. KOG1762.hmm.
                          (default: \$CEGMA/data/hmm_profiles)    
Example:

	cegma  --genome sample.dna --prot_num 4 --protein ORTH.fa --hmm_prefix ORTH \
           --hmm_profiles hmm_profiles/  --cutoff_file profiles_cutoff.tbl
 
For the previous command-line example, you must have 4 proteins per family and the proteins 
must be named protid___ORTH[0-1] (ex:At3g02190___ORTH0001). You must also have a directory 
with the hmm profile for each family name ORTH0001.hmm.


***************************************

### F. Authors and help ###


CEGMA has been written by Genis Parra (formerly at UC Davis Genome Center) and subsequently 
updated and maintained by Keith Bradnam (krbradnam@ucdavis.edu).

CEGMA home page is at http://korflab.ucdavis.edu/Datasets/cegma/

A FAQ with answers to common questions is also available: http://korflab.ucdavis.edu/Datasets/cegma/faq.html



***************************************

### G. Citing CEGMA ###


CEGMA was first published in 2007 as a tool to train genefinders in novel genomes. In 2009
we adapted it to be able to estimate the completeness of the gene space in draft genomes,
a measure often used as a proxy for assessing the completeness of a genome or 
transcriptome assembly. CEGMA can be cited by referring to these two papers:

+ [Genis Parra, Keith Bradnam and Ian Korf. CEGMA: a pipeline to accurately annotate core genes 
in eukaryotic genomes. Bioinformatics, 23: 1061-1067 (2007)](http://bioinformatics.oxfordjournals.org/content/23/9/1061.long)
+ [Genis Parra, Keith Bradnam, Zemin Ning, Thomas Keane, and Ian Korf. Assessing the gene space 
in draft genomes. Nucleic Acids Research, 37(1): 298-297 (2009)](http://bioinformatics.oxfordjournals.org/content/23/9/1061.long)
