# Root Notes

## TTree::Draw() notes

TTree::Draw() is a neat way to quickly produce plots from root files. This probably shouldn't be used for any sort of selection, i.e. for extracting diffusion we want to extract hits from tracks which are at a specific angle, and this method isn't great for that. Use the MakeClass for this.

An example TTree::Draw() script:

```c++
void ttreeDrawExample() {
  TTree *t = (TTree*)_file0->Get("analysistree/anatree");
  
  TH1F *h = new TH1F("h", "", 50, 0, 50);

  t->Draw("tBranchName >> h", "some_cut");

  h->Draw();
}
```

## MakeClass notes

The makeclass method for root scripts is, as mentioned above, useful for instances where you want to have a lot of control over cuts in a selection, but note: this is slow. A typical script:
```c++
void makeClassExample() {
  TTree *t = (TTree*)_file0->Get("analysistree/anatree");

  // set up branch addresses
  int branch;
  t->SetBranchAddress("branch_name", &branch);

  short branchArray[n];
  t->SetBranchAddress("branchArray_name", branchArray);

  // define histograms
  TH1F* h = new TH1F("h", "", 50, 0, 50);
  TH1F* hArray = new TH1F("hArray", "", 50, 0, 50);

  // get number of entries for the tree
  int nentries = t->GetEntries();

  // loop over tree entries
  for (Long64_t jentry=0; jentry < nentries; jentry++) {
  
    t->GetEntry(jentry);

    h->Fill(branch_name);
    
    for (int i_branchArray; i_branchArray < branchArraySize; branchArraySize++) {
    
      h->Fill(branchArray_name[i_branchArray]);
    
    }
  
  }

}
```


## Things to remember and error code solutions

### ERROR leaf:%leafName%, len=%someNumber% and max=%someLowerNumber%
Problem is likely that you have some branch which holds an array is overruning in to unallocated memory:
```c++
   const int maxEntries =  1000;
   Float_t someVar[maxEntries];

   t->SetBranchAddress("someVarInTree", "someVar");
```
In this case, if any entry of the someVarInTree is an array of length > 1000, then this can cause the root script to fail in this way. Increasing maxEntries value should fix this problem but note that it doesn't need to be increased to the number indicated by "len" in the error code.
