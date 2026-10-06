# 🌳 Makueni County Tree Cover & Land Cover Monitoring — 2026

## 📌 Project Overview

This repository documents a county-scale **tree cover and land-cover assessment for Makueni County, Kenya**, using Sentinel-2 satellite imagery, Google Earth Engine, remote sensing indices, canopy-height information, field data and Random Forest classification.

The work was undertaken as part of a **JICA-supported tree cover monitoring and new tree planting initiative** at the Kenya Forest Service (KFS).

The workflow combined multi-season satellite observations with derived vegetation and moisture indicators, canopy-height information and field-based training data to produce a **10-class land-cover classification** and a subsequent **tree/non-tree tree-cover product**.

---

## 🎯 Objectives

The main objectives were to:

* Assess and extract tree cover across Makueni County.
* Produce a county-scale land-cover classification.
* Produce final geospatial outputs for project use.

---

# 🛰️ Data & Inputs

### Sentinel-2

The primary satellite dataset was:

**COPERNICUS/S2_SR_HARMONIZED**

Six spectral bands were used:

| Band | Band Name  | General application                      |
| ---- | ---------- | ---------------------------------------- |
| B2   | Blue       | Spectral characterization                |
| B3   | Green      | Vegetation / land-cover discrimination   |
| B4   | Red        | Vegetation analysis                      |
| B8   | NIR        | Vegetation analysis                      |
| B11  | SWIR 1     | Moisture / vegetation characterization   |
| B12  | SWIR 2     | Land-cover and moisture characterization |

### Canopy Height

A canopy-height dataset was incorporated as an additional predictor.

A **2-metre threshold** was subsequently used to generate a tree-height criterion.

### Training Data

Training points representing the project's original 10 land-cover classes were used to extract predictor values and train the Random Forest classifier.

---

# 📅 Multi-Season Satellite Compositing

Two temporal Sentinel-2 datasets were prepared.

### Dry Season

**15 January 2026 – 28 February 2026**

Images were filtered using a maximum scene cloudiness threshold of **20%**.

Scene Classification Layer (SCL) information was used to retain selected land-cover-related pixels before creating a median composite.

### Wet Season

**2 March 2026 – 31 May 2026**

Images were filtered using a maximum scene cloudiness threshold of **60%**.

The wet-season workflow incorporated the **Sentinel-2 Cloud Probability** dataset and combined cloud-probability information with Sentinel-2 imagery using matching system indices.

Clouds and cloud shadows were subsequently identified and masked before generating the median wet-season composite.

---

# ☁️ Cloud & Shadow Masking

The wet-season preprocessing workflow incorporated:

* Sentinel-2 cloud probability
* Cloud probability threshold of **40%**
* Dark-pixel detection using NIR reflectance
* Cloud-shadow projection
* SCL-based exclusion of cloud/shadow-related classes
* Spatial morphological operations
* A **50-metre buffer** around detected cloud/shadow areas

This produced a cloud- and shadow-masked wet-season composite suitable for subsequent analysis.

---

# 🌱 Spectral Indices

Several vegetation and moisture indicators were derived from both seasonal composites.

### NDVI

Normalized Difference Vegetation Index:

**NDVI = (NIR − Red) / (NIR + Red)**

Both dry- and wet-season NDVI were generated.

### NDMI

Normalized Difference Moisture Index:

**NDMI = (NIR − SWIR) / (NIR + SWIR)**

Dry- and wet-season NDMI were generated.

### EVI

Enhanced Vegetation Index was calculated using the blue, red and NIR bands.

Both dry- and wet-season EVI were generated.

### Seasonal Differences

Temporal differences between the wet and dry periods were calculated:

* **dNDVI**
* **dNDMI**
* **dEVI**

These variables provided additional information about seasonal vegetation and moisture behaviour.

---

# 🧩 Predictor Stack

The final predictor stack combined:

### Sentinel-2 Reflectance

* B2 dry
* B3 dry
* B4 dry
* B8 dry
* B11 dry
* B12 dry
* B2 wet
* B3 wet
* B4 wet
* B8 wet
* B11 wet
* B12 wet

### Spectral Indices

* NDVI dry
* NDVI wet
* NDMI dry
* NDMI wet
* EVI dry
* EVI wet
* dNDVI
* dNDMI
* dEVI

### Structural Information

* Canopy height
* Canopy height ≥ 2 m criterion

This predictor stack was used as the input to the Random Forest classification.

---

# 🌍 Land-Cover Classification

The classification consisted of **10 original land-cover classes**:

| Class | Land Cover        |
| ----: | ----------------  |
|     1 | Dense Forest      |
|     2 | Moderate Forest   |
|     3 | Open Forest       |
|     4 | Wooded Grassland  |
|     5 | Open Grassland    |
|     6 | Perennial Cropland|
|     7 | Annual Cropland   |
|     8 | Agroforestry      |
|     9 | Wetland           |
|    10 | Other Land        |

---

# 🌲 Random Forest Classification

A **Random Forest classifier** implemented in Google Earth Engine was used.

Key parameters included:

| Parameter               | Value |
| ----------------------- | ----: |
| Number of trees         |   300 |
| Bag fraction            |   0.5 |
| Minimum leaf population |     1 |
| Random seed             |    42 |

Predictor values were sampled at the training points using a **10-metre scale**.

---

# 🧪 Training & Validation

The sampled dataset was divided into training and validation subsets using a **stratified 70/30 split**.

A fixed random seed of **42** was used to support reproducibility.

The validation dataset was independently classified using the trained Random Forest model.

---

# 📊 Accuracy Assessment

Classification performance was evaluated using an error/confusion matrix.

The workflow calculated:

* **Overall accuracy**
* **Kappa coefficient**
* **Producer's accuracy**
* **Consumer's accuracy**

The validation results were exported as a CSV table for further analysis and reporting.

---

# 🌳 Tree Cover Extraction

The final tree-cover product was derived from the 10-class classification rather than treating every pixel in the classification as automatically representing a tree.

### Dense Forest

All pixels classified as **Dense Forest** were included as tree cover.

### Mixed Classes

For:

* Moderate Forest
* Open Forest
* Wooded Grassland
* Perennial
* Agroforestry

only pixels meeting the defined tree criteria were extracted.

### Tree Criteria

A pixel had to satisfy all three conditions:

**1. Canopy height ≥ 2 m**

**2. Wet-season NDVI ≥ 0.35**

**3. Wet-season EVI ≥ 0.20**

This approach was used to reduce the inclusion of non-tree vegetation within mixed land-cover classes.

---

# 🗺️ Final Tree Cover Product

The final tree-cover layer was generated by combining:

* Dense Forest
* Tree pixels from Moderate Forest
* Tree pixels from Open Forest
* Tree pixels from Wooded Grassland
* Tree pixels from Perennial
* Tree pixels from Agroforestry

The result was a binary **tree/non-tree raster**.

---

# 📐 Area Statistics

The workflow calculated area statistics for:

### Land Cover

For each of the 10 classes:

* Area in hectares
* Area in km²
* Percentage of the study area

### Tree Cover

For tree/non-tree classes:

* Tree area
* Non-tree area
* Area in hectares
* Area in km²
* Percentage

A final county-level **tree-cover percentage** was also calculated.

---

# 📤 Outputs

The Google Earth Engine workflow generated the following principal outputs:

### Raster

* `Makueni_classified_10class_2026`
* `Makueni_treecover_final_2026`
* `Makueni_mixed_class_tree_extraction_2026`

### Tables

* 10-class land-cover area statistics
* Tree-cover area statistics
* Final tree-cover percentage
* Validation dataset and classification results

Raster outputs were exported as **GeoTIFF** at 10-metre resolution.

Tabular outputs were exported as **CSV**.

The export coordinate reference system was:

**EPSG:32737 — WGS 84 / UTM Zone 37S**

---

# 🛠️ Software & Technologies

* **Google Earth Engine**
* **QGIS**
* **SEPAL**
* **Random Forest**
* **Remote Sensing**
* **GIS / Spatial Analysis**

---

# 👤 My Contribution

My work on the project involved practical participation across the remote sensing, GIS and field-data workflow.

Key activities included:

* Sentinel-2 imagery preparation and analysis
* Multi-season image compositing
* Cloud and cloud-shadow masking
* Generation of vegetation and moisture indices
* Development of remote sensing predictor variables
* Integration of canopy-height information
* Preparation and use of training data
* Random Forest classification
* Accuracy assessment
* Tree-cover extraction
* Area and percentage calculations
* GIS processing and compilation
* Field data collection
* Preparation and compilation of final geospatial outputs

---

# 🔄 Overall Workflow

```text
                 Makueni County AOI
                         │
                         ▼
              Sentinel-2 Satellite Data
                         │
              ┌──────────┴──────────┐
              ▼                     ▼
        Dry Season              Wet Season
              │                     │
              ▼                     ▼
       SCL Filtering       Cloud Probability
              │             + Shadow Masking
              ▼                     │
       Median Composite             ▼
              │              Median Composite
              └──────────┬──────────┘
                         ▼
                Spectral Indices
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
            NDVI       NDMI        EVI
              │          │          │
              └──────┬───┴───┬──────┘
                     ▼       ▼
                Seasonal Differences
                     │
                     ▼
              Predictor Stack
                     │
            + Canopy Height
                     │
                     ▼
               Training Data
                     │
                     ▼
             Random Forest
               10 Classes
                     │
                     ▼
            Accuracy Assessment
                     │
                     ▼
          10-Class Land Cover Map
                     │
                     ▼
             Tree Extraction
                     │
       ┌─────────────┴─────────────┐
       ▼                           ▼
 Dense Forest              Mixed Classes
    = ALL                 = Tree Criteria
                                  │
                         Height ≥ 2 m
                         NDVI ≥ 0.35
                         EVI ≥ 0.20
                                  │
                                  ▼
                       Final Tree Cover Map
                                  │
                                  ▼
                    Area & Percentage Statistics
```

---

# 🔐 Data & Reproducibility

This repository documents the technical methodology, analytical workflow and selected publicly shareable evidence from the project.

Where appropriate, the workflow can be adapted using publicly available datasets for demonstration and reproducibility.

**Due to project data and institutional confidentiality requirements, source scripts, training datasets and certain project materials are not publicly distributed. The repository presents selected non-sensitive outputs and a high-level description of the methodology.**

---

## 🌱 Key Takeaway

This project demonstrates the integration of **multi-season satellite remote sensing, spectral indices, canopy-height information, machine learning, field observations and GIS** to produce a county-scale tree-cover and land-cover assessment.

It provided practical experience in taking a geospatial project from **satellite data preparation and preprocessing through classification, validation, tree-cover extraction, spatial statistics and final GIS compilation**.



