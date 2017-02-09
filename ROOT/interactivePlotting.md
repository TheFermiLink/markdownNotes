#ROOT: Interactive plotting

##Guide on how to explore TTrees interactively.

With a TTree (such as "experiment.root"), what you want to do is the following:

```
root experiment.root
.ls
tdirectory1->cd()
```
(if there are directories)

Once you are in the TDirectory with your TTrees, you can:

```
ttree1->Scan()
```

Which lets you see row-to-row information for each branch.
You can select also certain branches with:

```
ttree1->Scan("event:pdg:pot")
```

Another useful command is:

```
ttree1->Print()
```
Which lets you see all the branches in the TTree and their type.

Finally, you can draw plots with:

```
ttree1->Draw("ebeam") // Or whatever branch is inside the tree
ttree1->Draw("ebeam:px") // To plot a 2D scatter plot
ttree1->Draw("ebeam:px", "chi2>0.5 && chi2<2", "LEGO") // To apply a cut
```