# Parsimony analysis
PAUP\* (pronounced Pop-Star) can be used for phylogenetic analysis under the parsimony criteria (and ML which the \* indicate). Here the instructions are on the command line way of doing things, but if you have the GUI version you can do it the same way through menue options.
First thing you need to do is to load your data file; **exe FileName.nex** (Menu option is File->Open then browse to select file, and click the Execute option instead of Edit).

## Make a point estimate - Search for most parsimonious trees
We will use the heuristic search method. Learn about this by typing **hsearch** or **hs**.
1. Do a heuristic search; you can use the default branch swaping method TBR: **hs addseq=random nreps=100**. This means it will add sequences randomly,
instead of the order you have them in the file. The default number of repetitions for the search is 10, but this isn't enough, so we'll increase it to 1001.
You may get this warning: !
-Max trees has been reached, increase trees? **Yes**
!-Enter new value **1000**
!-Action if limit is hit? Change to (**2**) Automatically increase by 100.!
Note: If your search isn't finished within ten minutes and you see that the
number of trees saved are constantly increasing as are the trees remaining to
swap, then go to #6 below.
2. Look at your tree using the command **describetree**. The tree you see is not
rooted as you would expect. What does this mean? How did this happen?
3. If you want, you can designate your **outgroup**. Use the **describetree** command
again - your tree is now rooted. It is not critical that you root your tree in Paup,
but it will make looking at your tree easier. Most tree-viewing programs will
easily re-root a tree once you open it, so you can always root it at a later time.
4. Save trees. **Savetree file=NameItSomethingGood.tre brlens=yes**
How many trees were saved? Why is it (probably) more than one tree? Brlens
means branch lengths. You want to keep these.
5. To see a strict consensus tree of your many trees, type in: **contree / strict=yes treefile=NameThisSomethingElseConsensus.tre**
What does this tell you? Why does the consensus tree not have branch
lengths?
6. If your analyses doesn't seem to finish within some appropriate amount of
time, you may want to redo the analyses with other settings.
    a. Stop your search by quitting PAUP.
    b. restart PAUP and execute your file again.
    c. Restrict your search by not keeping all trees found. One way of doing this is to restrict the number of trees saved in each replicate.
    d. Type **hsearch addseq=random nreps=100 nchuck=10 chuckscore=1** (this means that no more than 10 trees of score (length) greater than or equal to 1 will be saved in each replicate). This will be
    a rather quick & dirty analysis but will work OK for your purposes.!
    e. Remember to pick up at steps #4 and #5 to save your trees as well asconsensus tree!

## Get an estimate of the precision of the point estimate - calculate bootstrap support
1. Now you will calculate your bootstrap support: **bootstrap nreps=500 search=heuristic /addseq=random nreps=10**
2. If this seems to take a long time, try this:
    a. **bootstrap nreps=500 search=heuristic / addseq=random nreps=1 multrees=no**
3. If this still seems to take a long time - talk to your teacher.
4. Now save your consensus tree with bootstrap support values: **savetrees file=filename.tre from=1 to=1 savebootp=nodelabels**

