# ClimBiodiv - a framework for cross-scale analysis on climate-biodiversity relations using remote sensing data

Here you will find a set of JavaScript for Earth Engine and R codes for analyzing the effect of climate on vegetation diversity and resilience from regional to local scale 

The effects of wind gusts associated with hurricanes and of rainfall trends in the Caribbean and Gulf of Mexico mangroves are used as examples here

<p align="center">
  <img width="600" height="450" src="https://user-images.githubusercontent.com/67020853/135640372-0978a3a6-8c68-46a1-967a-5b63dfd20857.png">
</p> 

### (i) JavaScript for Earth Engine code for spectral mixture analysis, the Normalized Degradation Fraction Index for Wetlands - NDFIW calculation, and damage classification (data = Landsat-5/TM, and/or Landsat-8/OLI)

```javascript
/* You must remove the double slash from the lines you wish to run  */


/* Set Area of Interest to run the code - Here (example), Caribbean and Gulf of Mexico */
 var region = ee.Geometry.Rectangle(-99.33, 6.11, -55.88, 31.86);

/* Set Area of Interest to clip final results - Here (example), Mangrove area impacted by Tropical Cyclones in 2017 */
 var Impact2017_mangrove = ee.FeatureCollection('users/cibeleha/ImpactArea_byIntensity_2017_V01');


/* 1a. Use the reflective bands (6 bands from TM-LT05) */
//var bands = ['B1', 'B2', 'B3', 'B4', 'B5', 'B7']; 

/* 1b. Use the reflective bands (7 bands from OLI-LC08) */
var bands = ['B1', 'B2', 'B3', 'B4', 'B5', 'B6', 'B7']; 

/* 2a. Define spectral endmembers in units of reflectance * 10,000 
  The list corresponds to [blue, green, red, NIR, SWIR1, SWIR2] TM-LT05 */ 
   
//var soil = [1012.277487, 1427.709424, 1745.294503,	2758.594241,	4613.549738, 3621.282723]
//var gv = [288.4434948,	601.2250712,	387.0379867,	5322.217474,	2176.452042,	804.2222222]
//var wat = [2086.406826,	2952.297899,	1765.183701,	233.6695272,	34.79322281,	23.32144428]
//var shade = [0, 0, 0, 0, 0, 0]
//var cloud = [20000,	7603.183705,	7689.343374,	8482.153581,	5980.014929,	3717.336439]

/* 2b. Define spectral endmembers in units of reflectance * 10,000 
  The list corresponds to [coastal blue, blue, green, red, NIR, SWIR1, SWIR2] OLI-LC08 */ 
   
var soil = [1670.252686, 1963.641968, 2995.935303, 3972.607178, 5040.171387, 6073.298340, 4193.060059]
var gv = [316.969421, 315.344269, 663.793884, 290.263885, 6282.166016, 2036.244629, 744.364685]
var wat = [1452.331299, 1820.917603, 2353.042725, 1401.942993, 213.995239, 188.619644, 146.396194]
var shade = [0, 0, 0, 0, 0, 0, 0]
var cloud = [5475.341797, 5766.443848, 6162.468262, 6453.780762, 7216.141113, 5988.309570, 4724.089844]

/* 3a. Define and map image time-series to do spectral unmixing 
(For pre-disturbance condition set 2 years Before the disturbance (B) and for post-disturbance condition 3 months After (A)) */

//var landsatB = ee.ImageCollection('LANDSAT/LT05/C01/T1_SR')
 //             .filterBounds(region)
 //             .filterDate('1990-08-01', '1992-07-31')
 //             .filterMetadata('CLOUD_COVER', 'less_than',30);
//print(landsatB);

//Map.layers().map(function(i){i.setShown(0)})
  
//Map.addLayer(landsatB, 
//{bands: ['B5','B4','B3'], min: 0, max: 6000}, 
//'Unmasked L5 Collection-B');

//var landsatA = ee.ImageCollection('LANDSAT/LT05/C01/T1_SR')
 //             .filterBounds(region)
 //             .filterDate('1992-12-31', '1993-03-31')
 //             .filterMetadata('CLOUD_COVER', 'less_than',30);
//print(landsatA);

//Map.layers().map(function(i){i.setShown(0)})
  
//Map.addLayer(landsatA, 
//{bands: ['B5','B4','B3'], min: 0, max: 6000}, 
//'Unmasked L5 Collection-A');

/* 3b. Define and map image time-series to do spectral unmixing 
(For pre-disturbance condition set 2 years Before the disturbance (B) and for post-disturbance condition 3 months After (A)) */

var landsatB = ee.ImageCollection('LANDSAT/LC08/C01/T1_SR')
              .filterBounds(region)
              .filterDate('2015-08-01', '2017-07-31')
              .filterMetadata('CLOUD_COVER', 'less_than',30);
//print(landsatB);

Map.layers().map(function(i){i.setShown(0)})
  
Map.addLayer(landsatB, 
{bands: ['B5','B4','B3'], min: 0, max: 6000}, 
'Unmasked L8 Collection-B');

var landsatA = ee.ImageCollection('LANDSAT/LC08/C01/T1_SR')
              .filterBounds(region)
              .filterDate('2017-12-31', '2018-03-31')
              .filterMetadata('CLOUD_COVER', 'less_than',30);
//print(landsatA);

Map.layers().map(function(i){i.setShown(0)})
  
Map.addLayer(landsatA, 
{bands: ['B5','B4','B3'], min: 0, max: 6000}, 
'Unmasked L8 Collection-A');

/* 4a. Apply cloud mask */

//var cloudMaskL457 = function(image) {
//  var qa = image.select('pixel_qa');
  // If the cloud bit (5) is set and the cloud confidence (7) is high
  // or the cloud shadow bit is set (3), then it's a bad pixel.
//  var cloud = qa.bitwiseAnd(1 << 5)
//                  .and(qa.bitwiseAnd(1 << 7))
//                  .or(qa.bitwiseAnd(1 << 3));
  // Remove edge pixels that don't occur in all bands
//  var mask2 = image.mask().reduce(ee.Reducer.min());
//  return image.updateMask(cloud.not()).updateMask(mask2);
//};

//var imageB = ee.ImageCollection('LANDSAT/LT05/C01/T1_SR')
//                  .filterBounds(region)
//                  .filterDate('1990-08-01', '1992-07-31')
//                  .map(cloudMaskL457);

//Map.addLayer(imageB, 
//  {bands: ['B5','B4','B3'], min: 0, max: 6000}, 
// 'Masked L5 Collection');
 
// var imageB05 = imageB.select('B1', 'B2', 'B3', 'B4', 'B5', 'B7');
 
// var cloudMaskL457 = function(image) {
//  var qa = image.select('pixel_qa');
  // If the cloud bit (5) is set and the cloud confidence (7) is high
  // or the cloud shadow bit is set (3), then it's a bad pixel.
//  var cloud = qa.bitwiseAnd(1 << 5)
//                  .and(qa.bitwiseAnd(1 << 7))
//                  .or(qa.bitwiseAnd(1 << 3));
  // Remove edge pixels that don't occur in all bands
//  var mask2 = image.mask().reduce(ee.Reducer.min());
//  return image.updateMask(cloud.not()).updateMask(mask2);
//};

//var imageA = ee.ImageCollection('LANDSAT/LT05/C01/T1_SR')
//                  .filterBounds(region)
//                  .filterDate('1992-12-31', '1993-03-31')
//                  .map(cloudMaskL457);

//Map.addLayer(imageA, 
//  {bands: ['B5','B4','B3'], min: 0, max: 6000}, 
// 'Masked L5 Collection');
 
// var imageA05 = imageA.select('B1', 'B2', 'B3', 'B4', 'B5', 'B7');
 
 /* 4b. Apply cloud mask */

var cloudMaskL457 = function(landsatB) {
  var qa = landsatB.select('pixel_qa');
   // If the cloud bit (5) is set and the cloud confidence (7) is high
   // or the cloud shadow bit is set (3), then it's a bad pixel.
  var cloud = qa.bitwiseAnd(1 << 5)
                  .and(qa.bitwiseAnd(1 << 7))
                  .or(qa.bitwiseAnd(1 << 3));
  // Remove edge pixels that don't occur in all bands
 var mask2 = landsatB.mask().reduce(ee.Reducer.min());
 return landsatB.updateMask(cloud.not()).updateMask(mask2);
};

var imageB = ee.ImageCollection('LANDSAT/LC08/C01/T1_SR')
                  .filterBounds(region)
                  .filterDate('2015-08-01', '2017-07-31')
                  .map(cloudMaskL457);

Map.addLayer(imageB, 
  {bands: ['B6','B5','B4'], min: 0, max: 6000}, 
 'Masked L8 Collection-B');
 
  var imageB08 = imageB.select('B1', 'B2', 'B3', 'B4', 'B5', 'B6', 'B7');

var cloudMaskL457a = function(landsatA) {
  var qa = landsatA.select('pixel_qa');
    // If the cloud bit (5) is set and the cloud confidence (7) is high
   //  or the cloud shadow bit is set (3), then it's a bad pixel.
  var cloud = qa.bitwiseAnd(1 << 5)
                  .and(qa.bitwiseAnd(1 << 7))
                  .or(qa.bitwiseAnd(1 << 3));
  // Remove edge pixels that don't occur in all bands
  var mask2a = landsatA.mask().reduce(ee.Reducer.min());
  return landsatA.updateMask(cloud.not()).updateMask(mask2a);
};

var imageA = ee.ImageCollection('LANDSAT/LC08/C01/T1_SR')
                 .filterBounds(region)
                  .filterDate('2017-12-31', '2018-03-31')
                  .map(cloudMaskL457a);

Map.addLayer(imageA, 
  {bands: ['B6','B5','B4'], min: 0, max: 6000}, 
  'Masked L8 Collection-A');
 
 var imageA08 = imageA.select('B1', 'B2', 'B3', 'B4', 'B5', 'B6', 'B7');
 
/* 5. Do spectral unmixing and calculate NDFIW */
// before disturbance
var unmixNDFIWB08 = function(imageB08) {
var unmixImageB08 = ee.Image(imageB08).unmix([gv, shade, wat, soil, cloud], true,true) //change the variable #image for the variable you want run (e.g., imageB05, imageA05) 
               .rename(['GVb', 'Shadeb', 'Waterb','Soilb','Cloudb'])
var imgWithEndmembersB08 = ee.Image(imageB08).addBands(unmixImageB08)
  
var ndfiwb = imgWithEndmembersB08.expression(
   '((GV / (1 - SHADE)) - (SOIL + WATER)) / ((GV / (1 - SHADE)) + (SOIL + WATER))', {
      'GV': unmixImageB08.select('GVb'),
     'SHADE': unmixImageB08.select('Shadeb'),
      'WATER': unmixImageB08.select('Waterb'),
     'SOIL': unmixImageB08.select('Soilb')
   }).rename('NDFIWb')
    
var gvShadeb = imgWithEndmembersB08.expression(
    'GV / (1 - SHADE)', {
      'GV': unmixImageB08.select('GVb'),
      'SHADE': unmixImageB08.select('Shadeb'),
    }).rename('GVshadeb')
    
    
return imgWithEndmembersB08.addBands([ndfiwb,gvShadeb])
 };

var endmembersCollectionB08 = imageB08.map(unmixNDFIWB08) //change the variable #endmembersCollection to discriminate the products (e.g., imageendmembersCollectionB05, endmembersCollectionA05)

Map.layers().map(function(i){i.setShown(0)});

 Map.addLayer(endmembersCollectionB08.select('NDFIWb'),
 {bands: ['NDFIWb'], min: -.5, max: 1, palette: ['#d73027','#f46d43','#fdae61','#fee08b','#ffffbf','#d9ef8b','#a6d96a','#66bd63','#1a9850']},
 'NDFIW Collection-B');
 
 Map.addLayer(endmembersCollectionB08.select('GVb'),
 {bands: ['GVb'], min: -0.5, max: 1, palette: ['#d73027','#f46d43','#fdae61','#fee08b','#ffffbf','#d9ef8b','#a6d96a','#66bd63','#1a9850']},
 'GV Collection-B');
 
  Map.addLayer(endmembersCollectionB08.select('GVshadeb'),
 {bands: ['GVshadeb'], min: -0.5, max: 1, palette: ['#d73027','#f46d43','#fdae61','#fee08b','#ffffbf','#d9ef8b','#a6d96a','#66bd63','#1a9850']},
 'GVshade Collection-B');
 
  Map.addLayer(endmembersCollectionB08.select('Soilb'),
 {bands: ['Soilb'], min: -0.5, max: 1, palette: ['#d73027','#f46d43','#fdae61','#fee08b','#ffffbf','#d9ef8b','#a6d96a','#66bd63','#1a9850']},
 'Soil Collection-B');
 
   Map.addLayer(endmembersCollectionB08.select('Waterb'),
 {bands: ['Waterb'], min: -0.5, max: 1, palette: ['#d73027','#f46d43','#fdae61','#fee08b','#ffffbf','#d9ef8b','#a6d96a','#66bd63','#1a9850']},
 'Water Collection-B');
 
//after disturbance

var unmixNDFIWA08 = function(imageA08) {
var unmixImageA08 = ee.Image(imageA08).unmix([gv, shade, wat, soil, cloud], true,true) //change the variable #image for the variable you want run (e.g., imageB05, imageA05) 
               .rename(['GVa', 'Shadea', 'Watera','Soila','Clouda'])
var imgWithEndmembersA08 = ee.Image(imageA08).addBands(unmixImageA08)
  
var ndfiwa = imgWithEndmembersA08.expression(
   '((GV / (1 - SHADE)) - (SOIL + WATER)) / ((GV / (1 - SHADE)) + (SOIL + WATER))', {
      'GV': unmixImageA08.select('GVa'),
     'SHADE': unmixImageA08.select('Shadea'),
      'WATER': unmixImageA08.select('Watera'),
     'SOIL': unmixImageA08.select('Soila')
   }).rename('NDFIWa')
    
var gvShadea = imgWithEndmembersA08.expression(
    'GV / (1 - SHADE)', {
      'GV': unmixImageA08.select('GVa'),
      'SHADE': unmixImageA08.select('Shadea'),
    }).rename('GVshadea')
    
    
return imgWithEndmembersA08.addBands([ndfiwa,gvShadea])
 };

var endmembersCollectionA08 = imageA08.map(unmixNDFIWA08) //change the variable #endmembersCollection to discriminate the products (e.g., imageendmembersCollectionB05, endmembersCollectionA05)

Map.layers().map(function(i){i.setShown(0)});

 Map.addLayer(endmembersCollectionA08.select('NDFIWa'),
 {bands: ['NDFIWa'], min: -.5, max: 1, palette: ['#d73027','#f46d43','#fdae61','#fee08b','#ffffbf','#d9ef8b','#a6d96a','#66bd63','#1a9850']},
 'NDFIW Collection-A');
 
 Map.addLayer(endmembersCollectionA08.select('GVa'),
 {bands: ['GVa'], min: -0.5, max: 1, palette: ['#d73027','#f46d43','#fdae61','#fee08b','#ffffbf','#d9ef8b','#a6d96a','#66bd63','#1a9850']},
 'GV Collection-A');
 
  Map.addLayer(endmembersCollectionA08.select('GVshadea'),
 {bands: ['GVshadea'], min: -0.5, max: 1, palette: ['#d73027','#f46d43','#fdae61','#fee08b','#ffffbf','#d9ef8b','#a6d96a','#66bd63','#1a9850']},
 'GVshade Collection-A');
 
  Map.addLayer(endmembersCollectionA08.select('Soila'),
 {bands: ['Soila'], min: -0.5, max: 1, palette: ['#d73027','#f46d43','#fdae61','#fee08b','#ffffbf','#d9ef8b','#a6d96a','#66bd63','#1a9850']},
 'Soil Collection-A');
 
   Map.addLayer(endmembersCollectionA08.select('Watera'),
 {bands: ['Watera'], min: -0.5, max: 1, palette: ['#d73027','#f46d43','#fdae61','#fee08b','#ffffbf','#d9ef8b','#a6d96a','#66bd63','#1a9850']},
 'Water Collection-A');
 
/* 6. Calculate the NDFIW median value from a time-series */

//var medianNDFIW_imageB05 = endmembersCollectionB05.select('NDFIWb').median();
//var medianNDFIW_imageA05 = endmembersCollectionA05.select('NDFIWa').median();
var medianNDFIW_imageB08 = endmembersCollectionA08.select('NDFIWa').median();
var medianNDFIW_imageA08 = endmembersCollectionB08.select('NDFIWb').median();

/* 7. Compute difference between pre- and post-disturbance NDFIW images */
//diff05_1992 = medianNDFIW_imageB05.subtract(medianNDFIW_imageA05)
//Map.addLayer(diff05_1992, {min: -1, max :1, palette: ['black', 'white']},'difference_1992');

var diff08_2017 = medianNDFIW_imageA08.subtract(medianNDFIW_imageB08).clip(Impact2017_mangrove)
Map.addLayer(diff08_2017, {min: -1, max :1, palette: ['black', 'white']},'difference_2017');

/* 8. Remap values */
//var degrad_1992 = diff05_1992
//.remap([-1-0.299, 0.3-1],
//       [0,1]);
//Map.addLayer(degrad_1992, {min: 0, max :1}, palette: ['black', 'white']},'nondegraded(0)_degraded(1)_1992');

//var degrad_2017 = diff08_2017
//.remap([-1-0.299, 0.3-1],
//       [0,1]);
//Map.addLayer(degrad_2017, {min: 0, max :1},'nondegraded(0)_degraded(1)_2017');

var degrad_2017 = ee.Image(1)
          .where(diff08_2017.gt(-1).and(diff08_2017.lte(0.299)), 2)
          .where(diff08_2017.gt(0.3).and(diff08_2017.lte(2)), 3);
Map.addLayer(degrad_2017, {min: 1, max :3, palette: ['grey', 'orange', 'blue']},'nondegraded(2)_degraded(3)_2017');

/* 9. Convert each pixel value to a sample point */
//var degrad_2017_point = degrad_2017.sample({
//  region: region,
//  geometries: true,
//  scale:30,
//  tileScale: 16,
//});

/* 10. Export sample points */
//Export.table.toDrive({
//  collection: degrad_2017_point,
//  description:'Caribbean_Degrad_2017season_samplepoints',
//  fileFormat: 'SHP'
//});
```
![ClimBiodiv_readme_fig2](https://user-images.githubusercontent.com/67020853/135663699-6aa915e7-a737-4975-b779-e616bea846f7.png)
Visualization of step 5 - NDFIW before (A) and after (B) disturbance, step 7 - difference, and step 8 - damaged areas (in blue). Zoom-in in southern Florida.

*You might run the code above for multiple years and unify the annual sample points into one layer for a long-term analysis.

### (ii) JavaScript for Earth Engine code for exporting multi-source remote sensing data from damage sample points as a table (data = multiple sources)
