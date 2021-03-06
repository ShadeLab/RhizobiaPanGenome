# nifH

264 sequenced Rhizobium genomes were taken from the JGI database. Functional nifH gene viability as a marker for OTU classification was first explored using this database.

The tools I used to isolate, align, and check for OTU groupings were:
- blastn
- mothur
- MEGA
- Custom python script used for formatting

blastn was used to locate the nifH genes on the genomes.

A single query.txt file was created by extracted the contents of all fasta files from the individual genome directories. This is relatively large at 1.7gb and will most likely be excluded from this git directly for the time being. 

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

The end product of this should be two csv files that show what genes were found in what genomes and in what quantities pre and post MEGA alignment and quality/length control. I will also have alignments for all housekeeping genes. This will be done following the same process as done with nifH minus the amino acid conversion and using the scripts in the new jupyter notebook to create the excel sheets. 

Some steps of the nifH will have to be redone as the initial blastnout file converted to fasta has change. My original script the removed all the matches from the blastnout file into a fasta formatted file had issues with reformatting some of the tags. This is also fine as certain components of my original nifH processing were done in a different location and are not contained in this directory. 

## 16srRNA
3 16srRNA fasta files were taken from NCBI for usage as references in blastn
 - >NC_021111.1:13807-15294 Cistanche deserticola chloroplast, complete genome - 1488 bp
 - >NC_015402.1:81741-83236 Ptilidium pulcherrimum chloroplast, complete genome - 1497 bp
 - >NC_000913.3:4166659-4168200 Escherichia coli str. K-12 substr. MG1655, complete genome - 1542 bp

These fastas are all contained in the House/16s/references subdirectory

All other processes were followed as seen in the nifH gene isolation and alignment notes

285 matches were found with blastN. What genomes these matches corresponded with can be seen in the genematches.csv file. Some genomes had 2 matches and one had 6, leading to more matches being found than the number of genomes (264).

The toFasta function was previously appending all matches of a given scaffold into one sequence. I do not believe that this was an issue before as only a maximum of one match per scaffold was found per blast. THe toFasta function has been modified to only take the match with the highest score (the matches are sorted by highest score to lowest score so the first can always be taken). 

## recA
5 recA fasta files were taken from NCBI for usage as references in blastn
 - >NC_018610.1:1038758-1039897 Lactobacillus buchneri CD034, complete genome - 1140 bp (https://www.ncbi.nlm.nih.gov/gene/34324109)
 - >NC_029008.1:20730-21809 Bacillus phage phi4J1, complete genome - 1080 bp (https://www.ncbi.nlm.nih.gov/gene/26648639)
 - >NC_003295.1:596177-597235 Ralstonia solanacearum GMI1000 chromosome complete sequence - 1059 bp (https://www.ncbi.nlm.nih.gov/gene/1219356)
 - >NC\_000915.1:162928-163971 Helicobacter pylori 26695 chromosome, complete genome - 1044 bp (https://www.ncbi.nlm.nih.gov/nuccore/NC_000915.1?report=fasta&from=162928&to=163971)
 - >NZ_CYSI01000007.1:c525521-524469 Mycolicibacterium mucogenicum strain CSUR P2099, whole genome shotgun sequence - 1053 bp (https://www.ncbi.nlm.nih.gov/gene/32359521)

nifH gene isolation and alignment notes were followed with the recA references

246 matches were found with blastn. The distribution of these matches can be seen in genematches.csv

## glnII
Using "glnII" as a gene search term in ncbi did not return many results. Only 4 sequences were tagged with "glnII" but two were complement sequences. I've been avoiding the usage of complements as I'm not sure how they work as references or if I can adjust them to be non-complement. I may come back and use the complement strands later if not many matches are produced by only 2 references. 
 - >NC_004463.1:4607187-4608221 Bradyrhizobium japonicum USDA 110 chromosome, complete genome - 1035bp (https://www.ncbi.nlm.nih.gov/gene/1054220)
 - >NC_005027.1:3850538-3851638 Rhodopirellula baltica SH 1 chromosome, complete genome - 1101bp (https://www.ncbi.nlm.nih.gov/gene/1796405)

218 matches were found with blastn. 

## gltA
4 sequences were used as references
 - >NC_017047.1:3473103-3474386 Rahnella aquatilis HX2, complete genome - 1284bp (https://www.ncbi.nlm.nih.gov/gene/34350111)
 - >NC_005295.2:128215-129462 Ehrlichia ruminantium strain Welgevonden, complete genome - 1248bp (https://www.ncbi.nlm.nih.gov/gene/33058199)
 - >NC_021084.1:763591-764832 Wolbachia endosymbiont of Drosophila simulans wNo, complete genome - 1242bp (https://www.ncbi.nlm.nih.gov/gene/32546691)
 - >NC_017384.1:1767787-1769079 Ketogulonigenium vulgarum WSH-001 chromosome, complete genome - 1293bp (https://www.ncbi.nlm.nih.gov/gene/12374555)

Only 50 matches were found. 

I will not be doing alignments or considering usage for phylogeny. 50 is low. 

## dnaK
6 sequences were used as references
 - >NC_000962.3:419835-421712 Mycobacterium tuberculosis H37Rv, complete genome - 1878bp (https://www.ncbi.nlm.nih.gov/gene/885946)
 - >NC_004350.2:85642-87480 Streptococcus mutans UA159 chromosome, complete genome - 1839bp (https://www.ncbi.nlm.nih.gov/gene/1029666)
 - >NC_008596.1:796766-798634 Mycobacterium smegmatis str. MC2 155 chromosome, complete genome - 1869bp (https://www.ncbi.nlm.nih.gov/gene/4532040)
 - >NC_000853.1:392268-394058 Thermotoga maritima MSB8 chromosome, complete genome - 1791bp (https://www.ncbi.nlm.nih.gov/gene/897332)
 - >NC_002946.2:1390552-1392480 Neisseria gonorrhoeae FA 1090 chromosome, complete genome - 1929bp (https://www.ncbi.nlm.nih.gov/gene/3281746)
 - >NC_000913.3:12163-14079 Escherichia coli str. K-12 substr. MG1655, complete genome - 1917bp (https://www.ncbi.nlm.nih.gov/gene/944750)

140 matches were found.

## rpoA
5 sequences were used as references
 - >NC_038051.1:114353-115297 Gracilaria changii chloroplast, complete genome - 945bp (https://www.ncbi.nlm.nih.gov/gene/37500278)
 - >NC_034803.1:5119-6138 Vernicia fordii chloroplast, complete genome - 1020bp (https://www.ncbi.nlm.nih.gov/gene/32891847)
 - >NC_034644.1:161197-162207 Peucedanum japonicum chloroplast, complete genome - 1011bp (https://www.ncbi.nlm.nih.gov/gene/32879479)
 - >NC_029770.1:181179-182159 Drosera rotundifolia chloroplast, complete genome - 981bp (https://www.ncbi.nlm.nih.gov/gene/27110401)
 - >NC_008289.1:47688-48758 Ostreococcus tauri chloroplast, complete genome - 1070bp (https://www.ncbi.nlm.nih.gov/gene/4238797)

0 matches were found - I will not be doing alignments or considering usage for phylogeny 

# House gene concatenation

A script was ran in the gene_listing notebook to count the number of genomes containing a given set of found genes. These were the results.

 - total: 264
 - rRNA, recA: 238
 - rRNA, glnII: 206
 - rRNA, recA, glnII: 193
 - rRNA, recA, glnII, gltA: 93
 - rRNA, recA, glnII, gltA, dnaK: 12

I will concatenating the 3 genes rRNA, recA, and glnII for up to 193 genomes (some may be removed in the length control/alignment process. 


The geneMatch script in the gene_listing notebook was modified to also produce a table of the scaffold names found to contain genes and their correspondence with their respective genomes. This was then used in another new script "genePresOnly" to remove all genes from the rRNA, recA, and glnII fasta files that did not have the other 2 genes matched to the given genome. The name of the genome was also appended to the beginning of the scaffold tags to allow for easier removal during alignment and concatenation. These files were named with "gpo" (genePresOnly) in place of "out" .fasta. 

The new table with the scaffold names of the matched genes was converted to csv as "nametable.csv"

Before alignment in MEGA, the sequences were flipped using mothur's alignment tool. This autoformats the sequences so a new script was written in the conversions_function notebook to write the original tags over the reformatted tags. This will be used on any fasta file that is to be read into mothur and aligned as they will need to be flipped first.

17 genomes were removed due to one or more of the genes being removed in the alignment process, leaving 176 genomes instead of 193. Lost genomes of the 193 seen in nametable.csv were: 2599185236, 2585427975, 2585427529, 2582581298, 2791355262, 2619619304, 2513237144, 2585428001, 2738541311, 2738541333, 2738541317, 2617270885, 2738541319, 2558860983, 2643221558, 2738541331, and 2738541322. 

Each fasta for each gene was reduced to one gene per genome during MEGA processing. All fastas have 176 tags and are ordered based on corresponding genome. This allowed for easy concatenation into a single fasta "houseConcat.fasta" using a script in the gene listing notebook. This script was not made to be reusable but I may go back later and improve it. This new fasta and the three fastas used to make it were moved into a new subdirectory labeled "phylogeny".

A distance matrix and OTU information for houseConcat.fasta was produced using MEGA and mothur with the same process as seen in the initial nifH processing notes. 

# House genes redo

I used chloroplast genes for 16srRNA blasting as well as other unideal references for other genes. I will be redoing most if not all of "House genes location" with proper correct references. 

A new function in conversion functions notebook "lengthAve" calculates average length of all sequences in a fasta file and allows for percentile control for comparison of old and new fastas produced by better references. 

## 16s rRNA 

 - >NR_074499.2 Rhizobium etli strain CFN 42 16S ribosomal RNA, complete sequence - 1481bp
 - >NR_115872.1 Rhizobium fabae strain CCBAU 33202 16S ribosomal RNA, partial sequence - 1353bp
 - >NR_044774.1 Rhizobium leguminosarum bv. viciae USDA 2370 16S ribosomal RNA, partial sequence - 1423bp
 - >NR_036785.1 Rhizobium gallicum strain R602sp 16S ribosomal RNA, partial sequence - 1474bp

309 hits compared to original 285 hits. 0.3 to 0.95 percentile average length is ~1481 compared to original ~1280. Average length of all sequences is ~1299 compared to original ~1217.

0.3 was chosen as a bottom line as it the bottom 30% contains many outliers, some under 100bp. Top percents don't contain any outliers.

## recA

 - >NC_007761.1:c2423762-2422674 Rhizobium etli CFN 42, complete genome - 1089bp
 - >NC_011369.1:c2005252-2004164 Rhizobium leguminosarum bv. trifolii WSM2304, complete genome - 1089bp
 - >NC_020059.1:c2000543-1999458 Rhizobium tropici CIAT 899, complete genome - 1086bp

263 hits compared to original 246. Average of all lengths is ~1072 compared to original ~877. 0.1 to 0.9 percentiles is ~1073 compared to original ~885. New recAout.fasta has a range of 987 to 1091 while old has a range from 623 to 983 with no outliers in either fasta.

## glnII

I only found one glnII gene belonging to a Rhizobium in ncbi. I found 3 more using uniprot. 
 - >CP006877.1:211936-212976 Rhizobium gallicum bv. gallicum R602, complete genome - 1041bp
 - >ENA|CAA47710|CAA47710.1 Rhizobium leguminosarum glutamate--ammonia ligase - 981 bp
 - >X17523.1 R.meliloti DNA for glutamine synthetase II (GSII) - 990 bp
 - >ENA|AGS22903|AGS22903.1 Rhizobium etli bv. mimosae str. Mim1 glutamine synthetase 2 - 1041bp

272 matches were made compared to the original 218. Average length of all sequences is 989 compared to 981. Average length of 0.2 to 0.95 is 1031 compared to 1003.


## gltA

 - >ENA|AGS21774|AGS21774.1 Rhizobium etli bv. mimosae str. Mim1 citrate synthase 1 - 1290bp
 - >ENA|AAB82405|AAB82405.1 Sinorhizobium meliloti citrate synthase - 1290bp
 - >ENA|AGB71100|AGB71100.1 Rhizobium tropici CIAT 899 citrate (Si)-synthase - 1290bp
 - >ENA|AJD43901|AJD43901.1 Rhizobium gallicum bv. gallicum R602 citrate synthase 2 - 1290bp

271 matches were made compared to original 50. Average length of all is ~1255 compared to original 1118.

## dnaK

 - >ENA|CAA74982|CAA74982.1 Rhizobium leguminosarum hypothetical protein - 1917bp
 - >ENA|AGS20025|AGS20025.1 Rhizobium etli bv. mimosae str. Mim1 molecular chaperone protein DnaK - 1917bp
 - >ENA|AGB69577|AGB69577.1 Rhizobium tropici CIAT 899 chaperone protein DnaK - 1920bp
 - >ENA|AEY82528|AEY82528.1 Rhizobium favelukesii DnaK - 1917bp

264 matches were made compared to original 140. Average length of all is 1902 compared to original 1132. It appears that the original fasta conversion did affect the original dnaKoutOnlyHits to fasta conversion and some others may have been affected. This is another reason to not use the original fastas. 

## rpoA

 - >NC_007761.1:1781361-1782371 Rhizobium etli CFN 42, complete genome - 1011bp
 - >CP017101.1:1672454-1673464 Rhizobium gallicum strain IE4872, complete genome - 1011bp
 - >ENA|KEC75252|KEC75252.1 Rhizobium leguminosarum bv. phaseoli CCGM1 DNA-directed RNA polymerase subunit alpha - 1011bp
 - >ENA|KPH04744|KPH04744.1 Rhizobium acidisoli DNA-directed RNA polymerase subunit alpha - 1011bp
 - >ENA|PDS54494|PDS54494.1 Rhizobium anhuiense DNA-directed RNA polymerase subunit alpha - 1011bp
 - >ENA|PDT00351|PDT00351.1 Rhizobium sp. C5 DNA-directed RNA polymerase subunit alpha - 1011bp
 - >ENA|PCK77361|PCK77361.1 Rhizobium sophoriradicis DNA-directed RNA polymerase subunit alpha - 1011bp

263 matches were made compared to the original 0. Average length of all sequences is 1005bp.

# House gene concatenation redo

All new fasta files were read into a new genome match count and scaffold name table, "newgenematches.csv" and "newnametable.csv"

Count of genomes containing a given set of the 6 available genes:
- total: 264
- rRNA, glnII: 250
- rRNA, glnII, gltA: 250
- rRNA, glnII, gltA, dnaK: 250
- rRNA, glnII, gltA, dnaK, rpoA: 250
- rRNA, recA, glnII, gltA, dnaK, rpoA: 250

All 6 genes can be used with only a loss of 14 from the total of 264 genomes. I expect to lose a greater number of genomes in MEGA processing than before due to the increase of genes but the end genome total should be higher and with 6 genes instead of 3. *gene*out.fasta files were reduced to only genes included in the 250 common genomes. These were labeled as *gene*con.fasta.

Sequences were automatically flipped with mothur, marks were removed with python function removeMarks, formatted tags by mothur were replaced with originalTags function. 




# functional genes

nifH process will be followed for all genes in list ~

## nifH redo

nifH was redone using the same references as before. Some of the conversion functions were modified to preserve tag formatting for usage in creating the gene to genome count tables. The results should be the same. I will be replacing all nifH files with these new ones.

References:
 - >NZ_HF536778.1:c82817-81924 Rhizobium mesoamericanum STM3625, whole genome shotgun sequence - 894bp
 - >ENA|AAA26319|AAA26319.1 Rhizobium phaseoli hypothetical protein - 894bp
 - >ENA|KKZ84372|KKZ84372.1 Rhizobium phaseoli Ch24-10 nitrogenase reductase - 894bp
 - >ENA|ANM26530|ANM26530.1 Rhizobium sp. N941 nitrogenase iron protein NifH 1 - 894bp

108 matches were found

nifH gene to genome information was added to genematches.csv

## nodC

6 nodC references from ncbi were used 
 - >NC_004041.2:c299424-298093 Rhizobium etli CFN 42 plasmid symbiotic plasmid p42d, complete sequence - 1332bp (https://www.ncbi.nlm.nih.gov/gene/24297332)
 - >NC_011368.1:685725-687005 Rhizobium leguminosarum bv. trifolii WSM2304 plasmid pRLG201, complete sequence - 1281bp (https://www.ncbi.nlm.nih.gov/gene/34190652)
 - >NC_020061.1:236925-238283 Rhizobium tropici CIAT 899 plasmid pRtrCIAT899b, complete sequence - 1359bp (https://www.ncbi.nlm.nih.gov/gene/32499773)
 - >NZ_HF536778.1:c155538-154189 Rhizobium mesoamericanum STM3625, whole genome shotgun sequence - 1350bp (https://www.ncbi.nlm.nih.gov/gene/31829151)
 - >NC_010529.1:c310015-308663 Cupriavidus taiwanensis str. LMG19424 plasmid pRALTA, complete genome - 1353bp (https://www.ncbi.nlm.nih.gov/gene/31819335)
 - >NC_010627.1:c490711-489374 Paraburkholderia phymatum STM815 plasmid pBPHY02, complete sequence - 1338bp (https://www.ncbi.nlm.nih.gov/gene/27747179)

98 matches were found

nodC gene to genome information was added to genematches.csv



#4/16/19 update 
Much has been done since last updated but not documented here or pushed to the git repository. 

##MLSa phylogenetic tree creation 
After locating gene matches within the database using improved reference files, it was found the 250 of the 264 genomes were found to have all 6 of the housekeeping genes of interest. After alignment and removal of short sequences in MEGA, it was possible to include all but 16srRNA and still have 240 genomes. With the inclusion of 16srRNA, this number was lowered to below 230 due to many of the 250 sequences having short 16srRNA genes. 

Two MLSas were produced, one with all 6 genes and another without 16srRNA that only had 5 genes but more total genomes. It was intended that phylogenetic trees were to produced with both of these concetanations to see if inclusion of 16srRNA would provide a higher enough level of improvement to confidence that it justified removing 10-20 genomes from the MLSa, but a phylogenetic tree was only produced for the 5 gene MLSa without 16srRNA due to shortness of time before UURAF. 

The 5 gene MLSa was realigned in MEGA before passing it into MEGAs phylolgenetic tree creation. The phylogenetic tree was calculated using the neighbor joining method and was done for 1000 iterations to provide bootstrapping values. 

The tree was exported with bootstrapping and distance values in newick format.

The tree was read into itol for better visualization and annotation. There were 2 genomes on the tree that had a very high relative distance. The genomes for these trees will be looked at later to see if a low quality sequence for a gene in the MLSa is causing higher distance than intended.


Base pair length of each genome as well as marks for full completedness as included in the metadata were annotated onto the phylogeneteic tree. Presence of nifH and nodC as documented in genematches.csv were then placed. Species classification for the plants the sequenced Rhizobium were isolated from was also available in the metadata and was annotated onto the tree. The family classification for the plant species was also provided through a color bar and a corresponding legend (there were only 4 different family classifications). 6 genomes were not present in the metadata so did not have any annotations. These information for these genomes were later located but not in time for inclusion on the phylogenetic tree used on the UURAF poster.


##OTU classification comparison




blastn -db allspeciesref -query fullquery.txt -perc_identity 100 -outfmt 6 -out allspeciesout2.txt

