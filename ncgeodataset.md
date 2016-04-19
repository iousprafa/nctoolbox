# NCGEODATASET CLASS #

The ncgeodataset class is a high level class for dealing with local or remote CF compliant geographic netcdf datasets. The constructor is called with the filesystem path or OPeNDAP link as its argument. This class is the most sophisticated class dealing with data and aims to be the most interoperable by exposing methods to aid data access and comparison in a consistent way.

Ncgeodataset exposes the `variable`, `attributes`, `variables`, `data`, `grid` etc. methods that the cfdataset class implements, but also adds its own methods like `metadata` and `geovariable`.  These methods provide a higher level and more interoperable version of the variable class.


# Details #


### ncgeodataset ###
subclass of cfdataset
```
>> nc = ncgeodataset('http://testbedapps.sura.org/thredds/dodsC/estuarine_hypoxia/chesroms/agg.nc')

nc = 
  ncgeodataset handle

  Properties:
     location: 'http://testbedapps.sura.org/thredds/dodsC/estuarine_hypoxia/chesroms/agg.nc'
       netcdf: [1x1 ucar.nc2.dataset.NetcdfDataset]
    variables: {92x1 cell}
  Methods, Events, Superclasses
```
Grab the metadata for the dataset:
```
>> nc.metadata

ans = 

  >> nc.metadata
ans = 
    global_attributes: {32x2 cell}
               ntimes: {'long_name'  'number of long time-steps'}
              ndtfast: {'long_name'  'number of short time-steps'}
                   dt: {2x2 cell}
        ...
                    u: {7x2 cell}
                    v: {7x2 cell}
                    w: {5x2 cell}
                 temp: {8x2 cell}
                 salt: {8x2 cell}
                  rho: {5x2 cell}
...

```
Grab just the global attributes of the dataset:
```
>> nc.attributes
ans = 
    'type'                'ROMS/TOMS history file'               
    'Conventions'         'CF-1.0'                               
    'title'               'ChesROMS (UMCES) - ROMS-2.2'          
    'var_info'                                       [1x54  char]
    'rst_file'            'chesroms_rst_Nz20.nc'                 
    'his_base'            'chesroms_his_Nz20'                    
    'avg_base'            'chesroms_avg_Nz20'                 
    ...
    'creator_name'        'Wen Long'                             
    'creator_email'       'wenlong@umces.edu'                    
    'creator_url'         'http://www.umces.edu/'                

```
Create variable object:
```
var = nc.variable('salt');
```
Create instance of ncgeovariable object:
```
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
Grab attribute value from specific variable/key (in that order) or global (with just 1 argument):
```
>> saltunits=nc.attribute('salt','units')
saltunits =
PSU

>> modeltitle = nc.attribute('title')

modeltitle =
ChesROMS (UMCES) - ROMS-2.2
```

Find the bounding box of a particular variable:
```
>> nc.extent('salt')
ans = 
    lon: [-77.1732 -75.0920]
    lat: [36.0202 39.9742]
```

It may be useful to return to identify the time axis for a given geovariable.

To find the name of the netcdf variable that provides the time axis for geovariable use `gettimename`.
```
>> name = nc.gettimename('u_water')
```

To return the geovariable object representing the time axis for a given geovariable use `gettimevar`.
```
>> t_gvar = nc.gettimevar('u_water')
```


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
>>s.time={'01-Jan-2002', '01-Feb-2002'}; % Can be omitted or s.t_index=[i1 i2]; or s.time=[datenum1, datenum2];
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