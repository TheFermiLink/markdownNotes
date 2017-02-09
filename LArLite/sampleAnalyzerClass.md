#LArLite: Sample analyzer class

You should first start by actually setting up LArLite.

```
source ${HOME}/LArLite/config/setup_seltool.sh
source ${HOME}/LArLite/config/setup_reco.sh
source ${HOME}/LArLite/config/setup.sh
maketop -j 4
```

You can now move to `UserDev` and generate your repository with:

```
gen_repository xxx
```

You then want to make your own package:

```
gen_package xxx
```

And finally you can make your own analyzer class with:

```
gen_class_anaunit xxx
```

At this point you can write your analysis code in:

```
xxx.h
xxx.cxx
```

You can now move to `mac/example_anaunit.py` where you need to change this one line:

```
my_proc.add_process(fmwk.ana_base()) -> my_proc.add_process(fmwk.xxx())
```


* `xxx.h` needs a couple of things: You should definitely `#include` all the LArLite dataproducts that you're planning to use in the header files, as well as ROOT dataproducts. (E.g. plotting an histogram of mctruth objects would require both `#include "DataFormat/mctruth.h"` and `#include <TH1D.h>`)
Also your objects (such as histograms) should be declared here. You can put them in protected (but not necessarily).

* `xxx.cxx` is where you do your stuff.
Just look at some other scripts around on GitHub to get an idea.

* In order to get an idea of which data products you may look at, you should read the header files in the larlite directory `core/DataFormat`



