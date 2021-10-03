# ClimBiodiv - a methodological framework for cross-scale analysis on climate-biodiversity relations using remote sensing data

Here you will find a set of JavaScript for Earth Engine and R codes for analyzing the effect of climate on vegetation diversity and resilience from regional to local scale:

(i) JavaScript for Earth Engine code for spectral mixture analysis, the Normalized Degradation Fraction Index for Wetlands - NDFIW calculation, and damage classification using Landsat-5/TM, and/or Landsat-8/OLI

(ii) JavaScript for Earth Engine code for exporting multi-source remote sensing data from damaged and undamaged mangrove sample points as a table

(iii) R code for Descriptive Statistic calculation, Machine Learning classification, and Variable Importance definition using multi-source tabulated data

(iv) R code for quantile regression between current ecosystem structure using GEDI-L3 and long-term wind speed data using ERA5 reanalysis: 40-yrs cumulative wind speed, and time since the last hurricane

(v) R code for ecosystem beta-diversity mapping using DESIS-L2A data and vertical profile visualization using GEDI-L1B and -L2A data  

The drivers of the Caribbean and Gulf of Mexico mangroves damage following tropical cyclones, the effect of wind gusts associated with cyclones on the structure of the mangrove, the resilience to hurricanes, and the role of biodiversity in the Floridian mangroves structural resistance and resilience are used as examples

<p align="center">
  <img width="600" height="450" src="https://user-images.githubusercontent.com/67020853/135728986-6f0f4dcd-34ae-4ef8-8b98-84a69b9d84b6.png">
</p> 
Fig 1. Graphical illustration of the effects of climate extreme events (herein high wind gusts) on mangrove structural biodiversity and of beta-biodiversity on mangrove structural resistance and resilience

### (i) JavaScript for Earth Engine code for spectral mixture analysis, the Normalized Degradation Fraction Index for Wetlands - NDFIW calculation, and damage classification using Landsat-5/TM, and/or Landsat-8/OLI

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
Fig 2. Visualization of the results from step 5 - NDFIW before (A) and after (B) disturbance, step 7 - difference (C), and step 8 - undamaged and damaged mangrove areas in orange and blue, respectively (D). Zoom-in in southern Florida.

*You might run the code above for multiple years and unify the annual sample points into one layer for a long-term analysis.

### (ii) JavaScript for Earth Engine code for exporting multi-source remote sensing data from damaged and undamaged mangrove sample points as a table

```javascript
/* Set Area of Interest - Here, Caribbean and Gulf of Mexico */
var region = ee.Geometry.Rectangle(-99.33, 6.11, -55.88, 31.86);

/* Set Sample points */
var dmg9620 = ee.FeatureCollection('users/cibeleha/DMG9620_Merge154m');

/* Set Variables of Interest (some examples) */ 
var Monthly_Climate = ee.ImageCollection("ECMWF/ERA5/MONTHLY")
//var AGB_BGB = ee.ImageCollection("NASA/ORNL/biomass_carbon_density/v1");
//var Topo_diversity = ee.Image("CSP/ERGo/1_0/Global/ALOS_topoDiversity");
//var Soil_moisture = ee.ImageCollection("NASA_USDA/HSL/SMAP10KM_soil_moisture");
//var Land_cover = ee.ImageCollection("COPERNICUS/Landcover/100m/Proba-V-C3/Global");

/* Extract mean value from raster */
var Monthly_Clim_dmg9620 = Monthly_Climate.map(function(image){
  return image.reduceRegions({
    collection: dmg9620, 
    reducer:ee.Reducer.mean(), 
    scale: 30
  });
});

/* Export the FeatureCollection to a CSV file */
  Export.table.toDrive({
  collection:Monthly_Clim_dmg9620,
  folder: 'ClimBiodiv',
  description:'Monthly_Clim_dmg9620',
  fileFormat: 'CSV'
  });
```

### (iii) R code for Descriptive Statistic calculation (first box), Machine Learning classification and Variable Importance definition (second box) using multi-source tabulated data

```r
### Load packages

library(caret) 
library(randomForest) 
library(dplyr)
library(compareGroups)
library(corrplot)

### Read data ans display summary

dataD <- read.table("DB_DMG_UNDMG_893_EXAMPLE.csv", header = TRUE, sep = ",", dec = ".")
head(dataD)

dataD$Class = factor(dataD$Class)
dataD$Geomorphology = factor(dataD$Geomorphology)
head(dataD)

summary(dataD)

## Class     TC_Recurrence    Prec_dryseason_YoD Prec_wetseason_YoD Wind_annualSCL_YoD Prec_annual_mean_LT
## Damaged  :443   Min.   : 5.000   Min.   :152.6      Min.   : 551.9     Min.   : 37.76     Min.   : 96.84     
## Undamaged:450   1st Qu.: 8.000   1st Qu.:248.3      1st Qu.:1213.5     1st Qu.: 53.29     1st Qu.:119.33     
##                 Median : 8.000   Median :328.9      Median :1514.8     Median : 61.49     Median :146.96
## ...

by(dataD, dataD$Class, summary)

## dataD$Class: Damaged
##       Class     TC_Recurrence    Prec_dryseason_YoD Prec_wetseason_YoD Wind_annualSCL_YoD Prec_annual_mean_LT
## Damaged  :443   Min.   : 5.000   Min.   :152.6      Min.   : 551.9     Min.   : 37.76     Min.   : 96.84     
## Undamaged:  0   1st Qu.: 8.000   1st Qu.:257.9      1st Qu.:1400.8     1st Qu.: 53.80     1st Qu.:146.96     
##                 Median : 8.000   Median :418.9      Median :1556.9     Median : 87.83     Median :170.08    
## ... 
-------------------------
## dataD$Class: Undamaged
##       Class     TC_Recurrence    Prec_dryseason_YoD Prec_wetseason_YoD Wind_annualSCL_YoD Prec_annual_mean_LT
## Damaged  :  0   Min.   : 5.000   Min.   :164.9      Min.   : 736.8     Min.   :38.20      Min.   : 96.84     
## Undamaged:450   1st Qu.: 8.000   1st Qu.:227.3      1st Qu.:1146.5     1st Qu.:53.29      1st Qu.:113.85     
##                 Median : 8.000   Median :303.9      Median :1424.6     Median :55.67      Median :146.96     
## ...                 

### Compute descriptive statistics by group and calculate normality and identity tests

tableDMG_EXAMPLE <- compareGroups(Class ~ ., data = dataD, method = 4, include.label = TRUE)
print (tableDMG_EXAMPLE) 

## -------- Summary of results by groups of 'Class'---------


##   var                     N   p.value  method                selection
## 1  TC_Recurrence           893 <0.001** continuous non-normal ALL      
## 2  Prec_dryseason_YoD      893 <0.001** continuous non-normal ALL      
## 3  Prec_wetseason_YoD      893 <0.001** continuous non-normal ALL      
## 4  Wind_annualSCL_YoD      893 <0.001** continuous non-normal ALL      
## 5  Prec_annual_mean_LT     893 <0.001** continuous non-normal ALL      
## 6  Prec_annual_trend_LT    893 0.042**  continuous non-normal ALL      
## 7  TempMax_annual_mean_LT  893 <0.001** continuous non-normal ALL      
## 8  TempMax_annual_trend_LT 893 <0.001** continuous non-normal ALL      
## 9  Drought_annual_trend_LT 893 0.526    continuous non-normal ALL      
## 10 Canopy_height_2000      893 <0.001** continuous non-normal ALL      
## 11 GV_fraction_2YbD        893 <0.001** continuous non-normal ALL      
## 12 Soil_fraction_2YbD      893 <0.001** continuous non-normal ALL      
## 13 Water_fraction_2YbD     893 <0.001** continuous non-normal ALL      
## 14 Geomorphology           893 <0.001** categorical           ALL      
## 15 Dist_shore              893 <0.001** continuous non-normal ALL      
## 16 Dist_crop               893 0.093*   continuous non-normal ALL      
## 17 Dist_settlem            893 <0.001** continuous non-normal ALL      
## 18 Dist_road               893 <0.001** continuous non-normal ALL      
## -----
## Signif. codes:  0 '**' 0.05 '*' 0.1 ' ' 1 

plot (tableDMG_EXAMPLE) 
export_table <- createTable(tableDMG_EXAMPLE)
export2word(export_table, file = "tableDMG_EXAMPLE.docx")

```
<p align="center">
  <img width="680" height="500" src="https://user-images.githubusercontent.com/67020853/135730816-133e439e-0c02-4d33-83d3-0ec0d7bafd1d.png">
</p> 
Fig 3. Example of normality plots for the variable "annual maximum sustained wind speed (km/hr)" 


```r
### Calculate correlation between variables and plot Correlation matrix

my_dataD <- dataD %>% select_if(is.numeric)
mydataD.cor = cor(my_dataD, method = c("kendall"))
corrplot(mydataD.cor, method="number", tl.col="black")

### Indicate highly correlated variables to remove

hcv = dataD %>% select_if(is.numeric) %>% cor() %>% findCorrelation(cutoff = 0.75, names = T)
print("variable to be removed from dataframe")
print(hcv)

### Define parameters for 10-fold cross-validation

control <- trainControl(method="repeatedcv", number=10, repeats=3, search="random")

### Run machine learning model (e.g., method = Random Forest)

set.seed(500)
rf_DMG_EXAMPLE <- train(Class ~., data = dataD, method = "rf", metric = "Accuracy", 
                         importance = TRUE, ntree=700, trControl=control)

# Print results from RF classification

print(rf_DMG_EXAMPLE)

## Random Forest 

## 893 samples
## 18 predictor
##  2 classes: 'Damaged', 'Undamaged' 

## No pre-processing
## Resampling: Cross-Validated (10 fold, repeated 3 times) 
## Summary of sample sizes: 804, 804, 804, 803, 803, 804, ... 
## Resampling results across tuning parameters:

## mtry  Accuracy   Kappa    
##   9    0.9749688  0.9499164
##  15    0.9719892  0.9439590
##  18    0.9716188  0.9432164

# Calculate and plot variable importance (i.e., drivers definition) 

var_import<- varImp(rf_DMG_EXAMPLE) 

## rf variable importance

##                         Importance
## Wind_annualSCL_YoD         100.000
## Water_fraction_2YbD         61.691
## Prec_wetseason_YoD          34.917
## Prec_dryseason_YoD          29.768
## Soil_fraction_2YbD          28.584
## Dist_shore                  21.263
## Dist_settlem                18.961
## Prec_annual_mean_LT         16.066
## GeomorphologyLagoon         13.393
## Prec_annual_trend_LT        13.254
## GV_fraction_2YbD            12.291
## Dist_road                   11.408
## TC_Recurrence               11.164
## Dist_crop                   10.352
## TempMax_annual_trend_LT      9.947
## GeomorphologyOpenCoast       9.174
## Canopy_height_2000           9.009
## Drought_annual_trend_LT      8.776
## TempMax_annual_mean_LT       7.288
## GeomorphologyEstuary         0.000

var_import
plot(var_import)
```
<p align="center">
  <img width="770" height="500" src="https://user-images.githubusercontent.com/67020853/135771828-e9ff41ed-d594-41c5-993a-8255f2bb143c.png">
</p> 
Fig 3. Correlation matrix between variables. Here, Kendall's rank correlation coefficient. Variables with correlations higher than 0.75 are expected to be excluded from the data frame before classification.  

<p align="center">
  <img width="770" height="500" src="https://user-images.githubusercontent.com/67020853/135730951-fd236340-9327-4e2b-9378-3d3a5f8ac837.png">
</p> 
Fig 4. Variable importance to the Random Forest classification. Wind speed is the most important driver of mangrove damage in our example.

## (iv) R code for quantile regression between current ecosystem structure (GEDI-L3) and long-term wind speed data (ERA5 reanalysis): 40-yrs cumulative wind speed (first box), and time since the last hurricane (second box)
```r
### Load package

library(quantreg)

### Read data

data <- read.table("DB_GEDI_HURRICANES_all.csv", header = TRUE, sep = ",", dec = ".")
head(data)

dataB <- read.table("DB_ALL_BAH.csv", header = TRUE, sep = ",", dec = ".")
head(dataB)

dataF <- read.table("DB_ALL_FLO.csv", header = TRUE, sep = ",", dec = ".")
head(dataF)

dataECA <- read.table("DB_ALL_ECA.csv", header = TRUE, sep = ",", dec = ".")
head(dataECA)

dataGAN <- read.table("DB_ALL_GAN.csv", header = TRUE, sep = ",", dec = ".")
head(dataGAN)

dataSCA <- read.table("DB_ALL_SCA.csv", header = TRUE, sep = ",", dec = ".")
head(dataSCA)

dataSWCA <- read.table("DB_ALL_SWCA.csv", header = TRUE, sep = ",", dec = ".")
head(dataSWCA)

dataWCA <- read.table("DB_ALL_WCA.csv", header = TRUE, sep = ",", dec = ".")
head(dataWCA)

dataSGM <- read.table("DB_ALL_SGM.csv", header = TRUE, sep = ",", dec = ".")
head(dataSGM)

dataNGM <- read.table("DB_ALL_NGM.csv", header = TRUE, sep = ",", dec = ".")
head(dataNGM)

### Multiple quantile regression to analyze the impact of 40-yrs cumulative wind gusts in the current canopy height

multi_rqfit <- rq(HEIGHT_GEDI ~ SUM_GUST, data = data, tau = seq(0, 1, by = 0.1))
multi_rqfit
?summary.rq

# Plot different quantiles

colors <- c("#ffe6e6", "#ffcccc", "#ff9999", "#ff6666", "#ff3333",
            "#ff0000", "#cc0000", "#b30000", "#800000", "#4d0000", "#000000")
plot(HEIGHT_GEDI ~ SUM_GUST, data = data, pch = 16, main = "Canopy height (m) ~ Cumulative wind speed (km/h)")
for (j in 1:ncol(multi_rqfit$coefficients)) {
  abline(coef(multi_rqfit)[, j], col = colors[j])
}

# Plot selected quantiles
  
plot(HEIGHT_GEDI ~ SUM_GUST, data = data, pch = 16, main = "Canopy height (m) ~ Cumulative wind speed (km/h)")
abline(lm(HEIGHT_GEDI ~ SUM_GUST, data = data), col = "red", lty = 2)
abline(rq(HEIGHT_GEDI ~ SUM_GUST, tau = .5, data = data), col = "blue", lty = 2)
abline(rq(HEIGHT_GEDI ~ SUM_GUST, tau = .8, data = data), col = "green", lty = 2)
legend("topright", legend = c("lm", "qr 0.5", "qr 0.8"), col = c("red", "blue", "green"), lty = 2)

# Plot selected quantile by ecoregion

plot(HEIGHT_GEDI ~ SUM_GUST, data = data, pch = 16, main = "Canopy height (m) ~ Cumulative wind speed (km/h)")
abline(rq(HEIGHT_GEDI ~ SUM_GUST, data = dataB), col = "blueviolet", lty = 1)
abline(rq(HEIGHT_GEDI ~ SUM_GUST, tau = .8, data = dataECA), col = "chartreuse2", lty = 1)
abline(rq(HEIGHT_GEDI ~ SUM_GUST, tau = .8, data = dataF), col = "darkgoldenrod", lty = 1)
abline(rq(HEIGHT_GEDI ~ SUM_GUST, tau = .8, data = dataGAN), col = "cyan", lty = 1)
abline(rq(HEIGHT_GEDI ~ SUM_GUST, tau = .8, data = dataNGM), col = "darkgreen", lty = 1)
abline(rq(HEIGHT_GEDI ~ SUM_GUST, tau = .8, data = dataSCA), col = "darkslateblue", lty = 1)
abline(rq(HEIGHT_GEDI ~ SUM_GUST, tau = .8, data = dataSGM), col = "brown2", lty = 1)
abline(rq(HEIGHT_GEDI ~ SUM_GUST, tau = .8, data = dataSWCA), col = "hotpink", lty = 1)
abline(rq(HEIGHT_GEDI ~ SUM_GUST, tau = .8, data = dataWCA), col = "deepskyblue3", lty = 1)
legend("topright", legend = c("BAH", "ECA", "FLO", "GAN", "NGM", "SCA", "SGM", "SWCA", "WCA"), col = c("blueviolet", "chartreuse2", "darkgoldenrod", "cyan", "darkgreen", "darkslateblue", "brown2", "hotpink", "deepskyblue3"), lty = 1)
```



```r
### Read data (mangroves impacted by hurricanes - i.e., wind speed higher than 119 km/h - only)

dataI <- read.table("DB_GEDI_HURRICANES_only_impacted.csv", header = TRUE, sep = ",", dec = ".")
head(dataI)

dataBI <- read.table("DB_IMP_BAH.csv", header = TRUE, sep = ",", dec = ".")
head(dataBI)

dataFI <- read.table("DB_IMP_FLO.csv", header = TRUE, sep = ",", dec = ".")
head(dataFI)

dataECAI <- read.table("DB_IMP_ECA.csv", header = TRUE, sep = ",", dec = ".")
head(dataECAI)

dataGANI <- read.table("DB_IMP_GAN.csv", header = TRUE, sep = ",", dec = ".")
head(dataGANI)

dataWCAI <- read.table("DB_IMP_WCA.csv", header = TRUE, sep = ",", dec = ".")
head(dataWCAI)

dataNGMI <- read.table("DB_IMP_NGM.csv", header = TRUE, sep = ",", dec = ".")
head(dataNGMI)

### Multiple quantile regression to analyze the current canopy height in sites with varying time since the last hurricane (i.e., wind gust > 119 km/hr)

multi_rqfitI <- rq(HEIGHT_GEDI ~ G119_TS, data = dataI, tau = seq(0, 1, by = 0.1))
multi_rqfitI

# Plot different quantiles

colors <- c("#ffe6e6", "#ffcccc", "#ff9999", "#ff6666", "#ff3333",
            "#ff0000", "#cc0000", "#b30000", "#800000", "#4d0000", "#000000")
plot(HEIGHT_GEDI ~ G119_TS, data = dataI, pch = 16, main = "Canopy height (m) ~ Time since the last hurricane (yr)")
for (j in 1:ncol(multi_rqfit$coefficients)) {
  abline(coef(multi_rqfit)[, j], col = colors[j])
}

# Plot selected quantiles

plot(HEIGHT_GEDI ~ G119_TS, data = dataI, pch = 16, main = "Canopy height (m) ~ Time since the last hurricane (yr)")
abline(lm(HEIGHT_GEDI ~ G119_TS, data = dataI), col = "red", lty = 2)
abline(rq(HEIGHT_GEDI ~ G119_TS, tau = .5, data = dataI), col = "blue", lty = 2)
abline(rq(HEIGHT_GEDI ~ G119_TS, tau = .9, data = dataI), col = "green", lty = 2)
legend("topright", legend = c("lm", "rq_05", "rq_0.9"), col = c("red", "blue", "green"), lty = 2)

# Plot selected quantile by ecoregion

plot(HEIGHT_GEDI ~ G119_TS, data = dataI, pch = 16, main = "Canopy height (m) ~ Time since the last hurricane (yr)")
abline(rq(HEIGHT_GEDI ~ G119_TS, data = dataBI), col = "blueviolet", lty = 1)
abline(rq(HEIGHT_GEDI ~ G119_TS, tau = 1, data = dataECAI), col = "chartreuse2", lty = 1)
abline(rq(HEIGHT_GEDI ~ G119_TS, tau = .9, data = dataFI), col = "darkgoldenrod", lty = 1)
abline(rq(HEIGHT_GEDI ~ G119_TS, tau = .9, data = dataGANI), col = "cyan", lty = 1)
abline(rq(HEIGHT_GEDI ~ G119_TS, tau = .9, data = dataNGMI), col = "darkgreen", lty = 1)
abline(rq(HEIGHT_GEDI ~ G119_TS, tau = .9, data = dataWCAI), col = "deepskyblue3", lty = 1)
legend("topright", legend = c("BAH", "ECA", "FLO", "GAN", "NGM", "WCA"), col = c("blueviolet", "chartreuse2", "darkgoldenrod", "cyan", "darkgreen", "deepskyblue3"), lty = 1)
```

## (v) R code for ecosystem beta-diversity mapping using DESIS-L2A data (first box) and vertical profile visualization using GEDI-L1B and -L2A data (second box)  

```r
# ===============================================================================
# biodivMapR
# ===============================================================================
# PROGRAMMERS:
#
# Jean-Baptiste FERET <jb.feret@teledetection.fr>
# Florian de Boissieu <fdeboiss@gmail.com>
#
# Copyright 2020/06 Jean-Baptiste FERET
#
# biodivMapR is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>
#
# ===============================================================================

### Load packages

library(raster)
library(biodivMapR)

### Define parameters

# path (absolute or relative) for the image to process
# a ENVI format header file with information about spectral bands is expected:
# please make sure 'wavelength' and 'wavelength units' are defined
Input_Image_File <- 'DESIS20191203_SPECTRAL'

# full path for the Mask raster corresponding to image to process
# expected to be the same dimensions (columns and lines) as the image
# if possible in ENVI HDR format, 1 band, integer 8bits
# expected values in the raster: 0 = masked, 1 = selected
# set to FALSE if no mask available
Input_Mask_File <- FALSE

# Output directory: files created by script will be written there.
# For each image processed, a subdirectory will be created after its name
# Output_Dir <- '~/biodiv' # fill with your own path
Output_Dir <- 'RESULTS_MapBiodivR_v160_DESIS_EVER' # fill with your own path

# SPATIAL RESOLUTION
# resolution of spatial units for alpha and beta diversity maps (in pixels), relative to original image
# if Res.Map = 10 for images with 10 m spatial resolution, then spatial units will be 10 pixels x 10m = 100m x 100m surfaces
# rule of thumb: spatial units between 0.25 and 4 ha usually match with ground data
# too small window_size results in low number of pixels per spatial unit, hence limited range of variation of diversity in the image
window_size <- 10

# PCA FILTERING: 		Set to TRUE if you want second filtering based on PCA outliers to be processed.
# Slower process
# Automatically set to FALSE if TypePCA     = 'MNF'
FilterPCA <- FALSE

# type of dimensionality reduction:
# PCA:  no rescaling of the data
# SPCA: rescaling of the data
# MNF:  minimum noise fraction
TypePCA <- 'SPCA'

# should continuum removal be performed on the image for spectral normalization?
# Continuum_Removal recommended for multi and hyperspectral data
Continuum_Removal <- TRUE

### Define parameters for processing

nbCPU <- 4
MaxRAM <- 0.5
nbclusters <- 50

### Process images
# Filter data in order to discard non vegetated / shaded / cloudy pixels
NDVI_Thresh <- 0.5
Blue_Thresh <- 500
NIR_Thresh <- 1500
print("PERFORM RADIOMETRIC FILTERING")
Input_Mask_File <- perform_radiometric_filtering(Image_Path = Input_Image_File, Mask_Path = Input_Mask_File,
                                                 Output_Dir = Output_Dir, TypePCA = TypePCA,
                                                 NDVI_Thresh = NDVI_Thresh, Blue_Thresh = Blue_Thresh,NIR_Thresh = NIR_Thresh)

# Perform  dimensionality reduction
print("PERFORM DIMENSIONALITY REDUCTION")
PCA_Output <- perform_PCA(Input_Image_File = Input_Image_File, Input_Mask_File = Input_Mask_File,
                          Output_Dir = Output_Dir, TypePCA = TypePCA, FilterPCA=FilterPCA,
                          nbCPU = nbCPU, MaxRAM = MaxRAM, Continuum_Removal = Continuum_Removal)

PCA_Files <- PCA_Output$PCA_Files
Pix_Per_Partition <- PCA_Output$Pix_Per_Partition
nb_partitions <- PCA_Output$nb_partitions
Input_Mask_File <- PCA_Output$MaskPath
PCA_model <- PCA_Output$PCA_model
SpectralFilter <- PCA_Output$SpectralFilter

# Select principal components from the PCA raster
# Sel_PC = path of the file where selected components are stored
Sel_PC <- select_PCA_components(Input_Image_File = Input_Image_File,
                                Output_Dir = Output_Dir, PCA_Files = PCA_Files,
                                TypePCA = TypePCA, File_Open = TRUE)

### Map beta-diversity
print("MAP SPECTRAL SPECIES")
map_spectral_species(Input_Image_File = Input_Image_File, Output_Dir = Output_Dir,
                     PCA_Files = PCA_Files, PCA_model = PCA_model,
                     SpectralFilter = SpectralFilter, Input_Mask_File = Input_Mask_File,
                     Pix_Per_Partition = Pix_Per_Partition, nb_partitions = nb_partitions,
                     nbCPU = nbCPU, MaxRAM = MaxRAM, nbclusters = nbclusters, TypePCA = TypePCA,
                     Continuum_Removal = Continuum_Removal)

print("MAP BETA DIVERSITY")
map_beta_div(Input_Image_File = Input_Image_File, Output_Dir = Output_Dir, TypePCA = TypePCA,
             window_size = window_size, nb_partitions=nb_partitions, nbCPU = nbCPU, MaxRAM = MaxRAM,
             nbclusters = nbclusters)
```             

```r
# ===============================================================================
# rGEDI: An R Package for NASA's Global Ecosystem Dynamics Investigation (GEDI) Data Visualizing and Processing.
# ===============================================================================
# Authors: Carlos Alberto Silva, Caio Hamamura, Ruben Valbuena, Steven Hancock, Adrian Cardil, Eben N. Broadbent, Danilo R. A. de Almeida, Celso H. L. Silva Junior and Carine 
# Klauberg
# ===============================================================================

### Load rGEDI package

library(rGEDI)

### Set output dir

outdir=getwd()

### Read GEDI data

gedilevel1b<-readLevel1B(level1Bpath = paste0(outdir,"\\GEDI01_B_2019260084659_O04324_03_T05330_02_005_01_V002.h5"))
gedilevel2a<-readLevel2A(level2Apath = paste0(outdir,"\\GEDI02_A_2019260084659_O04324_03_T05330_02_003_01_V002.h5"))

### Get GEDI Pulse Geolocation (GEDI Level1B)

level1bGeo<-getLevel1BGeo(level1b=gedilevel1b,select=c("elevation_bin0"))
head(level1bGeo)

# Converting shot_number as "integer64" to "character"
level1bGeo$shot_number<-paste0(level1bGeo$shot_number)

# Converting level1bGeo as data.table to SpatialPointsDataFrame
library(sp)
level1bGeo_spdf<-SpatialPointsDataFrame(cbind(level1bGeo$longitude_bin0, level1bGeo$latitude_bin0),
                                        data=level1bGeo)

# Exporting level1bGeo as ESRI Shapefile
raster::shapefile(level1bGeo_spdf,paste0(outdir,"\\GEDI01_B_2020181152926_O08763_03_T00908_02_005_01_V002"))

### Get GEDI Elevation and Height Metrics (GEDI Level2A)

level2AM<-getLevel2AM(gedilevel2a)
head(level2AM[,c("beam","shot_number","elev_highestreturn","elev_lowestmode","rh100")])

##          beam       shot_number elev_highestreturn elev_lowestmode rh100
##  1: BEAM0000 19640002800109382           740.7499        736.3301  4.41
##  2: BEAM0000 19640003000109383           756.0878        746.7614  9.32
##  3: BEAM0000 19640003200109384           770.3423        763.1509  7.19
##  4: BEAM0000 19640003400109385           775.9838        770.6652  5.31
##  5: BEAM0000 19640003600109386           777.8409        773.0841  4.75
##  6: BEAM0000 19640003800109387           778.7181        773.6990  5.01

# Converting shot_number as "integer64" to "character"
level2AM$shot_number<-paste0(level2AM$shot_number)

# Converting Elevation and Height Metrics as data.table to SpatialPointsDataFrame
level2AM_spdf<-SpatialPointsDataFrame(cbind(level2AM$lon_lowestmode,level2AM$lat_lowestmode),
                                      data=level2AM)

# Exporting Elevation and Height Metrics as ESRI Shapefile
raster::shapefile(level2AM_spdf,paste0(outdir,"\\GEDI02_A_2020181152926_O08763_03_T00908_02_003_01_V002"))

### Getting GEDI Full-waveform (GEDI Level1B)

# Extracting GEDI full-waveform for a giving shotnumber
wf <- getLevel1BWF(gedilevel1b, shot_number="43240600300638451")

par(mfrow = c(2,1), mar=c(4,4,1,1), cex.axis = 1.5)

plot(wf, relative=FALSE, polygon=TRUE, type="l", lwd=2, col="forestgreen",
     xlab="Waveform Amplitude", ylab="Elevation (m)")
grid()
plot(wf, relative=TRUE, polygon=FALSE, type="l", lwd=2, col="forestgreen",
     xlab="Waveform Amplitude (%)", ylab="Elevation (m)")
grid()

### Plot waveform with RH metrics

shot_number = "43240600300638451"

png("43240600300638451_green_dmg2.png", width = 8, height = 6, units = 'in', res = 300)
plotWFMetrics(gedilevel1b, gedilevel2a, shot_number, rh=c(25, 50, 75, 90))
dev.off()
```

![ClimBiodiv_readme_figZFINAL](https://user-images.githubusercontent.com/67020853/135767758-dd8a719b-e418-4b49-80eb-9464ee6179ef.png)
Fig Z. 

### References

Alonso, K., Bachmann, M., Burch, K., Carmona, E., Cerra, D., De los Reyes, R., ... & Tegler, M. (2019). Data products, quality and validation of the DLR Earth Sensing Imaging Spectrometer (DESIS). Sensors, 19(20), 4471.

Amaral, C. H., Poulter, B., Fatoyinbo, T., Lagomasino, D., Taillie, P., Lizcano, G., Roman-Cuesta, R. M. (2021). Assessing the long-term effect of hurricanes on the Caribbean mangrove structure with GEDI L3 data. In SilviLaser 2021. Vienna, Austria, 28-30.09.2021.

Amaral, C. H., Poulter, B., Lagomasino, D., Fatoyinbo, T., Taillie, P., Lizcano, G., Roman-Cuesta, R. M. (2021). Climate change and coastal development affect vulnerability and recovery of mangroves impacted by tropical cyclones. (in prep.)

Dubayah, R., Hofton, M., Blair, J., Armston, J., Tang, H., Luthcke, S. (2020). GEDI L2A Elevation and Height Metrics Data Global Footprint Level V001. NASA EOSDIS Land Processes DAAC. Accessed 2021-10-03 from https://doi.org/10.5067/GEDI/GEDI02_A.001

Dubayah, R., Luthcke, S., Blair, J., Hofton, M., Armston, J., Tang, H. (2020). GEDI L1B Geolocated Waveform Data Global Footprint Level V001. NASA EOSDIS Land Processes DAAC. Accessed 2021-10-03 from https://doi.org/10.5067/GEDI/GEDI01_B.001

Dubayah, R.O., S.B. Luthcke, T.J. Sabaka, J.B. Nicholas, S. Preaux, and M.A. Hofton. 2021. GEDI L3 Gridded Land Surface Metrics, Version 1. ORNL DAAC, Oak Ridge, Tennessee, USA. https://doi.org/10.3334/ORNLDAAC/1865

Féret, J.-B., Asner, G.P., 2014. Mapping tropical forest canopy diversity using high-fidelity imaging spectroscopy. Ecol. Appl. 24, 1289–1296. 

Féret, J.-B., de Boissieu, F., 2019. biodivMapR: an R package for α‐ and β‐diversity mapping using remotely‐sensed images. Methods Ecol. Evol. 00:1-7. 

Hijmans, R. J., Van Etten, J., Cheng, J., Mattiuzzi, M., Sumner, M., Greenberg, J. A., ... & Hijmans, M. R. J. (2015). Package ‘raster’. R package, 734.

Koenker, R., Portnoy, S., Ng, P. T., Zeileis, A., Grosjean, P., & Ripley, B. D. (2018). Package ‘quantreg’. Cran R-project. org.

Kuhn, M. (2015). Caret: classification and regression training. Astrophysics Source Code Library, ascl-1505.

Liaw, A., & Wiener, M. (2002). Classification and regression by randomForest. R news, 2(3), 18-22.

Patel, K. (2021). 2020 Hurricanes Damage Vulnerable Mangroves. Accessed 2021-10-03 from https://earthobservatory.nasa.gov/images/147787/2020-hurricanes-damage-vulnerable-mangroves

Silva,C.A; Hamamura,C.; Valbuena, R.; Hancock,S.; Cardil,A.; Broadbent, E. N.; Almeida,D.R.A.; Silva Junior, C.H.L; Klauberg, C. rGEDI: NASA's Global Ecosystem Dynamics Investigation (GEDI) Data Visualization and Processing. version 0.1.9, accessed on October. 22 2020, available at: https://CRAN.R-project.org/package=rGEDI

Subirana, I., Sanz, H., & Vila, J. (2014). Building bivariate tables: The compareGroups package for R. Journal of Statistical Software, 57(1), 1-16.

Wei, T., Simko, V., Levy, M., Xie, Y., Jin, Y., & Zemla, J. (2017). Package ‘corrplot’. Statistician, 56(316), e24.

Wickham, H., Francois, R., Henry, L., & Müller, K. (2015). dplyr: A grammar of data manipulation. R package version 0.4, 3, p156.

### Acknowledgments

BNP-PARIBAS Foundation, through the CORESCAM project. Images from Thematic Mapper/Landsat-5 and Operational Land Imager/Landsat-8 are courtesy of the U.S. Geological Survey. Images from DESIS are courtesy of the DLR German Aerospace Center. Trees illustrations (Fig 1) are courtesy of the Integration and Application Network, University of Maryland.

### Cite ClimBiodiv

Amaral, C. H., Poulter, B., Fatoyinbo, T., Lagomasino, D., Taillie, P., Roman-Cuesta, R. M. (2021). ClimBiodiv - a methodological framework for cross-scale analysis on climate-biodiversity relations using remote sensing data. Accessed 2021-10-03 from https://github.com/CibeleAmaral/ClimBiodiv 
