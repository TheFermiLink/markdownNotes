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

One aspect initially confusing to me was the difference between the number of events and the number of jobs. The number of jobs is the total number of times the script will call the .fcl file and communicate with the grid. This translates to the number of output directories and output files you will have. While the number of events is the *total* number of events you will generate. This means that you can calculate the number of events in a given file by simply taking `numevents/numjobs`.

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
If for some reason you need to submit the whole job again, you should either configure the .xml file to make a new repository, or you can get project.py to clean the repository for you by doing
```xml
project.py --xml xml_name.xml --stage stage_name --clean
```

The `--help` command for project.py is also rather useful should you run into further problems.

To monitor your submitted jobs you can use the command `jobsub_q --user=user_name`.
Or to delte your jobs you can do `jobsub_rm --user=user_name`. Sometimes when attempting to remove your jobs, it is also useful to apply the `-G` tag, which tells `jobsub` which experiment you are looking at.

Again you can also use `jobsub --help` for a further list of commands, such as `jobsub_submit` which will allow you to submit jobs to the grid without needing to use project.py. This is especially relevant when running non-LArSoft jobs.

## Things that go wrong...
I've had some issues whereby running several different stages from one xml file results in one of those stages being multiplied numerous times or failing. A use case for this is if you want to process a single detsim files through several reco with several different tweakings, say reco1, reco2, reco3. One work around to stop these failing is to wait until all of the jobs from the reco1 stage are running before you begin running the reco2 stage: they don't have to be completed, just no longer idle.

Alternatively, just don't run from the same fcl...
