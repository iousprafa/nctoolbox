# Release Notes #


---

## nctoolbox-20110616-alpha2 ##
  * Addition of _ncgeodataset_ class and _ncgeovariable_ class.
  * Using ncgeodataset allows NCTOOLBOX users most of the functionality from  NetCDF-java Toolbox (njTBX).
  * Fixed an internally reported issue where variable names with special characters were causing exceptions. Variable names in `ncdataset` are now escaped _if needed_.
  * Support for matlab-style indexing and mexcdf style variable access have been added.
  * _location_ property specifying the dataset location has been added to ncdataset

  * Changes made to deal with Netcdf4 groups
  * Added a 'save' method that allows nctoolbox to save data from a remote file or dataset from an OpeNDAP server to a NetCDF local file.




---

## nctoolbox-20101101 ##
  * Fixed issue in  [ncdataset.m](http://code.google.com/p/nctoolbox/source/browse/cdm/ncdataset.m) that caused [demo5.m](http://code.google.com/p/nctoolbox/source/browse/demos/demo5.m) to fail while attempting to convert time to Matlab's format.
  * Fixed [issue](http://code.google.com/p/nctoolbox/issues/detail?id=8&can=1) with [ncdataset.m](http://code.google.com/p/nctoolbox/source/browse/cdm/ncdataset.m) that occurred in Matlab R2008a with incorrect access permissions for the 'delete' method.
  * Updated to latest NetCDF-Java 4.2


---

## nctoolbox-20100305 ##
  * Added support for fetching the coordinate data from CF/COARDS compliant datasets (not the data for the variable, only the variables coordinate data! Very useful for subsetting). The new method is _cfdataset.grid_ and is simliar to _cfdataset.struct_ (but _struct_ returns both the coordinate data and the variable data). See demos [demo9.m](http://code.google.com/p/nctoolbox/source/browse/demos/demo9.m) and [demo9a.m](http://code.google.com/p/nctoolbox/source/browse/demos/demo9a.m).
    * Example of _grid_ vs. _struct_:
```
>> ds = cfdataset('http://dods.mbari.org/cgi-bin/nph-nc/data/ssdsdata/deployments/m1/200810/OS_M1_20081008_TS.nc');
>> g = ds.grid('TEMP')

g = 

         TIME: [9043x1 double]
        DEPTH: [11x1 double]
     LATITUDE: 36.7640
    LONGITUDE: -122.0460

>> s = ds.struct('TEMP')

s = 

         TEMP: [9043x11 single]
         TIME: [9043x1 double]
        DEPTH: [11x1 double]
     LATITUDE: 36.7640
    LONGITUDE: -122.0460

```


---

## nctoolbox-20100223 ##

  * Added support for configuring a network proxy. See [setproxy.m](http://code.google.com/p/nctoolbox/source/browse/cdm/setproxy.m)
  * Fixed [issue](http://code.google.com/p/nctoolbox/issues/detail?id=6) with converting times with units that fall outside the gregorian calendar


---

## nctoolbox-20100128 ##

**IMPORTANT - As of his release, nctoolbox-20100128 requires Java 6+. You can verify the version of Java the matlab is running with ` version('-java') `. The version returned should start with _Java 1.6._. If you are running Java 5 you can use nctoobox-20091112 instead**

  * Added a _struct_ method to [cfdataset.m](http://code.google.com/p/nctoolbox/source/browse/cdm/cfdataset.m). This method returns a structure composed of a variable's data and it's associated coordinate variables data.  It supersedes the _cfdataset.variable_ method which will be made private in the next release
    * See [demo9.m](http://code.google.com/p/nctoolbox/source/browse/demos/demo9.m) for an example of _struct_ usage.
  * Fixed incorrect documentation in [cfdataset.m](http://code.google.com/p/nctoolbox/source/browse/cdm/cfdataset.m) and [ncvariable.m](http://code.google.com/p/nctoolbox/source/browse/cdm/ncvariable.m)
  * Added private _delete_ method which will close the underlying Java _Netcdf_ object when the Matlab ncdataset object is cleared or goes out of scope. This should properly close up the Java resources without requiring any changes to a users code.
  * Upgrade from netcdf-java 4.0 to 4.1. This change requires Java 6 is installed and used by Matlab. This update address several access bugs we've been running into internally at MBARI.


---

## nctoolbox-20091112 ##

  * Addressed a [bug](http://code.google.com/p/nctoolbox/issues/detail?id=1&can=1) that prevented _cfdataset.variable_ from working with CF compliant datasets.
  * Changed output of _cfdataset.variable_:
    * OLD WAY In nctoolbox-20091022: **t = ds.variable** yields a nested structure containing _metadata_ and _data_ structures
    * NEW WAY In nctooblox-20091112: **t = ds.variable** yeilds only a structure containing data. You can query the metadata in **t** using **ds.name** and **ds.axes**
      * Refer to [demos7.m](http://code.google.com/p/nctoolbox/source/browse/demos/demo7.m) and [demos8.m](http://code.google.com/p/nctoolbox/source/browse/demos/demo8.m) for examples on the usage of _cfdataset.variable_


---

## nctoolbox-20091022 ##

  * Added ability to intelligently subset CF or COARDS compliant datasets using _cfdataset.variable_. Subsetting a single variable will also return the correctly subsetted coordinate axes. Refer to [demos7.m](http://code.google.com/p/nctoolbox/source/browse/demos/demo7.m) for an example of it's usage.