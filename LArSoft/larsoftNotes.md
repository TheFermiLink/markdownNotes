# LArSoft Notes

## LArSoft Basics
### Setting up LArSoft
To set up uboonecode on the uboone GPVMs first need to run the setup script
```bash
source /grid/fermiapp/products/uboone/setup_uboone.sh
```
You can then grep for a particular version of uboonecode using `ups list -aK+ uboonecode | grep vXX_XX_XX`, and setup the version you want with appropriate qualifiers:
```bash
setup uboonecode vXX_XX_XX -q {some qualifier}
```
These are a bitch to remember so I've got an alias to do only the sourceing `srcub`, and the full setup `srcuboone vXX_XX_XX -q {some qualifier}`.

The ups service can sometimes be difficult to navigate - especially when you have any type of version conflics. You can always type `ups active` to check which products you have currently setup.

From here, you can run the version of LArSoft you've set up by using the command
```bash
lar -c some_fcl_file.fcl ...
```

### building a local copy of LArSoft
After the previous commands, cd to somewhere in your app directory and make a new directory. cd into it and type the following instructions
```bash
mrb newDev # sets up a build, srcs and localproducts directory
source localProducts_larsoft_.../setup
cd srcs
mrb g -t vXX_XX_XX uboonecode
```
You can also use this to pull down other products, such as larpandora:
```bash
mrb g -t vXX_XX_XX larpandora
```
but note the version number doesn't necessarily correspond to the uboonecode version. This is why the `-t` tag is important - this fetches you a tagged release. You can find the corresponding tagged releases needed in the larsoft release notes, found on [here](https://cdcvs.fnal.gov/redmine/projects/larsoft/wiki/LArSoft_release_list). Now cd to your build directory and run
```bash
mrbsetenv
mrb i -j4
```
to install. You can also run `make install -j4` if the above steps have previously been followed and there are no new local packages. The `-j4` tag correpsonds to the number of cores used during compilation (using only one core will be very slow). You should now run `mrbslp` from the base directory to setp up local products and so the code can be run.

If for some reason your installation of larsoft is no longer building due to changes or incompatible packages, you can always perform a "make clean" by typing `mrb z`. Following this make sure to set your environmental variables again using `mrbsetenv`, and then `mrb i -j4`.

#### Setting up using ninja
You can build uboonecode with the ninja flag to make compiling go significantly faster (after initial compilation). This is not setup bu default with uboonecode so you need to set up the UPS product yourself.
You can check for versions of ninja:
```bash
ups list -aK+ ninja
```
and then to setup, just type
```bash
setup ninja vX_XX_XX 
```
note: this has no qualifiers.

Then, when building larsoft, you should run:
```bash
mrb i -j4 --generator ninja
```

### Making a new branch and developing
Go to `srcs/<package>` i.e `srcs/uboonecode` and create your own branch to begin development
```bash
git flow feature start $USER_testFeature
```
then you can use standard git commands to develop, i.e. if you've made a change to `uboonecode/uboone/DetSim/SimWireMicroboone_module.cc`, then go: 
```bash
git add uboonecode/uboone/DetSim/SimWireMicroBooNE_module.cc
git commit -m "made a change to noise modeling"
```
You should also occasionally update your feature branch with changes from develop:
```bash
git fetch origin
git rebase origin/develop # this will merge any changes from origin develop in to your local code
```
From here, you can merge your changes back in to origin develop by
```bash
git flow feature finish # merges feature branch in to your develop branch
git push origin master
```
## Things to remember and error code solutions

**Header files** Header files have changed location between LArSoft version 5 and LArSoft version 6. Typically what this means is that files of type `lardata/XXX.h` has changed to `lardataobj/XXX.h`, although this isn't always true and varies on a case to case basis.

## Specifics of fcl

### Production fcl parameters
#### v04_XX_XX ...
##### Generation
Several fcl files. For example look for fcl files with "prod" in the name.

##### G4
Using `standard_g4_uboone.fcl`
Transverse and longitudingal diffusion, as well as electron lifetime are set at this stage:
```
services.user.LArProperties.Electronlifetime: 10e3 #in microseconds
services.user.LArG4Parameters.TransverseDiffusion: 0.0 #cm2/mus
services.user.LArG4Parameters.LongitudinalDiffusion: 6.2e-9 #cm2/mus
```
Using `standard_detsim_uboone.fcl`
##### DetSim
Turn on data driven noise model (only available with custom built code)
```
physics.producers.daq.GenNoise: 3 # 0 = no noise, 1 = time domain noise, 2 = freq domain noise 
```

##### Reco1
Using `reco_uboone_mcc7_driver_stage1.fcl`
Turn on new Gaussian filter (available post v05_08_00)
```
services.SignalShapingServiceMicroBooNE.FilterFuncVec:  [ "(x>0.0)*gaus", "(x>0.0)*gaus", "(x>0.0)*gaus" ]
services.SignalShapingServiceMicroBooNE.FilterParamsVec:   [ [ 1.0, 0.0, 0.1 ], [ 1.0, 0.0, 0.1 ], [ 1.0, 0.0, 0.1 ] ] 
services.SignalShapingServiceMicroBooNE.FilterWidthCorrectionFactor: [ 0.738, 0.738, 0.738]
```
Turn noise processing off
```
physics.producers.digitfilter.ProcessNoise: false
physics.producers.digitfilter.FFTAlg.TransformViewVec: [false, false, false]
physics.producers.digitfilter.FFTAlg.ZigZagCorrectVec: [false, false, false]

```

##### Reco2
Using `reco_uboone_mcc7_driver_stage2.fcl`

#### v06_16_00

##### Generation
Several fcl files, for instance look for fcl files with "prod" in name.

##### G4
Using `standard_g4_uboone.fcl`.
```
services.DetectorPropertiesService.Electronlifetime: 10.0e3 # in microseconds
services.LArG4Parameters.TransverseDiffusion:0.0
services.LArG4Parameters.LongitudinalDiffusion:6.2e-9
```
##### DetSim
Using `standard_detsim_uboone.fcl`
```
physics.producers.daq.GenNoise: 3 # 0 = no noise, 1 = time dom noise, 2 = freq dom noise. 3 by default
```

Can also use `standard_detsim_ddr_uboone.fcl` to run with the data driven response function turned on but note this will seg fault if you run a sample with no signal (i.e. noise only) due to a lack of hits.

### Reco1
Using `reco_uboone_mcc8_driver_stage1.fcl`
Noise filtering is turned off by default in mcc8 because of the data driven noise model

To modify the deconvolution filter width parameter, add this line to the driver fcl file.
```
services.SignalShapingServiceMicroBooNE.FilterWidthCorrectionFactor: [ 0.5, 0.5, 0.5 ]
```
### Reco2
Using `reco_uboone_mcc8_driver_stage2.fcl`

##Compiled List of Useful Files for LArSoft

For generating exclusively cosmic ray samples involving the whole cosmic ray flux spectrum consider using this .fcl file: `prodcosmics_corsika_cmc_uboone.fcl`. There are a non-trivial number of cosmic generation .fcl files, and this one was recommended by the Production Group. NOTE: Be sure to `kinit` during your gpvm session before running the .fcl file, as the cosmic simulation input files are acessed via ifdh.

## Updating a Feature Branch to a new LArSoft version

# AS YET UNTESTED!

```bash
git stash
git checkout develop
git pull
git checkout feature/$USER_featurebranch
git merge develop
git stash pop
```
