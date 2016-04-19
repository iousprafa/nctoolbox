# Introduction #

The nctoolbox has a set of functions that can be useful as basic matlab style functions to deal with different aspects of dealing with geospatial data.

These functions include (in no particular order):
  * **interptime**
    * Function to interpolate an up to order 4 data matrix (including time) to a specific time or resample to an array of times.
```
>> data = interptime(data, datatime, [2005 1 1 0 0 0]);
```
  * **zsliceg**
    * Horizontal slice at a fixed vertical level.
```
>> varz=zsliceg(data,z,depth);
```
  * **getinterp**
    * This provides the matlab interpolant for interpolation of an input data matrix.
```
>> int = getinterp(data, lon, lat, method);
```
  * **interptoxy**
    * Interpolates a rank 2 or 3 data matrix to an xy point or array of points. (Matrices that are rank 3 return a profile at the point.)
```
>> data = interptoxy(data, datalon, datalat, -77, 45, 'linear');
```
  * **vsliceg**
    * Vertical slice along a line of xy coordinates.
```
>> [X,Z,VDATA] = vsliceg(DATA,GRD,CX,CY);
```
  * **nc\_genslice**
    * Takes a vertical slice along xy and time for comparing trajectories (like glider passes) to model data.
```
[Tvar,Tdis,Tzed,Tlon,Tlat] = nc_genslice(file,varname,lonTrk,latTrk,timTrk)
 
  Basically works like GSLICE. Get a "generic" vertical slice out of ROMS
  output file at coordinates specified by a vector of lon, lat and time
  (e.g. a ship transect or a glider track)
 
  Input:
    file      netcdf or opendap url (aggregation across time)
    varname   name of the variable we want along the track
    lon, lat  coordinates along the track
    timTrk    time values along the track
                 given as Matlab datenum
                 Must have the same dimension as lon/lat  OR  be a scalar in
                 which case it is assumed the track is for a single time
  Output:
    Tvar      interpolated 2D data along the track
    Tdis      2D range or distance along the track (in kilometers)
    Tzed      2D depth along the track
    Tlon      2D lon along the track
    Tlat      2D lat along the track
 
   gslice originally by John Evans
   modified by Weifeng Gordon Zhang
   This version by John Wilkin 2011-01-26
   Alexander Crosby 2011 April - Update to using new toolbox
```
  * **ncunits**
    * Very useful function for converting a data matrix from a set of known units to another set of units (specified as strings).
```
>> converted_data = ncunits(data, 'm/s', 'cm/s');
```

  * **ModelLook**
    * This is a frame work for easy model visualization and exploration. It is mostly point and click and provides a few different ways for understanding 2-, 3-, and 4-d variables from CF compliant netcdf files. At the moment this only works for regularly gridded model data, but it will be available for use with unstructured grid use as well, soon. It is called with the openDap link as the first argument and the matlab datevec or datenum for the time you want to look at (finds the nearest time in model).
    * For 3-d and 4-d gridded variables, the layer at index 1 is plotted. Sometime this does not correspond to the the surface layer of a model, which is the case with the example below.
```
>> modellook('uri','http://testbedapps.sura.org/thredds/dodsC/estuarine_hypoxia/ch3d/agg.nc','date', [2004 1 10 21 0 0],'level', 1);
```
http://nctoolbox.googlecode.com/hg/cdm/utilities/modellook.PNG?r=c1f2425a8006f3c5c92174a14cb4aac9dd3aae95