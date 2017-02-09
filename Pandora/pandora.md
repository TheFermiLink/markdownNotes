#Pandora

##Running Pandora EVD within LArSoft and save files to output
In order to do this, you just need to run a .fcl file which uses the `PandoraNu` and `PandoraWriter` modules (for visualization and writing, respectively).

All you need to do is to look for the line

```
physics.reco: [ pandoraNu, pandoraWriter ]
```

and make sure that pandoraNu and pandoraWriter are inserted.

You have to specify some settings (look at Cambridge slides for more info) like the .xml configuration files for the two modules and then run the reco2 files with the modified .fcl file.

The .xml files will allow you to set the outputs that you can then import in the stand-alone pandora. The outputs can be in .pndr or .xml format (but .pndr won't work on mac)


New algorithm:
Adding a new algorithm is really simple, all you need to do is:

```
python CreateNewAlgorithm.py --name xxx
```

It then works a bit like LArLite (variables go in xxx.h, development in xxx.cc)

A couple of things to remember:

* You need to import the header file to your new algorithm in Test/PandoraWorkshop.cc with: `#include "workshopcontent/Algorithms/xxx.h"`
* You also have to register the algorithm in Test/PandoraWorkshop.cc, by adding a line like: `PANDORA_THROW_RESULT_IF(PandoraApi::RegisterAlgorithmFactory(...xxx...)).`
* Finally you need to add the actual algorithm in the setting file 
`scripts/PandoraSettings_Workshop.xml` with variable instantiation:
`- <algorithm type = "xxx"/>`

You can run the algorithm with:

```
$MY_TEST_AREA/WorkshopContent/bin/PandoraWorkshop -i $MY_TEST_AREA/WorkshopContent/scripts/PandoraSettings_Workshop.xml -n 10
```

Declaration can either go in the header file or in the .cc file. John says that if it goes in the header file, the inline is the best place where to put it.

Your new functions are usually declared in the header file in private, along with your new variables.
Function execution goes in .cc file.


##Pandora APIs:
Pandora APIs are clever functions which lets you access (or modify!) list of objects in Pandora (such as MCParticles or CaloHits). You can find them in `PandoraPFA/PandoraSDK-v02-03-00/include/Api/PandoraContentApi.h.`

All calls MUST BE wrapped in preprocessor macro `PANDORA_RETURN_RESULT_IF()`.

Objects are often unordered, which means you have to sort them with a `LArClusterHelper` (or `LArMCParticleHelper`, or whatever) function

You can also use APIs to visualize specific events on the EVE. The functions are in `PandoraPFA/PandoraMonitoring-v02-03-00/include/PandoraMonitoringApi.h`
