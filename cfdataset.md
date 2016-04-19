# CFDATASET CLASS #

`cfdataset` inherits methods from `ncdataset`. All these methods are inherited by the high level class `ncgeodataset`.


# Details #

Add your content here.  Format your content with:
  * Text in **bold** or _italic_
  * Headings, paragraphs, and lists
  * Automatic links to other wiki pages

Call the constructor with an opendap url or local filepath:
```
>> nc = cfdataset('http://testbedapps.sura.org/thredds/dodsC/estuarine_hypoxia/cbofs2-1tdo/agg.nc')

nc = 

  cfdataset handle

  Properties:
     location: 'http://testbedapps.sura.org/thredds/dodsC/estuarine_hypoxia/cbofs2-1tdo/agg.nc'
       netcdf: [1x1 ucar.nc2.dataset.NetcdfDataset]
    variables: {84x1 cell}

  Methods, Events, Superclasses
```

Get the coordinate information (in `cfdataset` sometimes some axes are left out...):
```
>> grid = nc.grid('temp', [1 1 1 1], [1 1 50 50], [1 1 1 1])

grid = 

    lat_rho: [50x50 double]
    lon_rho: [50x50 double]
```

Get the variable object for the `temp` variable in the dataset:
```
>> var = nc.variable('temp')

var = 

  ncvariable handle

  Properties:
       dataset: [1x1 cfdataset]
          name: 'temp'
          axes: {2x1 cell}
    attributes: {7x2 cell}

  Methods, Events, Superclasses

>> nc.geovariable('temp')
??? No appropriate method, property, or field geovariable for class cfdataset.

```

Get the coordinate information and the values of the variable for a given subset:
```
>> grid = nc.struct('temp', [1 1 1 1], [1 1 50 50], [1 1 1 1])

grid = 

    lat_rho: [291x332 double]
    lon_rho: [291x332 double]
       temp: [4-D single]
```