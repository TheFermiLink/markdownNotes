#Project.py: TextFileGen

First thing you need is a series of text files in the hepevt format, each containing your events, and a list of the paths to those files. If you have a big text file with thousands of hepevt events, what you want to do is to break down that file into many little files, each with 20 or so events (each file will be used for a job, so you don't want the number of events per file to be too high).

You'll also need a file, _fileList.list_, which contains strings representing the paths to your text files.
The list file will need to be in the same directory with the .xml setting file.

After doing that, you need to build LArSoft.
Keep in mind that _textfilegen_ will need to have larsim installed as well. You need to be careful about the tagged version you use for the install.

You then need to place manually your `custom_textfilegen.fcl` in `srcs/uboonecode/fcl/gen/other`.

Run the following commands:

```
source localProducts_larsoft_v06_18_00_e10_prof/setup
cd build_slf6.x86_64
mrbsetenv
mrb i -j8
```

If you have any other custom made fcl file, like a custom `standard_ana_uboone.fcl` containing the labels to the dataproducts that you want to analyze, you may also want to place it in the source directory before building larsoft again. That way your .fcl files will end up in the local installation directory.

After building (and possibly testing interactively everything), you now need a tarred version of your local larsoft installation. Once you have sourced your local larsoft, all you need to do is to go on the main larsoft directory and run:

```
make_tar_uboone.sh -d localProducts_larsoft_v06_18_01_e10_prof v06_18_01.tar
```

Your version may be a different one. Change accordingly.
Now place also the .tar file in the GridJob directory.

Last thing you'll need is a .xml configuration file. 

To repeat again, your GridJob directory needs to contain:

* configFile.xml
* fileList.list
* larsoft_vxx_xx_xx.tar
