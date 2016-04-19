# Unstructured Grid Netcdf Support #

Classes, methods and descriptions of nctoolbox's growing support of unstructured grid netcdf files.

We are working on developing support for unstructured grids (like adcirc or fvcom) in nctoolbox. This is not yet available for use in the Alpha release (20110606-alpha) as we are trying to work out some java compatibility issues. The unstructured support is based on libraries written to incorporate support for the unstructured grid conventions outlined [\_here\_](http://groups.google.com/group/ugrid-interoperability/browse_thread/thread/6d5523de049d0ed2) into Unidata's netcdf-java libraries. This work is being done by the cyberinfrastructure team for the Sura/IOOS Super Regional Modeling Testbed project.


# Details #

As you can see below many of the methods will be the same or mirror the functionality of the other dataset/variable classes in **nctoolbox**.

# NCUGRID #

Methods:
  * uvariable
  * unstructuredLatLonSubset
  * attributes
  * attribute
  * data
  * grid\_interop
  * grid
  * size
  * timewindowij

# NCUVARIABLE #

Methods:
  * subset
  * attributes
  * attribute
  * data
  * grid
  * grid\_interop