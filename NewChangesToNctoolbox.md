# **New changes to nctoolbox (Incorporated Into the June 6, 2011 Alpha Release)** #
This page describes not-yet-stable syntax and functionality changes to nctoolbox, currently only available in the latest source code from the trunk. Instead of downloading a zip file, you will need to do
follow the instructions at
http://code.google.com/p/nctoolbox/source/checkout
to checkout the latest code.  Then just run "setup\_nctoolbox.m" as usual. As of June 7th 2011 all of the changes mentioned below are incorporated into the Alpha release available on the downloads page.

## Matlab Style Indexing ##
Basic start, stop, stride (`[1,1,1],[25,50,180],[2,1,1]`) indexing in the public facing api of the nctoolbox has been replaced with matlab style parenthesis/colon indexing, `(1:2:25, 1:50, 1:180)`. To use this in the traditional nctoolbox object syntax:
```
>> nc = cfdataset('http://megara.tamu.edu:8080/thredds/dodsC/gcoos/hypoxia');

>> var = nc.variable('var_name');

>> data = var.data(:,:,:,:);

>> grid = var.grid(:,:,:,:);
```

In order to implement `end` indexing functionality, `var.data` and `var.grid` must return the variable object itself if no indices are specified. This has the added bonus of avoiding errors related to requesting too much data by accident when one mistakenly forgets indices.

## New Classes ##
New classes have been added to deal with data where some assumptions about geographic data can be made.

### ncgeodataset ###
subclass of cfdataset
```
>> nc = ncgeodataset('http://testbedapps.sura.org/thredds/dodsC/estuarine_hypoxia/cbofs2-1tdo/agg.nc')

nc = 

  ncgeodataset handle

  Properties:
     location: 'http://testbedapps.sura.org/thredds/dodsC/estuarine_hypoxia/cbofs2-1tdo/agg.nc'
       netcdf: [1x1 ucar.nc2.dataset.NetcdfDataset]
    variables: {84x1 cell}

  Methods, Events, Superclasses
```
Grab all of the attributes in the entire file:
```
>> nc.metadata

ans = 

    global_attributes: {43x2 cell}
               ntimes: {'long_name'  'number of long time-steps'}
              ndtfast: {'long_name'  'number of short time-steps'}
                   dt: {2x2 cell}
               dtfast: {2x2 cell}
               dstart: {2x2 cell}
                 nHIS: {'long_name'  'number of time-steps between history records'}
                 nRST: {2x2 cell}
                 nSTA: {'long_name'  'number of time-steps between stations records'}
               Falpha: {'long_name'  'Power-law shape barotropic filter parameter'}
                Fbeta: {'long_name'  'Power-law shape barotropic filter parameter'}
               Fgamma: {'long_name'  'Power-law shape barotropic filter parameter'}
              Akt_bak: {2x2 cell}
              Akv_bak: {2x2 cell}
              Akk_bak: {2x2 cell}
              Akp_bak: {2x2 cell}
                 rdrg: {2x2 cell}
                rdrg2: {'long_name'  'quadratic drag coefficient'}
                  Zob: {2x2 cell}
                  Zos: {2x2 cell}
                gls_p: {'long_name'  'stability exponent'}
                gls_m: {'long_name'  'turbulent kinetic energy exponent'}
                gls_n: {'long_name'  'turbulent length scale exponent'}
             gls_cmu0: {'long_name'  'stability coefficient'}
        ...
...

>> ans.global_attributes

ans = 

    'type'                'ROMS/TOMS history file'                   
    'Conventions'         'CF-1.0'                                   
    'title'               'CBOFS2-1TDO (NOAA) - ROMS-3.0'            
    'var_info'            '../../../ROMS/External/varinfo.dat'       
    'rst_file'            'ocean_rst_synoptic_seg38.nc'              
    'his_file'            'ocean_his_synoptic_seg38.nc'              
    'sta_file'            'ocean_sta_synoptic_seg38.nc'              
    'grd_file'            'CBOFS2_grid_2mcut_CnD.nc'                 
    'ini_file'            'ocean_rst_synoptic_seg37.nc'              
    'frc_file_01'         'CBOFS2_forcing_rivers_synoptic.nc'        
    'frc_file_02'         'CBOFS2_forcing_surface_synoptic_Uwind.nc' 
    'frc_file_03'         'CBOFS2_forcing_surface_synoptic_Vwind.nc' 
    'frc_file_04'         'CBOFS2_forcing_surface_synoptic_Tair.nc'  
    'frc_file_05'         'CBOFS2_forcing_surface_synoptic_Pair.nc'  
    'frc_file_06'         'CBOFS2_forcing_surface_synoptic_Qair.nc'  
    'frc_file_07'         'CBOFS2_forcing_surface_synoptic_LWdown.nc'
    'frc_file_08'         'CBOFS2_forcing_surface_synoptic_SWnet.nc' 
    'bry_file'            'CBOFS2_forcing_obc_synoptic_doB.nc'       
    'script_file'         'toms_synoptic_seg38.in'                   
    'spos_file'           'stations_synoptic.in'                     
    'svn_url'             'https://www.myroms.org/svn/src/trunk'     
    'svn_rev'             ''                                         
    'code_dir'            '/gpfs/c4/nos/save/wx21ll/ROMS_MMAP_Final' 
    'header_dir'          './ROMS/Include'                           
    'header_file'         'cbofs2.h'                                 
    'os'                  'AIX'                                      
    'cpu'                 '00C885D24C00'                             
    'compiler_system'     'xlf'                                      
    'compiler_command'    'mpxlf95_r'                                
    'compiler_flags'                                     [1x74  char]
    'tiling'              '006x016'                                  
    'history'                                            [1x65  char]
    'ana_file'                                           [1x122 char]
    'CPP_options'                                        [1x703 char]
    'wms-link'            'http://testbedapps.sura.org/ncWMS/wms'    
    'wms-layer-prefix'    'cbofs2-1tdo'                              
    'id'                  'eh.noaa.cbofs2-1tdo.2004_only'            
    'naming_authority'    'noaa.ioos.testbed'                        
    'summary'                                            [1x61  char]
    'creator_name'        'Lyon Lanerolle'                           
    'creator_email'       ''                                         
    'creator_url'                                        [1x53  char]
    'cdm_data_type'       'Grid'        



```
Create variable object:
```
var = nc.variable('temp');
```
Create instance of ncgeovariable object:
```
>> gvar = nc.geovariable('temp')

gvar = 

  ncgeovariable handle

  Properties:
       dataset: [1x1 ncgeodataset]
          name: 'temp'
          axes: {4x1 cell}
    attributes: {7x2 cell}

  Methods, Events, Superclasses
```
Grab attribute value from specific key/variable (args in the order of variable name and then key name) or global (just 1 arg of key name):
```
>> tempunits = nc.attribute('temp', 'units')

tempunits =

Celsius

>> modeltitle = nc.attribute('title')

modeltitle =

CBOFS2-1TDO (NOAA) - ROMS-3.0
```

Find the bounding box of a particular variable:
```
>> nc.extent('temp')

ans = 

    lon: [-81.1744 -74.2308]
    lat: [36.1904 39.6118]
```

### ncgeovariable ###
subclass of ncvariable
```
>> nc = ncgeodataset('http://testbedapps.sura.org/thredds/dodsC/estuarine_hypoxia/cbofs2-1tdo/agg.nc');

>> gvar = nc.geovariable('temp')

gvar = 

  ncgeovariable handle

  Properties:
       dataset: [1x1 ncgeodataset]
          name: 'temp'
          axes: {4x1 cell}
    attributes: {7x2 cell}

  Methods, Events, Superclasses
```
Grab variable attribute value from specific key:
```

>> gvar.attributes

ans = 

    'long_name'        'potential temperature'      
    'units'            'Celsius'                    
    'time'             'ocean_time'                 
    'coordinates'      'lat_rho lon_rho'            
    'field'            'temperature, scalar, series'
    'wms-layer'        'cbofs2-1tdo/temp'           
    'standard_name'    'sea_water_temperature'    

>> longname = gvar.attribute('long_name')

longname =

potential temperature

>> var.attribute('long_name')

ans =

potential temperature

```
% Returns grid, but with standardized field names where appropriate (also `grid_interop` does the vertical coordinate conversions and time to matlab datenum convention conversion:
```
>> grid = gvar.grid_interop(1:end, :, 1:50, 1:50)

grid = 

     lat: [50x50 double]
     lon: [50x50 double]
    time: [400x1 double]
       z: [20x50x50 double] 

>> oldgrid = gvar.grid(1:end, :, 1:50, 1:50) % can still return grid with original variable names.

oldgrid = 

       lat_rho: [50x50 double]
       lon_rho: [50x50 double]
    ocean_time: [400x1 double]
         s_rho: [20x1 double] 

```

% Subset on lat/lon values and time values or indices based on the fields in input structure:
```
>>s.time=[now-3 now]; % Can be omitted or s.t_index=[i1 i2]; can be used for subsetting time using indices
>>s.t_stride=2; % Can be omitted
>>s.lat=[22 27];
>>s.lon=[117 123];
>>s.h_stride=[2 2]; % Can be omitted
>>s.z_index=[1 30]; % Can be omitted
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

## Syntax Compatibility with njTBX in **NCGEODATASET** ##
As much as possible nctoolbox's matlab object oriented syntax is being preserved, but new changes include equivalent njTBX syntax for backwards compatability. The goal is for this to be seamless for people transitioning from njTBX 2 to nctoolbox/njTBX3.

```
>> nc = ncgeodataset('...');
>> var = nc{'variable'};             % var = nc.geovariable('variable')
>> data = nc{'variable'}(1,1,:,:);   %  var.data(1,1,:,:)
>> grid = nc{'variable'}(1,1,:,:).grid;   %  var.grid_interop(1,1,:,:)
```