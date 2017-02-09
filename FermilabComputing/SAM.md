#SAM


##Some useful guides on the web
[SAM guide](https://cdcvs.fnal.gov/redmine/projects/uboonecode/wiki/Getting_to_Swizzled_Data)

[SAM guide (2)](https://cdcvs.fnal.gov/redmine/projects/sqam/wiki/) 

[SAM with Project.py](https://cdcvs.fnal.gov/redmine/projects/larbatch/wiki/User_guide)

##Introduction to SAM
SAM is a data handling system that can point at the location of the data files (real or Monte Carlo) that you need.
Let's say you're looking for `prodgenie_bnb_nu`, just run:

```
samweb list-files "defname: prodgenie_bnb_nu_uboone_mcc7"
```

There are other commands that can refine the search

We then need to get a kerberos certificate:

```
kx509
```
And finally create a "definition":

```
samweb create-definition MyDefinition "defname: prodgenie_bnb_nu_uboone_mcc7"
```

`MyDefinition` can be called whatever you want.

The SAM definition creates a tag of the query that can later be referred to a _project.py_ script running on the grid. You can double check if files are in your definition with:

```
samweb list-definition-files MyDefinition
samweb describe-definition MyDefinition
```

[Mike Kirby suggested to give a unique name to `MyDefinition`, like `prodgenie_bnb_nu_uboone_mcc7_sporzio`  because dataset definition is common and everyone can access it]

To use regexp:

```
samweb list-files "file_name=''%prodgenie_bnb_nu_uboone_%20160309T17%merged%'"
```

If you're looking for good, real data files that have been already tagged just go to: [Data](http://www-microboone.fnal.gov/at_work/AnalysisTools/index.html)

Common commands:

```
samweb list-files
samweb count-files
samweb count-definition-files
samweb create-definition
samweb list-definition-files
samweb delete-definition
```


Maximum number of events from a specific definition:
To get an idea of how many events you can expect you can count the number of files in your definition with:

```
samweb count-definition-files
```

Take a random file from the definition with:

```
samweb list-definition-files
```

Then you can look at the number of events in a file (assuming it's constant for all files) with: 

```
samweb get-metadata filename
```

And multiply those two numbers.


##More sophisticated queries
You can make more sophisicated queries. Let's say you're interested in the specific event 98724 from MCC7 BNB neutrino events. Then you can search the file(s) containing that specific event with:

```
samweb list-files "defname: 'prodgenie_bnb_nu_uboone_mcc7' and first_event<98724 and last_event>98724"
```

You can use other parameters for your search. In order to see the metadata that is associated with a specific file you can do:

```
samweb get-metadata filename
```

Once you get the filename of the file you're interested in, you can get its location with:

```
samweb locate-file filename
```

This gets you only the _PATH TO THE DIRECTORY_ containing the file. You have to append the filename by yourself.
