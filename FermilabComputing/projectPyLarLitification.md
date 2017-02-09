#Project.py: LArLitification

In order to generate LArLite files starting from pre-generated MCC files, you need to use SAM to generate a tag of your query that will then go in the .xml file that you're going to run with project.py.

You basically just need a stage in your .xml file like the following:

```
<stage name="larlite">
  <fcl>larlite_maker_mc.fcl</fcl>
  <!-- <numjobs>&job_n;</numjobs> -->
  <initscript>&grid_dir;/initLarlite.sh</initscript>
  <outdir>&data_dir;/7_larlite</outdir>
  <workdir>&local_dir;/7_larlite</workdir>
  <inputdef>MyDefinition</inputdef>
  <!-- <defname>lar</defname> -->
</stage>
```

Larlitification is a bit complicated. First of all you need a local setup of uboonecode which has been compiled to be able to larlitify (to know more on how to abilitate a uboonecode installation to larlitify check Kazu's video lessons on LArLite). So first make some local tests before going on.

Then you need to compress you localProducts folder in a .tar file, (there's some automatic LArSoft file that does it, `make_tar_uboone.sh`, available after setting up env) which should then go in the GridJob folder.

The .xml file should reference it in the following way:

```
<larsoft>
  <tag>development</tag>
  <local>&grid_dir;/uboonecode_v05_04_00.tar</local>
</larsoft>
```


Also you need to setup LArLite on the grid. In order to do it, create an `init.sh` file which will be sourced on the grid before starting the jobs. The .xml should contain:

```
<initscript>&grid_dir;/initLarlite.sh</initscript>
```

and inside the init file:

```
setup larsoft v05_04_00 -q e9:prof
setup larlite v05_02_00 -q e9:prof
```

Most important: Have your .fcl file in the same folder in which you're launching the .xml file!!!
