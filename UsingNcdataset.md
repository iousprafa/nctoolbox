
---

# Opening a Dataset #

```
>> ds = ncdataset('http://dods.mbari.org/opendap/data/ssdsdata/deployments/m0/200706/OS_MBARI-M0_20070621_R_TS.nc')

ds = 

  ncdataset handle

  Properties:
       netcdf: [1x1 ucar.nc2.dataset.NetcdfDataset]
    variables: {12x1 cell}

  Methods, Events, Superclasses
```

### Q: What's the netcdf property? ###

**A:** That's the Java object used to do the IO. If you're familiar with Java you can use this object, via the [Netcdf Java API](http://www.unidata.ucar.edu/software/netcdf-java/v4.1/javadoc/index.html), to do all kinds of crazy stuff. If you don't know Java you can pretty much ignore the netcdf property. One nice thing is that it can be used to dump out the [CDL](http://www.unidata.ucar.edu/software/netcdf/docs/netcdf/CDL-Syntax.html) and show you everything about the dataset. For example:

```
>> ds = ncdataset('http://dods.mbari.org/opendap/data/ssdsdata/deployments/m0/200706/OS_MBARI-M0_20070621_R_TS.nc');
>> ds.netcdf

ans =
 
netcdf dods://dods.mbari.org/cgi-bin/nph-nc/data/ssdsdata/deployments/m0/200706/OS_M0_20070621_TS.nc {
 dimensions:
   LONGITUDE = 1;
   LATITUDE = 1;
   DEPTH = 5;
   bnds = 2;
   TIME = UNLIMITED;   // (23510 currently)
 variables:
   double DEPTH_bnds(DEPTH=5, bnds=2);
   float PSAL(TIME=23510, DEPTH=5, LATITUDE=1, LONGITUDE=1);
     :_CoordinateAxes = "TIME DEPTH LATITUDE LONGITUDE ";
     :missing_value = -1.0E34f; // float
     :_FillValue = -1.0E34f; // float
     :long_name = "Hourly sea_water_salinity";
     :units = " ";
     :standard_name = "sea_water_salinity";
     :uncertainty = "0.02";
     :valid_min = "30";
     :valid_max = "36";
     :cell_methods = "time: mean";
     :history = "From m0_ctd0001_20070621_original";

<blah blah blah blah ... >
```

### Q: What's the variables property? ###

**A:** Thats a cell array of strings. Each cell contains the name of a variable found in the `ncdataset` object. You need those names to get to the data. (more about that later). To quickly see what variables your dataset has, you can do this:

```
>> ds = ncdataset('http://dods.mbari.org/opendap/data/ssdsdata/deployments/m0/200706/OS_MBARI-M0_20070621_R_TS.nc');
>> ds.variables

ans = 

    'DEPTH_bnds'
    'PSAL'
    'PSAL_QC'
    'TEMP'
    'TEMP_QC'
    'TIME_QC'
    'POSITION_QC'
    'DEPTH_QC'
    'LONGITUDE'
    'LATITUDE'
    'DEPTH'
    'TIME'

```


---

# Reading Data #

To read data from the dataset you need to supply a variable name to the _data_ method, either explicitly (i.e. you type it in) or by accessing the values in the `variables`  property (If you don't know what _variables property_ means go back to the top of the page and start reading from there). Here's an example:

```
>> ds = ncdataset('http://dods.mbari.org/opendap/data/ssdsdata/deployments/m0/200706/OS_MBARI-M0_20070621_R_TS.nc')
>> temp = ds.data('TEMP'); % Explicit
>> psal = ds.data(ds.variables{2}) % Accessing 'PSAL' using variables property
```

### Q: What if I don't want to read all the data in a variable? (i.e.  How do I fetch a subset of data) ###

**A:** That's a great question! Sometimes the data is simply too big to pull in all at once or you might only need the last week of a 10 year time-series. Fortunately, it's easy to retrieve a subset of data. First, you need to know the size (what the NetCDF API calls _shape_) of the variable. Here's an example of getting the size:

```
>> ds = ncdataset('http://geoport.whoi.edu/thredds/dodsC/coawst/fmrc/coawst_2_best.ncd');
>> ds.size('temp')

ans =

        4672          16         336         896
```

Calling `size` does **not** fetch the data; it reads the info from the CDL, which you've already fetched. So no matter how big the dataset is calling `size` is fast.

Once you know the shape, you can use the information to specify the _first_ data point or slice to retrive. If needed, you can provide a _last_ argument (data from _first_ until _last_ will be fetched). You can also specify a _stride_ argument that tells _ncdataset_ to skip data and only grab the nth point along a particular axis. Here's some examples:

```
>> firstIdx = [4672 16 1 1];
>> lastIdx =  [4672 16 336 896];
>> t = ds.data('temp', firstIdx,  lastIdx); % default stride here is [1 1 1 1]
>> size(t)

ans =

     1     1   336   896
```

Notice the size of `t` in the above example is `1 1 336   896`. Matlab _hates_ those singleton dimensions; they're easy to get rid of though:

```
>> t2 = squeeze(t);
>> size(t2)

ans =

   336   896
```

### Q: I fetched the data like your example, but Matlab complains with "Warning: CData must be double or uint8" ###
Matlab prefers data to be _double_ precision and most Matlab functions will complain if you try to use other types, such as uint16, unit32, unit64, int8, int16, int32, int64 and single. However, _ncdataset_ reads the data pretty much as it's stored in the dataset. If the dataset stores data as _floats_ (32-bit precision, or what Matlab calls _single_), then _ncdataset_ reads the data as _float_. It's very easy to convert to the _double_ precision data using the Matlab function `double`. Here's an example:

```
>> ds = ncdataset('http://geoport.whoi.edu/thredds/dodsC/coawst/fmrc/coawst_2_best.ncd');
>> tSingle = ds.data('temp', [4672 16 1 1],  [4672 16 336 896]);
>> tDouble = double(tSingle); % This converts anything to double
```


---

# Probing a Dataset's Metadata #

## Attributes ##

Most dataset's have metadata attached to them. In NetCDF terms, these are known as _attributes_. A dataset may have both _global attributes_, that describe the entire dataset, and _variable attributes_ that describe an individual variable. You can read both with the _ncdataset.attribute_ method. This method returns a 2xN (that means 2 columns and any number of rows) cell array. The first column contains the name of the attribute, the second column contains the corresponding value for that attribute. Here's an example of fetching the **global attributes**:

```
>> ds = ncdataset('http://www.esrl.noaa.gov/psd/thredds/dodsC/Datasets/ncep.marine/sst.mean.nc');
>> ds.attributes

ans = 

    'platform'       'Ship Observations'    
    'title'          'NCEP Real-time Marine'
    'history'                   [1x223 char]
    'Conventions'    'COARDS'
```

In the same vein, if you want to fetch the **variable attributes** you just use the name of the variable as an argument:

```
>> ds.attributes('sst')

ans = 

    '_CoordinateAxes'    'time lat lon '                                  
    'dataset'                                                [1x23 char  ]
    'var_desc'                                               [1x25 char  ]
    'level_desc'                                             [1x9  char  ]
    'statistic'                                              [1x6  char  ]
    'parent_stat'                                            [1x16 char  ]
    'actual_range'                                           [2x1  single]
    'precision'          [                                              2]
    'units'              'degC'                                           
    'long_name'          'Sea Surface Temperature Monthly Mean at Surface
```

If you are searching for a particular attribute, such as units, you can use the `value4key` function to fetch the value:

```
>> a = ds.attributes('sst');
>> value4key(a, 'units')

ans =

degC
```

## Axes ##

Many variables in a dataset have axes that defines the data's coordinates. Axes are very helpful for knowing things like when and where a particular point of data was measured. For example, a temperature variable will often have a time variable and possibly a latitude and longitude as it's axes. `ncdataset` provides basic access to these axes names for data files that follow [COARDS](http://ferret.wrc.noaa.gov/noaa_coop/coop_cdf_profile.html) conventions. The names returned by `ncdataset.axes` method are variables in the _ncdataset_ that represent an axis for you variable (got it).  Here's an example to clear things up:

```
>> ds = ncdataset('http://www.esrl.noaa.gov/psd/thredds/dodsC/Datasets/ncep.marine/sst.mean.nc');
>> ds.axes('sst')

ans = 

    'time'
    'lat'
    'lon'
```

Notice that the axes names above are all variables in the ncdataset. Just to refresh your memory, here's the variables in our dataset.

```
>> ds.variables

ans = 

    'sst'
    'lat'
    'lon'
    'time'
```

The axes are returned in order to a dimension of you variable. Notice the size of 'sst':

```
>> ds.axes('sst')

ans = 

    'time'
    'lat'
    'lon'

>> ds.size('sst')

ans =

         230          90         180
```

In this example, the first dimension of sst, with a length of 230, has an axis defined by the 'time' variable. The second  axes, with a length of 90, is 'lat'. Finally the 3rd axes, with 180 data points in it, is 'lon'. Let's put it all together:
```
>> sst = ds.data('sst', [230 1 1]); % Grab one day of data
>> sst = squeeze(double(sst)); % Make it a datatype matlab functions like
>> t = ds.time('time', ds.data('time', [230], [230])));
>> lat = ds.data('lat');
>> lon = ds.data('lon');
>> surf(lon, lat, sst); shading('interp');view(2);
>> title(datestr(t));
>> xlabel(value4key(ds.attributes('lon'), 'units'))
>> ylabel(value4key(ds.attributes('lat'), 'units'))
```


---

# Dealing with Time Variables #

Many datasets will contain at least one time variable. Unfortunately, the creators of the dataset you're using often use time units that are <strike>capricious</strike> <strike>arbitrary</strike> <strike><a href='http://code.google.com/p/nctoolbox/issues/detail?id=6&can=1'>evil</a></strike> chosen for domain specific reasons. Ideally, since you are in Matlab, you would like all your time units to be in [Matlab's native time format](http://www.mathworks.com/access/helpdesk/help/techdoc/ref/datenum.html).

`ncdataset` provides a convenience method for converting time to Matlab time. It does this by parsing the _units_ attribute on a variable. Let's take a look at a dataset with a time variable:

```
>> ds = ncdataset('http://www.marine.csiro.au/dods/nph-dods/dods-data/ocean_colour/seawifs/global/8_day/netcdf/f_L3m_8D_T865_9_2007.nc')
>> ds.variables

ans = 

    'flag'
    't865'
    'lon'
    'lat'
    'time'

>> value4key(ds.attributes('time'), 'units')

ans =

seconds since 1980-01-01 00:00:0.0
```

Aha, let's fetch the _time_ variable, with units of seconds since 1980-01-01 00:00:0.0, as Matlab time ...

```
>> t = ds.time('time');
>> datestr(t(1)) % Using Matlabs build in time handling functions

ans =

05-Jan-2007 00:20:11

>> datestr(t(end))

ans =

29-Dec-2007 12:07:42
```

### Q: What if I only want to fetch and convert a subset of the time data? ###
**A:** It is possible to fetch a subset, but it's a 2 step process where you:
  1. Fetch the subset of data
  1. Convert the data you fetched

```
>> t2 = ds.data('time', [1], [4]); % Fetch subset
>> t3 = ds.time('time', t2); % convert fetched data. the variable id 'time' is needed to find the conversion units
>> datestr(t3)

ans =

05-Jan-2007 00:20:11
13-Jan-2007 00:48:46
20-Jan-2007 23:36:19
29-Jan-2007 00:03:26
```


---

## More to Come ##