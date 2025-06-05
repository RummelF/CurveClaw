# CurveClaw

## OVERVIEW
CurveClaw was developed as a tool to extract numerical data from images, the aim was to create a modular system which allowed for user control at all levels. Further method scripts can be added to the methods folder to address other requirements.

Development is still ongoing and so far has only been tested on windows.

## BASIC WORKFLOW

- Launch CurveClaw (see section: "Running")
- Select the image file you wish to edit, this needs to be in a suitable format such as .png or .jpeg.
- Optional: The Draw Lines Button will allow for additional lines with desired thickness and colour to be drawn on the image at any point.
- Optional: The Hash Connector Button allows for hashes to be connected.
- Extract the graph, this should identify the graph area and crop the image, MAKE SURE the graph area is defined on all four sides, as the bounding box of the graph is used to crop. The graph may also be straightened, rotated or flipped as desired (thought to enable these options it should be straightened first). At the moment only 2D plots of binary or ternary type diagrans are supported.
- Optional: Automatic Cleaning provides a set of quick cleaning tools to edit the graph such as discarding small features, thinning and thickening as well as filling small areas.
- Use Curve Claw to define individual curves or areas by selecting the desired lines
- Define the graph information in the Data extractor, as well as the desired method to extract the output data.
- Output is found in the output folder as .txt and .png files, Images saved along the way are saved in the preview and curves folders and can be edited between steps if needed using other image editing software.

## FURTHER DETAILS

### Extraction

- Extraction of the graph area may be worse if the image is of poor quality, ensure high contrast and even lighting. 
   - If this still does not work, use the draw tool to draw white lines around the outside of the graph and black lines on the graph bounding box, this should maximize the contrast thus giving the best chance of the graph being identified. 
   - Alternatively you can crop the image yourself and directly proceed to the next step after selecting this as the start point. The extracted image is saved in the preview folder.

### Cleaning

- The Automatic Clean window allows the areas to be kept to be specified.
   - The algorithm ranks all black connected features by their pixel counts and will delete all but the *n* biggest.
      - *n* is the number specified. 
   - Generally this should be performed first.
- If thin gridlines are present thinning and then thickening the input image may help remove these.
   - similar results can be achieved by the same operation on the output image.
- The Histogram shows the distribution of area sizes as a log scale, and gives some indication on how many areas should be kept.
- After cleaning is performed small areas within the graph can be filled.
   - This is useful if hollow circles are used to mark points, here a percentage can be specified.
      - This is the percentage of the graph area used to determine if an area should be filled or not. 
- Thinning of all lines can be performed, this will thin all features without increasing the number of white areas present in the image.
   - Features not enclosing white pixels are removed entirely. This may not always be useful and often the curve selection will yield a better result in the following steps. 
- At any point changes can be undone by reloading the input, or discarding and exiting. 
- Once satisfied, the output should be saved. 
   - The output overwrites the input in the preview folder. 

### Curve Selection  

- The Curve selection feature is the main way in which the user can select areas and lines of interest. 
- **New Line** allows the user define a new line by selecting points on the line.
- **Select Points** allows the user to select pixels of interest
   - Note that wherever the user selects the program will find the closest black pixel and use this as the selected position
   - Note that if a pixel is in a feature which connects to the border of the image instead a border pixel is selected
   - This means you should only need to click in the rough area in which you want to select a pixel
- Once 2 points are selected the algorithm will attempt to connect these as follows:
   - If the two points are not part of the same feature:
      - A straight line is drawn between them.
   - If they are part of the same feature:
      - The feature is used to construct a maze where the final path may only navigate pixels on that feature
      - The shortest continous path is constructed.
- The last selected point of the current working line can be deleted iteratively. 
   - However this will not delete points in old lines, so ensure you’re fully satisfied with a line before pressing **New Line**.
- The line thickness can be controlled if needed.
   - This only affects the current line.
- **Drag Current Line** lets the user click and drag a line:
   - The initial cursor position finds the closest pixel on the curve
   - The release will determine the new position following the usual rules
- Generally the start and end of a line is sufficient for defining lines, but sometimes 3 or more points need to be defined.
- By virtue of the maze solving algorithm defining lines right to left is slightly different to left to right in some cases.
- Once all areas are defined, or all lines of interest are defined separately remember to save the output. 

### Data Extraction

- First select the curves you wish to work on:
   - Select a specific file or files
   - Automatic selection will select all curves which were manually selected in the curve selection section

- You may overlay these to display them
   - The number of areas defined by lines is displayed 

- The user may draw on the overlayed image, fill small areas or thin lines as described in the **Cleaning** section

- **Data Extraction mode** is a dropdown allowing binary, line or ternary to be selected
   - **Binary** indicates that there is an x and y axis, here the white areas on the image are analysed
   - **Ternary** indicates a barycentric x, y and z axis plot, here the white areas on the image are analysed
   - **Line** indicates that each selected file should be analysed seperately such that the position of each pixel on the line is returned
 
- **X, Y, Z Dimesions** defines the coordinates of the axis in a comma separated list “()” are for clarity: 
   - Entered as (xmin, xmax), (ymin, ymax),…

- **X, Y, Z Grid Control** allows some control over the number of sample points on the image
   - E.g. on a x/y plot two values must be defined in the order x, y. This will be the number of sample points along the axis
      - E.g. 20,20 would result in 20 steps along each axis and so 400 samples in total

- **Log Scales** is a true false comma list for x, y and z axis if applicable in that order, if left blank it will be false in all cases. 
   - E.g. log y axis and normal x axis needs to defined as false, true 
   - Note 1, yes and true are all accepted, the case is not important.
   - 0, false and no are accepted for linear axis.
   
- **Method Selection** allows the selection of the method script to be used to analyse the images and data defined.
   - **Binary Phase Assign** the grid specified is used to construct an array of test points, in each case the coordinate are recorded and the area this point is in.
   - **Binary Probability** the grid specified is used to construct an array of test points, in each case the coordinate is used to calculate the Probability of this point of being in each area of the diagram.
   - **Ternary Phase Assign** as the equivalent binary version except here we use barycentric coordinates.
   - **Ternary Probability** as the equivalent binary version except here we use barycentric coordinates.
   - **Line Data** here the X,Y,Z Grid control defines the number of boxes the image is split into along each axis, this is used to create a less fine determination of data. For each box only one pixel of the line, if the line intersects the box, is used to generate coordinates. 

- Once the method is selected further data to describe the curve/areas may be entered.

- **Enter Meta Data** allows for meta data describing the image to be saved.

- **Extract Data** will start the data extraction, this may take a few minutes depending on the inputs.
   - The lower the image resolution the faster the process.
   - The output is saved in the output folder. 
   
## Development

### Prerequisites
- Install [`uv`](https://docs.astral.sh/uv/getting-started/installation/)

## Running
```shell
uv run curveclaw
```

##  How to Run the Package

You can run the `curveclaw` package using any of the below **methods**:  

### **Method 1: Download from GitHub (Manual Installation)**
1. **Download** either of the following files from the release page:  
   - **`curveclaw-<version>.tar.gz`**  
   - **`curveclaw-<version>-py3-none-any.whl`**  
2. **Install the downloaded package** using pip:  
```bash
pip install path/to/curveclaw-<version>.tar.gz
# OR
pip install path/to/curveclaw-<version>-py3-none-any.whl
```
3. After successful installation, you can run the package using:
```bash
curveclaw
```

### **Method 2: Install manually using uv**

1. **Download** either of the following files from the release page:  
   - **`curveclaw-<version>.tar.gz`**  
   - **`curveclaw-<version>-py3-none-any.whl`**  
2. **Install and run the downloaded package** using uv: 

Run the following command:  
```bash
uv run --with ./curveclaw/dist/curveclaw-0.1.6-py3-none-any.whl --no-project -- python -m curveclaw
```
 
