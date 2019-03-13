# NIFH

264 sequenced Rhizobium genomes were taken from the JGI database. Functional nifH gene viability as a marker for OTU classification was first explored using this database.

The tools I used to isolate, align, and check for OTU groupings were:
- blastn
- mothur
- MEGA
- Custom python script used for formatting

blastn was used to locate the nifH genes on the genomes.

A single query.txt file was created by extracted the contents of all fasta files from the individual genome directories.

"toQuery(RJGI,query.txt,folderpos=0)"
Where RJGI is the greater directory containing all genomic data, query.txt is an empty text file, and folderpos is set equal to the position of the .fna files within the directories(in my case at the very top) - this could be further improved to automatically target .fna files and not require the folderpos parameter, but it works as is.

8 reference nifH genes from NCBI were concatenated into a single .fna file

"toDb(refs,ref.fna)" - refs - repository with individual reference fasta files, ref.fna - empty files

blastn can be used with the full query.txt and ref.fna files.

"makeblastdb -in ref.fna -dbtype nucl -out ref"

"blastn -db ref -query query.txt -out nifHout.txt"

 blastn has different options for out file formatting as well as options for score cutoffs but I used default settings.

 "blastnCount('nifHout.txt')" returns the total number of found matches
 - 100 for nifH

 "onlyHits('nifHout.txt','nifHoutOH.txt')" moves only the information of matches from nifHout.txt to an empty file nifHoutOH

 "toFasta('nifHoutOH.txt','nifHout.fasta')" isolates the headers and the aligned nucleotides of the matched scaffolds into a fasta formatted file.

 mothur is used for its ability to flip sequences to their reverse complements while making alignments.

 mothur uses only one reference when making alignments so all but one nifH gene sequence was removed form the ref.fna file.

 "mothur > align.seqs(candidate=nifHout.fasta, template=ref.fna, flip=t)"

 The produced nifHout.align file has dots and dashes removed before being passed into MEGA for realignment.

 "removeMarks('nifHout.align','nifHflipped.fasta')"

 nifHflipped.fasta was aligned in MEGA using muscle alignment with default settings.

 These nucleotide sequences were converted to amino acids.

 Ends of the nucleotide sequences were manually trimmed at ~ and ~ BP

 Amino acids were trimmed at ~ and ~

 Distance matrices were created for the aligned and trimmed nucleotide and amino acid sequences and were saved in csv format.

 The csv formatting of MEGA's phylip matrix output is similar to the format that mothur can use with the only difference being tab spacing instead of comma spacing.

 "phylipFormat(inmatrix.csv,outmatrix.dist)" - my out matrices were phylipNuc.dist and phylipAa.dist for nucleotides and amino acids respectively.

Running
 "mothur > cluster(phylip=”phylipNuc.dist”, method=average, precision=100, cutoff=10)"

 "mothur > cluster(phylip="phylipAa.dist", method = average, precision=100 ,cutoff=0.2)"

 produces .an.saboud files that show the number of OTUs that result from varying percent similarity thresholds.

__________________________________________________________________________________________

# House genes location

This will be the first step in creating a phylogenetic tree.

The housekeeping genes of rRNA, recA, glnII, gltA, dnaK, and rpoA will be isolated and aligned from the Rhizobium genomes in a similar manner that nifH was isolataed and aligned.

With the introduction of more genes, I will need to start documenting which genomes possess what genes. 

jupyter notebook "gene listing.ipynb" was written. This contains script that groups partial genome listings within original fasta genome files with the name of the full genome. This allows for tracing back of matches made by blastN to original genome folder as conversion of fasta files to the full query txt file removed this grouping. This file was saved as "scaftable.csv". This csv file had to be reformatted and the conversion of the blastn to fasta sctipt in conversion functions.ipynb had to be changed so they would have identical tags. With idential tags, a script "gene match" was written that can take blastnout.fasta files and automatically fill a table with what genomes have how many matches with what genes. This table will be filled out with all 6 housekeeping genes and their blastn matches as well as the nifH matches. 

Additional scripts will be later added to the gene listng notebook that will allow for post mega alignment fasta files to be used in counting gene matches. This will be useful as some fasta files will be removed due to shortness or quality in the MEGA alignment process. As MEGA reformats tags, another script will have to be written that associates MEGA reformated tags to the tags in the blastnout.fasta files. 

The end product of this should be two csv files that show what genes were found in what genomes and in what quantities pre and post MEGA alignment and quality/length control. I will also have alignments for all housekeeping genes. This will be done following the same process as done with nifH and using the scripts in the new jupyter notebook to create the excel sheets. 

Some steps of the nifH will have to be redone as the initial blastnout file converted to fasta has change. My original script the removed all the matches from the blastnout file into a fasta formatted file had issues with reformatting some of the tags. This is also fine as certain components of my original nifH processing were done in a different location and are not contained in this directory. 

## 16srRNA
3 16srRNA fasta files were taken from NCBI for usage as references in blastn
 - >NC_021111.1:13807-15294 Cistanche deserticola chloroplast, complete genome - 1488 bp
 - >NC_015402.1:81741-83236 Ptilidium pulcherrimum chloroplast, complete genome - 1497 bp
 - >NC_000913.3:4166659-4168200 Escherichia coli str. K-12 substr. MG1655, complete genome - 1542 bp

These fastas are all contained in the House/16s/references subdirectory

All other processes were followed as seen in the nifH gene isolation and alignment notes

285 matches were found with blastN. What genomes these matches corresponded with can be seen in the genematches.csv file.

## 

