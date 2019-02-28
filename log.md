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

 mothur used only one reference when making alignments so all but one nifH gene sequence was removed form the ref.fna file.

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
