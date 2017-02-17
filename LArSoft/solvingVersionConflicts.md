#LArSoft: Solving version conflicts

You'll often get errors from `mrbsetenv` caused by incompatibilities between different tools.
The reason for that is the they will try to set up two different versions of the same sub-tool.

There are two ways you can fix this:

* The first one is to go to `packagename/ups/product_deps` and straighten out the incompatibilities. The `mrbsetenv` message will tell you which tools are problematic, so you can just follow the error message and fix from there.
* The second and better solution is to go here: [SciSoft](http://scisoft.fnal.gov/). 
Then `MicroBooNE Distribution/v06_04_00/manifest/uboone-06.04.00-Darwin64bit+14-e10-debug_MANIFEST.txt` (or somewhere else if you are installing some different product). Look for the version of the tool you want to install (e.g. `larsim v06_03_00`), then replace the command you used for building it, with:

```
mrb g -t v06_03_00 larsim
```
* The third and best solution is to run 
```
ups depend uboonecode v06_16_00_01 -q e10:prof
```
with the appropriate version and qualifiers. You can also grep for the package you want i.e:
```
ups depend uboonecode v06_16_00_01 -1 e10:prof | grep larsim
```
which should make things easier.
