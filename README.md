ellipsenm: An R package for ecological niche’s characterization using
ellipsoids
================
Marlon E. Cobos, Luis Osorio-Olvera, Jorge Soberón, Laura Jiménez, A.
Townsend Peterson, Vijay Barve, and Narayani Barve

  - [Project description](#project-description)
      - [Status of the project](#status-of-the-project)
  - [Package description](#package-description)
  - [Installing the package](#installing-the-package)
  - [Using the package](#using-the-package)
      - [Modeling ecological niches using
        ellipsoids](#modeling-ecological-niches-using-ellipsoids)

<br>

**This repository is for the project “Grinnellian ecological niches and
ellipsoids in R” developed during the program GSoC 2019.**

<br>

## Project description

Student: *Marlon E. Cobos*

GSoC Mentors: *Luis Osorio-Olvera, Vijay Barve, and Narayani Barve*

Motivation:

Ecological niche modeling represents a set of methodological tools that
allow researchers to characterize and analyze species ecological niches.
Currently, these methods are used widely and their applications include
disease risk mapping, climate change risk predictions, conservation
biology, among others. Physiological data suggests that Grinnellian
ecological niches are convex in nature and they may probably have an
ellipsoidal form when multiple dimensions are considered. However, among
the diverse available software to characterize ecological niches,
algorithms to model these niches as ellipsoids in the environmental
space are scarce. Given the need for more solutions, the **ellipsenm**
package aimed for developing specialized tools to perform multiple
analyses related to ecological niches of species.

### Status of the project

At the moment we have completed the first of three phases. We have made
few modifications to the list of original products that have helped us
to improve the package functionality. Next steps are to complete
scheduled activities to obtain all products.

All commits made can be seen at the
<a href="https://github.com/marlonecobos/ellipsenm/commits/master" target="_blank">complete
list of commits</a>.

Following you can find a brief description of this R package, as well as
some examples of its use (still in development).

<br>

## Package description

The **ellipsenm** R package implements multiple tools to help in using
ellipsoid envelopes to model ecological niches of species. Handly
options for calibrating and selecting models, producing models with
replicates and projections, and assessing niche overlap are included as
part of this package. Other functions implemented here are useful to
perform a series of pre- and post-modeling analyses.

<br>

## Installing the package

**ellipsenm** is in a GitHub repository and can be installed and/or
loaded using the following code (make sure to have Internet connection).

Note: Try the following code first. If you have any problem during the
installation, restart your session, close other R sessions you may have
open, and try again. If during the installation you are asked to update
packages, please do it (select the option that says All). If any of the
packages gives an error, please install it alone using
install.packages(), then try re-installing **ellipsenm** again. Also, it
may be a good idea to update R and RStudio (if you are using it).

``` r
# Installing and loading packages
if(!require(devtools)){
    install.packages("devtools")
}
if(!require(ellipsenm)){
    devtools::install_github("marlonecobos/ellipsenm")
}
library(ellipsenm)
```

<br>

## Using the package

### Modeling ecological niches using ellipsoids

An ecological niche, from a Grinnellian perspective, is the set of
environmental conditions that allow a species to maintain populations
for long periods of time, without immigration events. Models created
with the ellipsenm package are ellipsoid envelope models and assume that
a species ecological niche is convex, has an only optimum, and the
species response to each variable covaries with the response to other
variables. Mahalanobis distances are used to represent how far is each
combination of environmental conditions from the optimum (ellipsoid
centroid). Suitability values result by default from a multivariate
normal transformation of Mahalanobis distances. Therefore, maximum
values of suitability will be close to the centroid and minimum values
will be close to the border of the ellipsoid.

We encourage the users to check the function’s help before using it.
This is possible using the code below:

``` r
help(ellipsoid_model)
```

Now the examples. Three distinct ways to create ecological niche models
using **ellipsenm** are presented below:

<br>

**Creating a simple ecological niche model**

When models are created this way, the whole dataset is used for fitting
the ellipsoids.

``` r
# reading data
occurrences <- read.csv(system.file("extdata", "occurrences.csv",
                                    package = "ellipsenm"))

# raster layers of environmental data
vars <- raster::stack(list.files(system.file("extdata", package = "ellipsenm"),
                                 pattern = "bio", full.names = TRUE))

# creating the model
ell_model <- ellipsoid_model(data = occurrences, species = "species",
                             longitude = "longitude", latitude = "latitude",
                             raster_layers = vars, method = "covmat", level = 99,
                             replicates = 1, prediction = "suitability",
                             return_numeric = TRUE, format = "GTiff",
                             overwrite = FALSE, output_directory = "ellipsenm_model")

class(ell_model)
# check your directory, folder "ellipsenm_model"
```

<br>

**Modeling an ecological niche with replicates**

When models are replicated, sub-samples of the data are used to create
each replicate. Mean, minimum, and maximum ellipsoid models are also
calculated. See more details about sub-sampling in the function’s help.

``` r
# reading data
occurrences <- read.csv(system.file("extdata", "occurrences.csv",
                                    package = "ellipsenm"))

# raster layers of environmental data
vars <- raster::stack(list.files(system.file("extdata", package = "ellipsenm"),
                                 pattern = "bio", full.names = TRUE))

# creating the model with replicates
ell_model1 <- ellipsoid_model(data = occurrences, species = "species",
                              longitude = "longitude", latitude = "latitude",
                              raster_layers = vars, method = "covmat", level = 99,
                              replicates = 5, prediction = "suitability",
                              return_numeric = TRUE, format = "GTiff",
                              overwrite = FALSE, output_directory = "ellipsenm_model1")

class(ell_model1)
# check your directory, folder "ellipsenm_model1"
```

<br>

**Example of an ecological niche model with projections**

Ellipsoid envelope models can also be projected to other scenarios. This
is one example using a RasterStack of layers. Projections can be done to
multiple scenarios in same modeling process by changing the type of
*projection\_variables*. Check the function’s help for more details.

``` r
# reading data
occurrences <- read.csv(system.file("extdata", "occurrences.csv",
                                    package = "ellipsenm"))

# raster layers of environmental data
vars <- raster::stack(list.files(system.file("extdata", package = "ellipsenm"),
                                 pattern = "bio", full.names = TRUE))

# creating the model with projections
pr_vars <- raster::stack(system.file("extdata", "proj_variables.tif",
                                     package = "ellipsenm"))
names(pr_vars) <- names(vars)

ell_model2 <- ellipsoid_model(data = occurrences, species = "species",
                              longitude = "longitude", latitude = "latitude",
                              raster_layers = vars, method = "covmat", level = 99,
                              replicates = 3, replicate_type = "bootstrap",
                              bootstrap_percentage = 75, projection_variables = pr_vars,
                              prediction = "suitability", return_numeric = TRUE,
                              format = "GTiff", overwrite = FALSE,
                              output_directory = "ellipsenm_model2")

class(ell_model2)
# check your directory, folder "ellipsenm_model2"
```

Other examples will be added soon.
