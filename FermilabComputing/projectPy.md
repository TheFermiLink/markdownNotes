# Project.py example file

Project.py  is a python script that should make grid submission easy. You write an `xml` file and hand that some fcl files and input/ouput directories and then running the script begins running the jobs automatically. It also comes with some hand checking features to ensure the files aren't corrupt.

## The xml file

```xml
<!-- project details -->
<!DOCTYPE project [
<!ENTITY release "v04_36_01">
<!ENTITY name "exampleName">
<!ENTITY user "alister1">
]>

<project name="&name;">

  <!-- group -->
  <group>uboone</group>
    
  <!-- project size -->
  <numevents>1000</numevents>
        
  <!-- batchOS -->
  <OS>SL6</OS>
            
  <!-- larsoft info -->
  <larsoft>
    <tag>&release;</tag>
    <qual>e9:prof</qual>
    <local>/pnfs/uboone/scratch/users/alister1/...</local>
  </larsoft>
                                
  <!-- gen -->
  <stage name = "stage_name">
    <fcl>/uboone/app/users/alister1/...</fcl>
    <workdir>/uboone/app/users/alister1/...</workdir>
    <outdir>/pnfs/uboone/scratch/users/alister1/...</outdir>
    <logdir>/pnfs/uboone/scratch/users/alister1/...</logdir>
    <numjobs>100</numjobs>
    <!-- optional -->
    <memory>2000</memory>  
    <inputlist>path/to/input/list</inputlist> 
  </stage>
                                                            
</project>
``` 
## How to run

```xml
project.py --xml xml_name.xml --stage stage_name --submit
```
and when that has finished, run 
```xml
project.py --xml xml_name.xml --stage stage_name --check 
```
note that for any process which outputs an anatree file, `--check` should be replaced with `--checkana`. This will tell you if any processes returned errors. If they did then you can automatically regenerate them:
```xml
project.py --xml xml_name.xml --stage stage_name --makeup
```

## Things that go wrong...
I've had some issues whereby running several different stages from one xml file results in one of those stages being multiplied numerous times or failing. A use case for this is if you want to process a single detsim files through several reco with several different tweakings, say reco1, reco2, reco3. One work around to stop these failing is to wait until all of the jobs from the reco1 stage are running before you begin running the reco2 stage: they don't have to be completed, just no longer idle.

Alternatively, just don't run from the same fcl...
