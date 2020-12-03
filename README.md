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
6) [jmodeltest2](https://github.com/ddarriba/jmodeltest2), a programme for selection of best-fit models of nucleotide substitution
7) [MrBayes (can be a pain to install)](http://nbisweden.github.io/MrBayes/download.html), for Bayesian analysis
8) [Tracer](https://github.com/beast-dev/tracer/releases), programme to analyze trace files generated by Bayesian MCMC runs 
9) [FigTree](https://github.com/rambaut/figtree/releases), for viewing phylogenetic trees

## 1. Trimming Sanger sequences and assembling contigs

The first step is to process the Sanger sequences that you generated. This involves: 
- calling bases/processing a chromatogram 
- trimming poor quality sequence
- merging the forward and reverse read together into a single consensus sequence or contig

We will use [Assseq](https://github.com/andersla/Assseq) for this task which is simple to use. 

Start by downloading your sequences from Studium. Use the [google sheets page](https://docs.google.com/spreadsheets/d/1nfNAsZtuzicLh_W1bSWl-pBTXshr-b7POu39lvsNn-8/edit#gid=0) to select the sequences you should assemble. Next, load the forward and reverse reads in .ab1 format, one sequence at a time in Assseq. Scroll to view the chromatograms. *Do you have good quality sequences?* 

Trim poor quality bases and assemble into a contig (**Assemble -> Reassemble everything with CAP3 external assembler plugin**). You can now edit the consensus sequence (only if necessary) or reverse complement it if it is in the incorrect orientation. Save the consensus sequence (**File -> Save consensus as Fasta**). 

*Imp*: Verify that you actually did amplify a mushroom sequence by [BLASTing](https://blast.ncbi.nlm.nih.gov/Blast.cgi) your consensus sequence.

Fill in your findings in [google sheets page](https://docs.google.com/spreadsheets/d/1nfNAsZtuzicLh_W1bSWl-pBTXshr-b7POu39lvsNn-8/edit#gid=0)!

## 2. Assembling datasets for each locus

To build your phylogenies, you will need sequences from other taxa in your assigned group. This is a non-trivial task and it can be time consuming to assemble your dataset. 

There are several good starting points for building your datasets:
1) The NCBI nucleotide database (GenBank)——an open access, annotated collection of all publicly available nucleotide sequences. Use this to find sequences for the genes you have selected. The [Taxonomy Browser](https://www.ncbi.nlm.nih.gov/Taxonomy/Browser/wwwtax.cgi) is a good place to start. Simply search for your group of interest and click on the the *Nucleotide* records. 

2) The [SILVA database](https://www.arb-silva.de/) which is a database of ribosomal genes (18S and 28S). 

3) Published papers! Search on [Google Scholar](https://scholar.google.com/) for phylogenetic studies of your mushroom group. This can point you to accession numbers of sequences used in previous studies, and is good for ensuring that you have a good taxon sampling. 

If possible, try to download sequences for the three loci from the same species. It is probable that sequences for one locus will be easier to find. 
**NB: Don't forget to retrieve sequences for the outgroup!**


The sequences you have downloaded from online databases should be stored as individual FASTA files (see below for description of the fasta format). You will have to edit the header to include only the information you are interested in. You will need both the species names, and the accession number for later use (in your report). Make sure that you have not edited any sequences using TextEdit/Microsoft Word etc. wich saves the files as RTF/doc/something else rather than as text files (use TextWrangler/BBEdit/Notepad++ for editing). 

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


[What is a nexus file?](https://en.wikipedia.org/wiki/Nexus_file)


## 4. Parsimony analysis

There are many different programs to perform parsimony analyses. For very large datasets (>1000 tips) [TNT](http://www.zmuc.dk/public/phylogeny/tnt/) is recommended. Here you will use PAUP* as in the Screwed analysis (except for step 2). PAUP* gives you large control of the analysis, and contain many different methods and tests you can use.

- Do a parsimony analysis with bootstrap for each gene.
- Do a parsimony analysis with bootstrap for the combined data set.
- Do an ILD test (modify and use code snippet ILD-snippet.nxs) on the combined file. The code snippet will perform the ILD run and by including this PAUP block in your nexus file, PAUP will run the analysis automatically when you execute the file with PAUP*. The ILD test is explained as partition homogeneity in chapter 9 of the book.

<details>
<summary>What is an ILD test?</summary>
<br>
Very briefly, the Incongruence Length Difference (ILD) test quantifies the conflicts that can occur between sets of characters from different data sources, such as nuclear or mitochondrial DNA sequences, protein sequences, or even morphological traits. It tests whether different genes are "congruent" (low ILD score; high p-value; i.e. contain a similar phylogenetic signal), or "incongruent" (high ILD score; low p-value; i.e. contain different phylogenetic signal and should not be concatenated).   
</details>

<details>
<summary>ILD snippet to add to nexus file of concatenated genes</summary>
<br>
  
```
BEGIN PAUP;
[Start logging to a file called ildtest.log in the current working directory]
log file=ildtest.log;

[Do the ILD test; note that the partition genes must have been defined in your NEXUS file as charpartition = genes, e.g., like the following example]
[ BEGIN SETS;
[         charset 28S = 1-707;                                    ]
[         charset RPB1 = 708-1306;                                ]
[         charset RPB2 = 1307-2791;                               ]
[         charpartition genes = tbr1:tbr1, 16S:16S, RAG1:RAG1;    ]
[ END;                                                            ]

hompart partition= genes nreps=100 / start=stepwise addseq=random nreps=10 savereps=no randomize=addseq rstatus=no hold=1 swap=tbr multrees=yes;

log stop;
END;
```
</details>


## 5. Model test

To do a Maximum Likelihood or Bayesian analysis, we need an explicit and adequate, but not too complex, model. There are several ways (LR-test, AIC, ...) to objectively select a model, which are implemented in jmodeltest2.

Run JModelTest on each of the three genes separately and identify the best model according to the Akaike information criterion (AIC). You do this in JModelTest by:

- Loading the DNA alignments.
- Compute Likelihood scores (default settings work).
- Do AIC calculations.
- Show results in table

Note which model is the preferred one for each gene and what this model means in terms of parameters.



## 6. Bayesian analysis

You will run a Bayesian analysis on the combined data set using the program MrBayes; you have the manual 3.2 available.

You will need to decide on probabilistic model for your data. For this practical you will have separate models for the three partitions, as declared using JModelTest, and also have different average substitution rates for the three partitions, just sharing the same tree topology and relation.

A Bayesian analysis is a bit different compared to the optimality criterion methods like parsimony and maximum likelihood in a few respects:

- You need to specify prior probabilities on all your parameters. It is common to try to use uniform priors, i.e. priors where all possible hypotheses have the same probability. For this project we can settle with the default settings in MrBayes, giving us uninformative priors where possible and reasonable naivë priors for other parameters (like branch length).
- You do not search for an optimal parameter set but explores the parameter space through an infinite Markov-Chain Monte Carlo (mcmc) walk where the state of the parameters are saved every nth generation (command mcmc in MrBayes). In practice we run a number of generations and check if we have done enough (next step). 
- After the first part has been discarded (burn-in) the remaining generations' output can be used to estimate parameter values, including credibility intervals. However, this means that you have to assess the convergence of the parameters (using the software tracer) to decide how many generations to discard as burn-in and if you have enough generations after burnin to adequately estimate the parameter distributions.
- Finally, you need to compute the parameter values from the generations after burn-in (commands sumt and sump in MrBayes).


You will set up a nexus file to run the analysis in MrBayes, test it on your laptop and when it can be launched without problems, save the nexus file and give it to the lab assistant according to her instructions so it can be run on the UPPMAX cluster.

To set up the file, use the code snippet Bayes-snippet.nxs (see dropdown area at end of this section); paste it into your nexus file and modify the lines where necessary. Please also modify the name of the log file that will be generated (from *combined-run.log* to *combined-run-[Mushroom group].log*). You can then run MrBayes from the command line:

```
mb myfile.nxs
```

Several files will be generated, including a general log file (combined-run.log if you stayed with the snippets names), file containing sampled trees (.t) and other parameters (.p). between branch lengths.

<details>
<summary>Bayes-snippet.nxs</summary>
<br>
  
```
BEGIN mrbayes;


[28S_RPB1_RPB2_Mushroom_party. Edit name of log file to include your mushroom clade.]
log start filename=Combined-run.log;


charset 28S = 1-814;
charset RPB1 = 815-1378;
charset RPB2 = 1369-2018;

partition genes = 3: 28S,RPB1,RPB2;

set partition = genes;



[This corresponds to a GTR+Gamma+Inv model for partition 1 and 3. If you settled on another model, change this]
lset applyto=(1,3) nst=6 rates=invgamma;


[This makes the base frequency, rate, gamma and proportion invariant parameters SEPARATE for each partition]
unlink statefreq=(all) revmat=(all) shape=(all) pinvar=(all);

[This lets the different partitions have different average substitution rates]
prset applyto=(all) ratepr=variable;



[This runs a Markov-Chain Monte Carlo simulation for 10 000 000 generations]
mcmc ngen=10000000 nruns=2 printfreq=1000 savebrlens=yes;

sump burnin=2500;
sumt burnin=2500;

quit;
END;
```
</details>

See [this github page](https://gist.github.com/brantfaircloth/895282) to see how to set different models in MrBayes.
See [this wiki page](http://mrbayes.sourceforge.net/wiki/index.php?title=Evolutionary_Models_Implemented_in_MrBayes_3&oldid=5249) to understand the different commands in MrBayes.

Here are two articles to help you better understand the gamma distribution: [article 1](https://link.springer.com/article/10.1007/BF00160154) and [article 2](https://www.sciencedirect.com/science/article/pii/0169534796100410) (article 2 is perhaps easier to understand)



## 7. Evaluate the Bayesian analysis

Before evaluating the results, it is a good idea to look at the MRBAYES block in your nexus file and try to understand what it means...

Running MrBayes will produce several output files, of particular interest are:
- Files with parameters listed every nthgeneration (.p).
- Files with trees (topologies and branch lengths) saved every nth generation (.t).
- A file with statistics on the Markov chain performance (.mcmc).

We have been running MrBayes with two independent, parallel chains, thus generating two sets of .p and .t files. This enable us to compare the two runs and see if they have converged on the same distribution.

We also let MrBayes compute the summary results of the of the chains, in terms of geometric mean values of the parameters (including branch lengths) and consensus tree for topology, producing the files
- Parameter statistics (.pstat)
- Consensus tree with branch lengths (.tre)

Note that we do not really know at this stage that the results in .pstat and .tre are reliable; they are based on the assumption that the 10 000 000 generations we have run are enough and that discarding the first 2 500 000 generations as burn-in is enough. This we have to assess.
Use the program Tracer to open and examine the .p files:
* Look at the traces for each parameter of each (of the two) chain:
  * Have they converged?
  * Is the burn-in adequate?
* Look at the trace for the combined chains, once again examining each parameter. Have they converged on the same distribution?
* Look at the Traces table for the combined chains, and in the ESS (which stands for Effective Sample Size); if they are all above 100 at an adequate burn-in, we have been running the mcmc for enough generations.

In the Trace table in Tracer you also see the estimated values for the parameters. Interpret these in the light of the used model – what do they tell us?

Look in the log file and particularly for the part following “Active parameters”. What are the priors? Which can be considered uninformative?

If the burn-in need to be changed you can re-run the sumt commands in MrBayes with your new burn-in. Be careful not to re-run the mcmc command as you the risk to overwrite your .p and .t files.

Re-running sumt will produce a new .tre file that should be an apropriate estimate of the posterior tree distribution.

Finally use the program FigTree to open the .tre file and look at the tree resulting from the Bayesian analysis. You can use FigTree to make nice looking trees for your report and presentation.
* Root the tree using your designated outgroup (is it possible?).
* Display the posterior probabilities on the branches
* How does the tree compare to the results from the parsimony analysis of the combined data set?

You can export the tree in pdf or svg format and improve the layout further in a vector graphic image editor (e.g. Illustrator or Inkscape).
