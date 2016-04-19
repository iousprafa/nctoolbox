# FAQ #

## Q: Why nctoolbox? ##
### nctoolbox can access many common data model datasets ###
```
nc = ncdataset('localfile.nc') % NetCDF file
nc = ncdataset('http://geoport.whoi.edu/thredds/dodsC/coawst_2_2/fmrc/coawst_2_2_best.ncd') % DODS URL
nc = ncdataset('http://www.mbari.org/staff/brian/pub/m1_pco2.nc') % NetCDF URL
```

### It's simple to use ###
```
nc = ncdataset('http://www.mbari.org/staff/brian/pub/m1_pco2.nc')
p = nc.data('pco2')
t = nc.data('time_d')
plot(t, p)
datetick('x')
set(gca, 'XLim', datenum(['2005-01-01'; '2008-01-01']))
```


## Q: Why is nctoolbox **READ-ONLY**? ##
In short, Matlab already has built-in support for writing HDF, HDF5, and NetCDF files. Unfortunately, Matlab has no built-in support for OpenDAP and very complicated API's for reading common data model formats. For many researchers/scientists/students, this makes getting data into Matlab ~~a big pain in the rear~~ a serious challenge.   **nctoolbox** greatly simplifies data access by providing a very simple and intuitive API. Here's a brief synopsis of the differences:

| | **NCTOOLBOX** | **Matlab's built-in API's** |
|:|:--------------|:----------------------------|
| Read local files | **YES!**      | **YES!**                    |
| Read files on a web server | **YES!**      | nope                        |
| Read OpenDAP URLs | **YES!**      | nope                        |
| Use the same API to read different types of data formats?| **YES!**      | nope                        |
| Easy to understand | **YES!**      | [ha ha ha ha](http://www.mathworks.com/help/techdoc/ref/netcdf.getatt.html) |
| Write HDF, HDF5, and NetCDF files | nope          | **YES!**                    |

### Code Comparison - Reading variable names from a local NetCDF file ###

#### Matlab's built in NetCDF API ####
```
ncid = netcdf.open('m1_pco2.nc', 'NC_NOWRITE');
[numdims, numvars, numglobalatts, unlimdimID] = netcdf.inq(ncid);
varnames = {};
for i = 0:(numvars - 1)
    [varnames{i + 1, 1}, xtype, dimids, numatts] = netcdf.inqVar(ncid,i);
end
```

#### nctoolbox ####
```
ds = ncdataset('m1_pco2.nc');
varnames = ds.variables;
```