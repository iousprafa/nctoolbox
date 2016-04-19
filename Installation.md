## Download NCTOOLBOX ##
### _Most users:_ standard version: ###
  1. [Download the nctoolbox zip file](http://code.google.com/p/nctoolbox/downloads/list)
  1. Extract the downloaded zip file somewhere
  1. Install NCTOOLBOX (see below)
### _Risk takers:_ development version: ###
  1. Download a recent zipfile from https://github.com/acrosby/nctoolbox_recent/zipball/master
  1. Extract the downloaded zip file somewhere
  1. Install NCTOOLBOX (see below)
or if you want to make sure ensure you have the latest, bleeding edge code:
  1. Get a Mecurial (hg) client from the [Mercurial web site](http://mercurial.selenic.com/)
  1. Clone the source code repository:
```
hg clone http://code.google.com/p/nctoolbox/ nctoolbox
```
  1. After the first time you clone, you can update to the latest source by doing the following:
```
hg pull http://code.google.com/p/nctoolbox/
hg update
```
  1. Install NCTOOLBOX (see below)
## Install NCTOOLBOX ##
  1. In Matlab, change to the nctoolbox directory. For example:
```
   >> cd ~/Documents/Matlab/nctoolbox
```
  1. In Matlab, run the setup\_nctoolbox function
```
   >> setup_nctoolbox
```
  1. If you want nctoolbox to be available each time you start Matlab, edit startup.m:
```
   >> edit startup.m
```
> and add these lines:
```
addpath('c:/change-to-your-path/nctoolbox')
setup_nctoolbox
```