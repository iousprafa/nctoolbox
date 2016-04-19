# The Big Picture #

![http://yuml.me/diagram/scruffy/class/%23%20NCTOOLBOX,%20%5Bncdataset%5D%5E-%5Bcfdataset%5D,%20%5Bcfdataset%5D%5E-%5Bncgeodataset%5D,%20%5Bcfdataset%5D+1-%3E*%5Bncvariable%5D,%20%5Bncgeodataset%5D+1-%3E*%5Bncgeovariable%5D.png](http://yuml.me/diagram/scruffy/class/%23%20NCTOOLBOX,%20%5Bncdataset%5D%5E-%5Bcfdataset%5D,%20%5Bcfdataset%5D%5E-%5Bncgeodataset%5D,%20%5Bcfdataset%5D+1-%3E*%5Bncvariable%5D,%20%5Bncgeodataset%5D+1-%3E*%5Bncgeovariable%5D.png)

For those who don't read UML, this means that cfdataset is a _subclass_ of ncdataset. (I'll get to what a subclass is in a minute). In turn, ncgeodataset is a _subclass_ of cfdataset.

So, you may be wondering what that means. In a nutshell, a subclass can do everything the parent class does but it also may do more things and/or do things slightly differently than the parent. (Got that?).

### Methods that  ncdataset, cfdataset and ncgeodataset have in common ###

All 3 classes (ncdataset, cfdataset and ncgeodataset) have the following methods that act _exactly_ the same:
  * **attribute** - Returns the value a global attribute specified by its key or the variable attribute specified by key and variable.
  * **attributes** - Returns the value a global attribute specified by its key or the variable attribute specified by key and variable.
  * **axes** - Returns a cell array containing the variable names of coordinate axes for the given variable.
  * **data** - Fetch the data for a given variable. This methods also allows you to fetch subsets of the data by specifying a the index of the first and last points as well as a stride (or spacing)
  * **save** - Save the data to a local netcdf file
  * **size** - Returns the size of the variable in the persistent store without fetching the data. Helps to know what you're getting yourself into. ;-)
  * **time** - Attempts to convert data to Matlab's native time format


### Methods that cfdataset and ncgeodataset have in common. (But ncdataset doesn't do) ###

_cfdataset_ adds some methods that are aware of CF and COARDS conventions; ncgeodataset inherits these so it has the same methods too. These methods make it a little more convient to use datasets that follow these conventions. The new methods are:
  * **grid** - Retrieve all or a subset of the coordinate data for the variable. The data is returned as a structure containing a variable for each dimension of the data. The data for the variable is NOT returned ONLY the coordinate data for the variable is returned!!!
  * **standard\_name** - Returns a _standard\_name_ for a variable, if it exists (usually this is a 'standard\_name' attribute on a variable)
  * **struct** - Retrieve all or a subset of the data for the given variable. The data is returned as a structure containing a variable for the data as well as for each dimension of the data.

**Q: The UML shows that cfdataset references _ncvariable_ objects. What are those for?**

**A:** You can use those directly, but honestly, I wouldn't. _cfdataset_ use ncvariables internally to group data for the _grid_ and _struct_ functions.


### Methods unique to ncgeodataset ###

_ncgeodataset_ adds features that make it easier to work with datasets based around a geographic coordinate system. **It also supports Matlab-style matrix indexing, which ncdataset and cfdataset do not have.** The additional methods it supports are:
  * **metadata** - Function to grab all of the attributes (global and variables) all at once.
  * **timextent** - Function to calculate the start and stop times of for a variable.

# Overview #

**Pardon our dust, we're working hard on updating the documentation**

## Completely unsupported classes in development ##
(Alpha)
  * ### NCUGRID ###
  * ### NCUVARIABLE ###
  * ### NCPOINT ###
  * ### NCSGRID ###
  * ### NCRGRID ###
  * ### NCCGRID ###