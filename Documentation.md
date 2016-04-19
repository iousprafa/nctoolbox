# Quick Start #
  * Run and examine the demos in the nctoolbox/demos directory
  * [Tutorial on reading data in NCTOOLBOX using the NCGEODATASET class.](UsingNcgeodataset.md)  This is the way most people will want to read data, for the reasons described below.


# The Rest of the Story #

There are several dataset classes that can be used to read data. Each provides different types of functionality. All dataset classes provide access to variable data, list of variable names, and netcdf attributes as well as the underlying netcdf-java dataset object.  Most users will want to use NCGEODATASET, the most recent class, since this the only class that gives access to the **geovariable** class. This allows subsetting by values of lat/lon, by windows of time and the ability to return coordinates that use standard names of time, lat, lon, z for every dataset variable(and even performs coordinate conversions). This class also has a method to pull attributes from a netcdf file into a convenient matlab structure and another to pull the extents of the coordinates.  The older NCDATASET and CFDATASET classes are retained mostly for backwards compatibility.

  * [nctoolbox Matlab Classes](NctoolboxClasses.md)
    * [NCDATASET](http://code.google.com/p/nctoolbox/source/browse/cdm/ncdataset.m) - Provides basic access to data via start/stop indices.
      * [Tutorial](UsingNcdataset.md)
      * [Overview of methods and functions](ncdataset.md)
    * [CFDATASET](http://code.google.com/p/nctoolbox/source/browse/cdm/cfdataset.m) - Provides additional capabilities for fetching data from CF and COARDS compliant datasets
      * [Tutorial](UsingCfdataset.md)
      * [Overview of methods and functions](cfdataset.md)
    * [NCGEODATASET](http://code.google.com/p/nctoolbox/source/browse/cdm/ncgeodataset.m) - Provides a wide variety of higher level functions that make use of the Unidata Common Data Model for interoperability
      * [Tutorial](UsingNcgeodataset.md)
      * [Overview of methods and functions](ncgeodataset.md)