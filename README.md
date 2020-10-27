# amazonlinux-gdal

Create an **AWS lambda** like docker image with python 3 and GDAL.

Inspired from [developmentseed/geolambda](https://github.com/developmentseed/geolambda) and [mojodna/lambda-layer-rasterio](https://github.com/mojodna/lambda-layer-rasterio).

The aim of this repo is to construct docker image to use when creating AWS Lambda package (with python 3).


## Create a Lambda package

You can use the docker container to either build a full package (you provide all the libraries)
or adapt for the use of AWS Lambda layer.

To build lambda layers, execute `scripts/build-all.sh`

### Default modules
- rasterio
- shapely
- numpy

### Create full package

### Use Lambda Layer

### Docker environment variables
A couple environment variables are set when creating the images:

- **PREFIX**: Path where GDAL has been installed, shoud be `/var/task`
- **GDAL_DATA**: `$PREFIX/share/gdal`
- **PROJ_LIB**: `$PREFIX/share/proj`
- **GDAL_CONFIG**: `$PREFIX/bin/gdal-config`
- **GEOS_CONFIG**: `$PREFIX/bin/geos-config`
- **GDAL_VERSION**: version of GDAL
- **PATH** has been updated to add `$PREFIX/bin` in order to access gdal binaries

## Package architecture and AWS Lambda config
:warning: AWS Lambda will need `GDAL_DATA` to be set to `/var/task/share/gdal` to be able to work :warning:

```
package.zip
  |
  |___ lib/      # Shared libraries (GDAL, PROJ, GEOS...)
  |___ share/    # GDAL/PROJ data directories   
  |___ rasterio/
  ....
  |___ other python module
```

## Layer architecture and AWS Lambda config
:warning: AWS Lambda will need `GDAL_DATA` to be set to `/opt/share/gdal` to be able to work :warning:

```
layer.zip
  |
  |___ bin/      # Binaries
  |___ lib/      # Shared libraries (GDAL, PROJ, GEOS...)
  |___ share/    # GDAL/PROJ data directories   
  |___ python/
```

## Optimal AWS Lambda config
- **GDAL_DATA:** /var/task/share/gdal or /opt/share/gdal
- **GDAL_CACHEMAX:** 512
- **VSI_CACHE:** TRUE
- **VSI_CACHE_SIZE:** 536870912
- **CPL_TMPDIR:** "/tmp"
- **GDAL_HTTP_MERGE_CONSECUTIVE_RANGES:** YES
- **GDAL_HTTP_MULTIPLEX:** YES
- **GDAL_HTTP_VERSION:** 2
- **GDAL_DISABLE_READDIR_ON_OPEN:** "EMPTY_DIR"
- **CPL_VSIL_CURL_ALLOWED_EXTENSIONS:** ".TIF,.tif,.jp2,.vrt"
