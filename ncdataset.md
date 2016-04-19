# NCDATASET CLASS #

Base dataset class for dealing with netcdf files in **nctoolbox**. Assumes very little about the conventions or structure of the netcdf file.


# Details #

Add your content here.  Format your content with:
  * Text in **bold** or _italic_
  * Headings, paragraphs, and lists
  * Automatic links to other wiki pages

```
>> nc = ncdataset('http://testbedapps.sura.org/thredds/dodsC/estuarine_hypoxia/cbofs2-1tdo/agg.nc')

nc = 

  ncdataset handle

  Properties:
     location: 'http://testbedapps.sura.org/thredds/dodsC/estuarine_hypoxia/cbofs2-1tdo/agg.nc'
       netcdf: [1x1 ucar.nc2.dataset.NetcdfDataset]
    variables: {84x1 cell}

  Methods, Events, Superclasses
```

```
>> temp = nc.data('temp', [1 1 1 1], [1 1 50 50], [1 1 1 1]);
>> size(temp)

ans =

     1     1    50    50
```

```
>> nc.axes('temp')

ans = 

    'ocean_time'
    's_rho'
    []
    []
```

```
>> nc.attributes

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
    ...
    ...
    'wms-link'            'http://testbedapps.sura.org/ncWMS/wms'    
    'wms-layer-prefix'    'cbofs2-1tdo'                              
    'id'                  'eh.noaa.cbofs2-1tdo.2004_only'            
    'naming_authority'    'noaa.ioos.testbed'                        
    'summary'                                            [1x61  char]
    'creator_name'        'Lyon Lanerolle'                           
    'creator_email'       ''                                         
    'creator_url'                                        [1x53  char]
    'cdm_data_type'       'Grid' 

>> nc.attributes('temp')

ans = 

    'long_name'        'potential temperature'      
    'units'            'Celsius'                    
    'time'             'ocean_time'                 
    'coordinates'      'lat_rho lon_rho'            
    'field'            'temperature, scalar, series'
    'wms-layer'        'cbofs2-1tdo/temp'           
    'standard_name'    'sea_water_temperature'                                         

>> nc.attribute('units','temp')

ans =

Celsius
```

```
>> nc.size('temp')

ans =

         400          20         291         332
```