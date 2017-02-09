#LArLite: Storage manager

The storage manager looks like this:

```
auto info = storage->get_data<event_datatype>(“producer”);
```

All you need to do is to replace the `datatype` and `producer` strings with the actual _datatype_ and _producer_.

A sample use is:

```
auto ev_mctrack = storage->get_data<event_mctrack>(“mcreco”);
```


The datatypes correspond to types defined in

```
core/DataFormat/storage_manager.h
```

The producer is the name of the module that created that information. You can get the name of the producer by running:

```
eventdump.py larlitefile.root 1
```

I'd suggest you write 1 at the end, or you'll get a wall of text for all the events in the file.

Each `ev_mctrack` (or whatever you called it), is usually a vector containing all the `mctracks` (or other dataproducts) for the specific event. Let's say you want a vector of all the starting positions of all the mctracks in the event. You should then create a vector of all the starting positions:

```
std::vector<double> xStart_v
```

Since in our case the starting _x_ position is a _double_, if it would have been a larlite dataproduct (like an `mcpart`), then you should have gone with:

```
std::vector<larlite::mcpart> partList_v
```

Now you can finally loop through all the element of the vector and get the data you need:

```
for(auto const& h : *ev_mctrack){
  xStart_v= h.Start().X();
}
```

