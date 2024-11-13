# GEE
Google Earth Engine Practice Code

Here’s a sample `README.md` file in Markdown format for your GitHub repository, detailing the Earth Engine code and its functionality:

```markdown
# Land Use Land Cover (LULC) Classification using Google Earth Engine (GEE)

This repository contains Google Earth Engine (GEE) code to classify Land Use and Land Cover (LULC) using Landsat 8 satellite imagery. The code applies a Support Vector Machine (SVM) classifier to classify four land cover types: water, settlement, vegetation, and agriculture. It uses specific training data points for each class and evaluates the classification accuracy using a confusion matrix and Kappa coefficient.

## Table of Contents
- [Description](#description)
- [Requirements](#requirements)
- [Data Sources](#data-sources)
- [Code Overview](#code-overview)
- [Output](#output)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)

---

## Description

The script performs LULC classification with the following steps:
1. Loads and filters Landsat 8 imagery within a specified date range and region of interest (ROI).
2. Prepares training samples for four land cover classes: water, settlement, vegetation, and agriculture.
3. Applies an SVM classifier to classify the land cover types based on spectral bands.
4. Evaluates classification accuracy using a confusion matrix and Kappa coefficient.
5. Exports the classified image to Google Drive for further analysis or visualization.

## Requirements

- **Google Earth Engine Account**: Access to the [Google Earth Engine](https://earthengine.google.com/) platform.
- **Training Data Points**: The training data for each land cover class (water, settlement, vegetation, agriculture) should be defined as `ee.FeatureCollection`.

## Data Sources

The script uses the following data:
- **Landsat 8 Surface Reflectance Tier 1 Collection (LANDSAT/LC08/C02/T1_TOA)**: Provides top-of-atmosphere reflectance data used for LULC classification.

## Code Overview

### Define the Region of Interest (ROI)
```javascript
var roi = ee.Geometry.Polygon(
  [[[-121.02, 37.06], [-121.02, 36.77], [-120.73, 36.77], [-120.73, 37.06]]]
);
```

### Load and Filter Landsat 8 Imagery
```javascript
var l8_composite = ee.ImageCollection("LANDSAT/LC08/C02/T1_TOA")
                    .filterDate('2024-04-01', '2024-05-30')
                    .filterBounds(roi)
                    .filter(ee.Filter.lt('CLOUD_COVER', 2))
                    .median()
                    .clip(roi);
```

### Define Visualization Parameters
```javascript
var vis_params = {
  bands: ['B5', 'B4', 'B3'],
  min: 0,
  max: 3000,
  gamma: 1.2
};
```

### Define Training Data Points and Labels
```javascript
var trainingPoint = water.merge(Settlement).merge(Vegetation).merge(Agriculture);
var trainingSample = trainingPoint.map(function(point) {
  return point.buffer(30);
});
```

### Select Bands and Train SVM Classifier
```javascript
var classifier = ee.Classifier.libsvm().train({
  features: trainSet,
  classProperty: 'class',
  inputProperties: bands
});
```

### Evaluate Classification Accuracy
The classifier's accuracy is calculated using a confusion matrix and Kappa coefficient:
```javascript
var confusionMatrix = ee.ConfusionMatrix(testSet.classify(classifier).errorMatrix({
  actual: 'class',
  predicted: 'classification'
}));
print('Overall Accuracy', confusionMatrix.accuracy());
print('Kappa', confusionMatrix.kappa());
```

### Export Classified Image to Google Drive
```javascript
Export.image.toDrive({
  image: classified,
  description: 'LULC',
  folder: 'GEE',
  scale: 30,
  region: roi,
  maxPixels: 1e13
});
```

## Output

- **Map Layers**: Display of the Landsat 8 composite and classified land cover map on the Earth Engine map viewer.
- **Exported Image**: The classified LULC map is exported to Google Drive in the specified folder (`GEE`) with 30-meter resolution.

## Usage

1. Clone or download this repository.
2. Copy the code to your Google Earth Engine script editor.
3. Define your ROI and training data points for each land cover class as `ee.Geometry` or `ee.FeatureCollection`.
4. Run the code to perform LULC classification and export the results to Google Drive.

## Contributing

Feel free to submit issues, fork the repository, and create pull requests. Contributions are welcome!

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
```

This README provides a comprehensive overview of the project, helping others understand, set up, and use your code effectively.
