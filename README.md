# Mushroom Phylogeny

This lab on Evolutionary Patterns course has been inspired by [this](https://github.com/iirisarri/PEB_Phylogenomics) guide on phylogenomics.

This document will guide you through phylogenetics pipeline—–allowing you to go from a bunch of sequences to a phylogenetic tree that represents how the sequences are related to each other. The phylogenetic analyses will yield a phylogenetic hypothesis in terms of shared history (tree topology), but also in terms of evolutionary change (branch lengths) and other
interesting parameters such as relative substitution rates between loci, base frequencies, relative substitution frequencies (between different bases), etc.

The phylogenomics pipeline can become very complex, many additional steps might be included (particularly at the satage of dataset assembly!) and some analyses can take weeks to complete. Pipelines are modular, meaning they can (and should) be improved, as well as modified to the particular question at hand.

## Objective and data

The objective of this lab is to use the sequence reads that you generated in conjunction with existing data in GenBank/SILVA for phylogenetic analyses of the mushroom group that you were assigned. This practical can be roken down into several steps:
1) Trimming Sanger sequences and assembling contigs
2) Assembling data sets for each locus based on your own sequences (above) and entries in GenBank/SILVA
3) Phylogenetic analyses of separate and combined data sets, using (a) Parsimony and (b) Bayesian methods 
4) Evaluating the results 

## Programs required
1) A simple text editor to view and edit files in plain text format. I recommend [TextWrangler or BBEdit](https://www.barebones.com/products/textwrangler/) for Mac and [Notepad++](https://notepad-plus-plus.org/downloads/) for Windows.
2) [Assseq](https://ormbunkar.se/assseq/downloads/), a programme for processing Sanger sequences
3) [AliView](https://github.com/AliView/AliView), an alignment viewer and editor. You can download it [here](http://www.ormbunkar.se/aliview/downloads/)
4) [Perl](https://www.perl.org/get.html), a programming language.
<details>
<summary>It may already be installed on your computer so check first</summary>
<br>
On Mac/Linux:
  type the following at the command prompt:

```
  perl -v
```
If it is not installed, you will get a message like this: "perl: command not found".
  
On Windows, use the *find* programme to search for perl. Alternatively, type "perl -v" at the command prompt.
</details>
  
5) [PAUP*](http://phylosolutions.com/paup-test/), for doing parsimony analysis
6) MrBayes.

## 1. Trimming Sanger sequences and assembling contigs

The first step is to process the Sanger sequences that you generated. This involves: 
- calling bases/processing a chromatogram 
- trimming poor quality sequence
- merging the forward and reverse read together into a single consensus sequence or contig

We will use [Assseq](https://github.com/andersla/Assseq) for this task which is simple to use. 

Start by downloading your sequences from StudentPortalen (**Documents -> Labs -> Mushroom Phylogeny -> Sanger sequences**). Only download the sequences generated by your group (check these in the Mushrooms&Labelling.xlsx file on StudentPortalen). Next, load the forward and reverse reads in .ab1 format, one sequence at a time in Assseq. Scroll to view the chromatograms. *Do you have good quality sequences?* 

Trim poor quality bases and assemble into a contig (**Assemble -> Reassemble everything with CAP3 external assembler plugin**). You can now edit the consensus sequence (only if necessary) or reverse complement it if it is in the incorrect orientation. Save the consensus sequence (**File -> Save consensus as Fasta**) (be sure to give it a good name - the name of the mushroom and the gene).

*Imp*: Verify that you actually did amplify a mushroom sequence by [BLASTing](https://blast.ncbi.nlm.nih.gov/Blast.cgi) your consensus sequence.


## 2. Assembling datasets for each locus

To build your phylogenies, you will need sequences from other taxa in your assigned group. This is a non-trivial task and it can be time consuming to assemble your dataset. 

There are several good starting points for building your datasets:
1) The NCBI nucleotide database (GenBank)——an open access, annotated collection of all publicly available nucleotide sequences. Use this to find sequences for 28S/LSU, RPB1 and RPB2. The [Taxonomy Browser](https://www.ncbi.nlm.nih.gov/Taxonomy/Browser/wwwtax.cgi) is a good place to start. Simply search for your group of interest and click on the the *Nucleotide* records. 

2) The [SILVA database](https://www.arb-silva.de/) which is a database of ribosomal genes (18S and 28S). 

3) Published papers! Search on [Google Scholar](https://scholar.google.com/) for phylogenetic studies of your mushroom group. This can point you to accession numbers of sequences used in previous studies, and is good for ensuring that you have a good taxon sampling. 

If possible, try to download sequences for the three loci from the same species. It is probable that sequences for one locus will be easier to find. 
**NB: Don't forget to retrieve sequences for the outgroup!**


The output from both the contig assembly (your own sequences) and the sequences you have downloaded from online databases should be stored as individual FASTA files (see below for description of the fasta format). You will have to edit the header to include only the information you are interested in. You will need both the species names, and the accession number for later use (in your report). Make sure that you have not edited any sequences using TextEdit/Microsoft Word etc. wich saves the files as RTF/doc/something else rather than as text files (use TextWrangler/BBEdit/Notepad++ for editing). 

<details>
<summary>What is a fasta file?</summary>
<br>
FASTA format is a text-based format for representing either nucleotide sequences or peptide sequences. A sequence in FASTA format consists of:
- Header: One line starting with a ">" sign, followed by a sequence identification code.
- Sequence line(s): One or more lines containing the sequence itself.
  
Example of 2 sequences in FASTA format:

```
>sequence A
ggtaagtcctctagtacaaacacccccaatattgtgatataattaaaattatattcatat
tctgttgccagaaaaaacacttttaggctatattagagccatcttctttgaagcgttgtc
>sequence B
ggtaagtgctctagtacaaacacccccaatattgtgatataattaaaattatattcatat
tctgttgccagattttacacttttaggctatattagagccatcttctttgaagcgttgtc
tatgcatcgatcgacgactg
```

I recommend NOT having any white spaces in your fasta header as it is likely that programmes you will use downstream will complain.
</details>


## 3. Multiple Sequence Alignment

You will work with the multiple sequence alignments (MSA) in the program **AliView**. This program allows you to manipulate the sequences and the MSA, but does not do automated multiple sequence alignment itself. However, [Muscle](http://www.drive5.com/muscle/) comes packed with AliView and can be called from within the main view, doing the MSA seamlessly. There are many different MSA programs; a commonly used program is [ClustalW](https://www.ebi.ac.uk/Tools/msa/clustalw2/) but it is now dated, and Muscle performs much better. Another commonly used
program that performs well in benchmark studies is [MAFFT](https://mafft.cbrc.jp/alignment/software/); but MAFFT is difficult to pack with other software and has to be installed separately. You could however, use the [online mafft aligner](https://mafft.cbrc.jp/alignment/server/). 

However, no matter which program you use for the alignment it is recommended to check the alignment manually as no program works perfectly.

To do the multiple alignment we first need to combine the individual sequences of each gene into a single file (so three different files in total). This can be done either by adding them one by one in AliView, or to just concatenate (and possibly edit in a text editor) all single sequence files in the terminal/command prompt.

<details>
<summary>On Macs/Linux</summary>
<br>
(Call the lab assisstant if you need help)
Open the terminal and navigate to the folder where your fasta files are present. This can be done by using the command "cd" followed by the path of the folder. For instance, if your fasta files are in the folder "rpb1" which is in the folder "mushroom_phylogeny" on your desktop, type the following:

``` 
cd Desktop/mushroom_phylogeny/rpb1
```

Next, concatenate the sequences into a single fasta file using the command "cat"

```
cat *.fst > rpb1.fasta
```
</details>

<details>
<summary>On Windows</summary>
<br>
(Call the lab assisstant if you need help)
Open the command prompt and navigate to the folder where your fasta files are present. This can be done by using the command "cd" followed by the path of the folder. For instance, if your fasta files are in the folder "rpb1" which is in the folder "mushroom_phylogeny" on your desktop, type the following:

``` 
cd Desktop\mushroom_phylogeny\rpb1
```
(NB: You may need to first change to the correct drive. To access another drive, type the drive's letter, followed by ":". For instance, if you wanted to change the drive from "C:" to "D:", you should type "d:" and then press Enter on your keyboard.)

Next, concatenate the sequences into a single fasta file using the command "type"

```
type *.fst > rpb1.fasta
```
</details>


In AliView, you will then need to:
- Align your sequences. Either use the built-in aligner (Muscle) or use the [online](https://mafft.cbrc.jp/alignment/server/) MAFFT aligner.
- Make sure that they are in the same reading direction (possibly reverse-complementing some of the sequences) and re-doing the alignment.
- Trim sequences to span the same homologous segment (i.e., the one between the primers in your sequence).
- Edit the names of the sequences so that the same species have exactly the same name in each of the three files, and so that the names only contain alphanumerical characters and underscores (one for each gene).
- Save the aligned file.
- Export as NEXUS.

In the end, you should have three FASTA files, one for each gene, and three NEXUS files, one for each gene.



### Creating the concatenated dataset

In addition to analyzing each gene separately, you will also test if the genes can be analyzed as one big data set and you will also actually analyze the combined data set. To do this you need to create a fourth data file that combines the previous three (but you also need to keep those intact...).

There are many ways to concatenate sequences but none really straight forward. The otherwise commonly used software for working with alignments do not have a really easy way to do it, and the alternative is to install an extra program, do it manually, or write a script. 
The problem with doing it manually is that file formats that allow keeping different genes in different blocks also include other information so that the files cannot be directly concatenated. They usually also require that the OTUs are in the same order for all genes, and that all OTUs are present for all genes (possibly only with gaps). In file formats where you cannot have the genes in different blocks you need to add the sequences of each gene to each OTU, and for the OTUs lacking the gene you need to add gaps instead. 
The problem is not too difficult to solve with a script, but that require some scripting skills and writing a script always take some time. However, since this is a common problem, scripts or small command line programs are already available, for example [cat_fasta.pl](https://github.com/mr-y/my_bioinfPerls). So use this or another script/program to concatenate your sequences. cat_fasta.pl only accepts fasta format, so use your fasta formatted files. The cat_fasta.pl also generates a file with the start and end position for each gene. 

*Imp!* Check the output since cat_fasta.pl is very strict in how it interprets names, so any slight difference in the given names to sequences may result in that they are not concatenated but treated as coming from different taxa.

Convert the concatenated alignment file into nexus format using AliView, and edit the sets block to contain the different partitions as charsets e.g.:

```
BEGIN SETS;
charset 28S = 1-703;
charset RPB1 = 704-1307;
charset RPB2 = 1308-1702;
charpartition genes = 28S:28S, RPB1:RPB1, RPB2:RPB2;
END;
```

## 4. Parsimony analysis

There are many different programs to perform parsimony analyses. For very large datasets (>1000 tips) [TNT](http://www.zmuc.dk/public/phylogeny/tnt/) is recommended. Here you will use PAUP* as in the Screwed analysis (except for step 2). PAUP* gives you large control of the analysis, and contain many different methods and tests you can use.

- Do a parsimony analysis with bootstrap for each gene.
- Do a parsimony analysis with bootstrap for the combined data set.
- Do a ILD test (modify and use code snippet ILD-snippet.nxs) on the combined file. The code snippet will perform the ILD run and by including this PAUP block in your nexus file, PAUP will run the analysis automatically when you execute the file with PAUP*. The ILD test is explained as partition homogenity in chapter 9 of the book.
