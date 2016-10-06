# cellprofiler-sanjana

## Description

### Input data

2D images
DAPI, GFP-Golgi, A647-CopBII

### Aim

Measure the correlation of CopII knockdown (A647 signal) and diffusion of Golgi signal.

### Workflow

#### Measure Golgi condensation

Compute ratio of tophat filtered signal vs. whole cell intensity; completely diffuse gives 1; completely condensed gives 0.

### Considerations

- One has to make sure that the A647 staining procedure does not introduce more variation than the actual treatment dependent effect. One way to do this is to have several wells with the same treatment to see that the intra-treatment variation between wells is less than the inter-treatment variation. 
- In order to judge complete knockdown we need the non-specific fluorescence of the cells in the A647 channel. You can add wells where you leave out the primary antibody against the CopII.

### Treatments

Neg9-siRNA  + CopII_primary_AB + CopII_2nd_AB-A647 (6 Wells)
CopII-siRNA + CopII_primary_AB + CopII_2nd_AB-A647 (6 Wells)
Neg9-siRNA  +                  + CopII_2nd_AB-A647 (2 Wells)

### Microscopy

Widefield is better for total A647 per cell quantification; usually 20x/0.75 air lens is good enough to see Golgi condensation changes, in fact for the morphological quantification lower NA (e.g. 20x/0.3) is even better because the whole Golgi is in focus; disadvantage of lower NA is that you collect less light; thus you will need more exposure times.

#### Adjust settings

Adjust the A647-CopII channel in a well with Neg9, because there it should be the brightest; choose settings such that the brightest pixels are around 2000 gray values for the 12bit camera where the maximum is 4095.

Adjust the GFP-Golgi channel in a well with Neg9, because there it should be the brightest, because it is condensed; choose settings such that the brightest pixels are around 2000 gray values for the 12bit camera where the maximum is 4095.

### Data management

For ScanR data please use the "Transfer tool" to have meaningful filenames and get rid of the unsigned int issue (32768) and get your data scaled from 0 to 4095.

### Plots to check the outcome

Check well-dependent effects (should be none ideally):
- cell-based jitterplot: x-axis is treatment_well_replicate; y-axis is A647 intensity
- cell-based jitterplot: x-axis is treatment_well_replicate; y-axis is condensation-score

Check knock-down vs. Golgi morphology:
- cell-based scatterplot: x-axis is A647 signal, y-axis is Golgi condensation; color the dots by 'treatment'


This automated image analysis workflow finds nuclei in fluorescence images (DAPI stain) and measures the fluorescence intensity (transient transfection of a YFP-tagged protein) around each nucleus. Based on the measured intensity cells are classified as transfected or untransfected. The Motivation to develop this workflow was to measure the transfection efficiency of the YFP-tagged protein.

## Teaching material

https://www.youtube.com/watch?v=IXsTba9Nxok


## Prerequisites

- Install [CellProfiler](http://www.cellprofiler.org/) (the pipeline has been developed for version 2.1.1)
- Download the files in this GitHub repository and open the project file (.cpproj) in CellProfiler


## Example data

- The YFP image is heavily saturated. Normally this is to be avoided! However here we only measure whether cells are transfected or not so this is not a problem.


## CellProfiler modules that may need adaptation

#### LoadImages

Input image file location: folder with your data (can contain subfolders)

#### NamesAndTypes 

The ending of the image filenames is important (and case sensitive). It must match the settings in the NamesAndTypes module of CellProfiler.

#### IdentifyPrimaryObjects

- Explore different "Thresholding Methods"
- If you feel that one method gives a decent threshold, but always slightly too high or low, you can adapt the "Threshold correction factor"
- Explore different "Methods to distinguish clumped objects". Note that this uses the "Typical diameter of objects" that you entered above (Read the help inside CP).

#### IdentifySecondaryObjects and IdentifyTertiaryObjects

As there is no dedicated staining for the Cytoplasm, we construct a "ring" around the nucleus to measure the intensity of the transfected protein.

- Change the "Number of pixels by which to expand the primary objects" to change the size of the cytoplasmic ring.

#### RescaleIntensity

Only for better display! This is not used for measurements!


#### DisplayDataOnImage

- Check the numbers here and decide on a threshold value above which you consider cells as transfected. You should have negative control images without transfected cells and take the "brightest" (most autofluorescent) cell of those samples as a threshold.


#### SaveImages

- You may adapt how and where the output images are stored.


#### ExportToSpreadsheet

Count_TransfectedNuclei: Number of transfected cells in this image
Count_Nuclei: Number of all cells in this image


## Important advice

- Always check all the output images to see whether the software did what you expected it to do. Tip: you can load all output images at once into ImageJ using [File>Import>Image Sequence..] with "File name contains" set to jpeg.
- If something is not OK in the output images you have to go back to the CP pipeline and adapt it!

## Typical troubleshooting 

- Not all nuclei are found: you probably have to change the Thresholding in IdentifyPrimaryObjects or change the "Typical diameter of objects" (If "Discard objects outside the diameter range" is active).
