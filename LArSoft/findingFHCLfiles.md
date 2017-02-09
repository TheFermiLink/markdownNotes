#LArSoft: Finding FHCL files

One of the very annoying things about LArSoft is that many of the variables you will need to know are defined in other .fcl files included in your .fcl files, and finding those .fcl files is not always straightforward. Most of the times they will be in the job directory living in your uboonecode/dunetcp/lar1ndcode main directory. However, other times, they will be part of the more generic LArSoft suite.

Luckily, ALL the fcl files that can be accessed by LArSoft live here:

```
FHICL_FILE_PATH
```

Which looks like this:

```
.:./job:/cvmfs/uboone.opensciencegrid.org/products/uboonecode/v04_35_00/job:.:./job:/cvmfs/fermilab.opensciencegrid.org/products/larsoft/larpandora/v04_09_04/job:/cvmfs/fermilab.opensciencegrid.org/products/larsoft/larana/v04_17_01/job:/cvmfs/fermilab.opensciencegrid.org/products/larsoft/larreco/v04_29_00/job:/cvmfs/fermilab.opensciencegrid.org/products/larsoft/larexamples/v04_06_13/job:/cvmfs/fermilab.opensciencegrid.org/products/larsoft/lareventdisplay/v04_14_02/job:/cvmfs/fermilab.opensciencegrid.org/products/larsoft/larsim/v04_20_00/job:/cvmfs/fermilab.opensciencegrid.org/products/larsoft/artextensions/v1_02_07/fcl:/cvmfs/fermilab.opensciencegrid.org/products/larsoft/larevt/v04_14_03/job:/cvmfs/fermilab.opensciencegrid.org/products/larsoft/lardata/v04_22_00/job:/cvmfs/fermilab.opensciencegrid.org/products/larsoft/nutools/v1_22_00/fcl:/cvmfs/fermilab.opensciencegrid.org/products/larsoft/larcore/v04_26_00/job
```

Let's take a look at prodsingle_uboone.fcl, which lives in:

```
/cvmfs/uboone.opensciencegrid.org/products/uboonecode/v04_35_00/job
```

```
#include "services_microboone.fcl"
#include "singles_microboone.fcl"
#include "largeantmodules_microboone.fcl"
#include "detsimmodules_microboone.fcl"
#include "triggersim_microboone.fcl"
#include "opticaldetectorsim_microboone.fcl"
#include "mccheatermodules.fcl"
```

I want to know where `singles_microboone.fcl` is. Trying

```
find . -name "*singles_microboone.fcl*"
```

tells me that `singles_microboone.fcl` is also in the job directory.

Looking inside the file:

```
#include "singles.fcl"
```

However,

```
find . -name "*singles.fcl*"
```
doesn't give me a result. It's not there.

Where can it be? Looking at the directories listed in `FHICL_FILE_PATH`, I can see we have the following available:

```
.
uboonecode
larsoft/larpandora
larsoft/larana
larsoft/larreco
larsoft/larexamples
larsoft/lareventdisplay
larsoft/larsim
larsoft/artextensions
larsoft/larevt
larsoft/lardata
larsoft/nutools
larsoft/larcore
```

`larcore`, `lardata` or `larsim` look like good candidates, and infact:

```
find /cvmfs/fermilab.opensciencegrid.org/products/larsoft/larsim/v04_20_00/job  -name "*singles.fcl*"
```

does indeed tell me that the fcl file is there.
