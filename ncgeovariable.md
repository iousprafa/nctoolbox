# Introduction #

Add your content here.


# Details #

### ncgeovariable ###
subclass of ncvariable

```
>> nc = ncgeodataset('http://testbedapps.sura.org/thredds/dodsC/estuarine_hypoxia/chesroms/agg.nc');


>> gvar = nc.geovariable('salt')

gvar = 
  ncgeovariable handle

  Properties:
       dataset: [1x1 ncgeodataset]
          name: 'salt'
          axes: {4x1 cell}
    attributes: {8x2 cell}
  Methods, Events, Superclasses
```

Get coordinate axes (NB this returns all the variable names, without the blanks as opposed to the ncvariable/ncdataset/cfdataset methods and properties):
```
>> gvar.axes

ans = 

    'lat_rho'
    'lon_rho'
    'ocean_time'
    's_rho'
```
Grab variable attribute value from specific key:
```

>> gvar.attributes

ans = 
    'long_name'        'salinity'                
    'units'            'PSU'                     
    'time'             'ocean_time'              
    'coordinates'      'lat_rho lon_rho'         
    'field'            'salinity, scalar, series'
    'missing_value'    [                       0]
    'wms-layer'        'chesroms/salt'           
    'standard_name'    'sea_water_salinity'    

>> longname = gvar.attribute('long_name')

longname =

potential temperature

>> var.attribute('long_name')

ans =

salinity

```
% Returns grid, but with standardized field names where appropriate (also `grid_interop` does the vertical coordinate conversions and time to matlab datenum convention conversion:
```
>> grid = gvar.grid_interop(1:end, :, 1:50, 1:50)
grid = 
     lat: [50x50 double]
     lon: [50x50 double]
    time: [5479x1 double]
       z: [20x50x50 double]

>> oldgrid = gvar.grid(1:end, :, 1:50, 1:50)
oldgrid = 
       lat_rho: [50x50 double]
       lon_rho: [50x50 double]
    ocean_time: [5479x1 double]
         s_rho: [20x1 double]

```

% Subset on lat/lon values and time values or indices based on the fields in input structure:
```
>>s.time=['01-Jan-2002' '01-Feb-2002']; % Can be omitted or s.t_index=[i1 i2]; can be used for subsetting time using indices
>>s.t_stride=2; % Can be omitted
>>s.lat=[36.1 37.0];
>>s.lon=[-76.0 -75.0];
>>s.h_stride=[2 2]; % Can be omitted
>>s.z_index=[1 3]; % Can be omitted
>>s.v_stride=2; % Can be omitted


>>sub = gvar.geosubset(s); % Subset method
```

Get time (returned as datenum) or indices for time within window:
```
>>>> time = gvar.timewindow(731944, 732272) %args in datevec or datenum form.

time =

      731944
      731945
      731946
      731947
      731948
      731949
      731950
      731951 ...
...

>> timewindow = gvar.timewindowij(731944, 732272);
timewindow = 

    index: [350x1 double]
     time: [350x1 double]
```
Or can also be used like:
```
time = timewindow(gvar, start, stop);
time2 = timewindowij(gvar, start, stop);
```

Get lat/lon indices within subset bounds. (If lat/lon are vectors, output is: `[minlat_ind maxlat_ind minlon_ind maxlon_ind]`.):
```
[rowmin rowmax colmin colmax] = geoij(gvar, subsetstruct);
```

It may be useful to return to identify the time axis for a given geovariable.

To find the name of the netcdf variable that provides the time axis for geovariable use `gettimename`.
```
>> name = gvar.gettimename()
```

To return the geovariable object representing the time axis for a given geovariable use `gettimevar`.
```
>> t_gvar = gvar.gettimevar()
```