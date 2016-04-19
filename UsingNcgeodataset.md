# NCGEODATASET #

The addition of the `ncgeodataset` object to NCTOOLBOX provides methods for data access and exploration based on the Netcdf-Java common data model for describing geographic data.  For additional information beyond this tutorial, see the [ncgeodataset](ncgeodataset.md) reference page.

Contents:
  * [Opening a Dataset](UsingNcgeodataset#Opening_a_Dataset.md)
  * [Reading Data](UsingNcgeodataset#Reading_Data.md)
  * [Subsetting Data](UsingNcgeodataset#Subsetting_Data.md)
  * [Reading Global and Variable Attributes](UsingNcgeodataset#Reading_Global_and_Variable_Attributes.md)
  * [An Example](UsingNcgeodataset#Example:_Reading_surface_salinity_data_using_different_syntax_styles.md): Here we show four different ways to read and plot surface salinity at a specific time step
    * [Open the dataset](UsingNcgeodataset#Open_the_dataset.md)
    * [Syntax 1: Matlab matrix style indexing](UsingNcgeodataset#Syntax_1.md)
    * [Syntax 2: Start, stop, stride indexing](UsingNcgeodataset#Syntax_2.md)
    * [Syntax 3: Legacy curly brace style  w/Matlab indexing](UsingNcgeodataset#Syntax_3.md)
    * [Syntax 4: nj\_tslice convenience function](UsingNcgeodataset#Syntax_4.md)


## Opening a Dataset ##
A dataset can be a local file (NetCDF, HDF, Grib) or a remote OPeNDAP Data URL.  Here we open a remote OPeNDAP Data URL.
```
>> ds = ncgeodataset('http://testbedapps.sura.org/thredds/dodsC/estuarine_hypoxia/ch3d/agg.nc')

ds = 

  ncgeodataset handle

  Properties:
     location: 'http://testbedapps.sura.org/thredds/dodsC/estuarine_hypoxia/ch3d/agg.nc'
       netcdf: [1x1 ucar.nc2.dataset.NetcdfDataset]
    variables: {15x1 cell}

  Methods, Events, Superclasses
```

## Reading Data ##

To read data from the dataset you need to supply a variable name to the `data` method, either explicitly (i.e. you type it in) or by accessing the values in the `variables`  property. Here's an example:

```
>> ds = ncgeodataset('http://dods.mbari.org/opendap/data/ssdsdata/deployments/m0/200706/OS_MBARI-M0_20070621_R_TS.nc')
>> temp = ds.data('TEMP'); % Explicit
>> psal = ds.data(ds.variables{2}) % Accessing 'PSAL' using variables property
```

## Subsetting Data ##
You can subset the [dataset object using start, stop and stride](UsingNcdataset.md) index vectors or you can create variable/geovariable objects to access the data in a more matlab-like syntax.

```
>> ds = ncgeodataset('http://geoport.whoi.edu/thredds/dodsC/coawst/fmrc/coawst_2_best.ncd');
>> ds.size('temp')

ans =

        4672          16         336         896
```

Calling `size` does **not** fetch the data; it reads the info from the CDL, which you've already fetched. So no matter how big the dataset is calling `size` is fast.

```
>> temp = ds.geovariable('temp');
>> t = temp.data(1, 1, :, :); % Can take stride and end index arugments as well.
>> size(t)

ans =

     1     1   336   896
```

Notice the size of `t` in the above example is `1 1 336   896`. Although this is explicitly what you asked for, you may need to use squeeze to get rid of them for other function to behave as expected:

```
>> t2 = squeeze(t);
>> size(t2)

ans =

   336   896
```


## Reading Global and Variable Attributes ##

You can get all of the attributes in a netcdf file with the `metadata` method. This returns a structure with attributes for each variable and an extra field called `global_attributes`.

```
>> ds = ncgeodataset('http://testbedapps.sura.org/thredds/dodsC/estuarine_hypoxia/ch3d/agg.nc');
>> ds.metadata

ans = 

    global_attributes: {12x2 cell}
                depth: {5x2 cell}
                 elev: {5x2 cell}
                    u: {7x2 cell}
                    v: {7x2 cell}
             salinity: {7x2 cell}
          temperature: {7x2 cell}
                 time: {2x2 cell}
                level: {6x2 cell}
                  lon: {5x2 cell}
                  lat: {5x2 cell}
```

If you are looking for the attributes of a specific variable or the dataset you can use the `attributes` method on the dataset object (in which case you need the variable name as an argument, otherwise it returns the global attributes) or on the variable/geovariable objects (where no arguments are needed, since we already know what variable we are referencing with the object).

```
>> temp = ds.geovariable('temperature');
>> temp.attributes

ans = 

    '_Netcdf4Dimid'    [                    3]
    'long_name'        'Water temperature'    
    'units'            'degC'                 
    'coordinates'      'level lat lon'        
    '_FillValue'       [                  NaN]
    'wms-layer'        'ch3d/temperature'     
    'standard_name'    'sea_water_temperature'

>> ds.attributes('temperature')

ans = 

    '_Netcdf4Dimid'    [                    3]
    'long_name'        'Water temperature'    
    'units'            'degC'                 
    'coordinates'      'level lat lon'        
    '_FillValue'       [                  NaN]
    'wms-layer'        'ch3d/temperature'     
    'standard_name'    'sea_water_temperature'
```

If you know the specific attribute you are looking for you can use the `attribute` method on any of the dataset or variable/geovariable objects.

```
>> temp = ds.geovariable('temperature');
>> temp.attribute('units')

ans =

degC

>> ds.attribute('units', 'temperature')

ans =

degC

```


---

# Example: Reading surface salinity data using different syntax styles #

NCTOOLBOX is fundamentally based on dataset and variable objects which each have their own methods.  You can write your scripts and functions using these objects and methods, which gives you the access to all the toolbox functionality, or you can use higher level functions which hide some of the object oriented syntax and can be more compact.  You can choose the syntax you prefer, a decision that may be motivated by the style of array indexing they are familiar with, and what functionality you need.  You can also combine the different approaches in scripts of functions-- there is no need to pick just one.

The following sections will demonstrate using each of the syntax styles, reading surface salinity data from an OPeNDAP dataset.

### Open the dataset ###

```
>> url ='http://geoport.whoi.edu/thredds/dodsC/examples/bora_feb.nc';
>> nc = ncgeodataset(url);
>> nc

nc = 

  ncgeodataset handle

  Properties:
     location: 'http://geoport.whoi.edu/thredds/dodsC/usgs/vault0/models/examples/bora_feb.nc'
       netcdf: [1x1 ucar.nc2.dataset.NetcdfDataset]
    variables: {80x1 cell}

  Methods, Events, Superclasses

```

To access the properties we can use typical dot notation like with ordinary Matlab structures. Here we want to get a list of the variables in the dataset we are looking at.
```

>> nc.variables

ans = 

    'AKs'
    'AKt'
    'AKv'
    'Akk_bak'
    'Akp_bak'
    'Akt_bak'
    'Akv_bak' ... % output truncated for display in documentation
    

```

The `size` method is a method of ncgeodataset that returns the length of each of the dimensions of a given variable in the dataset. This is a lot like Matlab's internal size command, but in this case we haven't even loaded any data into memory yet.
```
>> nc.size('salt')

ans =

           8          20          60         160

```

### Syntax 1 ###

Using objects and methods directly is the most flexible syntax and provides access to a greater breadth of functions.

In this example we create a geovariable object from the salt variable in this dataset. This is done by calling geovariable with the name of the netcdf variable we are interested in as an argument.
```

>> salt = nc.geovariable('salt')

salt = 

  ncgeovariable handle

  Properties:
       dataset: [1x1 ncgeodataset]
          name: 'salt'
          axes: {4x1 cell}
    attributes: {5x2 cell}

  Methods, Events, Superclasses

```

Now we can use Matlab style array indexing to subset the salt variable by its indices. Here we will assume that the arrangement of the dimensions follows the order of time, vertical level, horizontal coordinates. A subset of (1, end, :, :) means that we are grabbing the first time step, the last level, and the entire spatial domain of the dataset.

Note that the values that the toolbox returns are typically the same type that they are stored as in the netcdf file so they may need to be converted to Matlab's _double_.

Also, it may be necessary to remove singleton dimensions for Matlab commands like plotting using the function `squeeze`.
```

>> salinity = salt.data(1, end, :, :);
>> size(salinity)

ans =

     1     1    60   160
 
>> class(salinity)

ans =

single

>> salinity= squeeze(double(salinity));

```

In order to plot the salt values for the first time step/last level in the dataset as a Matlab `pcolor` plot, we need the spatial coordinates associated with the salt values. We could grab the lat and lon coordinates in the same manner that we did with the salt variable or if the data is CF/COARDS complaint we can take advantage of the netcdf-java common data model.

The grid method for the geovariable object is designed to grab the all the coordinates associated with the geovariable for the given indices. Usage is just like the data method, except the result is a Matlab structure containing fields for each of the geovariable's dimensions and whose values have been subset appropriately for the requested indices.
```
>> salinity_coords = salt.grid(1, end, :, :)

salinity_coords = 

       lat_rho: [60x160 double]
       lon_rho: [60x160 double]
    ocean_time: 1.0958e+009
         s_rho: -0.9750

```

A higher level option is to use the `grid_interop` method, which returns the dimensions of our geovariable using the standardized names of lat, lon, time, and z instead of the original netcdf names of the coordinate dimensions.

In addition to a more programmatic/standardized structure returned with `grid_interop` (interop for _interoperability_), the coordinate data is also transformed to a more standardized form:

  * Coordinates recoginized by the netcdf-java cdm as _time_ coordinates, are converted to Matlab's _datenum_.(This can be seen in the difference between result of the last code block and the one below.)
  * Longitude coordinates that use a 0-360 degree scheme are converted to -180 to 180 values.
  * Projected x and y values are converted to geographic coordinates lat/lon.
  * Boundary fitted vertical sigma coordinate schemes are converted to the actual vertical depth/elevation values for each grid element. (This can be seen in the difference between result of the last code block and the one below.)
```

>> salinity_coords = salt.grid_interop(1, end, :, :)

salinity_coords = 

     lat: [60x160 double]
     lon: [60x160 double]
    time: 7.3162e+005
       z: [4-D double]

>> size(salinity_coords.z)

ans =

     1     1    60   160
```

Plotting using `pcolor` is simple:
```
 
>> pcolor(salinity_coords.lon, salinity_coords.lat, salinity)
>> caxis([35 39])
>> shading flat
>> colorbar

```

Now let's add a title to the figure that includes the dataset's global attribute _title_ and the date of the data that we subset.
```
>> title({nc.attribute('title'); datestr(salinity_coords.time)})
```

### Syntax 2 ###

This syntax uses start, stop and stride arrays, with one-based indexing. We use the `data` method on the ncgeodataset object with arguments of variable name, start indices for each dimension, end indices for each dimension, and an optional stride vector for each dimension.  Because we want the last salinity level, and need to specify it explicitly, we need to first determine the size of the salinity array.
```

>> salt_size = size(nc.salt)

ans =

     8     20    60   160

>> nz = salt_size(2);

>> salinity = nc.data('salt', [1 nz 1 1], [1 nz 60 160], [1 1 1 1]);

>> salinity = squeeze(double(salinity));

```

In order to access the coordinate information for the salt variable call the `grid` method on the ncgeodataset object with the same arguments as `data`.

Note that accessing coordinate data this way depends on the _coordinates_ attribute in the netcdf variable to define the variable coordinates. In this case, even though time and z coordinates should be included, only lat\_rho and lon\_rho are defined in the _coordinates_ attributes, so only they are returned by the `grid` method.
```
>> salinity_coords = nc.grid('salt', [1 nz 1 1], [1 nz 60 160], [1 1 1 1])

salinity_coords = 

    lat_rho: [60x160 double]
    lon_rho: [60x160 double]

```

Lets grab the date for the subset we requested so that we can add it to the figure title, since it wasn't included in the `grid` command results. We can use the `time` method on the ncgeodataset object to do the conversion from the model's _ocean\_time_ to Matlab's _datenum_. The first argument should be the name of the time variable, and the second is the value of the time that you are trying to convert or an array of values. The second argument is optional, and if omitted the entire length of the time variable is converted to _datenum_.
```

>> time = nc.time('ocean_time', nc.data('ocean_time', 1, 1))

time =

  7.3162e+005

>> time = datestr(time)

time =

11-Feb-2003 12:00:00
```

Plotting using `pcolor`.
```

>> pcolor(salinity_coords.lon_rho, salinity_coords.lat_rho, salinity)
>> caxis([35 39])
>> shading flat
>> colorbar
```

Add a title to the figure that includes the dataset's global attribute _title_ and the date of the data that we subset.
```

>> title({nc.attribute('title'); time})
```

### Syntax 3 ###

This syntax should be familiar to those who have seen njTBX or the older Chuck Denham's netcdf toolbox for Matlab. We specify the variable name inside curly braces and use then Matlab style indexing to subset.
```
>> salinity = nc{'salt'}(1, end, :, :);
>> size(salinity)

ans =

     1     1    60   160

>> salinity = squeeze(double(salinity));

```

The `grid` method in this syntax is the exact same command as the `grid_interop` method in the approach from **Syntax 1**.  To access the coordinates of the data we just subset, we can issue the same command, but add a append a `.grid` at the end.

```
>> salinity_coords = nc{'salt'}(1, end, :, :).grid

salinity_coords = 

    time: 7.3162e+005
     lon: [60x160 double]
     lat: [60x160 double]
       z: [4-D double]

```

Plotting using `pcolor`:
```

>> pcolor(salinity_coords.lon, salinity_coords.lat, salinity)
>> caxis([35 39])
>> shading flat
>> colorbar

```

Now let's add a title to the figure that includes the dataset's global attribute _title_ and the date of the data that we subset.
```
>> title({nc.attribute('title'); datestr(salinity_coords.time)})
```

### Syntax 4 ###
Because extracting a volume or a vertical layer at a specific time is a common function, and that may be all we need to do, there is a high-level convenience function called `nj_tslice` which can also do our job. The function `nj_tslice` is intended to replicate the functionality found in njTBX's function of the same name. If you have both toolboxes on your working path, they will conflict.

```
>> [s,g] = nj_tslice(url,'salt',1,-1); % -1 for last level
>> pcolor(g.lon, g.lat, double(s))
>> caxis([35 39])
>> shading flat
>> colorbar
```

### Result ###
No matter which syntax we use, we should get this figure:
![http://nctoolbox.googlecode.com/hg/cdm/utilities/misc/bora.png](http://nctoolbox.googlecode.com/hg/cdm/utilities/misc/bora.png)


---

For additional information on dealing with Time, Coordinate Variables, Coordinate Conversions, and more, see the reference documentation for  [ncgeodataset](ncgeodataset.md) and [ncgeovariable](ncgeovariable.md).