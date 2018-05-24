# robert-clifford-zoomable
Documentation and HTML file to present large virtual raster in a zoomable browser window.

## Processing Steps:
1. Clip each raster in the mosaic and save as a separate .tif file
2. In [this GSheet](https://docs.google.com/spreadsheets/d/1x8Nua03fu2ohvlEUPlUh7l6hVn69gW9ObxgQN8dh7us/edit#gid=1693293994), record the width and height (in pixels) for each image in the mosaic. 
3. Using the key (which provides instructions on how the sheets fit together in the mosaic) and the known widths and heights, determine and record the top-left pixel location (x and y) for each sheet in the stitched mosaic. 
4. Copy the gdal_translate command provided in Column F of the GSheet and run it in OSGeo4W shell (or normal terminal). Example:  
**gdal_translate -of GTiff -a_srs EPSG:3857 -gcp 0 0 0 -54546 -gcp 21551 0 21551 -54546 -gcp 0 13752 0 -68298 -gcp 21551 13752 21551 -68298 Sheet_1.tiff tmp_Sheet_1.tiff**
5.  the gdalwarp command provided in Column G of the GSheet and run it in OSGeo4W shell (or normal terminal). Example:  
**gdalwarp -overwrite -r bilinear -s_srs EPSG:3857 -t_srs EPSG:3857 -order 1 -co COMPRESS=NONE tmp_Sheet_1.tiff georef\Sheet_1.tiff**
6. Build a virtual raster containing all georeferenced images. Example:  
**gdalbuildvrt -overwrite -a_srs EPSG:3857 georef\output.vrt georef\*.tiff**
7. Run gdal2tiles to create webtiles. Example
**gdal2tiles -s EPSG:3857 -z 10-17 georef\output.vrt tiles**
8. Upload tiles (using Filezilla or similar client) to [http://perec.mcmaster.ca/maps/robertclifford/pays-bas/](http://perec.mcmaster.ca/maps/robertclifford/pays-bas/)  
9. Edit the index.html page (included in this repo) and upload it to [http://perec.mcmaster.ca/maps/robertclifford/pays-bas/](http://perec.mcmaster.ca/maps/robertclifford/pays-bas/)  


*Note that currently, there is an outstanding issue generating tiles for zoom layer 16. This has been submitted to [this Stackexchange ticket](https://gis.stackexchange.com/questions/249809/gdal2tiles-error-5-illegal-values-for-buffer-size-error-for-a-single-zoom-lev)

## Final Product
View it at [http://perec.mcmaster.ca/maps/robertclifford/pays-bas/](http://perec.mcmaster.ca/maps/robertclifford/pays-bas/) 