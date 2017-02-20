# Using Gallery

Gallery can be used to access data products from artroot files without the weight of art. You can run an interactive root macro, or use gallery in Python, but the most robust way is to write a piece of c++ code, compile it, and then run it.

In this example (mostly adopted from Wes Ketchum's tutorial here: https://github.com/wesketchum/uboone-hvburst ), we extract a raw::RawDigit, and use it's methods to write a histogram of the waveform to a root tree.


```c++
/*****************************************
 *  Test Gallery script:
 *  this script takes a list of artroot
 *  files as an input and returns a 
 *  waveform from channel 2000 of the 
 *  first event in the first file
 ****************************************/

// c++ includes
#include <iostream>
#include <fstream>
#include <stdlib.h>
#include <string>
#include <vector>
#include <algorithm>
#include <chrono>

// root includes
#include "TInterpreter.h"
#include "TROOT.h"
#include "TH1D.h"
#include "TFile.h"
#include "TNtuple.h"
#include "TClonesArray.h"

// art includes
#include "canvas/Utilities/InputTag.h"
#include "gallery/Event.h"
#include "gallery/ValidHandle.h"
#include "canvas/Persistency/Common/FindMany.h"
#include "canvas/Persistency/Common/FindOne.h"

// larsoft object includes
// i.e. here include the data products you want to include
// onle interested in RawDigits for this script
#include "lardataobj/RawData/RawDigit.h"

// wes' tutorial suggests using
// using namespace std;
// using napespace art;
// but I'm not going to do that for the sake of explicitness

int main(int argv, char** argc){
   
    // check correct number of argument supplied
    if (argv!=3){
   
        // note input_file_list should be a text file with one file per line
        // this can be made with ls -d /1/2/3/*/*.root, for example
        std::cout << "ERROR: usage is testGalleryScript <input_file_list> <output_file>" << std::endl;
        
    }
    

    // create output file and histograms
    TFile f_output(argc[2], "RECREATE");
    TH1D *h = new TH1D("h", "waveform", 8256, 0, 8256);

    // the third arg is the different branches you want to create
    TNtuple ntp("ntp", "rawDigitNtuple", "run:ev");

    // vector for input files
    // has to be a vector, even if single input file
    std::vector<std::string> filenames;

    // extract input filename from input textfile
    std::string file_name;
    std::ifstream input_file(argc[1]);
    while (getline(input_file,file_name))
        filenames.push_back(file_name);
    
    // define the label for the data product we want to extract, i.e. 
    // for the raw::RawDigit data products, we can have "daq",
    // "digitfilter", or "wcNoiseFilter"
    art::InputTag daq_tag { "daq" };

    // begin event loop
    for (gallery::Event ev(filenames); !ev.atEnd(); ev.next()){
   
        // use eventAuxiliary() to extract run/event info
        std::cout << "Processing "
             << "Run " << ev.eventAuxiliary().run() << ", " 
             << "Event " << ev.eventAuxiliary().event() << ", "
             << "Time " << ev.eventAuxiliary().time().timeHigh() << std::endl;

        // create handle to rawDigit objects with the "daq_tag" label defined above
        // i tried to avoid using auto in this script but this is a monster
        // but in case someone desperately wants it, i think it should be
        // art::Handle< std::vector< raw::RawDigit > >
        const auto& rawDigitHandle = ev.getValidHandle< std::vector<raw::RawDigit> >(daq_tag);
        const raw::RawDigit& rawDigit = rawDigitHandle->at(2000);

        std::cout << "Successfully got channel 2000!" << std::endl;

        // the pedestal for each channel can be estimated to be the average of the ADC
        // values at each tick
        float pedestalVal = std::accumulate(rawDigit.ADCs().begin(),rawDigit.ADCs().end(), 0.0)/ 
            (float)(rawDigit.ADCs().size());
          
        std::cout << "pedestalVal: " << pedestalVal << std::endl;

        // get RawDigit after subtraction of pedestal
        std::vector<float> normRawDigit;
        for (size_t tick = 0; tick < rawDigit.ADCs().size(); tick++){

            normRawDigit.push_back( (float)rawDigit.ADC(tick) - pedestalVal);

            std::cout << normRawDigit.at(tick) << std::endl;

            h->SetBinContent(tick, normRawDigit.at(tick));
        }

        std::cout << "completed looping over ticks in waveform." << std::endl;

        // this demands that we run over the first event in the first file
        break;
  
    }

    f_output.Write();    
    f_output.Close();
 
    return 0;

}
```

You'll also need the following make file to run this (credit goes fully to Wes for this one...).
```
#makefile for gallery c++ programs.
#Note, being all-incllusive here: you can cut out libraries/includes you do not need
#you can also change the flags if you want too (Werror, pedantic, etc.)

CPPFLAGS=-I $(BOOST_INC) \
		 -I $(CANVAS_INC) \
		 -I $(CETLIB_INC) \
		 -I $(FHICLCPP_INC) \
		 -I $(GALLERY_INC) \
		 -I $(LARCOREOBJ_INC) \
		 -I $(LARDATAOBJ_INC) \
		 -I $(NUSIMDATA_INC) \
		 -I $(ROOT_INC)

CXXFLAGS=-std=c++14 -Wall -Werror -pedantic
CXX=g++
LDFLAGS=$$(root-config --libs) \
		        -L $(CANVAS_LIB) -l canvas_Utilities -l canvas_Persistency_Common -l canvas_Persistency_Provenance \
			    -L $(CETLIB_LIB) -l cetlib \
			    -L $(GALLERY_LIB) -l gallery \
			    -L $(NUSIMDATA_LIB) -l nusimdata_SimulationBase \
			    -L $(LARCOREOBJ_LIB) -l larcoreobj_SummaryData \
			    -L $(LARDATAOBJ_LIB) -l lardataobj_Simulation -l lardataobj_RecoBase -l lardataobj_MCBase -l lardataobj_RawData -l lardataobj_OpticalDetectorData -l lardataobj_AnalysisBase

testGalleryScript: testGalleryScript.cc
		@$(CXX) $(CPPFLAGS) $(CXXFLAGS) $(LDFLAGS) -o $@ $<

all: testGalleryScript

clean:
	rm *.o testGalleryScript
```

In order to run, setup larsoft as normal, and then setup 'larsoftobject' and 'gallery', then type 'make'.
